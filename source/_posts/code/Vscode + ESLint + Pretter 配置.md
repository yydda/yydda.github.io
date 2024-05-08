---
title: Vscode + ESLint + Pretter 配置
categories:
  - Code
tags:
  - vscode
  - eslint
abbrlink: 37336
date: 2018-09-30 11:04:22
updated: 2021-09-30 11:09:16
---

## 安装插件

[ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

[Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

## 配置 `settings.json`

```json
{
  "eslint.alwaysShowStatus": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": false
  },
  "editor.formatOnSave": false,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "eslint.workingDirectories": [{ "mode": "auto" }],
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "vue"
  ]
}
```
