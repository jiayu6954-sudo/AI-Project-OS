# BUILD_OPERATIONS — AI-Methodology 系统构建操作记忆库

> **定位**：本文件是随代码仓库存储的操作历史，记录每次迭代的问题、解决方案和待处理事项。  
> **迭代规则**：每次修改本仓库后，更新"当前文件清单"和 Backlog。  
> **Claude 会话专用记忆**：见 `~/.claude/skills/ai-methodology-ops.md`（可用 `/ai-methodology-ops` 触发）

---

## 系统概览

**目的**：构建一套工业级 AI 协作工程方法论，让任何人的任何项目都能复用相同的 AI 协作流程。

**核心设计**：两层分离
- **通用层**（本仓库）= 流程 HOW + 提示词 + 清单 — 所有项目共用
- **项目层**（project-overlay）= 决策 WHAT + 踩坑 WHY — 每项目一份

**提炼来源**：医保智能审核系统 · CDN边缘计算基础设施 · Seed AI CLI（三个工业级项目）

---

## v2.1 · 2026-04-10 · 初始构建

### 本次处理的问题

#### 🔴 P0：严重缺陷

**问题1 — `checklists/code-review-checklist.md` 格式损坏**
- **症状**：所有 Markdown 特殊字符前都有反斜杠（`\*`、`\#`、`\|`），文件无法正常渲染
- **根因**：从已渲染的 Markdown 页面复制内容时，编辑器自动对特殊字符做了转义
- **解决**：完整重写文件，移除所有转义字符；同时升级为 v1.2，新增 N/A 机制说明、1.5 和 8.5 两条新规则
- **预防**：以后从网页复制内容后，先粘贴到纯文本编辑器检查是否有多余转义

**问题2 — `templates/workflow-template.md` 附录全为占位符**
- **症状**：附录 A/B/C/D 内容均为 `（将...复制于此）`，完全无法使用
- **根因**：v1.0 只设计了结构，实质内容从未填写
- **解决**：重写为 v2.0，填入全部5个附录的可用内容（A=目录架构提示词、B=代码生成约束、C=审查提示词、D=交付清单、E=踩坑表模板）
- **关键决策**：附录内容内联在同一文件（不拆分），降低使用时的跳转成本

**问题3 — `prompts/02-code-review.md` 为空文件**
- **症状**：文件存在但 0 字节
- **根因**：用 `touch` 创建占位符后从未写入内容
- **解决**：写入完整审查提示词（含标准模式 + 快速模式）

#### 🟡 P1：架构缺失

**问题4 — `templates/project-overlay-template.md` 不存在**
- **症状**："两层分离"设计的核心组件缺失
- **根因**：概念有描述但实现文件从未创建
- **解决**：新建8节完整模板（定义书/决策/审查/修复/踩坑/交付状态/经验/项目信息）

**问题5 — `README.md` 内容是 Seed AI 项目的 README**
- **症状**：文件包含 npm badge、`seed config model` 命令等与方法论库无关的内容
- **根因**：文件混淆
- **解决**：完整重写为方法论库 README（含目录结构、使用指南、Skill 接入方法）

**问题6 — `case-studies/` 目录为空**
- **症状**：目录存在但无任何文件，案例库功能缺失
- **根因**：从未填充
- **解决**：创建 `case-studies/README.md`（含案例索引 + 高频踩坑 TOP 10）和第一个实测案例

### 框架实测：TaskFlow API（Python 3.12 + FastAPI）

**目的**：用一个全新项目（不同语言）验证模板普适性  
**发现并修复的摩擦点**：

| FP | 描述 | 修复位置 |
|----|------|---------|
| FP-1 | 附录 A 无 Python 目录约定（缺 pyproject.toml、包布局规范） | workflow-template.md 附录 A 加语言特定补充 |
| FP-2 | 清单第三节（AI Provider）对非 AI 项目无 N/A 跳过指引 | code-review-checklist.md 文件头加 N/A 规则 |
| FP-3 | 阶段二门禁验证命令无 Python 选项（只有 Java/Go/TS） | workflow-template.md 阶段二加 Python 命令组 |
| FP-4 | Python async Session 全局共享 = 新型 P0（FastAPI 高频错误） | checklist 新增 1.5 + 8.5 两条规则 |

