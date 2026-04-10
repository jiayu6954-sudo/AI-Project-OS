# AI-Project-OS — AI 协作工程方法论库

> 中文 | [English](README.en.md)
>
> 一套经过三个工业级项目验证的 AI 协作标准化方法论。  
> 让"AI + 人"的协作模式**可复制、可量化、可持续改进**。

---

## 为什么需要这个仓库？

| 痛点 | 本库的解法 |
|------|-----------|
| AI 生成代码质量不稳定，总有隐藏 Bug | 标准化**约束提示词**预防 + **审查清单**兜底 |
| 每个新项目都从头摸索流程 | **6 阶段通用工作流模板**固化最佳路径 |
| 项目经验停留在个人脑子里 | **项目覆盖层 + 案例库**沉淀可复用知识 |
| 模板越用越厚，维护困难 | 通用模板（HOW）和项目层（WHAT/WHY）严格分离 |

---

## 目录结构

```
AI-Methodology/
│
├── README.md                              ← 本文件（仓库入口）
│
├── templates/
│   ├── workflow-template.md               ← ★ 工业级通用工作流（6 阶段 + 5 个附录）
│   └── project-overlay-template.md       ← ★ 项目专属覆盖层（新项目必填）
│
├── checklists/
│   └── code-review-checklist.md          ← 代码审查强制清单（10 类 45+ 项）
│
├── prompts/
│   └── 02-code-review.md                 ← 代码审查提示词（直接粘贴给 AI）
│
├── case-studies/
│   ├── README.md                          ← 案例索引（项目名/日期/技术栈/核心标签）
│   ├── 医保智能审核系统-2026-04.md        ← 案例存档
│   ├── CDN边缘计算基础设施-2026-04.md    ← 案例存档
│   └── SeedAI-2026-04.md                 ← 案例存档
│
│   # 以下为本仓库的遗留历史文档（提炼经验用，非模板规范格式）
├── 工作流.md       ← 医保系统原始记录
├── 操作记忆库.md   ← CDN 系统原始记录
└── OPERATIONS.md   ← Seed AI 原始记录
```

---

## 核心概念：两层分离

```
┌─────────────────────────────────────────────────────────┐
│              通用层（本仓库维护）                          │
│  workflow-template.md  +  code-review-checklist.md      │
│  所有项目共用，只更新不重写，记录流程 HOW               │
└────────────────────┬────────────────────────────────────┘
                     │ 引用 / 参考
┌────────────────────▼────────────────────────────────────┐
│              项目层（每个项目维护一份）                    │
│  /memory/项目操作记忆.md（复制自 project-overlay-template）│
│  只记录本项目的决策 WHAT 和踩坑 WHY，不重复通用步骤      │
└─────────────────────────────────────────────────────────┘
```

---

## 30 秒决策树：我该用哪个文件？

| 我的情况 | 直接跳转 |
|---------|---------|
| 🆕 全新项目，从零开始 | [workflow-template.md](templates/workflow-template.md) — 走完整 6 阶段 |
| 🔍 已有代码，需要 AI 审查 | [prompts/02-code-review.md](prompts/02-code-review.md) + [审查清单](checklists/code-review-checklist.md) |
| ⏱️ 时间紧，只防安全大坑 | 打开清单第 6 节（安全）+ 附录 B（约束提示词），其余跳过 |
| 🔁 用了新框架，不确定有什么坑 | [case-studies/README.md](case-studies/README.md) — 高频踩坑 TOP 10 |
| 📦 项目完成，需要沉淀经验 | [project-overlay-template.md](templates/project-overlay-template.md) — 归档到 case-studies/ |

---

## 快速上手：启动新项目（5 分钟）

**Step 1** — 复制覆盖层模板到项目目录
```bash
cp E:\AI-Methodology\templates\project-overlay-template.md \
   [你的项目根目录]\memory\项目操作记忆.md
```

**Step 2** — 填写"项目定义书"（覆盖层第一节）  
只需填写：系统定位 / 硬性约束 / 成功标准 / 技术栈

**Step 3** — 按工作流模板执行 6 个阶段
```
打开：E:\AI-Methodology\templates\workflow-template.md
按阶段一到六顺序执行，每阶段结束通过门禁检查再继续
```

**Step 4** — 项目完成后归档
```bash
cp memory\项目操作记忆.md \
   E:\AI-Methodology\case-studies\[项目名称]-[YYYY-MM].md
```

---

## 如何使用各文件

### 1. 工作流模板（workflow-template.md）

| 附录 | 用途 | 使用时机 |
|------|------|---------|
| 附录 A | 项目目录架构提示词 | 阶段一末尾，让 AI 生成目录结构 |
| 附录 B | 代码生成强制约束提示词 | 阶段二，生成代码**前**先发此提示词 |
| 附录 C | 代码审查提示词 | 阶段三，与 checklist 全文一起发给 AI |
| 附录 D | 工程化交付检查清单 | 阶段五，逐项核对，全 ✅ 才算交付 |
| 附录 E | 踩坑记录表模板 | 贯穿全程，发现问题立即记录 |

### 2. 审查清单（code-review-checklist.md）

**在阶段二（生成代码前）**：将第一节（单例）+ 第六节（安全）作为约束前缀发给 AI  
**在阶段三（代码审查时）**：将清单全文 + `02-code-review.md` 一起发给 AI  
**在阶段六（知识沉淀时）**：追加本项目新发现的 Bug 模式

### 3. 与其他 AI 工具配合使用

**本方法论完全工具无关**：工作流模板、审查清单、约束提示词均可直接复制粘贴到任何 LLM（ChatGPT / Gemini / Cursor / GitHub Copilot / 本地模型等）。  
以下 Claude Code Skill 集成是可选的快捷方式，使用其他工具的用户跳过即可。

### 4. 作为 Claude Code Skill 使用（可选）

```bash
# 将工作流模板注册为 Skill（仅需操作一次）
cp E:\AI-Methodology\templates\workflow-template.md ^
   %USERPROFILE%\.claude\skills\ai-workflow.md

# 之后在 Claude Code 中，输入：
/ai-workflow
# 即可触发完整工业级工作流
```

---

## 维护规范

- **什么时候更新 checklist**：每完成一个项目，若发现新 Bug 模式，追加到对应章节
- **什么时候更新 workflow-template**：发现某个阶段有流程卡点，迭代对应附录
- **什么时候新增 case-study**：每个项目完成后，归档一份覆盖层文件到 `case-studies/`
- **遗留文档**（工作流.md / 操作记忆库.md / OPERATIONS.md）：只读参考，不再更新

---

## 经验来源

| 项目 | 技术栈 | 核心经验 |
|------|--------|---------|
| 医保智能审核系统 | Java / Spring Boot 3 / Drools | 规则引擎生命周期、安全合规、六轮迭代修复 |
| CDN 边缘计算基础设施 | Go / OpenResty / Lua | 基础设施工具链、WAF 防护、工业级运维 |
| Seed AI CLI 工具 | TypeScript / Node.js | AI Agent 架构、多 Provider 抽象、开源发布 |

---

*仓库版本：v2.1 · 最后更新：2026-04-10*
