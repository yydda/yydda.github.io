---
title: strapi-plugin-sdk 库及使用指南
categories:
  - Code
tags:
  - Strapi
abbrlink: 43352
date: 2023-04-18 17:46:46
---

Strapi 是一个开源的 Node.js Headless CMS，最近更新到了 v4 版本，其中一个最重要的更新就是插件 API 的改进，使 Strapi 更好地支持自定义插件的开发。而 strapi-plugin-sdk 就是这个新 API 的核心库之一，用于帮助开发者快速创建和集成自定义插件。

## 什么是 strapi-plugin-sdk？

strapi-plugin-sdk 是开发 Strapi 插件时必须使用的核心库之一。该库提供了一组常用的函数和工具，使得开发人员可以更轻松地与 Strapi 系统进行交互，并实现自定义插件的功能。

该库可以用于许多插件任务，例如添加自定义字段、创建自定义 Controller 和 Service 等等。

## strapi-plugin-sdk 的安装和使用

在开始使用 strapi-plugin-sdk 之前，你需要在 Strapi 项目中安装它。你可以通过 npm 命令来完成安装：

```bash
npm install --save strapi-plugin-sdk
```

安装完成后，你可以开始编写自己的插件并使用 strapi-plugin-sdk 进行开发。

下面是一个简单的示例，展示如何使用 strapi-plugin-sdk 添加一个自定义字段到用户模型中：

```js
const pluginId = "my-plugin";

// 定义自定义字段的名称和类型
const customFields = {
  myField: { type: "string" },
};

module.exports = {
  beforeInitialize: async () => {
    // 注册自定义字段到用户模型
    await strapi.db.query(`
      ALTER TABLE ${pluginId}_users ADD COLUMN my_field text;
    `);
  },
  initialize: async () => {
    // 创建自定义 Controller 和 Service
    const { myController, myService } = strapi.plugins[pluginId].controllers;

    // 添加自定义路由
    strapi.router.post(
      `/plugins/${pluginId}/my-route`,
      myController.myRouteHandler
    );
  },
  extendUserModel: (model) => {
    // 添加自定义字段到用户模型
    Object.assign(model.attributes, customFields);
  },
};
```

在上面的示例中，我们首先通过 pluginId 定义了一个插件标识符，然后定义了一个名为 customFields 的对象，其中包含了要添加到用户模型中的自定义字段定义。

接下来，在 beforeInitialize 阶段，我们使用 strapi.db.query 函数将新的字段添加到用户模型中。在 initialize 阶段，我们注册了一个自定义 Controller 和 Service，并添加了一个自定义路由。最后，在 extendUserModel 阶段，我们将自定义字段添加到用户模型中。

通过这些步骤，你就可以使用 strapi-plugin-sdk 构建自己的 Strapi 插件。

## strapi-plugin-sdk 的 API

strapi-plugin-sdk 提供了一组常用的函数和工具，使得开发人员可以更轻松地与 Strapi 系统进行交互，并实现自定义插件的功能。

### createSchema

createSchema 函数用于创建一个新的模式，该模式可以用于定义自定义字段。

```js
const { createSchema } = require("strapi-plugin-sdk");

const schema = createSchema({
  myField: {
    type: "string",
  },
});
```

### createModel

createModel 函数用于创建一个新的模型，该模型可以用于定义自定义字段。

```js
const { createModel } = require("strapi-plugin-sdk");

const model = createModel({
  name: "my-model",
  attributes: {
    myField: {
      type: "string",
    },
  },
});
```

### createController

createController 函数用于创建一个新的 Controller，该 Controller 可以用于定义自定义 Controller。

```js
const { createController } = require("strapi-plugin-sdk");

const controller = createController({
  myRouteHandler: async (ctx) => {
    // ...
  },
});
```

### createService

createService 函数用于创建一个新的 Service，该 Service 可以用于定义自定义 Service。

```js
const { createService } = require("strapi-plugin-sdk");

const service = createService({
  myServiceMethod: async (ctx) => {
    // ...
  },
});
```

### createRouter

createRouter 函数用于创建一个新的路由器，该路由器可以用于定义自定义路由。

```js
const { createRouter } = require("strapi-plugin-sdk");

const router = createRouter();

router.get("/my-route", async (ctx) => {
  // ...
});
```

### createLifecycle

createLifecycle 函数用于创建一个新的生命周期，该生命周期可以用于定义自定义生命周期。

```js
const { createLifecycle } = require("strapi-plugin-sdk");

const lifecycle = createLifecycle({
  beforeInitialize: async () => {
    // ...
  },
  initialize: async () => {
    // ...
  },
  beforeBootstrap: async () => {
    // ...
  },
  bootstrap: async () => {
    // ...
  },
  beforeStart: async () => {
    // ...
  },
  start: async () => {
    // ...
  },
  beforeStop: async () => {
    // ...
  },
  stop: async () => {
    // ...
  },
});
```

### createPlugin

createPlugin 函数用于创建一个新的插件，该插件可以用于定义自定义插件。

```js
const { createPlugin } = require("strapi-plugin-sdk");

const plugin = createPlugin({
  name: "my-plugin",
  // ...
});
```

### createStrapi

createStrapi 函数用于创建一个新的 Strapi 实例，该实例可以用于定义自定义 Strapi 实例。

```js
const { createStrapi } = require("strapi-plugin-sdk");

const strapi = createStrapi({
  // ...
});
```

## strapi-plugin-sdk 的示例

strapi-plugin-sdk 提供了一组常用的函数和工具，使得开发人员可以更轻松地与 Strapi 系统进行交互，并实现自定义插件的功能。下面是一个简单的示例，展示了如何使用 strapi-plugin-sdk 创建一个自定义插件：

```js
const { createPlugin } = require("strapi-plugin-sdk");

const pluginId = "my-plugin";

const customFields = {
  myField: { type: "string" },
};

module.exports = createPlugin({
  name: pluginId,
  beforeInitialize: async () => {
    await strapi.db.query(`
      ALTER TABLE ${pluginId}_users ADD COLUMN my_field text;
    `);
  },
  initialize: async () => {
    const { myController, myService } = strapi.plugins[pluginId].controllers;

    strapi.router.post(
      `/plugins/${pluginId}/my-route`,
      myController.myRouteHandler
    );
  },
  extendUserModel: (model) => {
    Object.assign(model.attributes, customFields);
  },
});
```

## strapi-plugin-sdk 的注意事项

在使用 strapi-plugin-sdk 开发插件时，需要注意以下几点：

1. strapi-plugin-sdk 仅支持 Strapi v3.6.0 及以上版本。
2. 异步函数：大多数 strapi-plugin-sdk 函数都是异步的，因此你需要注意这些函数的执行顺序和返回值。
3. 插件 ID：在使用 strapi-plugin-sdk 时，必须在你的插件中定义一个唯一的 ID，并在整个插件开发过程中保持一致。
4. 数据库访问：如果你需要访问 Strapi 数据库中的数据，你可以使用 strapi.query 或 strapi.db.query 函数。但请注意，访问数据库时需要小心，确保数据的安全性和合法性。

## 结论

strapi-plugin-sdk 是一个强大的库，可帮助开发者更轻松地构建自定义 Strapi 插件。通过使用该库，你可以加快插件开发的速度，并为你的应用程序添加更多的功能和定制选项。当然，在使用该库时，你需要小心并遵循最佳实践，确保插件的质量和安全性。