**实测结论**：6阶段骨架完全适用于 Python，主要问题在附录语言特定性不足，已修复。

### 新建/修改文件清单

| 操作 | 文件 | 版本 |
|------|------|------|
| 重写 | `README.md` | v2.0 |
| 重写 | `templates/workflow-template.md` | v2.1 |
| 新建 | `templates/project-overlay-template.md` | v1.0 |
| 重写 | `checklists/code-review-checklist.md` | v1.2 |
| 新建 | `prompts/02-code-review.md` | v1.0 |
| 新建 | `case-studies/README.md` | v1.0 |
| 新建 | `case-studies/taskflow-api-2026-04.md` | 实测案例 |
| 新建 | `BUILD_OPERATIONS.md` | 本文件 |

---

## v2.2 · 2026-04-10 · B-01 案例存档补全

### 本次处理的任务

**B-01 — 补全三个老项目的案例存档**

将三个原始记录文件按 `project-overlay-template.md` 格式重新组织，生成独立的案例存档文件：

| 文件 | 来源 | 主要内容 |
|------|------|---------|
| `case-studies/医保智能审核系统-2026-04.md` | `工作流.md` | 6 P0（KieSession单例、@Cacheable危险缓存、javax→jakarta）+ 6轮修复 |
| `case-studies/CDN边缘计算基础设施-2026-04.md` | `操作记忆库.md` | 7 P0（import末尾、硬编码API Key、1276行上帝类）+ 6轮修复 |
| `case-studies/SeedAI-2026-04.md` | `OPERATIONS.md` + `README.md` | AI Provider接口3次重犯 + P016无限循环 + 27项创新 |

**结果**：`case-studies/README.md` 中三个死链全部修复，案例库现在可完整使用。

---

