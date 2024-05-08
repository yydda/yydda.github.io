---
title: Express中间件
categories:
  - Code
tags:
  - NodeJS
  - Express
abbrlink: 55266
date: 2022-04-21 10:40:25
---

Express 是一个流行的 Node.js Web 框架，它提供了一个灵活而强大的路由和中间件系统。在 Express 中，中间件是一个函数，它可以访问请求和响应对象，并执行某些任务，例如身份验证、日志记录或错误处理。每个请求都会经过一系列中间件处理，直到到达最终处理程序或者出现错误。本文将重点介绍 Express 中间件。

## 什么是 Express 中间件？

中间件是一种在请求和响应的处理过程中，增加额外处理逻辑的技术方案。在 Express 中，中间件是一个函数，通常具有以下特点：

- 中间件函数需要有三个参数，分别是请求对象、响应对象和下一个中间件函数。
- 中间件函数可以访问和修改请求对象和响应对象，也可以调用下一个中间件函数来转移控制权。
- 中间件函数可以执行各种操作，例如身份验证、解析请求正文、记录日志、验证输入、缓存数据等等。

在 Express 中，我们可以通过 `app.use()` 方法来安装中间件，或者通过 `app.METHOD()` 方法（例如 app.get()、app.post()）来指定特定 HTTP 方法和 URL 路径所用的中间件。

## Express 中间件执行流程

在 Express 中，每个请求都会被发送到一系列中间件，直到到达最终路由或错误处理程序。这是因为中间件通常需要执行类似请求验证、缓存、传输数据等操作，同时也可以保持代码结构清晰、可重用和可维护性。以下是执行流程的示例：

![express-middleware-flow](/images/55266-1.png)

从上图可以看出，中间件函数按照顺序依次执行，每个中间件将请求对象（req）、响应对象（res）以及下一个中间件函数（next）作为参数。如果某个中间件函数不检查错误并且不调用 next()函数，则该请求将无法到达预期的路由或者最终处理程序。

## Express 中间件分类

在 Express 中，有两种主要的中间件类型：应用级别中间件和路由级别中间件。

### 应用级别中间件

应用级别中间件是指直接被注册到 app 对象上的中间件函数。这些中间件对于所有的请求和路由都会进行处理。例如，以下代码注册了一个简单的日志记录中间件：

```js
const express = require("express");
const app = express();

// 日志记录中间件
app.use((req, res, next) => {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.originalUrl}`);
  next();
});

// 路由
app.get("/", (req, res) => {
  res.send("Hello, World!");
});

app.listen(3000, () => {
  console.log("Server started on port 3000");
});
```

在这个示例中，`app.use()` 方法注册了一个中间件函数，用于记录每个请求的时间、HTTP 方法和 URL 路径。这个中间件将对该应用程序中的所有请求都进行处理，包括根路径的 GET 请求。

### 路由级别中间件

路由级别中间件是指只针对特定路由的中间件函数。这些中间件仅在与其路径匹配的请求中进行处理。例如，以下代码展示了添加路由级别中间件的正确方式：

```js
const express = require("express");
const app = express();

// 路由级别中间件
app.get(
  "/",
  (req, res, next) => {
    console.log("This is a middleware for the / route!");
    next();
  },
  (req, res) => {
    res.send("Hello, World!");
  }
);

app.listen(3000, () => {
  console.log("Server started on port 3000");
});
```

在这个示例中，`app.get()` 方法接受两个参数：URL 路径和一个回调函数的列表。我们在这里添加了一个中间件函数，用于输出信息，然后通过 `next()` 方法将控制权传递给下一个中间件或路由处理程序。

## Express 内置中间件

Express 提供了许多内置的中间件函数，可用于解决通用问题，例如处理静态文件（静态服务器）或解析请求正文（body-parser）。以下是一些常用的内置中间件：

### express.static

`express.static` 中间件函数可用于将 Express 应用程序设置为静态文件服务器。可以使用此函数来提供应用程序中的静态 HTML 页面、图像、CSS 和 JavaScript 文件。以下是一个示例：

```js
const express = require("express");
const app = express();

app.use(express.static("public"));

app.listen(3000, () => {
  console.log("Server started on port 3000");
});
```

在这个示例中，`express.static` 中间件函数将 public 目录下的所有静态文件映射到根路径（/）下。例如，如果有一个名为 index.html 的 HTML 文件位于 public/index.html，则可以通过 [http://localhost:3000/index.html](http://localhost:3000/index.html) 访问它。

### express.json

`express.json` 中间件函数可以帮助我们解析 POST 请求正文中的 JSON 数据，并将其转换为 JavaScript 对象。以下是一个示例：

```js
const express = require("express");
const app = express();

app.use(express.json());

app.post("/api/users", (req, res) => {
  const user = req.body;
  console.log(user);
  res.send("User created successfully!");
});

app.listen(3000, () => {
  console.log("Server started on port 3000");
});
```

在这个示例中，我们从客户端接收到 JSON 格式的用户数据，解析它并将其转换为 JavaScript 对象，然后将其存储到数据库中。

### express.urlencoded

`express.urlencoded` 中间件函数可以帮助我们解析 POST 请求正文中的表单数据，并将其转换为 JavaScript 对象。以下是一个示例：

```js
const express = require("express");
const app = express();

app.use(express.urlencoded({ extended: true }));

app.post("/api/users", (req, res) => {
  const user = req.body;
  console.log(user);
  res.send("User created successfully!");
});

app.listen(3000, () => {
  console.log("Server started on port 3000");
});
```

在这个示例中，我们从客户端接收到表单格式的用户数据，解析它并将其转换为 JavaScript 对象，然后将其存储到数据库中。

## Express 第三方中间件

除了内置的中间件函数之外，Express 还支持众多第三方中间件，可扩展其功能。以下是一些常用的第三方中间件：

### cors

`cors` 中间件可帮助我们处理跨域请求。如果我们想从一个域名（例如 [http://localhost:3000](http://localhost:3000)）向另一个域名（例如 [http://localhost:4000](http://localhost:4000)）发送请求，则需要启用 CORS。以下是示例：

```js
const express = require("express");
const cors = require("cors");
const app = express();

app.use(cors());

app.listen(3000, () => {
  console.log("Server started on port 3000");
});
```

### body-parser

`body-parser` 中间件可帮助我们解析 POST 请求正文中的表单数据、JSON 数据和原始数据。以下是示例：

```js
const express = require("express");
const bodyParser = require("body-parser");
const app = express();

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));

app.post("/api/users", (req, res) => {
  const user = req.body;
  console.log(user);
  res.send("User created successfully!");
});

app.listen(3000, () => {
  console.log("Server started on port 3000");
});
```

## 总结

Express 中间件是一个非常有用的工具，可帮助我们轻松地创建 RESTful API。只需通过合理组合和编写中间件即可实现良好的 API 设计和可维护性。除了 Express 自带的中间件之外，我们还可以使用各种第三方中间件来扩展其功能。需要注意的是，在处理数据时，请始终仔细验证和过滤请求，以确保应用程序的安全性和正确性。
