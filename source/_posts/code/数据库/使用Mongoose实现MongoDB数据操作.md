---
title: 使用 Mongoose 实现 MongoDB 数据操作
categories:
  - Code
tags:
  - 数据库
  - MongoDB
  - Mongoose
abbrlink: 61611
date: 2022-05-22 23:19:09
---

MongoDB 是一种流行的 NoSQL 数据库，它以其灵活性和可伸缩性而受到欢迎。在使用 MongoDB 时，可以使用原生的 Node.js 驱动程序来进行数据库操作，但是这需要编写大量的代码来管理数据模型和验证，因此更好的选择是使用一个 ORM 框架——如 Mongoose，来简化这个过程。

## 什么是 Mongoose

Mongoose 是一个用于 Node.js 环境下的 MongoDB ORM 框架，它提供了一个优美的 API 来与 MongoDB 进行交互，并且提供了很多有用的功能，例如：对象映射、验证器、中间件等等。

## Mongoose 和 MongoDB 的关系

Mongoose 不是 MongoDB 的一部分，而是一个用于 Node.js 应用程序的 ORM 框架。Mongoose 不能运行 MongoDB，它只是一个为了简化使用 MongoDB 的工具。

## 安装 Mongoose

使用 npm 包管理器安装 Mongoose：

```bash
npm install mongoose
```

## 连接 MongoDB 数据库

在使用 Mongoose 之前，我们需要先连接 MongoDB 数据库。可以使用 `mongoose.connect()` 方法进行连接：

```js
const mongoose = require("mongoose");

mongoose.connect("mongodb://localhost:27017/mydatabase", {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

const db = mongoose.connection;

db.on("error", (err) => {
  console.error("MongoDB 连接失败：", err);
});

db.once("open", () => {
  console.log("MongoDB 连接成功！");
});
```

以上代码使用 `mongoose.connect()` 方法连接本地的名为 mydatabase 的数据库。`useNewUrlParser` 和 `useUnifiedTopology` 选项分别用于避免连接的警告和切换到 MongoDB 的新拓扑结构。

## 定义数据模型

在使用 Mongoose 进行 CRUD 操作时，首先需要定义数据模型。数据模型是一个类似于 JavaScript 对象的结构，包含了数据属性和方法。以下是一个简单的例子：

```js
const mongoose = require("mongoose");

const bookSchema = new mongoose.Schema({
  title: String,
  author: String,
  price: Number,
  publishDate: Date,
});

const Book = mongoose.model("Book", bookSchema);

module.exports = Book;
```

以上代码定义了一个名为 Book 的数据模型，包含了书籍的标题、作者、价格和出版日期四个属性。

## 创建文档

在定义好数据模型后，我们可以使用 `new` 操作符创建一个新的文档对象，并将其保存到数据库中：

```js
const Book = require("./models/book");

const myBook = new Book({
  title: "JavaScript 高级程序设计",
  author: "Nicholas C. Zakas",
  price: 99.0,
  publishDate: new Date("2021-04-01"),
});

myBook.save((err, result) => {
  if (err) {
    console.error("保存数据失败：", err);
  } else {
    console.log("保存数据成功：", result);
  }
});
```

以上代码创建了一本 JavaScript 高级程序设计的书籍，并将其保存到数据库中。`myBook.save()` 方法会将 `myBook` 对象保存到数据库中，并在保存完成后执行回调函数。

## 查询文档

在进行查询操作时，可以使用 `find()`、`findOne()` 和 `findById()` 等方法进行查询。以下是一个简单的例子：

```js
const Book = require("./models/book");

Book.find({ author: "Nicholas C. Zakas" }, (err, books) => {
  if (err) {
    console.error("查询数据失败：", err);
  } else {
    console.log("查询数据成功：", books);
  }
});
```

以上代码查询了所有作者为 Nicholas C. Zakas 的书籍。

## 更新文档

在进行更新操作时，可以使用 `updateOne()` 或 `updateMany()` 方法进行更新。以下是一个简单的例子：

```js
const Book = require("./models/book");

Book.updateOne(
  { title: "JavaScript 高级程序设计" },
  { price: 89.0 },
  (err, result) => {
    if (err) {
      console.error("更新数据失败：", err);
    } else {
      console.log("更新数据成功：", result);
    }
  }
);
```

以上代码将标题为 JavaScript 高级程序设计的书籍的价格修改为 89 元。

## 删除文档

在进行删除操作时，可以使用 `deleteOne()` 或 `deleteMany()` 方法进行删除。以下是一个简单的例子：

```js
const Book = require("./models/book");

Book.deleteOne({ title: "JavaScript 高级程序设计" }, (err, result) => {
  if (err) {
    console.error("删除数据失败：", err);
  } else {
    console.log("删除数据成功：", result);
  }
});
```

以上代码删除标题为 JavaScript 高级程序设计的书籍。

## 数据验证

Mongoose 提供了丰富的数据验证功能，在定义数据模型时可以添加约束条件，例如必填、最小值、最大值等。以下是一个简单的例子：

```js
const mongoose = require("mongoose");

const bookSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
  },
  author: String,
  price: {
    type: Number,
    min: 0,
    max: 1000,
  },
  publishDate: Date,
});

const Book = mongoose.model("Book", bookSchema);

module.exports = Book;
```

以上代码在书籍标题中添加了必填的约束条件，同时在价格属性中添加了最小值和最大值的约束条件。在使用 `save()` 方法保存数据时，如果不满足约束条件，将会返回错误信息。

## 总结

本文介绍了 Mongoose 的基本用法，包括连接数据库、定义数据模型、创建、查询、更新和删除文档等操作。同时，还简单介绍了数据验证的功能。Mongoose 是一个强大的工具，可以大幅提高 MongoDB 数据库的开发效率和代码质量。
