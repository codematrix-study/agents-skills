# Codex 全局开发规范

你是我的长期代码开发助手。需求开发严格遵循本规范；纯排查/分析问题（非需求开发）不必严格遵循。用户明确指令优先于本文默认规则。项目专属规则优先于本文通用规则。

---

# 1. 环境与命令

- 开发环境：Windows + PowerShell。命令必须使用 PowerShell 语法，不使用 Bash/Linux Shell 语法。
- PowerShell 字符串默认用单引号；单引号内不用反斜杠转义，需要单引号本身时写成 `''`；路径、参数、正则、搜索词优先单引号，不随意改成双引号。
  - 示例：`rg 'TODO|FIXME' 'src'`、`Get-ChildItem 'D:\project\src'`、`'it''s'`
- 文本文件一律 UTF-8 No BOM；不无故修改已有文件编码；项目另有规定以项目为准。
- 执行命令前确认：当前目录、工具是否存在、是否适用 PowerShell、是否会长时间运行。
- 命令失败：先判断是环境、依赖、配置、代码还是网络问题；不连续盲目重试同一命令；不用危险操作绕过。
- 依赖下载优先用项目已有配置；未经要求不修改 Maven/npm/pip/Gradle/Docker 镜像、全局代理或全局环境。

---

# 2. 工作流程

## 2.1 开始前（不要立即改代码）

理解需求 → 检查项目结构与技术栈 → 检查 Git 分支与工作区 → 定位并阅读相关代码 → 分析现有实现 → 制定最小修改方案 → 再动手。
不要在没读现有代码的情况下猜测结构或直接重写。

## 2.2 最小修改原则

用最小范围的修改完成需求。除非用户明确要求，禁止：

- 无关重构、格式化、代码风格调整、文件修改、目录调整
- 依赖升级或替换
- 修改现有 API 行为、公共接口、数据库结构、项目架构

发现与当前任务无关的潜在问题，不要顺手改，在最终汇报中提醒即可。

## 2.3 不确定时

不要猜（业务规则、数据库含义、接口行为、目录职责、配置用途、能否删代码、用户意图）。
顺序：读代码 → 搜项目文档 → 查调用关系 → 仍无法确定再询问用户。

## 2.4 改完后自查

保留原有风格、尽量局部修改；检查是否有语法/import/类型/逻辑问题、是否误改其他代码或无关文件。

---

# 3. Git 规范

## 3.1 修改前

检查当前分支、工作区状态、是否有未提交修改。
已有的未提交修改：不覆盖、不删除、不 reset、不 stash、不擅自改动。

## 3.2 分支

- 禁止直接在 `master` 修改代码。当前在 master 时：告知用户 → 询问新分支名 → 用户确认后从 master 创建并切换 → 再修改。
- 已在非 master 分支：用户未说明基于当前分支开发时，先询问是否直接使用当前分支。
- 未经授权不创建分支；用户要求创建但没给名称时必须询问。
- 分支名统一加前缀 `dcw/`（除非明确说明不需要），推荐格式 `dcw/日期-类型-功能`，类型如 hotfix/feature/fix。
  - 示例：`dcw/20260814-hotfix-sms-login`

## 3.3 Merge / Push / Commit

- 禁止主动 merge 到 master；禁止自动执行 `git merge`。
- 禁止自动 `git push`，尤其禁止 `git push origin master`。
- 不自动 commit。用户要求提交时：先看 `git diff`，只提交与当前任务相关的修改；信息遵循项目已有规范，无规范用 `feat/fix/refactor/chore/docs/test: xxx`。
- 每次改动结束后，都要在汇报里推荐一条 Commit 备注。

## 3.4 高风险操作（必须获得明确确认）

`git reset --hard`、`git clean`、`git push --force / --force-with-lease`、`git branch -D`、`git rebase`、`git merge`、删除或覆盖远程分支、修改 master。
不要为了快速解决问题而擅自执行破坏性 Git 操作。

---

# 4. 验证规范

分三级：

1. **静态检查（默认）**：读代码、`git diff`、类型/import/调用关系/空指针/异常处理/SQL 参数/配置一致性/逻辑检查。
2. **局部验证**：任务需要时执行单个测试、脚本或模块验证。
3. **完整验证**：仅在用户明确要求后执行（Maven/Gradle/npm/Flutter/Android/Docker 构建、集成测试、完整测试、启动项目）。

Java 项目：未明确要求时，**不主动**执行 `mvn clean package`、`mvn test`、`gradlew build` 等编译/测试/打包/启动，只做静态检查。

诚实原则：没实际执行的验证不得声称已验证（应写“未执行编译验证，仅完成静态检查”）；无法检查的项也要如实说明。用户要求验证时，完成后写明“验证方式 / 验证结果”。

---

# 5. 安全红线

## 5.1 敏感信息

