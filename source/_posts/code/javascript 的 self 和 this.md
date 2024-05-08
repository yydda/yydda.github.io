---
title: javascript 的 self 和 this
categories:
  - Code
tags:
  - js
abbrlink: 8641
date: 2017-07-19 21:43:13
updated: 2021-10-10 21:44:13
---

## self

打开任何一个网页，浏览器会首先创建一个窗口，这个窗口就是一个 window 对象，也是 js 运行所依附的全局环境对象和全局作用域对象。self 指窗口本身，它返回的对象跟 window 对象是一模一样的。

## this

c# 有 this 关键字，它的主要作用就是指代当前对象实例（参数传递和索引器都要用到 this）。在 javascript 中，this 通常指向的是我们正在执行的函数本身，或者是指向该函数所属的对象（运行时）。

1. 不正确的方式

```JavaScript
<script type="text/javascript">
function thisTest(){
    alert(this.value); // 弹出undefined, this在这里指向??
}
</script>

<input id="btnTest" type="button" value="提交" onclick="thisTest()" />
```

分析：onclick 事件直接调用 thisTest 函数，程序就会弹出 undefined。因为 thisTest 函数是在 window 对象中定义的，所以 thisTest 的拥有者（作用域）是 window，thisTest 的 this 也是 window。而 window 是没有 value 属性的，所以就报错了。

2. 正确的方式

```JavaScript
<input id="btnTest" type="button" value="提交" />

<script type="text/javascript">
function thisTest(){
   alert(this.value);
}
document.getElementById("btnTest").onclick=thisTest;  //给button的onclick事件注册一个函数
</script>
```

## void

1. 定义

   javascript 中 void 是一个操作符，该操作符指定要计算一个表达式但是不返回值。

2. 语法
   void 操作符用法格式如下：

   （1）. javascript:void (expression)

   （2）. javascript:void expression

   注意：expression 是一个要计算的 js 标准的表达式。表达式外侧的圆括号是可选的，但是写上去你可以一眼就知道括弧内的是一个表达式（这和 typeof 后面的表达式语法是一样的）。

3. 在 a 元素下使用 void(0),a 标签不导航到其他页面，不需要网页位置的回滚，都会采取 void(0)那种写法。
