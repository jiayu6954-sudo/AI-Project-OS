# AI 协作工作流 — 工业级通用 Skill v2.1

> **Skill 名称**：`ai-workflow`  
> **保存位置**：`E:\AI-Methodology\templates\workflow-template.md`  
> **作为 Skill 使用**：将本文件复制到 `~/.claude/skills/ai-workflow.md`，之后在 Claude Code 中输入 `/ai-workflow` 即可触发。  
> **版本**：v1.0（2026-04-09 初稿）→ v2.0（2026-04-10，附录全部填实）→ v2.1（2026-04-10，Python 适配：目录约定、编译验证命令、async Session 约束）

---

## 设计理念

本模板提炼自三个工业级项目的完整交付过程：

| 项目 | 技术栈 | 规模 |
|------|--------|------|
| 医保智能审核系统 | Java / Spring Boot 3 / Drools / PostgreSQL | 8 微服务，20 条规则，6 轮修复 |
| CDN 边缘计算基础设施 | Go / OpenResty / Lua / Docker | 控制面板 + 数据面，6 个交付任务 |
| Seed AI | TypeScript / Node.js / Ink | 27 项创新，42 次迭代发布 |

**核心分离原则**：
- **本模板**（通用）= 流程 HOW + 提示词 + 检查清单
- **[项目操作记忆](project-overlay-template.md)**（项目专属）= 决策 WHAT + 踩坑 WHY

每个新项目只需维护一份轻量的"覆盖层"文件，通用流程直接引用本模板。

---

## 流程总览

```
阶段一：目标定义   →  产出：项目定义书（1页清单）
阶段二：代码生成   →  产出：可编译的初始代码骨架
阶段三：系统审查   →  产出：P0/P1/P2 分级审查报告
阶段四：分轮修复   →  产出：所有 P0/P1 已关闭，测试覆盖 ≥ 80%
阶段五：工程交付   →  产出：生产就绪的完整可交付系统
阶段六：知识沉淀   →  产出：案例存档 + 清单更新
```

> 每个阶段末尾有**门禁检查**，全部通过才能进入下一阶段。

---

## 阶段一：目标定义与约束固化

**目标**：产出一份清晰的项目定义书，确保 AI 与人的目标完全一致。

### 1.1 填写《项目定义书》

新建 `/memory/项目操作记忆.md`（复制 [project-overlay-template.md](project-overlay-template.md)），完成以下字段：

| 字段 | 示例 | 说明 |
|------|------|------|
| **系统定位** | "构建事前/事中/事后全流程医保监管平台" | 一句话，说明核心价值 |
| **硬性约束** | 实时审核 ≤300ms，等保三级，规则热更新 | 性能、安全、合规的量化要求 |
| **成功标准** | 核心场景全通过，CI 绿，文档完整可交付 | 可量化的完成定义 |
| **技术栈初选** | Java 17 + Spring Boot 3.x + Drools 8 | 语言、框架、数据库 |
| **排除项** | 不做前端，不做移动端 | 明确边界，防止范围蔓延 |

### 1.2 生成项目目录骨架

使用 **附录 A 的提示词**，让 AI 在指定路径生成完整目录结构。

### 1.3 门禁检查

- [ ] 项目定义书已人工确认，无歧义字段
- [ ] 目录骨架已生成，`.gitignore` 已配置（忽略 `*.env`、`secrets/`）
- [ ] 硬性约束已量化（性能指标有数值，合规要求有标准编号）

---

## 阶段二：架构设计与代码生成

**目标**：产出可编译的初始代码，搭建好系统骨架。

### 2.1 编写架构设计文档

让 AI 在 `/docs/架构设计文档.md` 中完成：

- [ ] 系统分层架构图（ASCII 或 Mermaid，必须包含所有服务和它们之间的通信方式）
- [ ] 核心模块划分与职责说明（每个模块一句话定义边界）
- [ ] 数据模型概要（核心表结构、ER 图或字段清单）
- [ ] API 接口概要（REST/gRPC 契约，含认证方式）

### 2.2 注入约束后生成代码

> ⚠️ **必须先发附录 B，再让 AI 生成代码。** 三个项目验证：跳过这步必然产生 P0 Bug。

操作步骤：
1. 发送 **附录 B 的代码生成约束提示词**
2. 等待 AI 确认理解后，再发送生成代码的指令
3. 代码生成完成后，立即验证编译

