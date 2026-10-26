# 技能

本项目用于编写和整理 Codex 技能。Python 仅用于将来可能增加的辅助脚本，目前没有第三方依赖。

## 目录结构

- `skills/work/`：公司项目使用的技能。
- `skills/personal/`：个人项目使用的技能，目前为空。
- `templates/skill-template/SKILL.md`：新技能的编写模板。
- `scripts/`：预留给将来的辅助脚本，目前为空。
- `AGENTS.md`：技能分类和编写规范。

## 新增技能

1. 复制 `templates/skill-template/`。
2. 将复制出的文件夹改为小写短横线命名，并把 `SKILL.md` 的 `name` 改为相同名称。
3. 填写 `description` 和正文各节，清除 `TODO` 占位文字。
4. 将技能文件夹放入对应类别目录：公司项目放在 `skills/work/`，个人项目放在 `skills/personal/`。
