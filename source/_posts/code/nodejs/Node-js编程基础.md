---
title: Node.js 编程基础
categories:
  - Code
tags:
  - NodeJS
abbrlink: 3320
date: 2022-04-17 10:25:58
---

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境，使得 JavaScript 能够在服务器端运行并具有 I/O 操作能力。本文主要介绍 Node.js 的基本概念、安装及使用方法。

## 什么是 Node.js？

Node.js 是一个可以在服务器端运行 JavaScript 的开源、跨平台运行时环境。它是一个基于事件驱动、非阻塞 I/O 的模型，使得开发者能够使用 JavaScript 快速地构建高性能的网络应用程序。

Node.js 的核心是 V8 引擎，V8 是 Google Chrome 浏览器中的 JavaScript 引擎，它将 JavaScript 编译为机器码，使得 JavaScript 在浏览器中可以运行得非常快。Node.js 利用了 V8 引擎的优势，同时为后端提供了 I/O 操作的能力，使得 JavaScript 可以在服务器端实现系统级别的操作，如文件读写、网络通信等。

## 安装 Node.js

Node.js 的安装非常简单，只需要到官网下载对应的安装包，然后按照提示进行安装即可。官网地址为：[https://nodejs.org/en/download/](https://nodejs.org/en/download/)。

安装完成后，我们可以在命令行中输入以下命令来检查是否安装成功：

```bash
node -v # 显示 Node.js 的版本号
npm -v # 显示 npm 的版本号
```

运行成功后，会分别显示 Node.js 和 npm 的版本号。

## Hello World

接下来我们将创建一个简单的 Node.js 应用程序，输出 "Hello World!"。首先，在命令行中创建一个文件夹并进入：

```bash
mkdir myapp
cd myapp
```

然后，创建一个名为 app.js 的文件，输入以下代码：

```js
console.log("Hello World!");
```

保存后，可以在命令行中通过以下命令运行该应用程序：

```bash
node app.js
```

运行成功后，会在命令行中输出 "Hello World!"。

## Node.js 模块与包管理器

Node.js 的一个重要特性是模块化开发。在 Node.js 中，每个 .js 文件都是一个模块，一个模块可以引用其他模块，形成一个完整的程序。

除了使用内置模块外，开发者还可以选择安装第三方模块或自己编写模块。而包管理器 npm 则是管理这些模块的工具。

npm 是 Node.js 的默认包管理器，它可以让你轻松地查找、安装和管理 Node.js 模块。以下是一些常用的 npm 命令：

- `npm install <package>`：安装一个 Node.js 模块；
- `npm uninstall <package>`：卸载一个 Node.js 模块；
- `npm init`：创建一个新项目；
- `npm search <keywords>`：搜索 Node.js 模块；
- `npm list`：列出当前项目所安装的所有模块。

## 总结

本文介绍了 Node.js 的基本概念、安装方法、应用程序开发以及模块化开发和包管理器 npm。Node.js 在 web 开发、网络编程等方面具有很大的优势，是一种非常强大的后端开发技术。希望本文可以帮助读者快速入门 Node.js 基础开发。