```bash
# Java/Maven
mvn compile -q && echo "✅ 编译通过" || echo "❌ 编译失败，先修复再继续"

# Node.js/TypeScript
npm run build && echo "✅ 编译通过" || echo "❌ 编译失败"

# Go
go build ./... && echo "✅ 编译通过" || echo "❌ 编译失败"

# Python（语法检查 + 类型检查，Python 无编译步骤）
python -m py_compile $(find . -name "*.py" | grep -v __pycache__) && echo "✅ 语法检查通过"
mypy . --ignore-missing-imports && echo "✅ 类型检查通过"
pytest tests/ -q --tb=short && echo "✅ 测试通过"
```

> **Python 项目补充说明**：Python 无编译步骤，用静态检查（mypy）+ 运行测试代替编译验证。

### 2.3 门禁检查

- [ ] 架构设计文档已人工审阅（重点：职责边界清晰、无循环依赖）
- [ ] 代码编译/语法检查通过，无错误
- [ ] 所有依赖已在依赖文件（pom.xml / package.json / go.mod / pyproject.toml）中声明

---

## 阶段三：系统性代码审查

**目标**：在修复成本最低的时机发现所有潜在问题。

### 3.1 执行 AI 自审

发送 **附录 C 的审查提示词**，附带 [`code-review-checklist.md`](../checklists/code-review-checklist.md) 全文，让 AI 逐项检查（10 个类别，约 45 个检查项）。

### 3.2 记录审查结果

在 `/memory/项目操作记忆.md` 的"审查报告"节中记录：

- **P0 致命缺陷**：会导致系统崩溃或安全漏洞（必须在下一阶段第一轮修复）
- **P1 架构缺失**：功能不完整、核心模块缺失
- **P2 代码异味**：不规范但当前可工作

### 3.3 门禁检查

- [ ] 审查报告已生成，所有问题已按 P0/P1/P2 分级
- [ ] 人工已确认优先级（P0 数量通常 3-8 个，P1 通常 5-15 个）
- [ ] 修复轮次计划已初步制定

---

## 阶段四：分轮次修复迭代

**目标**：按优先级系统地关闭所有问题，达到功能完整。

### 4.1 典型修复路线（参考三个项目的规律）

```
第一轮（P0-编译）：修复编译错误、import 错误、缺失实现类
  └── 验证：mvn compile / go build / npm run build 通过

第二轮（P0-安全）：修复硬编码密钥、认证绕过、不安全的随机数
  └── 验证：审查报告中所有 P0 安全项已关闭

第三轮（P1-架构）：补全缺失的服务、模块、DRL 规则、测试
  └── 验证：所有接口有实现，核心功能端到端可运行

第四轮（P1-质量）：修复 Mock 配置、补全测试断言、N+1 查询优化
  └── 验证：单元测试通过率 ≥ 80%

第五/六轮（按需）：集成测试、Schema 对齐、性能调优
  └── 验证：Testcontainers 集成测试通过，核心场景端到端验证
```

### 4.2 每轮执行规范

- 每轮开始前明确告诉 AI：**"本轮只处理 P0，不要改动 P1/P2 代码"**
- 修复完成后重新运行编译 + 测试，确认无回归
- 在 `/memory/项目操作记忆.md` 中记录本轮的关键**决策**（不记录代码细节，代码在 git 里）

### 4.3 门禁检查

- [ ] 所有 P0 问题已关闭
- [ ] 所有 P1 问题已关闭（或有明确延期计划）
- [ ] 单元测试通过率 ≥ 80%
- [ ] 核心业务场景 Happy Path 可端到端运行

---

## 阶段五：工程化交付物补全

**目标**：将"能运行的程序"变成"可交付的产品"。

### 5.1 交付物核查清单（见附录 D）

| 类别 | 交付物 | 标准位置 |
|------|--------|----------|
| 容器化 | Dockerfile（多阶段构建，非 root 用户） | `/deploy/Dockerfile` |
| 编排 | docker-compose.yml（含健康检查、命名卷） | `/deploy/docker-compose.yml` |
| CI/CD | 流水线配置（编译→测试→构建→部署） | `/tools/.gitlab-ci.yml` 或 `.github/workflows/` |
| 部署 | deploy.sh（支持 dev / test / prod 三环境） | `/scripts/deploy.sh` |
| 验证 | setup-and-verify.sh（环境自检脚本） | `/scripts/setup-and-verify.sh` |
| 文档-运维 | 运维手册（启动/停止/监控/备份/故障处理） | `/docs/运维手册.md` |
| 文档-API | API 文档（含认证、全部接口、请求/响应示例） | `/docs/API文档.md` |
| 文档-白皮书 | 白皮书（面向评估机构，含架构/安全/性能章节） | `/docs/白皮书.md` |
| 安全 | 安全加固指南（生产上线前的逐项核查） | `/docs/SECURITY-HARDENING.md` |
| 配置 | 环境变量模板（含所有必需变量及说明） | `/config/production.env.example` |

