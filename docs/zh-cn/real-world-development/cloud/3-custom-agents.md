---
title: "练习 3 - 自定义智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## 什么是自定义智能体？

GitHub Copilot 中的[自定义智能体][custom-agents-concept]是针对开发工作流中特定任务或领域定制的人工智能 (AI) 助手。你可以在存储库的 `.github/agents` 文件夹中使用 Markdown 文件定义智能体，为 Copilot 提供聚焦的指令、最佳实践、编码模式和领域知识，帮助它更有效地完成特定类型的工作。团队可以把专业知识整理成可复用的智能体，例如执行 [Web 内容无障碍指南 (WCAG)][wcag] 的智能体、遵循安全编码实践的智能体，或维护一致测试模式的智能体。

自定义智能体定义在项目的 `.github/agents` 文件夹或全局目录 `~/.copilot/agents` 中。每个文件都包含 YAML frontmatter，至少需要 `name` 和 `description` 字段，随后是用于定义智能体行为、专长和指令的 Markdown 提示。

### 自定义智能体与智能体技能的区别

自定义智能体与[智能体技能][agent-skills-concept]有些相似。两者主要通过 Markdown 文件定义，并指导 AI 执行任务。可以这样区分：**自定义智能体负责工作，技能提供工具**。

自定义智能体有自己的上下文窗口，并负责协调技能（甚至其他智能体）完成工作。本实验中的无障碍自定义智能体会根据无障碍指南审查并更新网站；执行过程中，它可以调用拉取请求工作流技能或用于运行和管理测试的技能。

> [!NOTE]
> 自定义智能体没有唯一正确的编写方式。与 AI 的其他用法一样，需要在适用的环境和场景中测试并不断调整。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

本练习将介绍：

- 自定义智能体的定义方式。
- 如何向自定义智能体分配任务。

## 场景

Tailspin Toys 致力于让所有用户都能访问众筹平台，不受视力能力或偏好的限制。近期用户反馈指出，当前深色主题中的文字与背景对比度不足，难以阅读。为解决无障碍问题，设计团队要求添加可开关的高对比度模式。

无障碍访问至关重要，因此希望尽快实现此功能。接下来使用自定义智能体生成该功能。

## 查看无障碍自定义智能体

已经为你创建了无障碍自定义智能体。先查看其内容，了解它将如何引导 Copilot。

返回 Codespace，然后检查无障碍自定义智能体文件：

1. 打开 `.github/agents/accessibility.md`。
2. 查看包含 `name` 和 `description` 字段的 YAML frontmatter。

> [!CAUTION]
> 自定义智能体必须包含 `name` 和 `description` frontmatter。

3. 浏览后续部分，查看其中的内容：
   - 生成无障碍网站代码时的核心职责。
   - 无障碍最佳实践。
   - HTML、CSS 和 JavaScript 示例。
   - 常见问题和错误列表。

## 创建并分配议题

智能体控制面板是管理环境中所有智能体的中心位置。你可以在此将任务分配给 Copilot 云智能体、监控任务，甚至引导进行中的会话。现在将创建高对比度模式的任务并分配给 Copilot。

1. 打开存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 创建空白议题。
5. 将 **Title** 设置为 `Add high contrast mode to website`。
6. 将 **Description** 设置为：

   ```plaintext
   We need a high contrast mode for the site. There should be a toggle for high contrast which the user can set. It should store the setting in local storage on the browser.
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。
9. 从自定义智能体列表中选择 **Accessibility agent**。

   ![Copilot 云智能体分配界面，突出显示自定义智能体和无障碍智能体](../../../_images/ex5-select-custom-agent.png)

10. 选择 **Assign**。
11. Copilot 将在后台开始处理任务。

## 总结和后续步骤

本课介绍了 GitHub Copilot 的[自定义智能体][custom-agents]，它们是针对特定任务和领域定制的 AI 助手。使用自定义智能体，可以将团队专业知识和标准整理成可复用的智能体，引导 Copilot 更有效地完成特定类型的工作。

本课介绍了以下内容：

- 自定义智能体的定义方式。
- 如何向自定义智能体分配任务。

Copilot 正在实现高对比度模式。接下来通过智能体控制面板[监控并引导智能体会话][next-lesson]。

## 资源

- [关于自定义智能体][custom-agents]
- [在组织中使用自定义智能体的准备工作][org-custom-agents]
- [在企业中使用自定义智能体的准备工作][enterprise-custom-agents]

---

| [← 上一课：GitHub Copilot 云智能体][previous-lesson] | [下一课：监控和管理智能体 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../2-cloud-agent/
[next-lesson]: ../4-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[org-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents
[enterprise-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents
