# PRD Generator Skills

> A toolkit for writing AI-executable Product Requirements Documents.
> Give your PRD directly to AI and get production-ready code — not code that "runs but can't ship."

[中文版 →](./README CH.md)

---

## Why This Exists

Traditional PRDs are **intent documents** — written for humans, relying on engineers to fill in edge cases, field naming, error handling, and everything else left unspecified. That worked fine when humans wrote all the code.

When AI becomes the primary code producer, three structural problems emerge:

- AI can't participate in stand-ups or Slack threads to resolve ambiguity
- Vague requirements translate directly into wrong code
- Without acceptance criteria, AI only covers the happy path — edge cases get random behavior

This project defines a standard for **executable specification documents** and provides the tooling to produce them, so AI can consume a PRD and generate code that actually matches your intent.

---

## Repository Structure

```
.
├── README.md                               # 中文说明
├── README_EN.md                            # English version (this file)
│
├── prd-generator/
│   └── SKILL.md                            # Standard Skill: single-pass generation
│
├── prd-generator-iterative/
│   └── SKILL.md                            # Iterative Skill: staged generation with checkpoints
│
├── templates/
│   ├── PRD模板-新版（AI可执行规格）.md      # New PRD template (AI-executable)
│   ├── PRD模板-旧版（面向程序员）.md        # Old PRD template (for comparison)
│   ├── PRD生成-需求信息输入模板.md          # Input form for the standard Skill
│   └── 大型模块PRD生成-需求信息输入表.md    # Input form for the iterative Skill
│
└── docs/
    └── PRD升级方法论-汇报文档.docx          # Full methodology deck (background, goals, value)
```

---

## Which Skill Should I Use?

| | `prd-generator` | `prd-generator-iterative` |
|---|---|---|
| **Best for** | Single features with clear scope | Large modules with multiple sub-features |
| **How it works** | Collect info → confirm → generate in one pass | Decompose → confirm → generate per feature → confirm |
| **PM involvement** | Fill in the form, wait for output | Confirm at each checkpoint before AI proceeds |
| **Paired template** | `PRD生成-需求信息输入模板.md` | `大型模块PRD生成-需求信息输入表.md` |

**Quick rule**: If you can describe the requirement clearly in 5 minutes, use the standard version. If you need to break down sub-features or aren't sure where the boundaries are, use the iterative version.

---

## Installation & Setup

### Claude Code

Claude Code loads project-level instructions from `CLAUDE.md` in your project root. Add the Skill there and it's automatically available in every session.

**Step 1: Clone this repo**

```bash
git clone https://github.com/your-username/prd-generator-skills.git
```

**Step 2: Add the Skill to your project's CLAUDE.md**

```bash
# Standard version
cat prd-generator-skills/prd-generator/SKILL.md >> your-project/CLAUDE.md

# Iterative version
cat prd-generator-skills/prd-generator-iterative/SKILL.md >> your-project/CLAUDE.md
```

If your project doesn't have a `CLAUDE.md` yet, copy directly:

```bash
cp prd-generator-skills/prd-generator/SKILL.md your-project/CLAUDE.md
```

**Step 3: Start Claude Code in your project**

```bash
cd your-project
claude
```

**Step 4: Trigger the Skill**

Just describe your feature — Claude Code picks up the Skill automatically:

```
Write a PRD for the user authentication module
```

> **Tip**: If your `CLAUDE.md` already has content, append at the bottom and add a `---` separator to keep things clean. Both Skills can coexist in the same `CLAUDE.md` — the AI selects the appropriate one based on the scope of your request.

---

### Claude.ai (Projects)

