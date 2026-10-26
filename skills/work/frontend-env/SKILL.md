---
name: frontend-env
description: 前端项目（Vue 2/3、React、TypeScript、Vite、Nuxt、Uni-app、Element UI/Element Plus、Vuex/Pinia）的环境与工具链规范。当任务涉及前端项目的 Node 版本、npm/pnpm/yarn/bun 选择、安装依赖、构建、ESLint/Prettier 配置，或在前端项目中新增/修改页面和组件时使用。
---

# 前端项目规范

## 1. 总原则

优先遵循项目现有规范（目录结构、组件写法、状态管理方式、请求封装、样式方案），不引入新写法。
本平台前端以 Vue 2.6 + Element UI 为主，部分移动端为 Uni-app（Vue 2 / Vue 3），具体见 skill `project-map`。

## 2. 未经用户明确要求，不主动

- 升级 Node
- 升级 npm
- 升级依赖
- 更换包管理器
- 修改构建工具
- 修改 ESLint 配置
- 修改 Prettier 配置

## 3. Node 环境

优先检查以下位置是否要求了 Node 版本：

```text
package.json（engines）
.nvmrc
volta 配置
```

项目明确要求 Node 版本时，优先使用该版本；本机版本不符时提醒用户，不要自行升级或全局切换。

## 4. 包管理器

根据锁文件判断，不要无理由更换：

```text
package-lock.json   -> npm
pnpm-lock.yaml      -> pnpm
yarn.lock           -> yarn
bun.lockb / bun.lock -> bun
```

多个锁文件并存或没有锁文件时，先询问用户，不要自行决定。
Monorepo（如 `independent-signature-wechat` 使用 Lerna + Yarn Workspaces）遵循仓库既有的 workspace 工具。

## 5. 验证

前端构建（`npm run build` 等）属于完整验证，仅在用户明确要求后执行；默认做静态检查：语法、import 路径、组件注册、接口调用与后端字段是否对应、Vuex/Pinia 状态流是否闭环。
没有实际运行的，汇报中如实写明“未执行构建验证”。

## 6. 依赖下载

依赖安装失败时先判断是网络、registry、代理、认证还是版本不存在；不擅自修改 npm registry、全局代理或全局环境。
