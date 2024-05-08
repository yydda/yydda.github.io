---
title: Storybook 入门指引和技术细节
categories:
  - Code
tags:
  - Storybook
abbrlink: 43580
date: 2023-01-23 19:16:08
---

Storybook 是一个用于辅助前端组件开发的开源工具。它支持多种框架，包括 React、Vue 和 Angular。它为 UI 组件提供了一个独立的沙箱环境，可以让开发者在这里创建各种场景模拟，并展现出来。这些场景模拟可以像一本书里的故事一样，用来展示组件的使用场景和效果。接下来，我们将带您进入 Storybook 的世界。

## 安装

如果您准备使用 React 开发组件，那么您需要安装 Storybook 的 React 版本。安装过程非常简单。以 React 为例，只需在终端中运行以下命令：

```bash
npx storybook@latest init
```

这个命令将在您的项目中生成.storybook 文件夹和.storybook/main.js 文件。现在您已经完成了安装过程，可以开始使用 Storybook 了。

## 创建故事（Story）

Story 是 Storybook 中的一个基本单位，它指一个组件在特定状态下的视觉呈现。以 React 为例，我们可以使用 storiesOf 函数来创建 Story。假设我们有一个 Button 组件，那么我们可以在.storybook 文件夹中创建一个 Button.stories.js 文件来定义它的 Story。代码如下：