### 5.2 集成测试

```bash
# 1. 启动测试环境
docker compose -f deploy/docker-compose.yml up -d

# 2. 等待所有服务健康检查通过
./scripts/setup-and-verify.sh

# 3. 执行核心业务场景验证
# [具体命令记录在 /memory/项目操作记忆.md 中]
```

### 5.3 门禁检查

- [ ] 附录 D 中所有检查项均为 ✅
- [ ] Docker 镜像可在干净环境中构建并启动
- [ ] 核心业务场景（Happy Path + 关键异常路径）集成测试通过
- [ ] README 中的快速启动步骤已在干净环境验证

---

## 阶段六：知识沉淀与模板反哺

**目标**：将本次项目经验固化为可复用的长期资产。

### 6.1 整理项目操作记忆

在 `/memory/项目操作记忆.md` 的"经验教训"节确认：
- [ ] 所有**关键设计决策**及理由已记录（只记录不显而易见的、有重要权衡的）
- [ ] **踩坑记录表**已完整填写（参见 project-overlay-template.md 中的格式）

### 6.2 反哺全局清单

对每个踩坑，判断是否是新的 Bug 模式（不在 code-review-checklist.md 中）：

```
新发现的 Bug 模式：
- 问题：[描述]
- 根因：[为什么 AI 会犯这个错]
- 防范：[在代码生成前应加哪条约束]
→ 追加到 E:\AI-Methodology\checklists\code-review-checklist.md 对应章节
```

### 6.3 归档案例

```bash
# 将项目操作记忆复制到案例库
copy /memory/项目操作记忆.md "E:\AI-Methodology\case-studies\[项目名称]-[YYYY-MM].md"
```

### 6.4 门禁检查

- [ ] 至少有一条新经验已更新到全局清单
- [ ] 项目案例已归档至 `E:\AI-Methodology\case-studies\`

---

## 附录 A：项目目录架构提示词

> 直接粘贴给 AI，替换 `[...]` 占位符后发送。

```
请在 [项目根路径，如 E:\projects\my-system] 下生成完整的工程目录骨架。

项目名称：[PROJECT_NAME]
技术栈：[TECH_STACK，如 Java 17 + Spring Boot 3 + PostgreSQL]

目录结构要求（只生成目录和占位文件，不生成业务代码）：

project-root/
├── docs/
│   ├── 架构设计文档.md
│   ├── API文档.md
│   ├── 运维手册.md
│   ├── 白皮书.md
│   └── SECURITY-HARDENING.md
├── src/                        # 应用源代码（按业务模块组织，单文件上限 300 行）
│                               # ⚠️ Python 项目：src/ 改为包名目录（如 taskflow/）
├── deploy/
│   ├── Dockerfile              # 多阶段构建（builder + runtime）
│   └── docker-compose.yml      # 含健康检查、命名卷、网络配置
├── scripts/
│   ├── deploy.sh               # 支持 dev / test / prod 三个环境
│   └── setup-and-verify.sh     # 环境自检脚本
├── tests/
│   ├── unit/
│   ├── integration/            # Testcontainers 或等效隔离机制
│   └── e2e/
├── tools/
│   └── .gitlab-ci.yml          # 或 .github/workflows/ci.yml
├── config/
│   ├── production.env.example
│   └── development.env.example
└── memory/
    └── 项目操作记忆.md          # AI 协作决策记录（参考 project-overlay-template.md）

根目录文件：
- README.md（包含：环境要求、快速启动、验证步骤、访问地址列表）
- .gitignore（忽略：*.env、secrets/、*.key、node_modules/、target/、dist/、build/、__pycache__/、.venv/）
- .env.example

【语言特定补充】请根据技术栈额外生成以下文件：
- Java/Maven：pom.xml（父 POM）、各模块 pom.xml
- Node.js：package.json（含 scripts: build/test/lint）、tsconfig.json
- Go：go.mod、go.sum
- Python：pyproject.toml（含 [tool.mypy] 和 [tool.pytest.ini_options]）、requirements.txt、requirements-dev.txt

请用 shell 命令列出创建步骤，并对关键文件给出内容模板（README、.gitignore、.env.example）。
```

---

## 附录 B：代码生成强制约束提示词

> 在要求 AI 生成业务代码前，先单独发送此提示词。

```
在开始生成代码前，请确认你已理解并将遵守以下所有强制约束。
这些约束来自三个工业级项目（医保系统 / CDN 基础设施 / Seed AI）的真实踩坑，
违反任何一条都曾导致系统级故障或安全漏洞。

═══════════════════════════════════════════
  架构约束
═══════════════════════════════════════════

□ 单文件行数上限 300 行（超出必须按职责拆分为多个文件）
□ Service 层必须 Interface + Impl 分离，不允许直接实现接口
□ 有状态对象不可注册为全局单例：
    Java：KieSession、非线程安全的 Session / Connection 对象
    Go：含可变状态的 struct（不加 Mutex 时）
    TypeScript：含内部状态的实例
    Python：async SQLAlchemy Session 不可作为模块级全局变量，必须用 sessionmaker 工厂 + 依赖注入
□ AI Provider / 外部 Client 必须通过接口抽象，禁止在业务代码中直接 new 具体 SDK 实现
□ 不允许空 catch/except 块，至少必须记录日志

【Python 专项约束】
□ async/sync 不混用：FastAPI 路由全用 async def，或全用 def，不混用
□ 数据库迁移使用 Alembic（不用 metadata.create_all()，生产环境无法做结构变更）
□ pytest 异步测试需配置 anyio 插件（pyproject.toml 中设置 asyncio_mode = "auto"），否则 async 测试静默跳过
□ 密钥生成使用 secrets.token_hex(32)（不用 random 或 uuid4，不具备密码学安全性）

═══════════════════════════════════════════
  安全约束（每一条都曾是真实安全漏洞）
═══════════════════════════════════════════

□ 零硬编码密钥 / 密码 — 全部从环境变量读取
□ 密钥生成必须使用密码学安全随机数：
    Java：SecureRandom
    Go：crypto/rand（不是 math/rand）
    Node.js：crypto.randomBytes(32)
□ Dockerfile 必须包含非 root USER 指令
□ 所有 API 接口默认需要鉴权，白名单接口必须显式列出

═══════════════════════════════════════════
  依赖与编译约束
═══════════════════════════════════════════

□ 代码中 import 的所有包必须在依赖文件中声明
□ import 语句必须放在文件顶部，不允许分散在代码中间
□ 同一包 / 模块内禁止重复定义同名函数或变量
□ 检查包版本兼容性：
    Spring Boot 3.x → 必须使用 jakarta.*（不是 javax.*）
    MyBatis Plus → 枚举字段必须加 @EnumValue 注解
    Go 1.21+ → 验证是否使用了当前版本支持的语法

═══════════════════════════════════════════
  测试约束
═══════════════════════════════════════════

□ 每个 Mock 对象的所有调用路径都必须有 when().thenReturn() 配置
□ 使用测试框架断言（JUnit Assertions.assertTrue()），禁止使用 Java assert 关键字
□ 集成测试使用 Testcontainers（或等效隔离机制），不依赖外部环境

生成完成后，请主动对照以上约束做一轮自检，列出所有不符合项。
```

---

## 附录 C：代码审查提示词

> AI 生成代码后，发送此提示词执行系统性审查。将 code-review-checklist.md 全文一并附上。

```
请对当前项目的全部源代码执行系统性审查。

审查步骤：
1. 阅读附带的《代码审查强制清单》（来自 code-review-checklist.md）中的全部检查项
2. 逐一检查当前项目代码，对每项标注状态：
   ✅ 符合
   ❌ 违反（必须给出：文件名 + 行号 + 问题描述 + 修复方案）
   ⚠️ 部分符合（说明差距）
3. 输出以下格式的《审查报告》：

---《审查报告》---

## P0 致命缺陷（系统崩溃 / 安全漏洞）
| # | 问题描述 | 文件:行号 | 修复方案 |
|---|---------|---------|---------|

## P1 架构缺失（功能不完整 / 核心模块缺失）
| # | 问题描述 | 文件:行号 | 修复方案 |
|---|---------|---------|---------|

## P2 代码异味（不规范但当前可工作）
| # | 问题描述 | 文件:行号 | 修复方案 |
|---|---------|---------|---------|

## 通过项 ✅
（列出所有符合的检查项编号）

## 审查统计
- P0 数量：X 个
- P1 数量：X 个
- P2 数量：X 个
- 建议修复轮次：X 轮
- 预计最小可用状态：第 X 轮结束后

---《报告结束》---

审查完成后，等待我确认优先级后再开始修复。
```

---

## 附录 D：工程化交付检查清单

> 阶段五结束时逐项核对，全部 ✅ 才能宣布"工业级交付完成"。

### D-1 容器化与部署

| # | 检查项 | 验证方法 |
|---|--------|---------|
| D-1.1 | Dockerfile 使用多阶段构建（builder + runtime 至少两个 FROM） | `grep -c "^FROM" deploy/Dockerfile` 应 ≥ 2 |
| D-1.2 | 运行时镜像以非 root 用户启动 | `grep "USER" deploy/Dockerfile` 有结果 |
| D-1.3 | docker-compose.yml 每个服务都有 healthcheck 配置 | `grep -c "healthcheck" deploy/docker-compose.yml` = 服务数量 |
| D-1.4 | 数据库 / 持久化目录使用命名卷（不是 bind mount） | `grep "volumes:" deploy/docker-compose.yml` |
| D-1.5 | deploy.sh 支持 dev / test / prod 三个环境参数 | `./scripts/deploy.sh --help` 或 `bash -n scripts/deploy.sh` |

### D-2 CI/CD

| # | 检查项 | 验证方法 |
|---|--------|---------|
| D-2.1 | 流水线包含：编译 → 测试 → 镜像构建 → 部署 四个阶段 | 检查 `.gitlab-ci.yml` 的 `stages:` 字段 |
| D-2.2 | 测试失败时流水线阻止后续阶段 | 确认各阶段有依赖关系配置 |
| D-2.3 | CI 使用 `.env.example` 中的变量名，不硬编码密钥 | `grep -rn "password\|secret" .gitlab-ci.yml` 无硬编码 |

### D-3 安全加固

| # | 检查项 | 验证方法 |
|---|--------|---------|
| D-3.1 | 源代码中无硬编码密钥 | `grep -rni "apikey\|secret\|password" src/ --include="*.java"` （检查结果） |
| D-3.2 | 提供 `production.env.example`，包含所有必需变量 | `cat config/production.env.example` 对比实际使用的环境变量 |
| D-3.3 | HTTPS 配置存在（至少有自签名方案或 Let's Encrypt 指引） | 检查 Nginx / 网关配置 |
| D-3.4 | 提供 SECURITY-HARDENING.md 并包含生产上线前检查项 | 检查文件存在且非空 |

### D-4 文档完整性

| # | 检查项 | 验证标准 |
|---|--------|---------|
| D-4.1 | README 包含：环境要求 + 安装 + 启动 + 验证 + 访问地址 | 按 README 步骤在干净机器上验证可行 |
| D-4.2 | 运维手册包含：启动/停止、监控查看、备份恢复、故障排查 | 各章节有实际命令，非空壳 |
| D-4.3 | API 文档覆盖所有接口，含认证、请求体、响应体、示例 | 抽查 3-5 个核心接口，逐一验证 |

### D-5 可运维性

| # | 检查项 | 验证方法 |
|---|--------|---------|
| D-5.1 | 所有服务有健康检查端点 | `curl -f http://localhost:[PORT]/health` 返回 200 |
| D-5.2 | 日志为结构化 JSON 格式，含 traceId / requestId | 实际触发一个请求，查看日志输出 |
| D-5.3 | 有 Prometheus 指标端点（或等效监控方案） | `curl http://localhost:[PORT]/metrics` 返回指标文本 |
| D-5.4 | 有数据库备份方案（至少有备份脚本或说明） | `ls scripts/` 是否有 backup 相关文件 |

---

## 附录 E：踩坑记录表模板

> 复制到 `/memory/项目操作记忆.md` 的「踩坑记录」节使用。

```markdown
## 踩坑记录

| # | 问题描述 | 严重性 | 发现位置 | 根因（一句话） | 修复方案 | 是否已反哺清单 |
|---|---------|--------|---------|-------------|---------|--------------|
| 1 | KieSession 被作为 Spring Bean 单例，第二次请求崩溃 | P0 | DroolsConfig.java | 有状态对象当无状态用 | KieContainer 单例，KieSession 每请求新建 | ✅ 清单 1.1 |
| 2 | @Cacheable 加在审核结果上，规则变更后旧结论持续生效 | P0 | RuleEngineService.java | 业务决策结果不应被缓存 | 移除 @Cacheable | ✅ 清单 2.1 |
| 3 | （填写本项目新发现的踩坑） | | | | | ☐ |
```

---

*模板版本：v2.0 · 更新日期：2026-04-10*  
*提炼来源：医保智能审核系统 · CDN 边缘计算基础设施 · Seed AI*  
*维护地址：`E:\AI-Methodology\templates\workflow-template.md`*
