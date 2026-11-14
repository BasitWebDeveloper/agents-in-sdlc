---
title: "第 4 课 - 使用自定义指令引导 Copilot"
description: "查看存储库指令，添加文档标准，并将其应用于筛选功能代码。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

使用生成式人工智能 (AI) 时，上下文至关重要。如果任务必须按特定方式完成，就应确保 Copilot 能获取相应指导。[指令文件][instruction-files]不仅说明希望生成什么代码，也说明代码应如何组织。完成筛选功能后，现在查看 Copilot 使用的指令、添加一项文档标准，并将它应用到代码中。

在本课中，你将：

- 了解存储库指令和路径范围指令文件如何传递给智能体。
- 更新指令文件，确保遵循编码标准。
- 查看指令文件对代码的影响。

## 场景

Tailspin Toys 团队制定了一些开发实践规范，包括：

- 注释应解释意图和不明显的决定，而不是复述代码。
- `db/` 和 `src/lib/` 中导出的函数应使用 TypeScript 文档注释 (TSDoc)/JavaScript 文档注释 (JSDoc) 说明用途、参数和返回值；如果有可注入的 `db` 参数，也应予以说明。
- 可复用的 Astro 组件应说明其 `Props` 接口；相关代码更改时应同步更新注释。
- 保留现有格式和 lint 指南。

通过指令文件，可以让 Copilot 按照这些实践要求完成任务。

## 指令文件

自定义指令可向 Copilot 提供上下文和偏好，帮助它理解编码风格和需求。你可以指定编码约定、库以及注释风格等，从而获得更贴合需求的建议和代码片段。指令既可以应用于整个存储库，也可以针对特定文件类型或任务。

有两类指令文件：

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此文件，适合存放适用于大多数请求的项目级信息。
- `.github/instructions/*.instructions.md`：针对特定任务或文件类型提供指南。

> [!NOTE]
> 不同工作环境支持的其他指令格式有所不同。依赖某种格式前，请查看[自定义指令支持参考][custom-instructions-support]。

## 查看项目中的自定义指令文件

项目模板已包含一组指令文件。先浏览这些文件，了解已有内容，再进行更改。

1. 返回 Codespace。
2. 在终端之外的 Codespaces 编辑器中打开 `.github/copilot-instructions.md`。
3. 浏览项目简介和编码指南。此文件适用于该存储库中与 Copilot 的所有交互。
4. 打开 `.github/instructions` 文件夹并查看其中的文件。这里有针对 Astro、Drizzle 数据层、测试等内容的指令。
5. 打开 `.github/instructions/unit-tests.instructions.md`。留意顶部的 `applyTo` 字段，它指定指令适用文件的 glob 模式。
6. 打开 `.github/instructions/drizzle.instructions.md`，查看它指向的其他指令文件和项目现有文件。通过这种方式，可以拆分较大的指令集并提供示例。

## 更新指令以符合团队规范

现有文件已经提供良好基础，但还缺少一项标准。接下来修改核心 `copilot-instructions.md`，要求新生成的 TypeScript 添加 TSDoc 注释。

1. 在 `.github/copilot-instructions.md` 中找到 **Code formatting guidance** 部分，约在第 35 行。
2. 在该部分末尾添加以下项目：

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

保存后文件会自动更新。

## 应用更新后的指南

Copilot CLI 会在对话启动时加载存储库指令。保存更改后，退出并恢复筛选对话，让 Copilot 在保留功能上下文的同时加载新指南。

1. 如果筛选对话仍处于打开状态，输入 `/exit` 关闭。Copilot CLI 会自动保存对话。
2. 在存储库根目录的 `game-filters-cli` 分支上恢复已保存的对话：

   ```bash
   copilot --resume
   ```

   如果出现提示，请选择筛选对话，并确认当前检出的是 `game-filters-cli` 分支。
3. 请 Copilot 应用更新后的指南：

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. 输入 `/diff` 并查看已更改的 TypeScript 文件。检查新生成的 TSDoc 注释，并确认它们准确说明代码。

## 总结和后续步骤

你探索了 Copilot CLI 如何读取指令文件，并将新标准应用到筛选功能。本课中，你：

- 了解了存储库指令和路径范围指令文件如何传递给智能体。
- 更新了指令文件，要求遵循编码标准。
- 查看了指令文件对代码的影响。

接下来[自定义并运行可复用的 quality-checks 技能][next-lesson]，确保一致地运行 lint 和测试。

## 资源

- [为 Copilot CLI 添加自定义指令][instruction-files]
- [自定义指令支持参考][custom-instructions-support]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [Awesome Copilot：指令文件和其他资源][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
