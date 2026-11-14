---
title: "练习 2 - 使用 Copilot 智能体模式添加功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是最简单的应用更新，通常也需要修改多个文件并执行测试等操作。开发者往往需要找到所有相关文件、完成更改、运行测试、调试、找出遗漏文件并再次修改，流程可能不断重复。

Copilot 智能体模式可以帮助处理这些工作。

Copilot 智能体模式在集成开发环境 (IDE) 中以更自主的方式工作。它会先探索现有项目结构，再执行所需更改、运行测试等任务，并自动修复发现的问题。接下来了解如何使用智能体模式为网站添加新功能。

> [!NOTE]
> 智能体模式和云智能体的名称相似，但适用于不同的工作方式。智能体模式在 IDE 中执行任务，适合快速反馈和交互。云智能体则像团队成员一样异步工作，通过议题和拉取请求与你协作。

在本练习中，你将了解如何：

- 使用 Copilot 智能体模式探索项目、查找相关文件并协调更改。
- 使用 GitHub Copilot 智能体模式跨用户界面和数据层实现功能。
- 在将代码合并到项目之前，审查智能体模式生成的更改和测试。

## 场景

随着游戏列表不断增长，希望用户可以按类别和发行商筛选游戏。上一练习已添加发行商辅助函数；现在将使用 Copilot 智能体模式完成剩余的数据层、用户界面和测试工作。

## 运行 Tailspin Toys 网站

更改之前，先浏览 Tailspin Toys 网站，了解现有功能。

该网站是一个带开发者主题的桌游众筹平台，可列出游戏并显示详情。它是一个 Astro 应用，构建时将页面渲染为静态超文本标记语言 (HTML)。页面通过 Drizzle 对象关系映射 (ORM) 在 frontmatter 中直接查询本地 SQLite 数据库，没有单独的后端应用程序编程接口 (API) 或客户端用户界面 (UI) 框架。可复用的数据访问辅助函数位于 `src/lib/`，交互功能由使用标准文档对象模型 (DOM) API 的小型 Astro `<script>` 实现。

### 启动网站

项目提供了 `npm run dev` 任务，用于启动单个 Astro 开发服务器。按照以下步骤在 GitHub Codespace 中运行：

1. 返回 Codespace。上一练习结束后，当前应位于 `main` 分支。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）在 Codespace 中打开新终端。
3. 为筛选功能创建并切换到新分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，会显示类似以下的 URL：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果弹出提示，询问是否要为 `http://localhost:4321` 打开浏览器窗口，请选择 **x** 关闭。

5. 按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），然后在终端中选择 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 在 Codespace 中选择终端里的 localhost 链接时，会自动跳转到 `https://<your-codespace-name>-4321.app.github.dev/`。这是通往 Codespace 的私有隧道，此时 Web 服务器正在其中运行。

### 浏览网站

网站运行后，可以查看其功能：

- **主页**：显示桌游列表，包括标题、图片和说明。
- **游戏详情页**：选择游戏后会打开详情页，显示标题、说明、发行商和类别等信息。

## 使用 Copilot 浏览待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始全新对话。模式和模型的选择因练习而异；相关步骤会在需要时说明。

网站的初始版本可以正常使用，但还需要添加更多功能。先查看待办事项。使用模板设置项目时，系统已自动创建议题；请让 GitHub Copilot 显示这些议题。