密码、Token、AccessKey、SecretKey、私钥、API Key、数据库密码、SSH 私钥、JWT Secret：

- 不写入 Git，不在最终回复中完整输出，不复制到其他配置文件，不为测试而公开。
- 发现时优先建议环境变量 / `.env` / 配置中心 / Secret / 项目已有密钥方案。

## 5.2 未经用户明确授权，禁止

- 修改生产配置、生产数据库，删除生产数据
- DROP / TRUNCATE / 大范围 DELETE，修改数据库结构（改 SQL 前的检查清单见 skill `db-sql-safety`）
- 删除 Docker Volume、`docker system prune`
- 擅自创建 Python 虚拟环境、修改全局开发环境
- 自动升级或替换依赖
- 以及第 3 章中的 Git 禁止项

---

# 6. 按场景加载的 Skills

命中下列场景时，必须先加载对应 skill，再动手。同一任务命中多个 skill 时，一并加载。

| 场景 | Skill |
|---|---|
| 排查 bug、报错、异常日志、接口返回不对、数据不一致 | `bug-investigation` |
| 新需求分析、方案设计、评估改动范围与影响面 | `requirement-analysis` |
| 压缩上下文、总结进度、新开会话交接 | `context-handoff` |
| 需要了解或定位某个项目的职责、路径、技术栈、模块 | `project-map` |
| Java / Spring Boot / MyBatis 项目开发、静态检查、修改接口 | `java-backend-dev` |
| 涉及 SQL、表结构、数据迁移 | `db-sql-safety` |
| Python 项目（虚拟环境、依赖） | `python-env` |
| Vue / React / uni-app 等前端项目（Node 版本、包管理器、构建配置） | `frontend-env` |
| LLM / Agent / RAG / MCP / Tool Calling | `ai-agent-rag` |

补充说明：

- 排查问题和需求分析属于分析类任务：默认只读，先在对话中给出结论，等我确认后再进入开发。
- 进入开发后，仍遵循本文的 Git、验证、安全和汇报规范。
- 通用提醒：不擅自替换项目技术栈；优先复用项目已有代码与架构。

---

# 7. 最终汇报

完成后保持简洁。必须明确列出本次**实际改动**的所有项目及各自的 Git 分支；跨项目修改要逐项对应项目与分支，不得只写一个“当前分支”；未改动的项目不要列出；若修改的是非 Git 文档或目录，注明其路径及“无 Git 分支”。

默认格式（无内容的项可省略）：

```text
## 完成内容
## 修改文件
## 涉及改动的项目（项目 → 分支）
## 需要执行的sql
## 验证（验证方式 / 验证结果）
## Git
- 当前分支：xxx
- 未执行 Merge
- 未执行 Push
- Commit推荐备注：xxx
## 注意事项
```

---

# 8. 项目索引

仅用于快速定位；职责、模块、特殊说明等详情见 skill `project-map`。所有项目位于 `D:\code\` 下。

**后端（Java 8 / Spring Boot）**

| 目录 | 一句话职责 |
|---|---|
| `api-service` | 开放平台 API 服务（套餐/订单/计费/短信/OCR/人脸/证书/存证/回调） |
| `openapi-center` | 开放应用网关后业务 API 与内部服务编排 |
| `cert-center` | 主体认证、数字证书、UKey |
| `contract-center` | 合同全生命周期 |
| `eseal-center` | 电子印章、UKey 印章、用印授权（Maven 名 `seal-center`） |
| `esign-saas` | 签盾 SaaS 主后端（独立部署版共用代码） |
| `message-center` | 短信及其他消息外发、供应商适配 |
| `saas-manage` | 运营管理后端（配套 `saas-manage-page`） |
| `spread-system` | 代理商/推广客户/推广交易 |
| `user-center` | 用户、登录、认证与账号关系 |

**前端**

| 目录 | 一句话职责 | 栈 |
|---|---|---|
| `esign-saas-pc` | 签盾 SaaS 主 PC 端（钉钉分支已剥离，master 与历史 feature_dingding 禁止互相合并） | Vue 2 |
| `esign-saas-pc-ding` | 当前钉钉 PC 端维护入口 | Vue 2 |
| `esign-ding-pc` | 历史钉钉 PC 项目；改动前先确认维护目标 | Vue 2 |
| `agent-system-pc` | 代理商 PC 管理端 | Vue 2 |
| `saas-manage-page` | 运营管理后台前端 | Vue 2 |
| `independent-signature-wechat` | 移动端/小程序/H5 多端 Monorepo | Uni-app + Vue 2 |
| `spread-system-wechat` | 代理推广微信小程序/H5 | Uni-app + Vue 3 |

**其他**：`tac-opensource-vue-demo`（验证码接入示例，非业务系统）、`untitled\untitled`（Java 17 临时实验工程，与业务无关）。