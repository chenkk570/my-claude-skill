# PRD Generator Skills

> 面向 AI 时代的产品需求文档生成工具包
> 让 AI 直接消费 PRD、生成可用代码，而不是「能跑但不能用」的代码。

[English Version →](./README.md)

---

## 背景

传统 PRD 是**意图传达文档**——写给人看，依赖工程师的职业经验补全边界场景、字段命名、异常处理等未定义内容。这套机制在人工编码时代运转良好。

当 AI 成为代码的主要生产者，问题暴露了：

- AI 不参与口头沟通，无法调用隐性知识
- PRD 的歧义直接转化为错误代码
- 缺乏验收标准，AI 只能覆盖正常路径，边界场景随机处理

本项目提供一套**可执行规格文档**的 PRD 设计标准与配套工具，使 AI 能够直接消费 PRD 并生成符合预期的高质量代码。

---

## 文件结构

```
.
├── README.md                               # 中文说明（本文件）
├── README_EN.md                            # English version
│
├── prd-generator/
│   └── SKILL.md                            # 标准版 Skill：单次完整生成
│
├── prd-generator-iterative/
│   └── SKILL.md                            # 渐进式 Skill：大型模块分阶段确认生成
│
├── templates/
│   ├── PRD模板-新版（AI可执行规格）.md      # AI 可直接消费的 PRD 模板
│   ├── PRD模板-旧版（面向程序员）.md        # 传统 PRD 模板（对比参考）
│   └── PRD生成-需求信息输入模板.md          # 配合标准版 Skill 使用的 PM 填写表
│
└── docs/
    └── PRD升级方法论-汇报文档.docx          # 背景、目标、方案、价值的完整汇报文档
```

---

## 两个 Skill 的选择

| | `prd-generator` | `prd-generator-iterative` |
|---|---|---|
| **适用场景** | 单一功能点、边界清晰的小需求 | 包含多个子模块的大型功能 |
| **生成方式** | 一次性完整生成 | 拆解 → 确认 → 逐功能点生成 → 确认 |
| **PM 参与度** | 填写输入表后等待结果 | 每个阶段结束后需确认才推进 |

**判断标准**：如果这个需求你能在 5 分钟内口头说清楚，用标准版；如果需要拆解子功能、不确定边界在哪，用渐进式。

---

## 安装与使用

### Claude Code

Claude Code 通过项目根目录的 `CLAUDE.md` 文件加载上下文指令，Skill 内容写入该文件后，每次对话会自动读取。

**Step 1：克隆本仓库**

```bash
git clone https://github.com/chenkk570/my-claude-skill.git
```

**Step 2：将 Skill 写入项目的 CLAUDE.md**

```bash
# 标准版
cat my-claude-skill/prd-generator/prd-generator-skill.md >> your-project/CLAUDE.md

# 渐进式
cat my-claude-skill/prd-generator/prd-generator-iterative-skill.md >> your-project/CLAUDE.md
```

项目中还没有 `CLAUDE.md` 则直接复制：

```bash
cp my-claude-skill/prd-generator/prd-generator-skill.md your-project/CLAUDE.md
```

**Step 3：在项目目录启动 Claude Code**

```bash
cd your-project
claude
```

**Step 4：触发 Skill**

直接描述你的需求，Claude Code 会自动识别并按 Skill 流程执行：

```
使用 prd-generator-iterative 帮我写商家入驻模块的 PRD
```

> **提示**：`CLAUDE.md` 已有其他内容时，在文件末尾追加并用 `---` 分隔，避免干扰原有指令。两个 Skill 可同时写入同一个 `CLAUDE.md`，AI 会根据需求规模自动选择合适的版本。

---

### Claude.ai（Projects）

**Step 1**：进入 [claude.ai](https://claude.ai)，打开或新建一个 Project

**Step 2**：点击左侧 Project 名称旁的设置图标 → **「Project instructions」**

**Step 3**：将 prd-generator-skill.md和prd-generator-iterative-skill.md 的全部内容粘贴进去，保存

**Step 4**：在该 Project 的任意对话中，附上填好的输入表发送：

```
请基于以下信息生成 PRD：
[粘贴填好的输入表内容]
```

> **提示**：建议为标准版和渐进式分别建两个 Project，切换使用。Project instructions 有字数上限，两个 Skill 内容不要写在同一个 Project 里。

---


## 新版 PRD 的核心章节

相比传统 PRD，新版增加了 4 个章节、改造了 3 个章节：

**新增章节**

| 章节 | 作用 |
|-----|-----|
| `〇 技术上下文` | 声明技术栈、禁用约束、代码规范，避免 AI 生成风格不符的代码 |
| `二 核心数据结构` | TypeScript Interface 精确定义字段名、类型、枚举值，消灭口头约定 |
| `E-XX 边界与异常场景` | 逐条列举非正常路径及期望行为，AI 最容易遗漏的部分 |
| `AC 验收标准` | Checkbox 格式，每条含操作路径 + 可观察终态，AI 可直接用于自检 |

**改造章节**

| 章节 | 旧版 | 新版 |
|-----|-----|-----|
| 功能需求 | 操作步骤 + 原型图 | 状态机描述 + 输入参数表 + 接口规格 |
| 非功能需求 | 文字描述，无具体指标 | 表格化，含具体数值（如 P99 < 500ms） |
| 用户与权限 | 角色简单描述 | 角色 × 功能 × 是否有权限的完整矩阵 |

---

## 验收标准（AC）写作方法

写作两步法：

1. 将核心用户路径拆成步骤，每个步骤的终态写一条 AC（覆盖正常路径）
2. 将边界场景（E-XX）逐一翻译为可操作的 AC（覆盖异常路径）

每条 AC 必须满足：没有参与需求的测试人员可独立执行；结果可明确判断通过或不通过；描述一个独立的可观察结果。

```markdown
示例：
- [ ] AC-1  打开 /products 页面 → 筛选面板展开，所有条件为空，列表展示默认数据
- [ ] AC-3  点击「查询」→ 按钮立即变 loading，不可重复点击
- [ ] AC-8  price_min > price_max 时点击查询 → 表单不提交，inline 红色提示出现
- [ ] AC-12 接口返回 500 → Toast「服务异常，请联系客服」，列表不清空
```

---

## 渐进式 Skill 的四个阶段

```
阶段 0  信息收集  →  [检查点] 确认基本信息
阶段 1  功能拆解  →  [检查点] 确认功能树（可增删改顺序）
阶段 2  逐功能点生成  →  [检查点 × N] 每个功能点单独确认后继续
阶段 3  公共章节  →  [检查点] 最终确认 + 导出
```

---

## 与传统 PRD 的本质区别

| 维度 | 传统 PRD | 新版 PRD |
|-----|---------|---------|
| 接收者 | 工程师（人） | AI（机器） |
| 表达方式 | 意图描述 | 规格定义 |
| 隐性知识 | 存在工程师经验中，靠沟通传递 | 显式化写入文档 |
| 验收方式 | 事后主观判断 | 事前可测试的 AC |
| 完备性保障 | 评审会议补充 | 结构化模板强制覆盖 |

**一句话**：旧版 PRD 是意图描述文档，依赖人的经验将意图转化为实现；新版 PRD 是可执行规格文档，将实现所需的全部决策前置并显式化，使 AI 可以直接消费。

---

## 贡献与反馈

欢迎通过 Issue 提交：
- 使用过程中发现的 Skill 逻辑问题
- 适用于特定技术栈或行业的模板变体
- 新的边界场景类型或 AC 写作模式

---

## License

MIT
