---
title: "练习 1 - 自定义指令（VS Code）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：智能体模式 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务必须按特定方式完成，或 Copilot 需要了解背景信息，就应确保它能访问这些上下文。可以通过多种方式向 Copilot 提供特定上下文，其中一种重要方式是使用[指令文件][instruction-files]指导代码生成。

在本练习中，你将：

- 了解项目上下文、编码指南和文档标准如何通过存储库自定义指令及路径范围指令文件传递给 Copilot。
- 使用当前指令生成筛选功能的第一个数据切片（发行商辅助函数）。
- 在 `.github/copilot-instructions.md` 中添加一条新的存储库级标准。
- 重新运行相同提示，观察生成的代码如何采用新标准。
- 将指令更新和筛选功能切片提交到 `main`，以便 Copilot 在下一练习中使用更新后的指南。

> [!CAUTION]
> 生成的代码可能不完全遵循你设置的标准。Copilot 的输出具有概率性。本练习的重点是观察更新指令前后行为的整体变化，而不是要求输出逐字一致。

## 指令文件

### 场景

Tailspin Toys 团队制定了一些开发规范，包括：

- 数据层必须有单元测试。
- 用户界面应采用深色模式并具有现代风格。
- 代码中应使用 TypeScript 文档注释 (TSDoc)。
- 每个文件开头都应添加一段注释，说明文件的用途。

通过指令文件，可以为 Copilot 提供这些规范，帮助它按照团队要求完成任务。

### 自定义指令

自定义指令可向 Copilot 提供上下文和偏好，帮助它理解你的编码风格和要求。你可以指定编码约定、库以及注释风格等，从而获得更贴合需求的建议和代码片段。指令既可以应用于整个存储库，也可以针对特定文件类型或任务。

有两类指令文件：

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此文件。它应包含适用于大多数 Copilot 请求的项目级信息，例如技术栈、项目简介、最佳实践和全局指南。
- `.github/instructions/*.instructions.md`：针对特定任务或文件类型提供指南，例如 TypeScript、Astro、用户界面组件或单元测试。

> [!NOTE]
> 在 IDE 中，指令文件仅用于 Copilot Chat 的代码生成，不用于代码补全或下一步编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时都会使用存储库级指令和带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> Copilot 还支持其他标准的指令文件，例如 AGENTS.md 和 CLAUDE.md，详情请参阅[指令文件支持说明][custom-instructions-support]。

### 管理指令文件的最佳实践

深入介绍如何创建指令文件超出了本工作坊的范围。示例项目展示了一种有代表性的做法：

- 在 `copilot-instructions.md` 中集中说明项目级指南，例如项目用途、结构和全局编码标准。
- 使用 `*.instructions.md` 为文件类型（例如单元测试、Astro 组件和数据层）或特定任务提供专门指令。
- 使用自然语言，清晰地描述指南，并提供代码示例说明正确和错误的写法。