**Step 1**: Open [claude.ai](https://claude.ai) and navigate to a Project (or create a new one dedicated to PRD generation)

**Step 2**: Click the settings icon next to the Project name → **"Project instructions"**

**Step 3**: Paste the full contents of your chosen `SKILL.md` and save

**Step 4**: In any conversation within that Project, paste your filled-in input form and send:

```
Please generate a PRD based on the following information:
[paste your completed input form here]
```

> **Tip**: Create separate Projects for the standard and iterative Skills — Project instructions have a character limit, so keep them in separate Projects and switch as needed.

---

### Cursor

Cursor injects project-level instructions via `.cursor/rules/` (Cursor 0.43+) or the legacy `.cursorrules` file.

**Using `.cursor/rules/` (recommended for Cursor 0.43+)**

```bash
mkdir -p your-project/.cursor/rules

# Standard version
cp prd-generator-skills/prd-generator/SKILL.md \
   your-project/.cursor/rules/prd-generator.mdc

# Iterative version
cp prd-generator-skills/prd-generator-iterative/SKILL.md \
   your-project/.cursor/rules/prd-generator-iterative.mdc
```

**Using the legacy `.cursorrules` file**

```bash
cat prd-generator-skills/prd-generator/SKILL.md > your-project/.cursorrules
```

**Trigger in Cursor Chat**

```
Write a PRD for this feature: [describe your feature]
```

> **Note**: `.cursorrules` has a content length limit. If you already have many rules, strip the YAML frontmatter (the `---` block at the top of the SKILL.md) and paste only the body starting from the first `#` heading.

---

### Any Platform with System Prompt Support

Works with OpenAI Playground, Coze, Dify, FastGPT, and any platform that accepts a custom system prompt.

**Generic steps**:

1. Find the "System Prompt" / "System Instructions" / "Character Setup" field
2. Paste the SKILL.md content — **remove the YAML frontmatter** first (the block between the two `---` lines at the top)
3. Save, then paste your completed input form into the conversation

**What to remove** — delete these lines before pasting:

```yaml
---                          ← remove this line
name: prd-generator          ← remove this line
description: ...             ← remove this line
---                          ← remove this line

# PRD Generator Skill        ← start pasting from here
```

---

## Quick Start (3 Steps, Any Platform)

Once the Skill is installed, the workflow is the same everywhere:

```
Step 1  Open the matching input form from templates/

Step 2  Fill in the required fields:
        feature name, core problem, user roles, tech stack

Step 3  Send the completed form to the AI:
        "Please generate a PRD based on the following information"
```

The AI follows the Skill's workflow automatically — no extra prompting needed.

---

## What's New in the AI-Executable PRD

Four new sections added, three existing sections upgraded:

**New sections**

| Section | Purpose |
|--------|---------|
| `§0 Technical Context` | Declares tech stack, prohibited dependencies, and code conventions — prevents AI from generating code in the wrong style |
| `§2 Core Data Structures` | TypeScript Interface definitions with exact field names, types, and enum values — eliminates verbal agreements |
| `E-XX Edge Cases` | Numbered list of non-happy-path scenarios with expected behavior — the section AI most commonly omits |
| `AC Acceptance Criteria` | Checkbox-format test cases with explicit action + observable outcome — enables AI self-verification |

**Upgraded sections**

| Section | Before | After |
|--------|--------|-------|
| Feature requirements | Step list + prototype link | State machine + parameter table + interface contract |
| Non-functional requirements | Prose description, no metrics | Table with specific values (e.g. P99 < 500ms) |
| Users & permissions | Role description | Full matrix: role × feature × allowed/denied |

---

## How to Write Acceptance Criteria

AC is the highest-leverage section for AI output quality. Two-step method:

1. **Happy path**: Walk through the core user journey step by step. Write one AC per step, describing the observable end state.
2. **Edge cases**: Translate each E-XX scenario into one or more executable ACs.

Every AC must pass three checks:
- A tester with no prior context can **execute it independently**
- The result can be clearly judged as **pass or fail**
- It describes **one distinct observable outcome** (no overlap with other ACs)

```markdown
Example:

**Happy path**
- [ ] AC-1  Open /products → filter panel visible, all fields empty, list shows default data
- [ ] AC-3  Click "Search" → button enters loading state immediately, cannot be clicked again
- [ ] AC-5  Search succeeds → URL contains all active filter params; refreshing preserves state

**Edge cases**
- [ ] AC-8  price_min > price_max, click Search → form does not submit; inline red error appears
- [ ] AC-12 API returns 500 → toast "Service error, please contact support"; list is not cleared
- [ ] AC-15 Zero results → empty state component shown; filter panel retains values; pagination hidden
```

---

## Iterative Skill: Four Stages

```
Stage 0  Collect & confirm basic info
   ↓  [Checkpoint] PM confirms completeness
Stage 1  Decompose into feature points (F-001, F-002...)
   ↓  [Checkpoint] PM confirms structure (can add/remove/reorder)
Stage 2  Generate PRD per feature point
   ↓  [Checkpoint × N] PM confirms each before AI continues
Stage 3  Generate shared sections (data structures, permission matrix, timeline)
   ↓  [Checkpoint] Final review + export
```

At each checkpoint, the AI outputs a fixed-format prompt listing available options: ✅ Continue / ✏️ Revise / ⏸️ Pause.

---

## Input Form Priority Guide

| Information | Priority | If omitted |
|------------|----------|------------|
| Feature name + core problem | ✅ Required | Cannot start |
| User roles + permission scope | ✅ Required | Auth logic is randomly generated |
| Tech stack + constraints | ✅ Required | Code style won't match your project |
| Data structure fields | Strongly recommended | AI names fields freely; DB incompatibility likely |
| Business rules + edge constraints | Strongly recommended | Edge case coverage will be incomplete |
| Performance / compatibility requirements | Optional | Generic defaults used |
| Launch timeline | Optional | Milestone dates left blank |

---

## Traditional PRD vs. AI-Executable PRD

| Dimension | Traditional PRD | AI-Executable PRD |
|-----------|----------------|-------------------|
| Primary reader | Engineers (humans) | AI (machine) |
| Communication style | Intent description | Specification definition |
| Tacit knowledge | Lives in engineers' heads, transferred via conversation | Explicitly written into the document |
| Acceptance process | Post-hoc subjective judgment | Pre-defined testable criteria |
| Completeness guarantee | Review meetings fill the gaps | Structured template enforces coverage |

**In one sentence**: A traditional PRD is an intent document that relies on human experience to bridge the gap to implementation. An AI-executable PRD is a specification document that makes every implementation decision explicit upfront — ready for direct machine consumption.

---

## Contributing

Issues and PRs welcome for:
- Bugs or logic gaps in the Skill workflows
- Template variants for specific tech stacks or industries
- New edge case patterns or AC writing conventions

---

## License

MIT
