---
title: h5 调试插件 eruda.js
categories:
  - Code
tags:
  - js
abbrlink: 4304
date: 2021-07-15 10:41:30
updated: 2021-09-19 21:34:01
---

在前端开发中，调试是一个非常重要的环节。为了提高代码质量和开发效率，我们需要使用一些工具来帮助我们快速定位和解决问题。eruda.js 就是这样一款非常实用的前端调试工具。

eruda.js 是一款基于移动设备的 Web 调试工具，它可以在 safari 等浏览器中使用。通过 eruda.js，我们可以方便地查看当前页面的 DOM 结构、CSS 样式、JavaScript 代码等，并且还能实时查看网络请求和性能数据。这些功能都大大提高了我们的开发效率和代码质量。

现在，让我们来看一下如何使用 eruda.js。

首先，在你的 HTML 文件中加入以下代码：

```html
<script src="//cdn.jsdelivr.net/npm/eruda"></script>
```

这个代码片段将会从 jsDelivr CDN 中引入 eruda.js 脚本。接着，在页面加载完成后，你需要通过执行以下代码初始化 eruda.js：

```js
eruda.init();
```

这行代码将会启动 eruda.js，并在页面底部添加一个工具栏。点击工具栏上的各个按钮，你可以查看当前页面的元素、控制台、网络请求等信息，以及优化性能和调试代码。

除了默认工具之外，eruda.js 还支持插件扩展。例如，以下代码演示了如何开发和使用一个 eruda.js 插件，用于查看页面上所使用的 JavaScript 库和版本号：

```js
// 定义一个新的eruda.js插件
var JsLibPlugin = eruda.get("JsLib");

if (!JsLibPlugin) {
  JsLibPlugin = eruda.add("JsLib", {
    init: function () {
      var jsLibs = {};

      // 遍历所有script标签，获取url中包含“.js”的链接，并记录其名称和版本号
      Array.prototype.forEach.call(
        document.querySelectorAll("script"),
        function (node) {
          if (node.src.match(/\.js$/)) {
            var name = node.src.split("/").pop().split(".")[0];
            var version = node.getAttribute("data-version") || "1.0.0";
            jsLibs[name] = version;
          }
        }
      );

      // 在控制台输出JavaScript库信息
      console.table(jsLibs);
    },
  });
}

// 启动eruda.js，并启用我们自定义的JsLib插件
eruda.init();
eruda.add(JsLibPlugin);
```

以上代码首先定义了一个新的 eruda.js 插件，名为 JsLib。在该插件的初始化方法中，我们遍历了页面中所有的 script 标签，筛选出其中包含“.js”后缀的脚本文件，并将其名称和版本号保存到一个 jsLibs 对象中。然后，我们通过 console.table()函数在控制台输出了 jsLibs 对象，以便查看当前页面所依赖的 JavaScript 库和版本号。

最后，我们启动了 eruda.js，并将自定义的 JsLib 插件添加到其中。运行以上代码后，在 eruda.js 的控制台中就可以查看当前页面所使用的所有 JavaScript 库及其版本信息了。

总之，eruda.js 是一款非常有用和实用的前端调试工具。如果你是一名前端开发者，我强烈推荐你尝试使用 eruda.js，相信它会为你的工作带来很多便利和惊喜！

<!--
经常会忘记名字，这里记录一下：

```js
    <script src="//cdn.jsdelivr.net/npm/eruda"></script>
    <script>eruda.init();</script>
``` -->
