---
title: "练习 1 - 自定义指令（云智能体）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：Copilot 云智能体 →][next-lesson]

使用生成式人工智能 (AI) 时，上下文至关重要。如果任务必须按特定方式完成，或 Copilot 需要了解背景信息，就应确保它能访问这些上下文。[指令文件][instruction-files]用于提供此类指导，让 Copilot 不仅了解要做什么，也了解应如何完成。

在本练习中，你将：

- 了解项目上下文、编码指南和文档标准如何通过存储库自定义指令及路径范围指令文件传递给 Copilot。
- 在 `.github/copilot-instructions.md` 中添加一条新的存储库级标准。

> [!NOTE]
> 与 VS Code 和 CLI 工作环境不同，本练习不会比较提示前后的结果。Copilot 云智能体会异步处理 GitHub 议题，因此难以实时并排展示指令的影响。稍后审查云智能体创建的拉取请求时，可以看到这些指令发挥的作用。

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

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此文件。它应包含适用于大多数 Copilot 请求的项目级信息。
- `.github/instructions/*.instructions.md`：针对特定任务或文件类型提供指南，例如 TypeScript、Astro、用户界面组件或单元测试。

> [!NOTE]
> 在集成开发环境 (IDE) 中，指令文件仅用于 Copilot Chat 的代码生成，不用于代码补全或下一步编辑建议。
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
> 每个使用 GitHub Copilot 的项目都应有一套完善的指令文件。浏览本项目的文件时，可以看到针对[用户界面][ui-instructions]和 [Astro][astro-instructions] 等任务的指令。
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
6. 最后，打开 `.github/instructions/drizzle.instructions.md` 并滚动到末尾。留意它指向其他指令文件（如 `unit-tests.instructions.md`）和项目现有文件的链接。这样可以将较大的指令集拆分成更小、可复用的文件，并为 Copilot 提供示例。（这些路径是相对于指令文件所在目录的。）

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分描述了项目编码标准，但尚未要求添加代码文档。接下来会添加 TSDoc 文档注释和文件注释头的规则。

## 添加存储库级标准

本练习只需编辑一次：添加一条项目级规则，要求代码使用 TSDoc 文档注释和文件级注释头。稍后的练习中，云智能体处理议题时会读取这条指令。

编辑前先创建分支。云智能体会读取议题目标分支中的指令，但仍应将本次更改作为独立提交处理。

1. 在 Codespace 终端中创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

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

## 提交、推送并合并指令更新

云智能体会读取议题目标分支中的指令。下一练习将从 `main` 分支创建智能体分支，因此必须先将指令更改合并到 `main`，云智能体才能使用。

1. 暂存并提交更改：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 合并到 `main` 的拉取请求并完成合并。下一练习中，云智能体处理议题时就会读取这些指令。

> [!TIP]
> 如果希望在本工作坊中直接操作 `main`，可以跳过分支步骤并直接提交到 `main`。此处保留分支流程，是为了模拟真实项目中的工作方式。

## 总结和后续步骤

你探索了 Copilot 如何读取本项目的指令文件，并在 `.github/copilot-instructions.md` 中添加了一条存储库级标准。接下来的练习中，可以在云智能体创建的拉取请求里看到这条标准的应用效果。

接下来，[将第一个议题分配给 Copilot 云智能体][next-lesson]。

## 资源

- [用于自定义 GitHub Copilot 的指令文件][instruction-files]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [编写更优 Copilot 自定义指令的 5 个技巧][copilot-instructions-five-tips]
- [Awesome Copilot：指令文件和其他资源][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
