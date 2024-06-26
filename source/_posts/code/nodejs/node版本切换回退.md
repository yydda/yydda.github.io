---
title: 如何在 Node.js 中切换或回退版本？
categories:
  - Code
tags:
  - NodeJS
abbrlink: 45331
date: 2021-06-01 16:06:33
updated: 2021-09-19 21:37:49
---

Node.js 是一个非常流行的 JavaScript 运行时，它允许开发人员使用 JavaScript 编写服务器端代码。随着时间的推移，Node.js 的不同版本已经发布，这些版本包含各种新特性和更改。有时候，为了兼容特定的应用程序或库，你可能需要切换或回退到旧版本的 Node.js。

在本文中，我将介绍如何在 Node.js 中进行版本切换或回退。

## 第一步：安装 n 工具

n 工具是一个简单易用的 Node.js 版本管理器，它允许你快速地切换和安装不同版本的 Node.js。要安装 n 工具，请打开终端窗口并键入以下命令：

```bash
npm install -g n
```

此命令会在全局范围内安装 n 工具。

## 第二步：查看可用的 Node.js 版本

要查看你可以安装的所有 Node.js 版本，请运行以下命令：

```bash
n ls
```

此命令将列出 n 工具支持的所有 Node.js 版本。

## 第三步：切换到特定版本的 Node.js

要切换到特定版本的 Node.js，请运行以下命令：

```bash
n <version>
```

其中，`<version>` 是你想要切换到的 Node.js 版本号。例如，如果你想要切换到 Node.js v14.16.0，则可以运行以下命令：

```bash
n 14.16.0
```

此命令将下载并安装 Node.js v14.16.0，并将其设置为默认的 Node.js 版本。

## 第四步：回退到先前的版本

如果你想要回退到先前安装的版本，请运行以下命令：

```bash
n -
```

此命令将返回先前安装的 Node.js 版本。

## 总结

在本文中，我们已经介绍了如何使用 n 工具在 Node.js 中进行版本切换或回退。这是一种非常简单且快速的方法，你可以在不同的应用程序和库之间轻松切换，以确保兼容性。

<!-- ##　 node 版本切换/回退

1. 安装 node 版本管理模块 n

```shell
sudo npm install n -g
````

2. 安装指定版本号

```shell
sudo n 10.20.1
```

跑完搞定。 -->
