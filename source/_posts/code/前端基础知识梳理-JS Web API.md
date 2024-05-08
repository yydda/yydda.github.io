---
title: 前端基础知识梳理-JS Web API
abbrlink: 9488
date: 2021-08-08 22:47:08
updated: 2021-08-15 21:11:50
categories:
  - Code
tags:
  - js
---

## JS Web API

### DOM

#### DOM 本质

- 树（DOM 树）

#### DOM 节点操作

- 获取 DOM 节点

  - document.getElementById('div1') // 元素

  * document.getElementsByTabName('div') // 集合
  * document.getElementsByClassName('.container') // 集合
  * document.querySelectorAll('p') // 集合

* attribute

  - p.setAttribute('data-name', 'imooc')
  - p.setAttribute('style', 'width: 5px')
  - p.getAttribute('data-name')

* property

  - p.style.width // 获取样式
  - p.style.width === '100px' // 修改样式
  - p.className // 获取 class
  - p.className = 'p1' //修改 class
  - p.nodeName // 节点名字
  - p.nodeType // 一般 dom 类型是 1，不常用

##### property 和 attribute

- property：修改对象属性，不会体现到 html 结构中
- attribute：修改 html 属性，会改变 html 结构
- 两者都有可能引起 DOM 重新渲染

#### DOM 结构操作

```js
const div1 = document.getElementById("div");
const div2 = document.getElementById("div");

// 新建节点
const newP = document.createElement("p");
newP.innerHTML = "this is newP";
// 插入节点
div.appendChild(p1);

// 移动节点
const p1 = document.getElementById("p1");
div2.appendChild(p1);

// 获取子元素列表
const div1 = document.getElementById("div");
const child = div1.childNodes;

// 获取父元素
const div1 = document.getElementById("div1");
const parent = div1.parentNode;

// 删除节点
const div1 = document.getElementById("div");
const child = div.childNodes;
div1.removeChild(child[0]);
```

#### DOM 性能

- DOM 操作非常“昂贵”，避免频繁的 DOM 操作
- 对 DOM 查询做缓存
- 将频繁操作改为一次性操作

---

### BOM

#### navigator

```js
const ua = navigator.userAgent;
const isChrome = ua.indexOf("Chrome");
console.log(isChrome);
```

#### screen

```js
screen.width;
screen.height;
```

#### location

```js
location.href; // 整个网址
location.host; // coding.imooc.com
location.protocol; // 'http:' 'https:'
location.pathname; // /learn/199
location.search; // ?a=xxx&b=xxx
location.hash; // #xxx
```

#### history

```js
history.back();
history.forward();
```

---

### 事件

#### 事件绑定

```js

```

#### 事件冒泡

> 阻止事件冒泡
> e.stopPropagation()

- 基于 DOM 树形结构
- 事件会顺着触发元素往上冒泡
- 应用场景：事件代理

#### 事件代理

- 基于事件冒泡来做的
- 场景：一般为瀑布流

```html
<div id="div1">
  <a href="#">a1</a>
  <a href="#">a2</a>
</div>
<button>点击增加一个a标签</button>
```

```js
const div1 = document.getElementById("div1");

div.addEventListener("click", (e) => {
  if (e.target.nodeName === "A") {
    alert(target.innerHTML);
  }
});
```

- 代码简洁
- 减少浏览器内存占用
- 但是，不要滥用（有一定的复杂度）

```js
function bindEvent(elem, type, selector, fn) {
	if (fn == null) {
		fn = selector
		selector = null
	}
	elem.addEventListener(type, event => {
		const target = event.target
		if (selector) {
			// 代理绑定
			// 判断target有没符合的标签
			if (target.matches(selector)) {
				fn.call(target, event) // call绑定this
			}
		} else {
			// 普通绑定
			fn.call(target, event)
		}
	)
}

// 普通模式
const btn1 = document.getElementById('btn1')
bindEvent(btn1, 'click', function (event) {
	event.preventDefault()
	alert(this.innerHTML)
})

// 代理模式
const btn2 = document.getElementById('btn2')
bindEvent(btn2, 'click', 'a', function (event) {
	event.preventDefault()
	alert(innerHTML)
})
```

### ajax

#### XMLHttpRequest

```js
// get 请求
const xhr = new XMLHttpRequest();
// false同步，true异步
// POST
xhr.open("GET", "/api", true);
xhr.onreadystatechange = function () {
  //
  if (xhr.readyState === 4) {
    if (xhr.status === 200) {
      alert(xhr.responseText);
    }
  }
};
xhr.send(null);

// POST
const postData = {
  name: "zhangshan",
  num: "100",
};
xhr.send(JSON.stringy(postData));
```

#### xhr.readyState

- 0-(未初始化)还没调用 send 方法
- 1-（载入）已调用 send 方法，正在发送请求
- 2-（载入完成）send()方法执行完成，已经接收到全部响应内容
- 3-（交互）正在解析响应内容
- 4-（完成）响应内容解析完成，可以在客户端调用

#### 状态码 xhr.status(http 协议)

- 2xx-表示成功处理请求，如 200
- 3xx-需要重定向，浏览器直接跳转，如 301 302 304
- 4xx-客户端请求错误，如 404 403
- 5xx-服务器端错误

#### 跨域：同源策略，跨域解决方案

##### 什么是跨域（同源策略）

- ajax 请求时，浏览器要求当前网页和 server 必须同源（安全）
- 同源：协议、域名、端口，三者必须一致

- 加载图片 css js 可无视同源策略

```js
<img src="跨域的图片地址"/> // 可用于统计打点，可使用第三方统计服务
<link href="跨域的css地址"/>
<script src="跨域的js地址"></script>
// <link><script>可使用CDN，CDN一般是外域
//<script>可实现JSONP
```

- 所有的跨越，都必须经过 server 端允许和配合
- 未经 server 端允许就实现跨域，说明浏览器有漏洞，危险信号

##### JSONP

- `<script>` 可绕过跨域限制
- 服务器可以任意动态拼接数据返回
- 所以， `<script>` 就可以获得跨域的数据，只要服务端愿意返回

##### CORS（服务端支持）-服务器端设置 http header

```js
// 第二个参数填写允许跨域的域名称，不建议直接写“*”
response.setHeader("Access-Control-Allow-Origin", "http://localhost:8011");
response.setHeader("Access-Control-Allow-Headers", "X-Requested-With");
response.setHeader(
  "Access-Control-Allow-Methods",
  "PUT,POST,GET,DELETE,OPTIONS"
);

// 接收跨域的cookie
response.setHeader("Access-Control-Allow-Credentials", "true");
```

### 存储

#### cookie

- 本身用于浏览器和 server 通讯
- 被借用到本地存储
- 可用 document.cookie = '...'来修改

##### cookie 的缺点

- 存储大小，最大 4kb
- http 请求时要发送到服务端，增加请求数据量
- 只能用 document.cookie = '...'来修改，太过简陋

#### localStorge 和 sessionStorage

- HTML5 专门为存储而设计的，最大可存 5M
- API 简单易用，setItem getItem
- 不会随着 http 请求被发送出去

##### localStorge 和 sessionStorage 的区别

- localStorage 数据会永久存储，除非代码或手动删除
- sessionStorage 数据只存在于当前会话，浏览器关闭则清空
- 一般用 localStorage 多
