# BUILD_OPERATIONS — AI-Project-OS 构建操作记忆库

> **定位**：随仓库存储的完整迭代历史，记录每次构建的问题、解决方案和 Backlog。  
> **迭代规则**：每次修改本仓库后，更新"当前文件清单"和 Backlog。  
> **Claude 会话记忆**：`~/.claude/projects/.../memory/build-ops.md`（自动加载）  
> **可调用 Skill**：`/ai-workflow` · `/ai-methodology-ops` · `/github-upload`

---

## 系统概览

**目的**：构建一套工业级 AI 协作工程方法论，让任何人的任何项目都能复用相同的 AI 协作流程。

**核心设计**：两层分离
- **通用层**（本仓库）= 流程 HOW + 提示词 + 清单 — 所有项目、所有 AI 工具共用
- **项目层**（project-overlay）= 决策 WHAT + 踩坑 WHY — 每项目一份

**工具无关性**：工作流模板、审查清单、约束提示词可直接用于任何 LLM（ChatGPT / Gemini / Cursor / 本地模型等）。`/skill` 触发命令仅为 Claude Code 专属快捷方式。

**提炼来源**：医保智能审核系统 · CDN 边缘计算基础设施 · Seed AI CLI（三个工业级项目）  
**GitHub 仓库**：https://github.com/jiayu6954-sudo/AI-Project-OS

---

## v2.1 · 2026-04-10 · 初始构建

### 修复的问题

#### 🔴 P0 严重缺陷

| # | 文件 | 问题 | 根因 | 解决 |
|---|------|------|------|------|
| 1 | `checklists/code-review-checklist.md` | 所有 Markdown 特殊字符前有反斜杠，无法渲染 | 从渲染页面复制时编辑器自动转义 | 完整重写，升级 v1.2，新增 N/A 机制 + 1.5 + 8.5 两条规则 |
| 2 | `templates/workflow-template.md` | 附录 A/B/C/D 全是占位符，完全不可用 | v1.0 只设计结构，内容从未填写 | 重写为 v2.1，填入全部 5 个附录实质内容 |
| 3 | `prompts/02-code-review.md` | 空文件（0 字节） | touch 创建占位符后未写入 | 写入完整审查提示词（标准 + 快速两模式）|

#### 🟡 P1 架构缺失

| # | 文件 | 问题 | 解决 |
|---|------|------|------|
| 4 | `templates/project-overlay-template.md` | 不存在（"两层分离"核心组件缺失）| 新建 8 节完整模板 |
| 5 | `README.md` | 内容是 Seed AI 项目 README（npm badge、功能列表）| 完整重写为方法论库说明 |
| 6 | `case-studies/` | 空目录 | 创建索引 + 全流程实测案例（TaskFlow API）|

### 框架实测：TaskFlow API（Python 3.12 + FastAPI）

| FP | 摩擦点 | 修复位置 |
|----|--------|---------|
| FP-1 | 附录 A 无 Python 目录约定 | workflow-template.md 附录 A |
| FP-2 | 清单第三节对非 AI 项目无 N/A 指引 | code-review-checklist.md 文件头 |
| FP-3 | 阶段二验证命令无 Python 选项 | workflow-template.md 阶段二 |
| FP-4 | Python async Session 全局共享（新型 P0）| 清单新增 1.5 + 8.5 |

---

## v2.2 · 2026-04-10 · B-01 案例存档补全

| 新建文件 | 来源 | 核心内容 |
|---------|------|---------|
| `case-studies/医保智能审核系统-2026-04.md` | `工作流.md` | 6 P0（KieSession单例/危险@Cacheable/javax→jakarta）+ 6 轮修复 |
| `case-studies/CDN边缘计算基础设施-2026-04.md` | `操作记忆库.md` | 7 P0（import末尾/硬编码Key/1276行上帝类）+ 6 轮修复 |
| `case-studies/SeedAI-2026-04.md` | `OPERATIONS.md` + `README.md` | Provider接口3次重犯 + P016无限循环 + 27项创新 |

**结果**：case-studies/ 三个死链全部修复，案例库完整可用。

---

## v2.3 · 2026-04-10 · GitHub 打包 + 白皮书

| 操作 | 文件 | 说明 |
|------|------|------|
| 新建 | `LICENSE` | MIT License |
| 新建 | `.gitignore` | 覆盖 macOS/Windows/编辑器/临时文件 |
| 新建 | `CONTRIBUTING.md` | 归档案例/改进模板/追加清单规则的贡献指南 |
| 新建 | `AI协作方法论白皮书.md` | 14 章专业白皮书，含全链路逻辑、验证数据、实施指南 |
| 修改 | `README.md` v2.1 | "快速上手"前新增"30 秒决策树" |
| 修改 | `case-studies/README.md` | TaskFlow 链接文字更新为"框架验证项目" |

---

## v2.4 · 2026-04-10 · GitHub 首次上传 + 一键上传 Skill

### GitHub 上传操作记录

