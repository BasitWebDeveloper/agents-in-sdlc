---
title: "第 6 课 - 使用 Playwright MCP 验证功能"
description: "在 Copilot CLI 中检查或配置 Playwright MCP，并在浏览器中检查筛选功能。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

编写代码不只是编写代码。还需要处理数据和外部服务，并允许 Copilot 使用其他自动化工具。模型上下文协议 (MCP) 服务器可以扩展 Copilot CLI 内置功能，为它提供更多工具和服务。

在本课中，你将：

- 了解模型上下文协议 (MCP) 以及 Copilot CLI 如何使用它。
- 在尚未配置时添加 Playwright MCP 服务器。
- 请智能体驱动浏览器，检查筛选功能。

## 场景

单元测试和端到端测试很重要，但验证用户界面更新还需要实际与界面交互。让 Copilot 像用户一样操作网站，可以进一步自动化更改检查，并提高对功能行为的信心。

## 什么是模型上下文协议 (MCP)？

[模型上下文协议 (MCP)][mcp-blog-post] 为 AI 智能体提供与外部工具和服务通信的方式。借助 MCP，智能体可以实时与外部工具和服务通信，以访问最新信息并代表你执行操作。

智能体通过 MCP 服务器访问工具和资源。MCP 服务器充当 AI 智能体与外部工具和服务之间的桥梁。每个 MCP 服务器都提供一组可供智能体访问的工具和资源。

常见的 MCP 服务器包括：

- **[GitHub MCP 服务器][github-mcp]**：提供管理 GitHub 存储库、议题和拉取请求的 API。
- **[Playwright MCP 服务器][playwright-mcp-server]**：使用 Playwright 提供浏览器自动化能力。

还有许多其他 MCP 服务器。GitHub 托管了 [MCP 注册表][mcp-registry]，帮助发现并为生态系统做出贡献。

> [!CAUTION]
> 请像对待项目中的其他依赖项一样谨慎对待 MCP 服务器。使用前应审查源代码、验证发布者并评估安全影响。

## 添加 Playwright MCP 服务器

如果尚未使用 Playwright MCP 服务器，请添加它，让 Copilot 可以像用户一样操作网站。

> [!NOTE]
> 如果 Copilot CLI 中已经配置了 Playwright MCP，请跳过这些设置步骤，直接继续下一部分。

1. 返回 Codespace。
2. 在 Copilot CLI 中输入以下命令，打开添加 MCP 服务器对话框：

   ```plaintext
   /mcp add
   ```

3. 名称输入 `playwright`，然后按 <kbd>Tab</kbd>。
4. 确认服务器类型为 **STDIO**，按 <kbd>Enter</kbd>，然后按 <kbd>Tab</kbd>。
5. 在 **Command** 对话框中输入：

   ```plaintext
   npx -y @playwright/mcp@latest --headless --no-sandbox
   ```

6. 按 <kbd>Control</kbd>+<kbd>S</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>S</kbd>（Windows/Linux）保存新的 MCP 服务器。
7. 按 <kbd>Esc</kbd> 退出 MCP 对话框。

## 使用 Playwright 请 Copilot 检查功能

此前已手动确认功能行为正常。现在使用 Copilot 中可用的 Playwright MCP 服务器再次检查。

1. 使用以下提示，请 Copilot 通过 Playwright MCP 服务器验证功能：

   ```plaintext
   Start the app and use Playwright MCP to check filtering against the issue and our plan. Tell me what works and what doesn't, without making changes. Stop the server you started when you're done.
   ```

> [!NOTE]
> 通常 Copilot 会自动选择 MCP 服务器，无需明确指定。不过，说明应使用哪个工具有助于保持结果一致，也能减少一些提示内容。

2. 观察 Copilot 在浏览器中执行的各项操作。
3. 阅读报告，确认功能行为符合预期。

Copilot 会启动服务器、使用 Playwright 操作网站、停止服务器，并报告结果。

## 总结和后续步骤

你通过 Copilot CLI 使用 Playwright MCP 服务器，在真实浏览器中检查了功能。本课中，你：

- 了解了模型上下文协议 (MCP) 以及 Copilot CLI 如何使用它。
- 在尚未配置时添加了 Playwright MCP 服务器。
- 请智能体驱动浏览器，检查筛选功能。

接下来[创建 QA 自定义智能体][next-lesson]，将技能和浏览器工具整合到专业角色中。

## 资源

- [MCP 是什么，为什么大家都在讨论？][mcp-blog-post]
- [Microsoft Playwright MCP 服务器][playwright-mcp-server]
- [为 Copilot CLI 添加 MCP 服务器][add-mcp]

[previous-lesson]: ../5-agent-skills/
[next-lesson]: ../7-qa-agent/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp
[github-mcp]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[add-mcp]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers
