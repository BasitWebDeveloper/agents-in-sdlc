---
title: "练习 5：迭代改进 GitHub Copilot 的工作"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

## 审查工作

本实验中，你让 GitHub Copilot 执行了多项任务，以改善用户体验并添加功能：为代码添加文档、实现供设计团队迭代的相关游戏功能，以及实现高对比度和浅色模式等无障碍功能。现在检查代码更改，并在需要时向 Copilot 提供反馈，改进结果。

### 场景

引入生成式人工智能 (AI) 并不会改变软件设计和开发运维 (DevOps) 的基本原则。仍需审查生成的代码，并遵循常规 DevOps 流程。接下来审查 Copilot 为代码文档、相关游戏功能和无障碍功能创建的更改，然后再为团队启用代码审查。

## GitHub Copilot 云智能体安全性

Copilot 云智能体异步且无人监督地执行任务，因此受到一些安全限制：

- Copilot 仅对存储库具有读取权限，并且仅对其工作分支具有写入权限。
- 云智能体在 GitHub Actions 中运行，并在独立的临时环境中工作。
- GitHub Actions 工作流必须经过人工批准才能运行。
- 默认情况下，[对外部资源的访问受到限制](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，包括模型上下文协议 (MCP) 服务器。

## 审查生成的文档

先查看 GitHub Copilot 云智能体创建的第一个拉取请求 (PR)，其中包含代码文档。此处使用 GitHub.com 上的标准 PR 界面。

> [!NOTE]
> 查看 PR 时，可能会看到 GitHub Copilot 被防火墙阻止的警告。这是预期行为，因为 Copilot 默认只能有限访问外部资源，包括外部 MCP 服务器调用。你也可以[自定义或禁用 Copilot 云智能体防火墙][agent-firewall]。

1. 返回 github.com 上的存储库。
2. 选择 **Pull Requests**，打开 PR 列表。
3. 打开标题类似 **Add missing documentation** 的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，PR 标题中会包含 **[WIP]**。此时请等待 Copilot 完成工作，可能需要几分钟。

4. PR 准备就绪后，选择 **Files changed** 选项卡并审查更改。

   ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

5. 查看新增的 TypeScript 文档注释 (TSDoc) 和其他文档。实际更改可能会有所不同。检查是否包含 TSDoc 文档注释、TypeScript 约定和文件注释头；这些要求来自练习 2 中查看的自定义指令文件。
6. 审查完成后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有等待批准的工作流。
8. 如果有工作流等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 工作流应在 PR 的检查区域中排队运行。对于单体 Astro 应用，项目检查应全部通过。此过程可能需要几分钟。

## 请求 GitHub Copilot 修改

与 Copilot 协作并非单向过程。你可以像在 PR 中提及其他团队成员一样，通过普通评论或代码行内评论提及 Copilot。Copilot 会读取评论，并启动新会话来处理请求。由于结果具有非确定性，无法提供唯一固定的提示。可以要求 Copilot：

- 在每个代码文件开头添加简短的文件注释头。
- 为 TypeScript 和 Astro 文件添加 TSDoc 文档注释。
- 创建说明 Astro 应用结构的 README。

1. 在评论中使用 **@copilot** 提出修改请求。可以使用上述建议，也可以提出其他文档改进要求。
2. 选择 **View Session**，查看 Copilot 执行修改的过程。Copilot 会启动新会话。
3. 选择 **Back to pull request** 返回 PR。

   ![Back to pull request](../../../_images/ex7-back-to-pr.png)

4. Copilot 完成更改后，PR 中会出现新的提交。
5. 选择 **Files changed** 选项卡并审查更改。

可以继续迭代，直到满意为止。完成后，可以将 PR 从草稿转换为准备审查状态，然后合并到 `main` 分支。

![将 PR 转为准备审查状态](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

接下来查看 Copilot 为解决“在游戏详情页显示相关游戏”议题而创建的 PR。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似 **Show related games on the game details page** 的 PR。
4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 确认更改符合要求后，返回 **Conversation** 选项卡并向下滚动。
6. 查看是否有等待批准的工作流。
7. 如果有工作流等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. 工作流应在 PR 的检查区域中排队运行。对于单体 Astro 应用，项目检查应全部通过。此过程可能需要几分钟。
9. **可选：** 可以在 Codespace 中切换到此分支，手动测试相关游戏功能。打开 Codespace 终端，并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称，例如 `copilot/fix-8`）：

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

Copilot 已实现相关游戏功能。与之前一样，可以通过评论让云智能体继续迭代，例如调整显示的相关游戏数量，或要求添加文件注释头和 TSDoc 文档注释（注意：分配该任务时尚未更新自定义指令）。在 **Conversation** 选项卡中添加评论，Copilot 会读取并启动新会话。

## 审查无障碍功能

最后，审查使用无障碍自定义智能体实现的功能。此 PR 应同时包含练习 3 中要求的高对比度模式，以及在练习 4 的控制面板中要求的浅色模式。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似 **Add high contrast mode to website** 的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，PR 标题中会包含 **[WIP]**。请等待 Copilot 完成，可能需要几分钟。

4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查实现时，特别留意：
   - 用于切换模式的用户界面组件。
   - 用于保存用户偏好的本地存储。
   - 高对比度和浅色模式的层叠样式表 (CSS) 或样式更改。
   - 无障碍属性（无障碍富互联网应用 (ARIA) 标签、键盘导航等）。
   - 管理模式切换的 JavaScript/TypeScript 代码。
6. 确认更改后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有等待批准的工作流。
8. 如果有工作流等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 工作流应在 PR 的检查区域中排队运行。对于单体 Astro 应用，项目检查应全部通过。此过程可能需要几分钟。
10. **可选：** 可以在 Codespace 中切换到此分支，手动测试无障碍功能。打开终端，并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    然后启动应用，在浏览器中测试高对比度和浅色模式切换，并确认页面重新加载后仍保留所选模式。

无障碍自定义智能体帮助 Copilot 根据无障碍最佳实践实现这些功能。如果发现无障碍问题或改进点，可以像处理前两个 PR 一样，在评论中提及 **@copilot** 并请求修改。

## 可选练习 — 继续委派工作

云智能体适合处理真实待办事项，让你能继续其他工作。可以在自己的存储库中创建更多议题并分配给 Copilot，练习这一工作方式。例如：

- 在游戏详情页创建支持者意向表单。
- 为游戏列表页添加分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

已完成云智能体工作环境的练习。在这些课程中，你：

- **检查了存储库自带的自定义指令文件**，以便之后观察它们对云智能体结果的影响。
- **将议题分配给 Copilot 云智能体**，并观察它设置环境、制定计划和异步执行任务。
- **创建并使用自定义智能体**处理无障碍工作，添加高对比度和浅色模式切换。
- **使用 Copilot 智能体页面作为控制面板**，监控并引导无障碍会话。
- **审查并迭代改进云智能体创建的拉取请求**，通过提及 `@copilot` 请求修改并批准工作流。

## 审查和后续步骤

已完成云智能体工作环境的练习。其他工作环境可以补充本次实践的内容：

- 🖥️ **[VS Code 工作环境](../../vscode/)** — 在 IDE 中探索 Copilot Chat 智能体模式和 MCP 集成。
- 💻 **[CLI 工作环境](../../cli/)** — 在终端中交付两项经过审查的更改，体验 Plan 和 Autopilot 模式、智能体技能、自定义 QA 智能体、Playwright MCP、Agent Merge，以及可选的 `/delegate` 云智能体桥接。

也可以在自己的存储库中尝试以下后续任务：

- 将重构或测试覆盖率议题分配给云智能体。
- 为其他领域创建自定义智能体。
- 为其他技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]
- [配置 Copilot 云智能体防火墙][agent-firewall]

---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
[previous-lesson]: ../4-managing-agents/
