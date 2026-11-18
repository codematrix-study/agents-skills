# TAPD Codex 插件

在 Codex 中查询 TAPD 需求、缺陷和任务，按单开发或修复。`tapd-workflow` 会在提交评论、更新状态或新建单据前展示拟写内容并等待确认。

## 准备令牌

1. 登录 TAPD，打开「我的设置 → 个人访问令牌」，创建个人访问令牌。令牌只显示一次，保存在本机安全位置。测试账号需能读取目标工作空间和需求。 [TAPD 个人访问令牌设置](https://www.tapd.cn/personal_settings/index?tab=personal_token)
2. 安装 uv，确认启动 Codex 的环境可以找到 `uvx`。本插件通过 `uvx mcp-server-tapd` 启动本地 MCP，已核对的 PyPI 版本为 `8.0.83`，要求 Python 3.13 或更高版本。 [uv 安装指南](https://docs.astral.sh/uv/getting-started/installation/) · [TAPD MCP README](https://pypi.org/project/mcp-server-tapd/)
3. 为启动 Codex 的进程设置 `TAPD_ACCESS_TOKEN`。在 Windows PowerShell 中可用下面的命令遮住输入。请在提示时输入自己的令牌（此处以 `<TAPD_ACCESS_TOKEN>` 指代），然后从同一窗口启动 Codex CLI。桌面版从开始菜单启动时，可在 Windows 用户环境变量中设置同名变量，再完全退出并重新打开 Codex。

   ```powershell
   $env:TAPD_ACCESS_TOKEN = [System.Net.NetworkCredential]::new('', (Read-Host 'TAPD 个人访问令牌' -AsSecureString)).Password
   ```

   `.mcp.json` 使用 `env_vars` 传递令牌，两个 URL 分别为 `https://api.tapd.cn` 和 `https://www.tapd.cn`。请把令牌留在本机环境变量中。

## 安装和检查

在 `agents-skills` 仓库根目录运行。

```powershell
codex plugin marketplace add .
codex plugin add 'tapd-plugin@agents-skills'
```

添加 marketplace 后，也可以从 Codex 桌面版的 **Agents Skills** 插件来源选择 **TAPD** 并安装。插件用官方 Plugin Creator 支持的 Codex 兼容 manifest。 [OpenAI 插件打包指南](https://developers.openai.com/plugins/build/plugins)

从设置令牌的 PowerShell 窗口运行 `codex --no-daemon`，或重启桌面版后打开新会话。CLI 中用 `/plugins` 查看 **TAPD**，用 `/mcp` 确认 `tapd` 可用，并检查工具列表中是否有 `get_stories_or_tasks`、`get_bug`。如需查看启动信息，在 CLI 中运行 `/mcp verbose`。 [Codex 开发者命令](https://learn.chatgpt.com/docs/developer-commands?surface=cli)


也可以直接在cc switch中的MCP进行添加,直接"粘贴配置"
```json
{
  "mcpServers": {
    "tapd": {
      "command": "uvx",
      "args": ["mcp-server-tapd"],
      "env": {
        "TAPD_ACCESS_TOKEN": "你的个人令牌",
        "TAPD_API_BASE_URL": "https://api.tapd.cn",
        "TAPD_BASE_URL": "https://www.tapd.cn"
      }
    }
  }
}
```

## 使用示例

- `读取 TAPD 需求 <测试需求链接>，告诉我标题、当前状态和验收点。` 用这一条做只读联调，核对返回的单号、标题和状态与 TAPD 页面一致。
- `修复 TAPD 缺陷 <缺陷链接>，开发完成后给我看拟回写的评论和目标状态。`
- `根据 TAPD 需求 <需求链接> 拆三个任务，先展示标题、内容和关联需求，等我确认再建单。`

跨空间使用时优先提供完整链接。只有短号时，补充空间 ID 和单据类型。
