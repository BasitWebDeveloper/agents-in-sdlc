---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：自定义指令][previous-lesson]

许多组织都面临技术债务，例如尚未解决的安全问题、需要更新的旧代码，或因缺少时间而长期搁置的功能请求。GitHub Copilot 云智能体可以自主执行更新代码和添加功能等任务。完成后，它会创建草稿拉取请求，供开发者审查。这样可以将繁琐任务交给智能体处理，让开发者专注于更重要的工作，加快开发进度。

本练习将介绍如何使用 Copilot 云智能体：

- 自定义代码生成环境。
- 确保操作安全。
- 编写范围明确的议题。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 有一些技术债务需要处理。最初承包网站开发的团队没有留下足够的文档。首先，团队希望为应用程序中所有导出的函数添加 TypeScript 文档注释 (TSDoc)。

此外，设计团队希望改进游戏发现功能。他们希望在每款游戏的详情页显示相关游戏，也就是同一类别中的其他游戏，以便访客继续浏览。目前不要求实现经过完善的功能，只需能供团队进行用户体验验收测试。虽然这项工作目前受阻，但还有优先级更高的议题。

这类工作很容易被搁置，适合分配给 Copilot 云智能体。它可以异步处理任务，让开发者继续其他工作，之后再回来审查结果。

## 认识 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)可以在后台执行任务，方式类似于开发者。你可以通过多种方式开始任务，例如[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配后，Copilot 会创建草稿拉取请求以跟踪进度、设置环境并开始处理任务。你可以在会话进行中或完成后查看详情。解决方案准备好供审查时，Copilot 会在拉取请求中提及你。

## 明确任务范围的重要性

GitHub Copilot 并非魔法，不能替代思考。看似简单的任务也可能包含复杂之处，因此请遵循[为 Copilot 云智能体明确任务范围的建议][cloud-agent-best-practices]。将它视为人工智能 (AI) 配对编程助手：分阶段工作、学习、试验并根据结果调整。引入生成式 AI 不会改变软件开发的基本原则。

## 本存储库中的自定义指令

前面的练习介绍了自定义指令及其作用。如果已完成自定义指令练习，就已经看过 `.github/copilot-instructions.md` 的效果；否则，现在可以快速查看。分配任务前，先只读浏览存储库中的指令文件，稍后便能检查它们对结果的影响。

在 GitHub 网页界面中打开以下文件；如果已有 Codespace，也可以在其中查看：

- `.github/copilot-instructions.md`：检查 **Code standards** 部分，尤其是 TypeScript 约定和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：留意 `applyTo` frontmatter，它将指令限定在 `**/*.test.ts` 文件中。

下一部分将把 `Code lacks documentation` 议题分配给云智能体。查看生成的拉取请求时，留意 TSDoc 文档注释、TypeScript 约定和文件注释头；它们来自这些指令文件，后续练习还会再次审查。

## 为 Copilot 云智能体设置开发环境

无论由谁编写代码，通常都需要准备特定环境并运行设置脚本。将任务分配给 Copilot 时也一样，它会以类似软件工程师的方式处理任务。

云智能体使用 [GitHub Actions][github-actions] 环境开展工作。你可以创建专用设置工作流来自定义环境，该工作流保存在 `.github/workflows/copilot-setup-steps.yml`，并在云智能体开始工作前运行。它可以安装所需开发工具和依赖项。本实验已提前配置此工作流，以确保云智能体能使用 Node.js、项目依赖项、端到端测试所需的浏览器，以及为单体 Astro 应用迁移并填充数据的 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# 允许你从存储库的“Actions”选项卡测试设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 将权限限制为步骤所需的最低级别。Copilot 会使用自己的令牌执行操作。
    permissions:
      # 如果要在设置步骤中克隆存储库（例如安装依赖项），需要 `contents: read` 权限。如果未在设置步骤中克隆存储库，步骤完成后 Copilot 会自动克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端/应用设置 - Node.js（整个应用现为 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，为构建和测试准备数据。
      - name: Set up the database
        run: npm run db:setup
```

此文件与其他 GitHub 工作流文件类似，但有几点需要注意：

- 它包含一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理拉取请求前，GitHub Actions 会运行此作业。
- `workflow_dispatch` 触发器允许你在存储库的 **Actions** 选项卡中手动运行工作流。这有助于验证工作流，而不必等待 Copilot 启动它。

## 添加文档

虽然人人都知道文档的重要性，但大多数项目的文档要么过时，要么缺失。这类技术债务常常无人处理，会拖慢工作效率，也增加维护代码库或帮助新开发者上手的难度。Copilot 擅长生成文档，因此很适合将这项任务交给云智能体；稍后的练习会审查它的工作。

1. 在新的浏览器选项卡中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 创建空白议题。
5. 将 **Title** 设置为 `Code lacks documentation`。
6. 将 **Description** 设置为：

   ```plaintext
   Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

   ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。
10. 选择 **Pull Requests** 选项卡。
11. 打开新创建的拉取请求 (PR)，标题可能类似于 `[WIP]: Code lacks documentation`。如果列表中尚未出现新 PR，请稍等片刻并刷新浏览器。
12. 几分钟后，Copilot 应会创建待办任务列表。

> [!NOTE]
> Copilot 可能需要几分钟才会在 PR 中显示待办任务列表。它正在创建环境（运行前面介绍的工作流）、分析项目并确定解决方案。

13. 查看任务列表及 Copilot 准备完成的工作。
14. 向下滚动 PR 时间线，查看 Copilot 已开始处理议题的更新。
15. 选择 **View session** 按钮。

   ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新页面才能看到更新后的状态。

16. 浏览实时会话，观察 Copilot 如何探索代码、了解当前状态、分析并决定合适方案，以及编写代码。

此过程可能需要几分钟。云智能体的主要优势之一是能够异步执行任务，让你腾出时间处理其他工作。接下来会再分配一项任务，然后将注意力转向应用功能开发。

## 在游戏详情页添加相关游戏

云智能体的一大优势是可以拆分工作：你专注于一项任务，它则处理另一项任务。为设计团队添加相关游戏区域可能不需要很长时间，但仍可利用这段时间处理其他工作。现在将这项任务分配给云智能体。

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 使用空白模板。
5. 将 **Title** 设置为 `Show related games on the game details page`。
6. 将 **Description** 设置为：

   ```markdown
   We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

   ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

不久后，议题的第一条评论中会出现 👀，表示 Copilot 已开始处理。

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 现在正在后台处理第二项请求。它的工作方式类似软件工程师，因此无需持续监控；完成后再审查即可。接下来开始创建和使用自定义智能体。

## 总结和后续步骤

本课介绍了 [GitHub Copilot 云智能体][copilot-agents]。可以将议题分配给它异步处理，从而解决技术债务、创建新功能，或协助将代码迁移到其他框架。

本课介绍了以下内容：

- 自定义代码生成环境。
- 确保操作安全。
- 编写范围明确的议题。
- 将议题分配给 Copilot。

云智能体正在后台处理任务。接下来创建并使用自定义智能体。[Copilot 云智能体也可以使用 MCP 服务器][cloud-agent-mcp]，并可使用前面练习中配置的自定义指令。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