指令文件没有唯一的编写方式，AI 的使用方式也不止一种。可以通过实践找出适合项目的做法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应有一套完善的指令文件。浏览本项目的文件时，可以看到针对[用户界面更新][ui-instructions]和 [Astro][astro-instructions] 等任务的指令。
>
> Copilot 也可以协助生成指令文件。不同产品界面的操作方式有所不同，例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`。对应产品的课程会在相关处说明。
>
> 若要查找模板或起点，请浏览包含指令文件、自定义智能体和其他资源的 [awesome-copilot][awesome-copilot] 存储库。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 浏览项目中的自定义指令文件

先查看存储库自带的指令文件：一个核心 `copilot-instructions.md` 文件，以及一组针对不同任务的 `*.instructions.md` 文件。可以在编辑器或 GitHub 网页界面中打开这些文件。

1. 打开 `.github/copilot-instructions.md`。
2. 浏览文件，留意项目简介，以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。它们适用于你与 Copilot 的各种交互。另请留意 **Code standards** 下的 **GitHub Actions Workflows** 指南。
3. 打开 `.github/instructions` 文件夹，查看其中的文件。这里有针对 Astro 文件、Drizzle 数据层、测试等内容的指令。
4. 打开 `.github/instructions/unit-tests.instructions.md`。留意顶部的 `applyTo` 字段，它指定相对于存储库根目录的 glob 模式，决定指令适用的文件。例如，匹配 `**/*.test.ts` 的 TypeScript 测试文件会应用这些指令。
5. 查看此项目的单元测试专用指令。
6. 最后，打开 `.github/instructions/drizzle.instructions.md` 并滚动到末尾。留意它指向其他指令文件（如 `unit-tests.instructions.md`）和项目现有文件的链接。这样可以将较大的指令集拆分成更小、可复用的文件，并为 Copilot 提供示例。（这些路径是相对于指令文件所在目录的，而非存储库根目录。）

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分描述了项目编码标准，但尚未要求添加代码文档。接下来会添加 TSDoc 文档注释和文件注释头的规则。

## 为更改创建分支

先为本次更改创建分支，遵循常见的版本控制实践。

1. 返回上一练习创建的 Codespace。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端。
3. 创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions
   ```

## 更新指令前先使用 Copilot Chat

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始全新对话。模式和模型的选择因练习而异；相关步骤会在需要时说明。

要查看自定义指令的效果，先在当前指令文件下发送提示。更新指令后，再发送相同提示进行比较。

1. 关闭之前练习留下的编辑器选项卡，避免 Copilot 获取不需要的上下文。
2. 打开 `src/lib/publishers.ts`，让 Copilot 知道辅助函数应放在哪里。
3. 在 Chat 视图的智能体下拉菜单中选择 **Agent**，以便 Copilot 修改文件。

   ![Chat 视图中的智能体选择器](../../../_images/shared-chat-mode-selector.png)

4. 发送以下提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot 会探索项目并应用代码更改，通常会同时修改辅助函数文件和测试。
6. 留意生成的辅助函数是带类型的函数：第一个参数为 `db` 客户端，返回带类型的发行商数组。这来自 `.github/instructions/drizzle.instructions.md` 中适用于 `src/lib/*.ts` 的数据层约定。
7. 留意生成的代码**缺少** TSDoc 文档注释和文件级注释头。

> [!CAUTION]
> Copilot 的输出具有概率性，即使没有明确要求，也可能添加文档注释。如果出现这种情况也没关系；本练习的重点仍是观察添加指令后行为的一致性改善。

## 添加存储库级标准

如前所述，`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。接下来更新其中的编码标准，以改善代码建议。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到约在第 27 行附近的 **Code formatting requirements** 部分。查看现有编码标准；这里尚未说明代码文档要求，因此生成的辅助函数没有文档注释。
3. 在现有标准下方添加以下 Markdown 内容，要求 Copilot 添加文件注释头和 TSDoc 文档注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 指令既可以放在存储库级文件 `.github/copilot-instructions.md` 中，作为全局指南；也可以放在 `*.instructions.md` 文件中，针对特定语言、文件类型或任务。刚添加的文档注释规则属于项目级标准，因此存储库级文件是合适的位置。

## 重新运行提示并观察变化

使用新指令再次生成辅助函数，观察结果是否有所不同。

1. 返回 Copilot Chat 并选择 **New Chat**，清除当前对话。
2. 再次打开 `src/lib/publishers.ts`，让 Copilot 聚焦于正确的文件。
3. 发送与之前**相同的提示**：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 留意生成的文件现在是否以类似以下的注释块开头：

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 留意生成的函数现在是否包含类似以下的 TSDoc 注释：

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

你已引导 Copilot 遵循新的项目标准，并将其应用于真实代码。下一练习会继续使用这部分代码。

## 提交指令并推送分支

指令文件与存储库中的其他文件一样，需通过版本控制管理。现在提交并推送分支。

1. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端。
2. 在终端中运行以下命令，确认指令和辅助函数更改均已存在：

   ```bash
   git status
   ```

3. 暂存并提交指令更新及 Copilot 为辅助函数基础所做的所有更改：

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # If git status shows additional supporting updates (for example tests), add those files too.
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 将分支推送到存储库：

   ```bash
   git push -u origin custom-instructions
   ```

## 创建并合并拉取请求

分支推送后，创建拉取请求，并将其关联到待办事项中的文档标准议题。Copilot 可以使用项目中已连接的 GitHub 工具查找议题、创建并关闭议题的 PR，然后合并 PR。

> [!NOTE]
> 这是第一次让 Copilot 代表你操作 GitHub。项目模板已配置 GitHub 工具，无需额外设置。Copilot 首次使用工具时，VS Code 可能会提示登录 GitHub。按照提示授权即可。后续练习会介绍此连接的工作方式，即模型上下文协议 (MCP)。

1. 在 Codespace 中打开 Copilot Chat。
2. 按 <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）打开 Copilot Chat 视图，并确认智能体选择器中已选中 **Agent**。
3. 请 Copilot 查找与更新指令文件相关的议题，并从当前分支创建 PR，说明指令更新和新增的发行商辅助函数：

   ```text
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot 会查找议题并创建 PR。
5. 当 Copilot 请求代表你执行 GitHub 操作时，检查操作内容，并根据情况选择 **Allow**。
6. PR 创建后，使用以下提示要求 Copilot 合并 PR，并将本地分支切回 `main`：

   ```text
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. 当 Copilot 请求代表你执行 GitHub 操作和 shell 命令时，检查命令并根据情况选择 **Allow**。

现在已借助 GitHub Copilot 创建并合并拉取请求。

## 总结和后续步骤

你在 VS Code 中探索了 Copilot 如何读取本项目的指令文件，并使用 Copilot Chat：

- 查看项目上下文、编码指南和文档标准如何通过存储库指令及路径范围指令文件传递给 Copilot。
- 根据**现有**指令生成筛选功能的第一个数据切片。
- 在 `.github/copilot-instructions.md` 中添加一条存储库级标准。
- 重新发送相同提示，观察生成的代码如何采用新标准。
- 将指令更新和辅助函数基础提交并推送到 `main`，供下一练习使用。

接下来，在 Copilot 使用[智能体模式][next-lesson]跨代码库添加新功能时，应用这些指令。

## 资源

- [用于自定义 GitHub Copilot 的指令文件][instruction-files]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [编写更优 Copilot 自定义指令的 5 个技巧][copilot-instructions-five-tips]
- [GitHub Copilot 个人自定义指令][personal-instructions]
- [Awesome Copilot：指令文件和其他资源][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
