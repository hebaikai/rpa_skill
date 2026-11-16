# WorkBuddy × Ui.Vision 自动化使用手册

给 WorkBuddy 桌面端增加一项技能：用中文描述要做的事，让 Ui.Vision 在你指定的浏览器里执行。可以只执行一次，也可以把流程保存成宏，下次按名字运行。

## 一次安装

1. **安装浏览器扩展。** 从 [Ui.Vision 官网](https://ui.vision/rpa) 进入扩展商店，安装最新版 **Ui.Vision RPA**。推荐 Chrome 或 Edge。没有 [Node.js](https://nodejs.org/en/download) 的电脑先安装 LTS 版（桥接要求 Node.js 18 或更高），然后完全退出并重开 WorkBuddy。
2. **导入技能。** 下载 [uivision-rpa-workbuddy.zip](./uivision-rpa-workbuddy.zip)，保持 ZIP 格式。打开 WorkBuddy 左侧 **专家·技能·连接器 → 技能 → 添加技能 → 上传技能**，选择下载的 ZIP。如果上传窗口支持 `.md`，也可以直接导入 [SKILL.md](./uivision-rpa/SKILL.md) 原文件。在「已安装」确认 **Ui.Vision 网页与桌面自动化** 已启用。[WorkBuddy 技能导入说明](https://www.codebuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Skills-Market)
3. **添加 MCP。** 进入 **专家·技能·连接器 → 连接器 → 自定义连接器 → 配置 MCP**。复制 [MCP 配置示例](./mcp.example.json) 到配置编辑器；如果已配置其他连接器，只把 `uivision` 条目加到现有的 `mcpServers` 里。保存后按提示信任、启用 `uivision`，并重开 WorkBuddy。这是本地 `stdio` 连接，WorkBuddy 会启动官方桥接程序，无需另外执行 Ui.Vision 的 `--setup` 命令。[WorkBuddy MCP 配置](https://www.codebuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/MCP-Guide) · [Ui.Vision 官方桥接说明](https://ui.vision/mcp)
4. **打开浏览器侧桥接。** 在目标浏览器打开 Ui.Vision 侧边栏，进入 **Settings → AI → MCP bridge (Claude Code)**，打开开关并点 **Test**。即使用 WorkBuddy，开关也可能仍显示 Claude Code。通常保留默认端口 `50888`，Token 留空。运行时保持侧栏打开。

这套接入需要在同一台电脑上运行 WorkBuddy 桌面端和浏览器。网页操作及点击页面自带的下载按钮只需浏览器扩展；要操作桌面软件、系统弹窗，或让宏直接访问本地文件，再装 [Ui.Vision Desktop App / XModules v2](https://ui.vision/rpa/x)。

## 第一次使用

在目标浏览器打开一个普通测试网页，例如 [example.com](https://example.com/)，然后在 WorkBuddy 新建任务并发送：

> 用 Ui.Vision 测试连接，运行一条只输出 UIVISION_SKILL_OK 的临时宏，不保存宏。

WorkBuddy 返回实际执行日志中的 `UIVISION_SKILL_OK`，才表示技能、MCP、浏览器和宏执行都通了。扩展里点 **Test** 变绿只说明浏览器侧连接成功。若没有自动选中技能或连接器，可在任务输入框中手动添加本技能和 `uivision` 连接器。

测试通过后，直接描述任务：

- “下载当前后台的昨日报表，并保存宏‘下载昨日报表’。”
- “读一下当前网页表格的商品名和库存，先给我看前十条。”
- “把这三条资料填到当前表单，先别提交。”
- “运行宏‘下载昨日报表’，告诉我下载的文件名和报表日期。”

登录、验证码和二次验证请在浏览器里完成。要中止执行，可在 Ui.Vision 侧栏按 **STOP**；再次使用前重新打开桥接。

## 遇到问题

| 看到的现象 | 怎么处理 |
| --- | --- |
| WorkBuddy 找不到技能 | 到「技能 → 已安装」确认已启用。在新任务里通过输入框的技能选择器添加。导入时请选择本仓库提供的技能 ZIP，而不是整个 GitHub 仓库的下载包。 |
| 自定义连接器没有 `uivision`，或启动失败 | 检查 JSON 合并位置和信任状态。打开终端确认 `node --version` 可用；Windows 用 `npx.cmd --version`，macOS 用 `npx --version`。新装 Node.js 后重开 WorkBuddy。若终端里的 `npx.cmd` 可用、连接器却报无法启动 `npx`，可在 Windows 上试用 `"command": "cmd.exe", "args": ["/d", "/c", "npx.cmd", "-y", "uivision-mcp-bridge"]`，再重开客户端。 |
| MCP 已载入，扩展 Test 未连接 | 确认目标浏览器已打开侧栏、桥接开关已启用，默认端口未被改动。若扩展要求配对，只在本机扩展里填写配对令牌，别发到对话或仓库里。 |
| 多个浏览器都开着 | 先告诉 WorkBuddy 用哪个浏览器和业务账号。可在 Ui.Vision 侧栏看到当前浏览器连接标签。 |
| 网页能跑，但桌面软件或本地文件操作失败 | 确认已装 XModules v2，并查看扩展的 Desktop Automation 自检结果。 |

[Skill 源文件](./uivision-rpa/SKILL.md) · [MCP 配置示例](./mcp.example.json) · [上游 A9T9/RPA](https://github.com/A9T9/RPA)
