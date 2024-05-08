---
title: Vue 模板编译原理解析
categories:
  - Code
tags:
  - vue
abbrlink: 25313
date: 2017-10-03 22:34:38
updated: 2021-10-04 00:39:56
---

## JS 的 with 语法

在了解 vue 编译原理之前，需要认识 [with](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/with) 语法。

简单来说，使用 with ，能改变 `{}` 内自由变量的查找方式，将 `{}` 内自由变量，当作 obj 的属性来查找。代码演示如下：

```js
const obj = { a: 100, b: 200 };
with (obj) {
  console.log(a); // 100
  console.log(b); // 200
  console.log(c); // Uncaught ReferenceError: c is not defined
}
```

> ⚠️ 需要注意的是：with 会打破作用域规则，易读性变差，一般不建议使用。

## vue template complier 将模板编译为 render 函数

vue 能将 template 解析为 JS 代码主要是依赖了[vue-template-compiler](https://www.npmjs.com/package/vue-template-compiler)，它的简要使用如下：

```js
const compiler = require("vue-template-complier");

const template = `<p>{{ message }}</p>`;

compiler(template);
// with(this){return _c('p',[_v(_s(toString(message)))])}
// h --> vnode
```

其作用是，将模板编译为 render 函数，然后执行 render 函数生成 vnode，基于 vnode 再执行 patch 和 diff 使页面得到渲染和更新。

如果需要，我们的 vue 组件可以使用 render 来替代 template。

## 组件渲染和更新过程

在理解了模板编译原理之后，我们可以继续[深入响应式原理](https://cn.vuejs.org/v2/guide/reactivity.html)。

![深入响应式原理](https://www.jojoer.com/upload/2021/10/data-5a62e8e77a554881823efa93c91d79f2.png)<center style="font-size:14px;color:#C0C0C0;">深入响应式原理</center>

我将其分为了以下 3 种情况，简要描述如下：

### 初次渲染

初次渲染第一步会将模板解析为 render 函数；然后会触发响应式，监听 data 的属性（也就是对应的 getter 和 setter）；最后是执行 render 函数，生成 vnode 和执行 patch 方法将 vnode 渲染为真实的 DOM。

这里需要注意的是执行 render 函数会触发 getter，绑定初始值。

### 更新过程

当我们修改了 data，会触发相应的 setter；然后会重新出发 render 函数，生成新的 vnode；最后通过 patch(vnode, newVnode)更新渲染页面。

### 异步渲染

vue 组件是异步渲染的，$nextTick 等待 DOM 渲染完再回调。页面渲染会将 data 的修改做整合，多次 data 修改只会渲染一次。目的是为了减少 DOM 的操作次数，提高性能。
