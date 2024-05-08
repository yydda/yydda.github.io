---
title: Node.js 文件操作与模块化
categories:
  - Code
tags:
  - NodeJS
abbrlink: 64689
date: 2022-04-17 22:25:58
---

Node.js 是一种基于事件驱动、非阻塞 I/O 的开发技术，可以在服务器端运行 JavaScript，并且具备 I/O 操作能力。在 Node.js 开发中，文件操作和模块化是两个非常重要的概念。

## 文件操作

Node.js 提供了一系列的内置模块，例如 `fs`（文件系统）模块、`path` 模块等，用于完成各种与文件相关的操作。

### 读取文件

`fs.readFile()` 方法用于读取文件，它的语法如下：

```js
const fs = require("fs");
fs.readFile("file.txt", (err, data) => {
  if (err) throw err;
  console.log(data.toString());
});
```

- `readFile()` 方法接收两个参数：要读取的文件路径以及读取完成后的回调函数。
- 回调函数包含两个参数：错误信息 `err` 和读取到的数据 `data`。
- 这里使用 `toString()` 方法将二进制数据转换为字符串。

### 写入文件

`fs.writeFile()` 方法用于写入文件，它的语法如下：

```js
const fs = require("fs");
fs.writeFile("file.txt", "Hello World!", (err) => {
  if (err) throw err;
  console.log("File saved!");
});
```

- `writeFile()` 方法接收三个参数：要写入的文件路径、要写入的数据以及写入完成后的回调函数。
- 回调函数只包含一个参数，即错误信息 `err`。

### 其他文件操作

除了读取和写入文件，Node.js 还提供了一系列的文件操作方法，例如：

- `fs.unlink(path, callback)`：删除文件；
- `fs.rename(oldPath, newPath, callback)`：重命名文件；
- `fs.appendFile(file, data[, options], callback)`：追加数据到文件末尾。

更多操作可参考 fs 模块文档：[1](https://nodejs.org/dist/latest-v14.x/docs/api/fs.html)。

## 模块化

Node.js 旨在让开发者能够通过模块化的方式来组织代码，使得代码更加简洁、易于维护。在 Node.js 中，每个 JavaScript 文件都是一个模块。

### 创建模块

在 Node.js 中，可以使用 module.exports 对象导出模块。以下是一个例子：

```js
// module1.js
const myModule = {
  greeting: "Hello, World!",
};
module.exports = myModule;
```

在另一个文件中，可以使用 `require()` 方法引入模块：

```js
// index.js
const myModule = require("./module1");
console.log(myModule.greeting);
```

上述代码中，`require()` 方法会返回 `module1.js` 中导出的对象，然后可以通过该对象访问该模块的内容。

### 内置模块

Node.js 除了支持自定义模块外，还提供了一些内置模块，例如 `os`（操作系统）、`path`（文件路径）等等。这些内置模块可以直接使用，无需进行安装。

以下是一个例子展示如何使用 `os` 模块获取操作系统的信息：

```js
const os = require("os");
console.log(`Platform: ${os.platform()}`);
console.log(`CPU Architecture: ${os.arch()}`);
console.log(`Memory: ${os.totalmem() / 1024 ** 3} GB`);
```

### 第三方模块

除了内置模块外，Node.js 还支持使用第三方模块，可以通过 npm 安装和管理。例如，`axios` 是一种流行的第三方模块，可以用于发送 HTTP 请求。

以下是一个简单的例子展示如何使用 `axios` 发送 GET 请求：

```js
const axios = require("axios");
axios
  .get("https://jsonplaceholder.typicode.com/todos/1")
  .then((response) => console.log(response.data))
  .catch((error) => console.error(error));
```

上述代码中，我们使用了 axios 的 get() 方法发送 GET 请求，并处理响应结果或错误信息。

## 总结

本文简要介绍了 Node.js 中的文件操作和模块化开发，希望读者能够掌握 Node.js 中的基础知识并能够使用它们构建高效、可维护的程序。Node.js 提供的强大功能使其在网络应用程序开发中具有很高的实用性，同时也是前端工程师的一种必备技能。