1. 在 Chat 视图的智能体下拉菜单中选择 **Agent**。该智能体可以自主规划并跨文件实现更改、运行终端命令和调用工具。

   ![Chat 视图中的智能体选择器](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本工作坊作者并未偏好某个模型。编写本工作坊时使用了 Claude Sonnet 4.5，因此示例指定了该模型，以便尽量保持体验一致。不过，大型语言模型的输出具有概率性，收到的建议可能与示例不同，这是正常且预期的情况。

> [!NOTE]
> 由于输出具有概率性，Copilot 可能会使用不同的 MCP 命令，但仍应能完成任务。

3. 向 Copilot 发送以下提示，查看待办议题：

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. 选择 **Continue**，允许运行列出所有议题的命令。
5. 查看生成的议题列表。

Copilot 还会根据它认为对用户最有用的程度为待办事项排序。

## 查看指令文件

让智能体生成代码前，先查看用于向 Copilot 提供上下文的指令文件。本练习将使用[用户界面 (UI) 指令文件](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)，其中说明了如何为网站添加功能。

1. 在 Codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 查看添加功能时的整体指南，包括：
   - 架构概述。
   - 组件设计、可测试性和无障碍原则。
   - 指向各类文件专用指令的链接，包括 Astro、Tailwind CSS 和 Drizzle。

> [!TIP]
> 指令文件可以引用其他指令文件和项目文件。路径相对于指令文件所在位置，因此可以复用指南、拆分复杂内容，并为 Copilot 提供示例和模板。

## 使用智能体模式实现筛选功能

完成筛选至少需要对应用进行以下三项更改：

- 在数据层 (`src/lib/`) 中添加或完善筛选逻辑。
- 添加或更新筛选行为测试。
- 更新游戏列表页，添加筛选界面。

合并前还需要运行并通过测试。Copilot 智能体模式可以执行这些任务。

1. 继续当前 Copilot 对话，或选择 **New Chat** 开始新对话。
2. 选择 **Add Context**、**Instructions**，然后选择 **ui** 指令文件。

   ![选择 UI 指令文件的示例](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉菜单仍选中 **Agent**。

   ![Chat 视图中的智能体选择器](../../../_images/shared-chat-mode-selector.png)

4. 确认仍选择 **Claude Sonnet 4.5** 模型。
5. 使用以下提示，请 Copilot 根据待办议题实现功能：

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. 观察 Copilot 如何探索项目并查找与功能相关的文件，包括数据层辅助函数、用户界面和测试，然后修改文件并运行测试。

   ![Copilot 探索项目文件](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会执行多个任务，例如探索项目、修改文件和运行测试。根据任务和代码库的复杂程度，这可能需要几分钟。处理过程中，编辑器中可能会出现 **Keep** 和 **Undo** 按钮。Copilot 完成后，每项更改都会显示 **Keep** 或 **Undo**，无需在工作进行时提前选择。

7. Copilot 提示时，选择 **Continue** 运行测试。

   ![Copilot Chat 面板提示确认运行测试](../../../_images/ex3-agent-mode-run-tests.png)

8. 处理中可能会暂停，甚至出现测试失败。这是正常的。Copilot 会在生成代码和运行测试之间迭代，直到完成任务且未检测到错误。

   ![Copilot 智能体模式的完整 Chat 会话](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 检查生成的代码，查看是否存在问题。

> [!CAUTION]
> 务必审查 Copilot 或其他 AI 工具生成的代码。

10. 返回正在运行网站的浏览器，试用新功能。
11. 确认功能正常并审查代码后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结和后续步骤

你了解了如何使用 GitHub Copilot 智能体模式为 Tailspin Toys 网站添加功能。本练习介绍了如何：

- 使用 GitHub Copilot 智能体模式跨用户界面和数据层实现新功能。
- 使用 Copilot 智能体模式探索项目、查找相关文件并协调更改。
- 在将代码合并到项目之前，审查智能体模式生成的更改和测试。

接下来使用 [Playwright MCP 服务器测试功能][next-lesson]，并为筛选工作创建拉取请求。

### 附加探索练习 — 实现分页

随着游戏列表增长，网站将需要分页功能。运用本练习中学到的知识，请 Copilot 为网站添加分页。编写提示和实现时，请考虑：

- 遵循现有最佳实践，包括使用已有指令文件。
- 确定分页方式，例如是否允许用户选择每页大小，还是使用固定大小。
- 在提示中提供必要的指南，确保 Copilot 按照预期实现功能。
- 可能需要通过提供更多上下文和修改请求与 GitHub Copilot 迭代。这是正常的协作流程。

## 资源

- [Copilot 的 ask、edit 和 agent 模式：用途和适用时机][choose-mode]
- [VS Code 中的智能体模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
