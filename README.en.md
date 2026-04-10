# AI-Project-OS — AI Collaboration Engineering Methodology

> [中文版](README.md) | English
>
> A battle-tested methodology for AI-assisted software development, distilled from three industrial-grade projects.  
> Makes "AI + Human" collaboration **repeatable, measurable, and continuously improvable**.

---

## Why This Repository?

| Pain Point | This Library's Solution |
|-----------|------------------------|
| AI-generated code has unpredictable quality and hidden bugs | Standardized **constraint prompts** to prevent + **review checklist** as safety net |
| Every new project reinvents the collaboration workflow | **6-phase universal workflow** with explicit gate checks |
| Project lessons stay in people's heads, not reusable | **Project overlay + case study library** to capture transferable knowledge |
| Templates grow too thick and become hard to maintain | Strict separation of universal layer (HOW) and project layer (WHAT/WHY) |

---

## Directory Structure

```
AI-Project-OS/
│
├── README.md                              ← Chinese version
├── README.en.md                           ← This file (English)
│
├── templates/
│   ├── workflow-template.md               ← ★ 6-phase workflow (5 appendices)
│   └── project-overlay-template.md       ← ★ Per-project overlay template
│
├── checklists/
│   └── code-review-checklist.md          ← Review checklist (10 categories, 47+ items)
│
├── prompts/
│   └── 02-code-review.md                 ← Code review prompt (standard + quick modes)
│
└── case-studies/
    ├── README.md                          ← Case index + Top 10 cross-project pitfalls
    ├── 医保智能审核系统-2026-04.md        ← Medical insurance audit system case
    ├── CDN边缘计算基础设施-2026-04.md    ← CDN infrastructure case
    └── SeedAI-2026-04.md                 ← Seed AI CLI tool case
```

---

## Core Concept: Two-Layer Separation

```
┌─────────────────────────────────────────────────────────┐
│  Universal Layer (this repository)                       │
│  workflow-template.md  +  code-review-checklist.md      │
│  Shared across all projects — records HOW               │
└────────────────────┬────────────────────────────────────┘
                     │ references
┌────────────────────▼────────────────────────────────────┐
│  Project Layer (one file per project)                    │
│  /memory/project-ops.md (copied from overlay template)  │
│  Records project-specific decisions (WHAT) and          │
│  lessons learned (WHY) — no duplication of process      │
└─────────────────────────────────────────────────────────┘
```

---

## 30-Second Decision Tree

| My Situation | Go To |
|-------------|-------|
| 🆕 New project, starting from scratch | [workflow-template.md](templates/workflow-template.md) — full 6 phases |
| 🔍 Existing code, need AI review | [prompts/02-code-review.md](prompts/02-code-review.md) + [checklist](checklists/code-review-checklist.md) |
| ⏱️ Time-limited, prevent security issues only | Checklist Section 6 (Security) + Appendix B (constraint prompt) |
| 🔁 New framework, unsure what pitfalls to expect | [case-studies/README.md](case-studies/README.md) → Top 10 pitfalls |
| 📦 Project done, need to capture lessons | [project-overlay-template.md](templates/project-overlay-template.md) → archive to case-studies/ |

---

## Quick Start: Launch a New Project (5 minutes)

**Step 1** — Copy the overlay template to your project
```bash
cp templates/project-overlay-template.md \
   [your-project-root]/memory/project-ops.md
```

**Step 2** — Fill in the "Project Definition" (Section 1 of the overlay)  
Required fields: System positioning / Hard constraints / Success criteria / Tech stack

**Step 3** — Follow the 6-phase workflow
```
Open: templates/workflow-template.md
Execute phases 1–6 in order.
Each phase has gate checks — pass all before moving on.
```

**Step 4** — Archive after project completion
```bash
cp memory/project-ops.md \
   case-studies/[project-name]-[YYYY-MM].md
```

---

## How to Use Each File

### 1. Workflow Template (workflow-template.md)

