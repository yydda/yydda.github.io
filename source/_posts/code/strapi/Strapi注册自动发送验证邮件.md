---
title: Strapi 注册自动发送验证邮件
abbrlink: 1132
date: 2023-03-25 19:41:19
categories:
  - Code
tags:
  - Strapi
---

## 安装插件

本方案使用 `@strapi/provider-email-nodemailer` 插件，在项目根目录执行以下命令安装依赖。

```shell
# using npm
npm install @strapi/provider-email-nodemailer --save

# using yarn
yarn add @strapi/provider-email-nodemailer
```

## 配置邮箱的授权码

这里使用的是 qq 邮箱，依次点击 “设置” - “帐户” - “帐户” - “获取授权码（如下图）：

![获取授权码](/images/1132-1.jpg)

## 配置插件文件

修改 strapi 工程 config/plugins.js ，如下图：

![在 strapi 工程配置插件](/images/1132-2.jpg)

```js
// path: ./config/plugins.js
module.exports = ({ env }) => ({
  // ...
  email: {
    config: {
      provider: "nodemailer",
      providerOptions: {
        // QQ邮箱服务器和默认端口
        host: env("SMTP_HOST", "smtp.qq.com"),
        port: env("SMTP_PORT", 465),
        auth: {
          // 发送账号和客户端鉴权码
          user: env("SMTP_USERNAME", "xxx@qq.com"),
          pass: env("SMTP_PASSWORD", "xxxxxxxxxxx"),
        },
        // ... any custom nodemailer options
      },
      settings: {
        // 默认发送账号
        defaultFrom: "xxx@qq.com",
        // 默认回复账号
        defaultReplyTo: "xxx@qq.com",
      },
    },
  },
  // ...
});
```

## 发送测试邮件

![在 strapi 工程配置插件](/images/1132-3.jpg)

收到以下邮件，代表配置成功：

![测试邮件](/images/1132-4.jpg)

## 注册自动发送验证邮件

### 管理后台开启注册自动发送邮件

![开启注册自动发送邮件](/images/1132-5.jpg)

### 开启 public 角色发送邮件权限

![开启 public 角色发送邮件权限](/images/1132-6.jpg)

### 修改注册邮件模板

![修改注册邮件模板](/images/1132-7.jpg)

### 测试注册接口

调用注册接口，可使用 postman 测试或 axios 等方式：

```js
import axios from "axios";

// Request API.
// Add your own code here to customize or restrict how the public can register new users.
axios
  .post("http://localhost:1337/api/auth/local/register", {
    username: "Strapi user",
    email: "user@strapi.io",
    password: "strapiPassword",
  })
  .then((response) => {
    // Handle success.
    console.log("Well done!");
    console.log("User profile", response.data.user);
    console.log("User token", response.data.jwt);
  })
  .catch((error) => {
    // Handle error.
    console.log("An error occurred:", error.response);
  });
```

调用成功会收到一封邮件以及在 user 表中有一条记录，未确认之前 CONFIRMED 字段是 false
![user 表 CONFIRMED 字段为 false](/images/1132-8.jpg)

![user 表 CONFIRMED 字段为 false](/images/1132-9.jpg)

确认之后，CONFIRMED 字段状态改变为 true，并且自动跳转到我们 5.1 设置的 url 中。