```

---

## 迭代 Backlog

### P0 — 阻塞后续使用

*当前无 P0 待办。*

### ✅ 已完成

| ID | 任务 | 完成版本 |
|----|------|---------|
| B-01 | 补全三个老项目的案例存档 `.md` 文件 | v2.2 · 2026-04-10 |

### P1 — 重要，可在下次会话处理

| ID | 任务 | 背景 |
|----|------|------|
| B-02 | 创建 `prompts/01-architecture-design.md` | 阶段二架构设计目前无专属提示词 |
| B-03 | 创建 `prompts/03-delivery-verification.md` | 阶段五交付验证目前无专属提示词 |
| B-04 | workflow-template.md 附录 B 新增 Rust/C++ 约束 | 扩展语言生态覆盖 |

### P2 — 锦上添花

| ID | 任务 | 背景 |
|----|------|------|
| ~~B-05~~ | ~~README 加"30秒决策树"~~ | ✅ 已完成（v2.3）|
| B-06 | 创建10项精华版快速审查清单 | 小项目/时间有限时 45 项清单过重 |
| B-07 | 建立摩擦点追踪表 | 长期追踪模板改进历史 |

---

## 版本规范

| 变化类型 | 版本号规则 | 示例 |
|---------|-----------|------|
| 新增内容（新清单项、新附录内容） | 小版本 +0.1 | v1.1 → v1.2 |
| 修复错误（格式、链接、内容错误） | 小版本 +0.1 | v1.2 → v1.3 |
| 结构性调整（新章节、重组） | 大版本 +1.0 | v1.x → v2.0 |
| 重大重写 | 大版本 +1.0 | — |

---

---

## v2.4 · 2026-04-10 · GitHub 上传 + 一键上传 Skill

### 本次处理的任务

**GitHub 首次上传（AI-Project-OS）**

| 步骤 | 操作 | 结果 |
|------|------|------|
| 凭证绑定 | `git config --global credential.helper store` + 写入 `~/.git-credentials` | Token 永久存储，后续无需重新输入 |
| 新建仓库 | GitHub API `POST /user/repos`（name: AI-Project-OS） | 仓库创建成功，不影响 seed-ai |
| 初始化 & 关联 | `git init` + `git remote add origin` | 本地 .git 初始化，关联新仓库 |
| 暂存 & 提交 | `git add`（显式列出15个目标文件）+ `git commit` | 15 文件，3208 行 |
| 推送 | `git push -u origin master` | `* [new branch] master -> master` ✅ |

**仓库地址**：https://github.com/jiayu6954-sudo/AI-Project-OS

**关键踩坑**：
- `curl -d` 传含中文描述的 JSON → `Problems parsing JSON` 错误 → 改为 `--data-raw` 并使用纯英文描述解决
- 旧的错误 remote（seed-ai）需先 `git remote remove origin` 再重新添加

**一键上传 Skill 创建**：
- 路径：`~/.claude/skills/github-upload.md`
- 触发：在 Claude Code 中输入 `/github-upload`
- 覆盖范围：凭证绑定 → 新建仓库 → init → 暂存 → 提交 → 推送 → 验证 + 常见问题排查

---

## v2.3 · 2026-04-10 · GitHub 打包 + 白皮书

### 本次处理的任务

| 操作 | 文件 | 说明 |
|------|------|------|
| 新建 | `LICENSE` | MIT License，`[Author]` 待替换 |
| 新建 | `.gitignore` | 覆盖 macOS/Windows/编辑器/临时文件 |
| 新建 | `CONTRIBUTING.md` | 归档案例/改进模板/追加清单规则的贡献指南 |
| 新建 | `AI协作方法论白皮书.md` | 14 章专业白皮书，含全链路逻辑、验证数据、实施指南 |
| 修改 | `README.md` | "快速上手"前新增"30 秒决策树"，版本升至 v2.1 |
| 修改 | `case-studies/README.md` | TaskFlow 链接文字更新为"框架验证项目" |

---

## 当前系统文件清单

```
E:\AI-Methodology\
├── README.md                          ✅ v2.1
├── BUILD_OPERATIONS.md                ✅ v2.3（本文件）
├── LICENSE                            ✅ MIT（需替换 [Author]）
├── .gitignore                         ✅ 新建
├── CONTRIBUTING.md                    ✅ 新建
├── AI协作方法论白皮书.md               ✅ v1.0（14章）
│
├── templates/
│   ├── workflow-template.md           ✅ v2.1
│   └── project-overlay-template.md   ✅ v1.0
│
├── checklists/
│   └── code-review-checklist.md      ✅ v1.2
│
├── prompts/
│   └── 02-code-review.md             ✅ v1.0
│   ⚠️  01-architecture-design.md    ❌ 缺失（Backlog B-02）
│   ⚠️  03-delivery-verification.md  ❌ 缺失（Backlog B-03）
│
└── case-studies/
    ├── README.md                      ✅ v1.0
    ├── taskflow-api-2026-04.md        ✅ 实测案例
    ├── 医保智能审核系统-2026-04.md    ✅ v1.0
    ├── CDN边缘计算基础设施-2026-04.md ✅ v1.0
    └── SeedAI-2026-04.md             ✅ v1.0

历史文档（只读参考）：
├── 工作流.md        ← 医保系统原始记录
├── 操作记忆库.md    ← CDN 系统原始记录
└── OPERATIONS.md    ← Seed AI 原始记录
```

---

---

## Claude Skill 清单

| Skill 文件 | 触发命令 | 用途 |
|-----------|---------|------|
| `~/.claude/skills/ai-workflow.md` | `/ai-workflow` | 触发完整 6 阶段工作流 |
| `~/.claude/skills/ai-methodology-ops.md` | `/ai-methodology-ops` | 快速恢复本仓库操作上下文 |
| `~/.claude/skills/github-upload.md` | `/github-upload` | 一键打包上传 GitHub（新建仓库全流程）|

---

*BUILD_OPERATIONS 版本：v2.4 · 更新：2026-04-10 · 下次更新节点：B-02/B-03 提示词文件创建*