| 步骤 | 命令/操作 | 结果 |
|------|---------|------|
| 凭证绑定 | `git config --global credential.helper store` + 写入 `~/.git-credentials` | Token 永久存储 |
| 新建仓库 | `curl POST /user/repos`（name: AI-Project-OS）| 仓库创建成功 |
| 初始化 | `git init` + `git remote add origin` | 关联新仓库（非 seed-ai）|
| 提交推送 | `git add` + `git commit` + `git push -u origin master` | 15 文件，3208 行，✅ |

**关键踩坑**：
- `curl -d` 传含中文的 JSON → `Problems parsing JSON` → 改 `--data-raw` + 纯英文描述
- 必须先确认 remote 地址不是已有仓库，绝不 force push 已有仓库

**新建 Skill**：`~/.claude/skills/github-upload.md`，触发 `/github-upload`

---

## v2.5 · 2026-04-10 · 双语 README + 工具无关性声明

| 操作 | 文件 | 说明 |
|------|------|------|
| 新建 | `README.en.md` | 完整英文版，内容与中文版对应，互相链接 |
| 修改 | `README.md` | 顶部加语言切换链接，新增"工具无关性"说明节 |

**工具无关性要点**（已写入 README）：
- 工作流模板、审查清单、约束提示词 → 任何 LLM 均可使用
- `/skill` 触发命令 → 仅 Claude Code 专属快捷方式，其他工具直接阅读 `.md` 内容

---

## 当前系统文件清单

```
E:\AI-Methodology\
├── README.md                          ✅ v2.2（含双语切换 + 工具无关性说明）
├── README.en.md                       ✅ v1.0（英文版）
├── BUILD_OPERATIONS.md                ✅ v2.5（本文件）
├── AI协作方法论白皮书.md               ✅ v1.0（14章）
├── LICENSE                            ✅ MIT（需将 [Author] 替换为真实姓名）
├── .gitignore                         ✅
├── CONTRIBUTING.md                    ✅
│
├── templates/
│   ├── workflow-template.md           ✅ v2.1（6阶段 + 5附录 + Python适配）
│   └── project-overlay-template.md   ✅ v1.0（8节结构）
│
├── checklists/
│   └── code-review-checklist.md      ✅ v1.2（10类50项，含N/A机制）
│
├── prompts/
│   ├── 02-code-review.md             ✅ v1.0
│   ⚠️  01-architecture-design.md    ❌ 缺失（Backlog B-02）
│   ⚠️  03-delivery-verification.md  ❌ 缺失（Backlog B-03）
│
└── case-studies/
    ├── README.md                      ✅ v1.0（索引 + 高频坑 TOP 10）
    ├── taskflow-api-2026-04.md        ✅ 框架验证案例
    ├── 医保智能审核系统-2026-04.md    ✅ v1.0
    ├── CDN边缘计算基础设施-2026-04.md ✅ v1.0
    └── SeedAI-2026-04.md             ✅ v1.0

历史文档（只读参考，不再更新）：
├── 工作流.md        ← 医保系统原始记录
├── 操作记忆库.md    ← CDN 系统原始记录
└── OPERATIONS.md    ← Seed AI 原始记录
```

---

## 迭代 Backlog

### P0 — 阻塞后续使用

*当前无 P0 待办。*

### ✅ 已完成

| ID | 任务 | 完成版本 |
|----|------|---------|
| B-01 | 补全三个老项目案例存档 | v2.2 |
| B-05 | README 加"30 秒决策树" | v2.3 |

### P1 — 重要，可在下次会话处理

| ID | 任务 | 背景 |
|----|------|------|
| B-02 | 创建 `prompts/01-architecture-design.md` | 阶段二架构设计无专属提示词 |
| B-03 | 创建 `prompts/03-delivery-verification.md` | 阶段五交付验证无专属提示词 |
| B-04 | workflow-template.md 附录 B 新增 Rust/C++ 约束 | 扩展语言覆盖 |

### P2 — 锦上添花

| ID | 任务 | 背景 |
|----|------|------|
| B-06 | 10 项精华版快速审查清单 | 50 项对小项目过重 |
| B-07 | 摩擦点追踪表 | 长期记录模板改进历史 |

---

## Claude Skill 清单

| 触发命令 | 文件 | 用途 |
|---------|------|------|
| `/ai-workflow` | `~/.claude/skills/ai-workflow.md` | 触发完整 6 阶段工作流 |
| `/ai-methodology-ops` | `~/.claude/skills/ai-methodology-ops.md` | 快速恢复本仓库操作上下文 |
| `/github-upload` | `~/.claude/skills/github-upload.md` | 一键打包上传 GitHub |

---

## 版本规范

| 变化类型 | 版本号规则 |
|---------|-----------|
| 新增内容（新清单项、新附录）| 小版本 +0.1 |
| 修复错误（格式、链接、内容）| 小版本 +0.1 |
| 结构性调整（新章节、重组）| 大版本 +1.0 |

---

*BUILD_OPERATIONS 版本：v2.5 · 更新：2026-04-10 · 下次更新节点：B-02/B-03 完成后*
