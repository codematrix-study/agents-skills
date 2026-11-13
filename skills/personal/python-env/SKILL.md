---
name: python-env
description: Python 项目的虚拟环境与依赖管理规范。当任务涉及运行 Python 项目、安装依赖、执行 Python 测试或工具、使用 pip/poetry/uv/pipenv、查找或创建 venv/.venv 时使用。
---

# Python 项目规范

## 1. 虚拟环境

项目目录中存在 `venv`、`.venv`、`env` 等虚拟环境时：

- 优先使用项目虚拟环境的解释器（Windows 下如 `'.venv\Scripts\python.exe'`），禁止优先使用系统 Python。
- 命令使用 PowerShell 语法，路径和参数用单引号包裹。

## 2. 不存在虚拟环境

如果没有检测到虚拟环境，而任务需要安装依赖、运行项目、执行测试或 Python 工具：

- **不要直接创建**虚拟环境。
- 先询问用户：`当前项目没有检测到 Python 虚拟环境，是否创建项目虚拟环境并安装依赖？`
- 用户确认后再创建；创建位置与名称遵循项目习惯（默认 `.venv`）。

## 3. 依赖管理

未经用户明确要求：

- 不修改 `requirements.txt`
- 不升级依赖
- 不替换依赖
- 不全局安装依赖

优先使用项目已有的依赖管理方式：`requirements.txt`、`pyproject.toml`、`poetry`、`uv`、`pipenv`。根据项目中已有的锁文件和配置判断，不要擅自换工具。

## 4. 网络与镜像

依赖下载失败时，先判断是网络、镜像、DNS、代理、认证还是版本不存在；不要擅自修改 pip 镜像、全局代理或全局环境。
