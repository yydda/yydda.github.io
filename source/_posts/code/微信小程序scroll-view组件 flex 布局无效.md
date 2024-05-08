---
title: 微信小程序scroll-view组件 flex 布局无效
categories:
  - Code
tags:
  - 小程序
abbrlink: 9607
date: 2021-05-13 11:20:16
updated: 2021-09-19 21:38:10
---

当我们在使用微信小程序开发时，会遇到许多问题，其中之一就是 scroll-view 组件中的 flex 布局无效的问题。这个问题在很多情况下都会出现，尤其是当我们需要设置一个固定高度的 scroll-view 组件时。

首先，我们需要了解 scroll-view 组件是如何工作的。scroll-view 组件可以用于在可滚动区域内显示大量内容，它支持竖向和横向滚动，并且可以设置固定高度或者自适应高度。而 flex 布局是一种常用的布局方式，它能够让子元素按照一定的规则排列，使页面更加美观和易于维护。

但是，在 scroll-view 组件中使用 flex 布局时，我们会发现设置了 flex 属性后，子元素并没有按照我们所期望的方式排列。这是因为 scroll-view 组件默认是不支持 flex 布局的，需要我们手动启用 flexbox 布局。

![image.png](/images/image_1620875828262.png)

代码示例：

```html
<scroll-view scroll-y enable-flex>
  ...
</scroll-view>
```
