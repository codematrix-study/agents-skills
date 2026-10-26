---
name: java-backend-dev
description: Java / Spring Boot / Spring Cloud / MyBatis 后端项目的开发规范。当任务涉及 Java 代码修改、Controller/Service/Mapper 调整、新增或修改接口、DTO/VO 变更、Feign 调用、Maven/Gradle 项目，或需要对 Java 改动做静态检查时使用。
---

# Java 后端开发规范

## 1. 技术栈原则

优先遵循项目现有技术栈，不擅自替换：Java、Maven/Gradle、Spring Boot、Spring Cloud、MyBatis / MyBatis-Plus、Lombok、MapStruct、Redis、MySQL、RabbitMQ、Nacos、Docker。
不要为了方便引入新依赖或替换已有组件；确需新增依赖，先询问用户。

## 2. 默认验证：只做静态检查

用户**没有明确要求**编译、打包、单元测试、集成测试、启动项目时：

- **不主动**执行 `mvn clean package`、`mvn test`、`gradlew build` 等任何 Maven/Gradle 构建命令。
- 只做静态检查，并在汇报中写明：`未执行编译验证，仅完成静态检查`。

用户明确要求编译/测试/启动/打包/验证时，按要求执行，完成后写明：

```text
验证方式：
验证结果：
```

验证结果必须基于实际执行，不得假装验证。

## 3. 静态检查清单

改动完成后逐项检查（无法检查的项要如实说明，不要假装已验证）：

- 源码阅读：改动是否符合需求、是否误改其他代码
- 类型检查、方法签名与调用是否一致
- import 是否缺失或多余
- 空指针风险（尤其是新增的对象取值、集合遍历、Feign 返回值）
- 异常处理是否完整、是否吞异常
- SQL 参数检查：Mapper 接口方法参数与 XML/注解 SQL 中的占位符是否对应，`#{}` / `${}` 使用是否合理
- Mapper / Service / Controller 调用关系是否闭环
- 配置一致性：Nacos 配置项、`application*.yml`、`@Value` / `@ConfigurationProperties` 是否对应
- 业务逻辑检查：边界条件、事务边界（`@Transactional`）、幂等性
- 代码风格是否与周边代码一致（命名、缩进、日志风格、返回体封装方式）

## 4. 修改接口时

不要只改 Controller 而忽略上下游，优先检查：

- Controller、DTO、VO
- Service、Mapper
- 前端调用方（对应前端项目，见 skill `project-map` 中的配套关系）
- Feign 契约（如 `*-api` 模块）与其他服务的调用方
- API 文档
- 参数校验（`@Valid` / `@NotNull` 等）
- 异常处理与统一返回体

如果修改的是**已有接口的行为**，必须在最终汇报里明确说明兼容性影响（字段增删、必填变化、返回结构变化、调用方是否需要同步修改）。
不修改公共接口、现有 API 行为、数据库结构，除非用户明确要求。

## 5. 多模块项目

本平台后端多为多模块结构（`*-api` / `*-common` / `*-dal` / `*-core` / `*-web` 等）：

- 新增类放入职责匹配的已有模块，不为个人习惯新建模块或目录。
- `*-api` 模块是对外 Feign 契约，改动会影响调用方，改前先搜索全部引用。
- 无法判断某个模块职责时，先读代码；仍不确定再询问用户。

## 6. 涉及数据库时

同时遵循 skill `db-sql-safety`。需要给出迁移 SQL 的，在最终汇报的“需要执行的sql”中列出，不要自行执行。
