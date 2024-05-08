---
title: Node.js 服务端入门
categories:
  - Code
tags:
  - NodeJS
abbrlink: 10915
date: 2022-04-19 23:23:18
---

Node.js 是一个基于 JavaScript 运行时的平台，可以用来开发各种类型的网络应用程序。在本文中，我们将介绍如何使用原生的 Node.js API ，从零开始构建一个简单的服务端，并实现一些基本的功能。

## 安装 Node.js

首先，需要在本机上安装 Node.js。可以从官网下载最新版本的 Node.js，然后运行安装程序进行安装。

## 创建项目

使用命令行工具进入所需目录，然后使用以下命令来创建一个新的 Node.js 项目：

```bash
mkdir myserver
cd myserver
npm init -y
```

该命令创建了一个名为 myserver 的文件夹，并初始化一个新的 package.json 文件。

## 编写代码

接下来就可以开始编写服务端的代码了。 在 myserver 文件夹中创建一个 server.js 的文件，然后输入以下内容：

```js
const http = require("http");
const fs = require("fs");

const server = http.createServer((req, res) => {
  if (req.method === "GET") {
    if (req.url === "/") {
      fs.readFile("./index.html", (err, data) => {
        if (err) {
          res.writeHead(404, { "Content-Type": "text/plain" });
          res.end("404 Not Found");
        } else {
          res.writeHead(200, { "Content-Type": "text/html" });
          res.end(data);
        }
      });
    } else if (req.url === "/about") {
      fs.readFile("./about.html", (err, data) => {
        if (err) {
          res.writeHead(404, { "Content-Type": "text/plain" });
          res.end("404 Not Found");
        } else {
          res.writeHead(200, { "Content-Type": "text/html" });
          res.end(data);
        }
      });
    } else {
      res.writeHead(404, { "Content-Type": "text/plain" });
      res.end("404 Not Found");
    }
  } else if (req.method === "POST") {
    let body = "";
    req.on("data", (chunk) => {
      body += chunk.toString();
    });
    req.on("end", () => {
      console.log(`Received data: ${body}`);
      res.writeHead(200, { "Content-Type": "text/plain" });
      res.end("Data received!");
    });
  } else {
    res.writeHead(405, { "Content-Type": "text/plain" });
    res.end("Method Not Allowed");
  }
});

const port = process.env.PORT || 3000;
server.listen(port, () => {
  console.log(`Server is listening on port ${port}...`);
});
```

该代码创建了一个 HTTP 服务器，并实现了三个路由：一个用于处理默认请求，一个用于处理/about 路径的请求，以及一个处理 404 错误的路由。在启动应用程序时，服务端将监听特定的端口（默认为 3000）。

## 运行服务

最后一步是运行服务。在 myserver 文件夹中，打开命令行工具，输入以下命令：

```bash
node server.js
```

服务端就会启动，并开始监听来自客户端的请求。可以在浏览器中访问 [http://localhost:3000/](http://localhost:3000/)，确认服务端已经成功运行。

## 结论

本文介绍了如何使用原生的 Node.js API 快速搭建一个简单的服务端，并实现了一些基本的功能。Node.js 具有较高的性能和可扩展性，是一种非常优秀的后端开发语言。在实际应用中，可以使用它来构建各种类型的应用程序，并获得更好的用户体验和业务效率。
