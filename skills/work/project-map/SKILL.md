---
name: project-map
description: 电子签约平台全部项目的目录、职责、技术栈、模块与特殊说明。当用户提到合同中心、证书中心、电子印章中心、用户中心、消息中心、开放平台、api-service、openapi-center、cert-center、contract-center、eseal-center、esign-saas、message-center、saas-manage、spread-system、user-center、agent-system-pc、esign-saas-pc、esign-saas-pc-ding、esign-ding-pc、independent-signature-wechat、saas-manage-page、spread-system-wechat 等项目名，或需要判断某个功能应该改哪个项目时使用。
---

# 项目地图

所有项目位于 `D:\code\` 下。使用方式：先根据用户描述定位项目，再进入该项目目录阅读代码；跨项目调用链（如短信）要逐个项目确认，不要凭下面的说明猜测实现。

## 后端项目

### api-service（开放平台 API 服务）
- 路径：`D:\code\api-service`
- 用途：为电子签约平台提供开放 API、内部 API 和第三方服务接入；平台能力出口与接口聚合层。
- 核心功能：签约套餐、产品订单、计费、短信、OCR、人脸、证书、存证、时间戳及回调处理。
- 技术栈：Java 8、Spring Boot、Spring Cloud Alibaba、Nacos、OpenFeign、MyBatis、ShardingSphere、MySQL、Redis。
- 模块：`api-service-api`、`api-service-web`、`api-service-dal`、`api-service-provider`、`api-service-common`、`api-service-core`。
- 特殊说明：同时存在管理端、内部接口、v2 接口和短信供应商回调等多类入口；部分历史 Controller 整类已注释，必须以当前未注释入口为准。根目录不再保留短信多渠道/备案审核迁移 SQL，通用业务表迁移须依据发布记录、目标库 DDL 与 `ShardingConfig` 单独确认；`docs/` 目录被 Git 忽略，默认不属于随代码提交的版本化文档。

### openapi-center（开放平台中心）
- 路径：`D:\code\openapi-center`
- 用途：面向开放平台应用提供网关鉴权后的业务 API，并编排平台内部服务能力；开放应用接入层与业务能力聚合服务。
- 核心功能：开放应用的签约、认证、证书、文件、存证、OCR、人脸、短信签名及短信发送等接口。
- 技术栈：Java 8、Spring Boot 2.6.10、Spring Cloud 2021、Spring Cloud Alibaba、Nacos、OpenFeign、MyBatis、Redis。
- 模块：`openapi-api`、`openapi-common`、`openapi-core`、`openapi-dal`、`openapi-web`。
- 特殊说明：Nacos 服务名为 `openapi-center`；`openapi-web` 的网关入口使用 `LoginIdentityType.GATEWAY`；短信 `mes_send` 会调用 `api-service`，再进入 `message-center` 的供应商路由与发送记录链路。

### cert-center（证书中心）
- 路径：`D:\code\cert-center`
- 用途：处理主体认证、数字证书和 UKey 相关业务；电子签名身份认证与 CA 证书生命周期服务。
- 核心功能：企业/个人主体认证、证书申请、吊销、UKey 管理、证书授权和认证任务。
- 技术栈：Java 8、Spring Boot、Spring Cloud Alibaba、Nacos、OpenFeign、MyBatis、MySQL、钉钉 SDK、Hutool。
- 模块：`cert-api`、`cert-common`、`cert-dal`、`cert-core`、`cert-web`。
- 特殊说明：同时提供 SaaS、开放接口和 App/网关调用入口。

### contract-center（合同中心）
- 路径：`D:\code\contract-center`
- 用途：负责合同全生命周期管理；电子合同领域核心服务。
- 核心功能：合同发起、签署、查询、文件、模板、分类、归档、批量签署和审批流程。
- 技术栈：Java、Spring Boot、Spring Cloud Alibaba、Nacos、OpenFeign、MyBatis、MySQL。
- 模块：`contract-api`、`contract-common`、`contract-dal`、`contract-service`、`contract-core`、`contract-web`、`contract-deploy`。
- 特殊说明：`contract-api` 对外提供 Feign 合同服务契约。

### eseal-center（电子印章中心）
- 路径：`D:\code\eseal-center`
- 用途：管理电子印章、UKey 印章和用印授权；电子签章与印章安全服务。
- 核心功能：印章创建、授权、验证、用印任务、UKey 印章、法人章授权和印章记录。
- 技术栈：Java 8、Spring Boot 2.6.10、Spring Cloud 2021、Spring Cloud Alibaba、Nacos、OpenFeign、MyBatis、MySQL。
- 模块：`seal-api`、`seal-common`、`seal-core`、`seal-dal`、`seal-web`、`seal-test`。
- 特殊说明：Maven 项目标识为 `seal-center`，目录名为 `eseal-center`。

### esign-saas（签盾 SaaS 主后端）
- 路径：`D:\code\esign-saas`
- 用途：承载签盾 SaaS 和独立部署版的核心业务；电子签约平台主业务聚合服务。
- 核心功能：合同签署、支付充值、账户、存证、UKey、营销内容和基础配置。
- 技术栈：Java 8、Spring Boot、Spring Cloud Alibaba、Nacos、OpenFeign、MyBatis-Plus、MySQL、Redis。
- 模块：`esign-dependencies`、`esign-module`、`module-base`、`module-marketing`、`module-pay`、`module-proof`、`module-ukey`、`news-service`、`esign-deploy`。
- 特殊说明：README 表明独立部署系统与 SaaS 系统共用一套代码。

### message-center（消息中心）
- 路径：`D:\code\message-center`
- 用途：负责短信及其他消息的外发；统一消息发送和供应商适配服务。
- 核心功能：系统短信、开放短信、企业微信消息、模板发送及发送结果处理。
- 技术栈：JDK 8、Spring Boot 2.6.10、Spring Cloud 2021、Nacos、OpenFeign、MySQL、阿里云 SDK。
- 模块：`message-api`、`message-common`、`message-dal`、`message-core`、`message-web`。
- 特殊说明：短信发送渠道由配置和处理器选择，和 `api-service` 的短信入口存在协作关系。

### saas-manage（SaaS 运营管理后端）
- 路径：`D:\code\saas-manage`
- 用途：为运营后台提供管理 API；平台运营、财务、代理商和 UKey 管理中心。
- 核心功能：用户、主体、代理商、产品、订单、账户、充值、审批、营销、UKey 和系统配置管理。
- 技术栈：Java 8、Spring Boot、Spring Cloud Alibaba、MyBatis-Plus、MySQL、Redis/Redisson、阿里云 OSS/MNS、Druid。
- 模块：`saas-common`、`saas-dal`、`saas-mapper`、`saas-core`、`saas-controller`、`saas-aliyun`、`batchBonusPoints`、`saas-generator`、`saas-test`。
- 特殊说明：与前端 `saas-manage-page` 配套使用。

### spread-system（代理推广系统后端）
- 路径：`D:\code\spread-system`
- 用途：处理代理商、推广客户及推广交易业务；渠道分销和代理商增长系统。
- 核心功能：代理商入驻、客户管理、邀请注册、推广素材、产品购买、支付、钱包、物流和组织成员管理。
- 技术栈：Java、Spring Boot、Spring Cloud Alibaba、Nacos、OpenFeign、MyBatis-Plus、MySQL、Redis/Redisson、阿里云、微信 SDK。
- 模块：`spread-system-api`、`spread-system-common`、`spread-system-dal`、`spread-system-core`、`spread-system-web`、`spread-system-test`。
- 特殊说明：对应 PC/移动推广端（`agent-system-pc`）和微信小程序端（`spread-system-wechat`）。

### user-center（用户中心）
- 路径：`D:\code\user-center`
- 用途：统一管理用户、登录、认证和账号关系；电子签约平台的身份与访问控制中心。
- 核心功能：手机号登录、短信验证、密码、Token、二维码登录、UKey 登录、联系人、微信公众号和企业微信接入。
- 技术栈：Java 8、Spring Boot、Spring Cloud Alibaba、Nacos、OpenFeign、MyBatis、MySQL、微信 SDK。
- 模块：`user-api`、`user-auth`、`user-common`、`user-core`、`user-dal`、`user-web`。
- 特殊说明：同时提供 Web、App、微信和企业微信相关接口。

## 前端项目

### agent-system-pc（代理商系统 PC 端）
- 路径：`D:\code\agent-system-pc`
- 用途：代理商侧 PC 管理界面；代理商日常运营和客户经营前端。
- 核心功能：代理商首页、客户、邀请、产品、SaaS 配置和个人设置。
- 技术栈：Vue 2.6、Vue CLI、Element UI、Vuex、Vue Router、Axios、SCSS。
- 模块：`agent`、`client`、`invite`、`product`、`saas`、`settings`。
- 特殊说明：与 `spread-system` 后端及代理相关业务协同。

### esign-ding-pc（钉钉版签盾 PC 端，历史项目）
- 路径：`D:\code\esign-ding-pc`
- 用途：钉钉生态下的签盾 PC 页面；钉钉集成版电子签约前端。
- 核心功能：登录、合同、签署、审批、模板、企业控制台和主体管理。
- 技术栈：Vue 2.6、Element UI、Vuex、Vue Router、Axios、钉钉 JSAPI。
- 模块：`audit`、`contract`、`control`、`file`、`home`、`invite`、`sign`、`subject`、`template`。
- 特殊说明：历史钉钉 PC 项目。当前钉钉前端独立维护仓库为 `D:\code\esign-saas-pc-ding`；如需继续改动本项目，先确认维护目标。

### esign-saas-pc-ding（签盾 SaaS 钉钉 PC 端，当前维护入口）
- 路径：`D:\code\esign-saas-pc-ding`
- 用途：独立维护从 `esign-saas-pc` 剥离的钉钉版签盾 PC 页面；钉钉生态下的电子签约前端。
- 核心功能：钉钉企业应用登录、企业绑定、通讯录、合同签署与审批、模板、企业控制台和主体管理。
- 技术栈：Vue 2.6、Vue CLI、Element UI、Vuex、Vue Router、Axios、SCSS、钉钉 JSAPI。
- 模块：`audit`、`contract`、`control`、`file`、`home`、`invite`、`noAuth`、`point`、`sign`、`subject`、`syotai`、`template`、`third`。
- 特殊说明：当前钉钉 PC 端的维护入口；`package.json` 仍使用原包名 `esign-saas-pc`，仓库目录与 Git 仓库名为 `esign-saas-pc-ding`。

### esign-saas-pc（签盾 SaaS PC 端）
- 路径：`D:\code\esign-saas-pc`
- 用途：签盾 SaaS 的主要 PC 用户端；企业和个人用户使用电子合同服务的主界面。
- 核心功能：合同管理、发起、签署、审批、模板、企业控制台、认证、积分和用户中心。
- 技术栈：Vue 2.6、Vue CLI、Element UI、Vuex、Vue Router、Axios、SCSS。
- 模块：`audit`、`contract`、`control`、`file`、`home`、`invite`、`point`、`sign`、`subject`、`syotai`、`template`。
- 特殊说明：钉钉端已从原 `feature_dingding` 分支剥离至 `D:\code\esign-saas-pc-ding` 独立维护；本仓库的 `master`（普通 PC 端）与历史 `feature_dingding` 分支**不允许相互合并**。

### independent-signature-wechat（独立签名移动端 Monorepo）
- 路径：`D:\code\independent-signature-wechat`
- 用途：维护签盾移动端、小程序和 H5 多端应用；电子签约移动入口及平台适配层。
- 核心功能：移动端合同操作、签署、账户、业务首页及微信/钉钉端适配。
- 技术栈：Uni-app、Vue 2、Vuex、Webpack/Vue CLI、Lerna、Yarn Workspaces。
- 模块：`apps/app`、`apps/independent`、`apps/sass`、`apps/ding-saas`、`packages/sg-config`、`sg-core`、`sg-services`、`sg-store`、`sg-utils`。
- 特殊说明：同一仓库维护多个产品变体，支持 H5、微信小程序及钉钉小程序构建。

### saas-manage-page（运营管理后台前端）
- 路径：`D:\code\saas-manage-page`
- 用途：为运营人员提供 SaaS 管理后台界面；运营、财务、代理商、用户和系统管理前端。
- 核心功能：代理商、审批、权限、财务、营销、统计、UKey、用户和系统配置管理。
- 技术栈：Vue 2.6、Vue CLI、Element UI、Vuex、ECharts、Quill、CodeMirror、Axios。
- 模块：`agent`、`approval`、`auth`、`cost`、`finance`、`marketing`、`operate`、`statistics`、`system`、`ukey`、`user`。
- 特殊说明：基于 `vue-admin-template` 改造，与 `saas-manage` 后端配套。

### spread-system-wechat（代理推广微信端）
- 路径：`D:\code\spread-system-wechat`
- 用途：代理商和推广客户使用的微信小程序/H5；代理推广、销售和客户服务移动端。
- 核心功能：登录注册、代理商、客户、推广、购买、充值、余额、售卖、客服和员工管理。
- 技术栈：Uni-app、Vue 3、Vite、Pinia、微信/支付宝等多平台适配。
- 模块：`agent`、`client`、`promotion`、`purchase`、`recharge`、`sell`、`staff`、`user`、`customerService`。
- 特殊说明：当前脚本明确支持 H5 和 `mp-weixin` 构建。

## 其他

### tac-opensource-vue-demo（TAC 验证码示例）
- 路径：`D:\code\tac-opensource-vue-demo`
- 用途：演示前端集成 TAC/天爱滑块验证码，验证码组件接入和联调样例；**不是业务系统**。
- 技术栈：Vue CLI，分别使用 Vue 2 和 Vue 3。
- 模块：`vue2-captcha-demo`、`vue3-captcha-demo`。
- 特殊说明：示例页面默认调用本地 `/gen` 和 `/check` 验证接口。

### untitled（Java 示例工程）
- 路径：`D:\code\untitled\untitled`
- 用途：基础 Maven/Java 示例或临时实验工程，不属于当前电子签约业务链路。
- 技术栈：Maven、Java 17。`Main.java` 仅输出欢迎文本和循环示例，无业务框架或模块。
- 特殊说明：外层目录为 `D:\code\untitled`。

## 常见跨项目链路

- 短信发送：`openapi-center`（`mes_send`）→ `api-service` → `message-center`（供应商路由与发送记录）。
- 运营后台：`saas-manage-page` ↔ `saas-manage`。
- 代理推广：`agent-system-pc` / `spread-system-wechat` ↔ `spread-system`。
- 钉钉 PC：改 `esign-saas-pc-ding`，不要改 `esign-ding-pc`（历史）；`esign-saas-pc` 的 master 与 `feature_dingding` 禁止互相合并。