| Appendix | Purpose | When to Use |
|---------|---------|-------------|
| Appendix A | Directory scaffold prompt | End of Phase 1 — generate project structure |
| Appendix B | Code generation constraint prompt | Phase 2 — send **before** asking AI to generate code |
| Appendix C | Code review prompt | Phase 3 — send with checklist full text |
| Appendix D | Engineering delivery checklist | Phase 5 — all ✅ before declaring delivery complete |
| Appendix E | Pitfall log template | Throughout — record issues immediately when found |

### 2. Review Checklist (code-review-checklist.md)

**Before Phase 2 (before code generation)**: Send Section 1 (Singleton) + Section 6 (Security) as constraint prefix  
**During Phase 3 (code review)**: Send full checklist + `02-code-review.md` to AI  
**During Phase 6 (knowledge distillation)**: Append new bug patterns discovered in this project

### 3. Tool Compatibility

**This methodology is fully tool-agnostic.** The workflow, checklist, and constraint prompts can be copy-pasted into any LLM:
- ChatGPT / GPT-4
- Google Gemini
- Cursor / GitHub Copilot
- Local models (Ollama, LM Studio, etc.)

For Claude Code users, an optional Skill integration is available (see below).

### 4. Claude Code Skill Integration (Optional)

```bash
# Register the workflow as a Skill (one-time setup)
cp templates/workflow-template.md \
   ~/.claude/skills/ai-workflow.md

# Then in Claude Code, type:
/ai-workflow
# to trigger the full industrial workflow
```

---

## Validation: Three Industrial Projects

| Project | Tech Stack | Scale | Final Status |
|---------|-----------|-------|-------------|
| Medical Insurance Audit System | Java 17 / Spring Boot 3 / Drools 8 / PostgreSQL | 8 microservices, 6 fix rounds | Industrial-grade, ready for institutional review |
| CDN Edge Computing Infrastructure | Go 1.21 / OpenResty / Lua / Docker | Control plane + edge nodes | Industrial certified 🏆, production-ready |
| Seed AI CLI Tool | TypeScript / Node.js / Ink 4 | 27 innovations, 42 iterations | alpha.25, published on npm |

**Validated languages**: Java · Go · TypeScript · Python (via framework validation test)

---

## Top 10 Cross-Project Pitfalls

> These bugs appeared across multiple projects — AI generation is especially prone to these.

| Rank | Bug Pattern | Projects Affected |
|------|------------|------------------|
| 🔴 1 | Stateful object (KieSession/connection) injected as singleton | All 3 projects |
| 🔴 2 | Hardcoded credentials or predictable random API key generation | CDN, Medical |
| 🔴 3 | Service interface declared with no implementation class — crash on startup | Medical, CDN |
| 🔴 4 | Business decision results cached with @Cacheable | Medical |
| 🟡 5 | Mock object with no stub configuration — assertions always fail | Medical, Seed AI |
| 🟡 6 | Wrong import placement (Go: import at file bottom; Java: javax vs jakarta) | CDN, Medical |
| 🟡 7 | Duplicate function name in same package — compile conflict | CDN |
| 🟡 8 | Java `assert` keyword used instead of JUnit `Assertions` — never executes | Medical |
| 🟡 9 | Enum field missing `@EnumValue` — type mismatch on storage | Medical |
| 🟡 10 | Internal LLM calls bypass Provider interface — `new Anthropic()` directly | Seed AI (3×) |

---

## Maintenance Rules

- **When to update the checklist**: After each project, if a new bug pattern is found, append to the relevant section
- **When to update the workflow**: If a phase causes friction, iterate the corresponding appendix
- **When to add a case study**: After every project — archive one overlay file to `case-studies/`

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to:
- Archive new case studies (highest value contribution)
- Fix template friction points
- Add language-specific constraints (Appendix B extensions)
- Append new checklist rules (new bug patterns)

---

## License

[MIT License](LICENSE) — free to use, adapt, and share.

---

*Repository version: v2.1 · Last updated: 2026-04-10*  
*GitHub: [jiayu6954-sudo/AI-Project-OS](https://github.com/jiayu6954-sudo/AI-Project-OS)*
