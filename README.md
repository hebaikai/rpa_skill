# Ui.Vision 自动化 Skill

在本机用中文交代任务，让 Ui.Vision 操作指定的网页或桌面。可以运行一次，也可以把流程保存成宏供下次调用。Claude、Codex、Cursor、WorkBuddy 和 DeepSeek Harness 共用 [同一份 Skill 正文](./uivision-rpa/SKILL.md)。

| 客户端 | 安装入口 |
| --- | --- |
| Claude | [Claude Code](#claude-code) · [Claude Desktop](#claude-desktop) |
| Codex | [Codex 桌面端、CLI 和 IDE 扩展](#codex) |
| Cursor | [Cursor 本机 Agent](#cursor) |
| WorkBuddy | [WorkBuddy 桌面端](#workbuddy) |
| DeepSeek | [DeepSeek Harness 桌面端](#deepseek-harness) · [在其他客户端使用 DeepSeek 模型](#deepseek-模型接入) |

## 通用准备

1. 在要操作的电脑上安装最新版 **Ui.Vision RPA** 浏览器扩展和 **Node.js LTS**（桥接要求 Node.js 18+）。推荐 Chrome 或 Edge。安装 Node.js 后重开客户端。[Ui.Vision 扩展](https://ui.vision/rpa) · [Node.js 下载](https://nodejs.org/en/download)
2. 按下方对应说明安装 `uivision-rpa` 技能并添加同名本地 MCP。新建配置时可以复制完整示例。已有配置时仅合并其中的 `uivision` 条目。[通用 Skill ZIP](./uivision-rpa.zip) · [MCP JSON 示例](./mcp.example.json)
3. 启用 MCP 后，在同一台电脑的目标浏览器打开 Ui.Vision 侧栏，进入 **Settings → AI → MCP bridge (Claude Code)**，开启后点 **Test**。开关里的 Claude Code 字样也可出现在其他客户端的接入场景。保留默认端口 `50888`，通常可留空 Token。运行时保持侧栏打开。[Ui.Vision MCP 官方说明](https://ui.vision/mcp)

在本地会话中使用这套接入。普通网页任务和浏览器自身的下载使用扩展即可。操作桌面软件、系统弹窗，或让宏直接访问本地文件时，再装 XModules v2。[XModules 下载](https://ui.vision/rpa/x)

## Claude Code

1. 解压通用 Skill ZIP，把整个 `uivision-rpa/` 文件夹放到 `~/.claude/skills/`。仅在一个项目使用时，放到项目的 `.claude/skills/`。[Claude Code 技能文档](https://code.claude.com/docs/en/skills)
2. 在终端添加 MCP，然后从本机项目打开 Claude Code。用 `/mcp` 查看 `uivision`，需要手动选技能时可输入 `/uivision-rpa`。[Claude Code MCP 文档](https://code.claude.com/docs/en/mcp)

   ```bash
   claude mcp add --transport stdio --scope user uivision -- npx -y uivision-mcp-bridge
   ```

   Windows 如果提示无法启动 `npx`，把命令中 `--` 后面的部分换成 `cmd.exe /d /c npx.cmd -y uivision-mcp-bridge`。已注册同名服务时，先在 MCP 设置中核对现有条目。

## Claude Desktop

1. 在 Claude 的 **Customize → Skills** 上传通用 Skill ZIP 并启用。新版界面入口可能显示为 **Plugins**。按界面要求开启文件创建能力。[自定义 Skill 上传说明](https://support.claude.com/en/articles/12512180-use-skills-in-claude) · [新版 Plugins 入口](https://support.claude.com/en/articles/17740565-manage-plugins-skills-and-connectors-from-one-plugins-page)
2. 在 **桌面应用设置 → Developer → Edit Config** 打开本机 MCP 配置，在 `mcpServers` 内加入 `"uivision": {"command":"npx","args":["-y","uivision-mcp-bridge"]}`。保存并完全退出重开。在能使用本地连接器的桌面对话中启用 `uivision`。[Claude Desktop 本地配置说明](https://modelcontextprotocol.io/docs/develop/connect-local-servers) · [Ui.Vision 支持的客户端](https://ui.vision/mcp)

## Codex

1. 解压后把整个 `uivision-rpa/` 放到 `~/.agents/skills/`，或者放到项目的 `.agents/skills/`。需要手动指定时在技能选择器选择 `uivision-rpa`，CLI 中也可用 `$uivision-rpa`。[Codex 技能文档](https://learn.chatgpt.com/docs/build-skills)
2. 在 **设置 → MCP servers → Add server** 选 **STDIO**。名称填 `uivision`，命令填 `npx`，参数依次填 `-y` 和 `uivision-mcp-bridge`。保存并重启。CLI 可执行下面这条命令。[Codex MCP 文档](https://learn.chatgpt.com/docs/extend/mcp?surface=desktop)

   ```bash
   codex mcp add uivision -- npx -y uivision-mcp-bridge
   ```

   首次下载桥接超时或长宏需要更多时间时，可在已有的 `[mcp_servers.uivision]` 段落分别设置 `startup_timeout_sec = 120`、`tool_timeout_sec = 900`。[Ui.Vision 的 Codex 超时示例](https://ui.vision/mcp)

## Cursor

1. 把解压出的 `uivision-rpa/` 放到 `~/.cursor/skills/` 或项目内的 `.cursor/skills/`。如果已给 Codex 安装在 `~/.agents/skills/`，Cursor 本机也会发现同一份技能。[Cursor Agent Skills 文档](https://cursor.com/docs/skills)
2. 把 MCP JSON 示例的 `uivision` 条目添加到 `~/.cursor/mcp.json`，或项目的 `.cursor/mcp.json`。在 Cursor 设置中确认该 MCP 已启用，使用本机 Agent 发起任务。[Cursor MCP 文档](https://cursor.com/docs/mcp)

## WorkBuddy

1. 下载 WorkBuddy ZIP，在 **专家·技能·连接器 → 技能 → 添加技能 → 上传技能** 选择这个 ZIP。若上传窗口支持 `.md`，也可上传 Skill 原文。在「已安装」确认已启用。[WorkBuddy 导入包](./uivision-rpa-workbuddy.zip) · [WorkBuddy 技能文档](https://www.codebuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Skills-Market)
2. 从 **专家·技能·连接器 → 连接器 → 自定义连接器 → 配置 MCP** 合并仓库内 MCP JSON 示例。保存后按提示信任、启用 `uivision`，再重开 WorkBuddy。必要时在任务输入框手动添加技能和连接器。[WorkBuddy MCP 文档](https://www.codebuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/MCP-Guide)

## DeepSeek Harness

1. 安装并打开 **DeepSeek Harness** 桌面端。在要作为本机工作区的项目根目录创建 `.agents/skills/`，把解压后的 `uivision-rpa/` 整个放进去。随后在 Harness 中打开这个工作区。[DeepSeek Harness 下载](https://www.deepseek.com/harness/) · [官方 Skill 目录规则](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/subsystems/skills.md)
2. 下载并解压本仓库源文件。在 Harness 的 **插件 → 添加插件** 中，选择仓库内 `deepseek-harness/uivision-mcp/` 这个本地目录。目录中的配置会把官方 `uivision-mcp-bridge` 注册为本地 MCP。启用后新开会话，选择或点名 `uivision-rpa`，再运行下方连通测试。[本地插件目录](./deepseek-harness/uivision-mcp/) · [Harness MCP 配置文档](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/subsystems/mcp.md)
3. Windows 如报 `npx` 启动失败，可在该目录的 `cordis.patch.yml` 把 `command` 改为 `cmd.exe`，`args` 改为 `['/d', '/c', 'npx.cmd', '-y', 'uivision-mcp-bridge']`，再重新加载这份配置。Harness 仍在预览阶段，入口随版本可能变化。[Harness 本地插件安装说明](https://github.com/deepseek-ai/deepseek-harness/blob/master/packages/preset/agent-preset/skills/dsh-doc/references/plugin-development.md)

## DeepSeek 模型接入

想用 DeepSeek 模型，也可以先在 Claude Code、Codex 或 WorkBuddy 装好技能和 MCP，再按 DeepSeek 官方指南配置模型。客户端负责加载 Skill 和调用本机 MCP，DeepSeek API 负责模型推理。普通 DeepSeek 网页和手机聊天请选择上面的 Harness 桌面端或这条模型接入路径来操作本机浏览器。[Claude Code 接入 DeepSeek](https://api-docs.deepseek.com/quick_start/agent_integrations/claude_code/) · [Codex 接入 DeepSeek](https://api-docs.deepseek.com/quick_start/agent_integrations/codex/) · [WorkBuddy 接入 DeepSeek](https://api-docs.deepseek.com/quick_start/agent_integrations/workbuddy/)

## 第一次使用

在目标浏览器打开一个测试页，例如 [example.com](https://example.com/)，在所选客户端的新会话里发送下面这句话。

> 用 Ui.Vision 测试连接，运行一条只输出 UIVISION_SKILL_OK 的临时宏，不保存宏。

客户端应返回实际执行日志中的 `UIVISION_SKILL_OK`。扩展侧栏里的 **Test** 变绿只确认浏览器与桥接程序已连接。连通后可直接说要做什么。

- “下载当前后台的昨日报表，并保存宏‘下载昨日报表’。”
- “读一下当前网页表格的商品名和库存，先给我看前十条。”
- “把这三条资料填到当前表单，先别提交。”
- “运行宏‘下载昨日报表’，告诉我下载的文件名和报表日期。”

登录、验证码和二次验证请在浏览器里完成。要中止当前宏，可在 Ui.Vision 侧栏按 **STOP**，再次使用前重新启用桥接。

## 排查连接

开始任务时，Skill 会检查 `127.0.0.1:50888`（使用自定义端口时按实际配置检查）。如果端口未监听，先尝试让当前客户端启动或重新加载已注册的 `uivision`，然后复查端口、扩展状态和 `UIVISION_SKILL_OK` 日志。需要你配合时，助手会指出具体卡住的步骤。Windows 可以在浏览器所在电脑的 PowerShell 手动检查。

```powershell
Test-NetConnection 127.0.0.1 -Port 50888 -InformationLevel Quiet
```

在本机运行时，`True` 表示 TCP 端口可达。用 `bridge_status` 检查 Ui.Vision 和目标浏览器，再用测试宏确认执行正常。[Ui.Vision 官方连接指南](https://ui.vision/mcp)

| 看到的现象 | 怎么处理 |
| --- | --- |
| 找不到技能 | 核对客户端对应目录下是 `uivision-rpa/SKILL.md`，或确认 ZIP 已在客户端启用。打开新会话并点名技能。 |
| MCP 已注册，但端口未监听 | 在客户端启用或重新加载 `uivision`。Codex 从 **设置 → MCP servers** 重载，界面没有此入口时，完全退出并重开 Codex。`--setup` 只负责注册，客户端启动桥接后才会监听。 |
| MCP 启动失败 | 确认终端中的 `node --version` 和 `npx --version` 可用。Windows 可用 `npx.cmd --version` 检查。如果终端可执行但客户端报找不到 `npx`，试用 `cmd.exe` 和参数 `["/d", "/c", "npx.cmd", "-y", "uivision-mcp-bridge"]`。 |
| 端口可达，但扩展 Test 未连接 | 检查 `bridge_status`，再核对目标浏览器的侧栏开关和端口。若扩展明确要求配对，在本机扩展里输入令牌。 |
| 多个浏览器已连接 | 告诉 Agent 使用哪个浏览器和业务账号，并对照 Ui.Vision 侧栏里的连接标签。 |
| 网页能跑，桌面或本地文件任务失败 | 确认已装 XModules v2，并查看扩展的 Desktop Automation 自检。 |

上游项目为 [A9T9/RPA](https://github.com/A9T9/RPA)。
