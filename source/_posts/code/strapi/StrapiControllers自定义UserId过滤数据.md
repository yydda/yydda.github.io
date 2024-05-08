---
title: Strapi Controllers 自定义 UserId 过滤数据
categories:
  - Code
tags:
  - Strapi
abbrlink: 24794
date: 2023-09-11 10:49:04
---

## 背景

在使用 Strapi 进行开发时，我们经常需要使用控制器进行业务逻辑的处理。如果涉及到数据查询，则需要在控制器中调用 Service 来获取数据。当我们需要根据用户 ID 来查询数据时，可以使用一些方法来实现自定义过滤器。

## 实现步骤

下面介绍如何通过自定义 UserId 来过滤数据。

### 第一步：复写控制器

首先，需要在 src 下新建公共方法，如 `src\utils\controllers\apiUserFilter.js`：

```js
module.exports = {
  apiUserFilter: (strapi, api) => {
    return {
      async getIsUserData(ctx) {
        const item = await strapi.entityService.findOne(api, ctx.params.id, {
          fields: ["userID"],
        });

        if (
          item == null ||
          item.userID == null ||
          Number(item.userID) !== ctx.state.user.id
        ) {
          ctx.response.status = 403;
          ctx.response.body = {
            data: null,
            error: {
              status: 403,
              name: "ForbiddenError",
              message: "Forbidden",
              details: {},
            },
          };
          return false;
        }

        return true;
      },

      async create(ctx) {
        ctx.request.body.data = {
          ...ctx.request.body.data,
          userID: ctx.state.user.id,
        };

        const response = await super.create(ctx);

        return response;
      },

      async find(ctx) {
        ctx.query = {
          ...ctx.query,
          filters: {
            userID: {
              $eq: ctx.state.user.id,
            },
          },
        };

        const { data, meta } = await super.find(ctx);

        return { data, meta };
      },

      async findOne(ctx) {
        const response = await super.findOne(ctx);

        if (
          response &&
          Number(response.data.attributes.userID) !== ctx.state.user.id
        ) {
          ctx.response.status = 403;
          ctx.response.body = {
            data: null,
            error: {
              status: 403,
              name: "ForbiddenError",
              message: "Forbidden",
              details: {},
            },
          };
        } else {
          return response;
        }
      },

      async update(ctx) {
        const isUserData = await this.getIsUserData(ctx);

        if (isUserData) {
          const response = await super.update(ctx);
          return response;
        }
      },

      async delete(ctx) {
        const isUserData = await this.getIsUserData(ctx);

        if (isUserData) {
          const response = await super.delete(ctx);
          return response;
        }
      },
    };
  },
};
```

### 第二步：添加到 api 的控制器

在 `src\api\[api-name]\controllers\[api-name].js` 文件中，我们需要添加一个自定义的方法来过滤数据。例如，下面的代码可以在 `post` 时自动添加 `UserId`，当用户请求时，自动通过 `UserId` 来查询对应的数据：

```js
const { createCoreController } = require("@strapi/strapi").factories;
const { apiUserFilter } = require("../../../utils/controllers/apiUserFilter");

module.exports = createCoreController("api::[api-name].[api-name]", ({ strapi }) =>
  apiUserFilter(strapi, "api::[api-name].[api-name]")
);
```

在上述代码中，我们复写了一个名为 `create`、 `find`、 `findOne`、 `update`、 `delete` 的方法，并且使用 `ctx.state.user.id` 来自动创建、查找、更新、删除数据。然后，我们调用 Strapi 的服务层来查询数据，并将结果返回给客户端。

### 注意事项

需要注意的是，上述实现方式仅适用于根据 UserId 来查询数据。如果需要进行更复杂的数据过滤，例如根据不同字段的值进行查询，则需要使用类似 Query Params 的方式来构建查询条件，并在服务层中进行判断和查询。

## 总结

本文介绍了如何使用 Strapi 控制器来实现自定义数据过滤。通过对控制器文件进行修改，可以根据需要来查询和返回数据。对于更复杂的数据查询需求，可以使用类似 Query Params 的方式来构建查询条件。
