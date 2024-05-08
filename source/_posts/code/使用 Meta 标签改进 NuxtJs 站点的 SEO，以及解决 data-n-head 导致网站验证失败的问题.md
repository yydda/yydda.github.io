---
title: 使用 Meta 标签改进 NuxtJs 站点的 SEO，以及解决 data-n-head 导致网站验证失败的问题
categories:
  - Code
tags:
  - vue
  - ssr
  - nuxt
  - seo
abbrlink: 2347
date: 2021-08-16 20:28:37
updated: 2021-09-19 21:33:25
---

这篇文章并非 SEO 指南，它只是一篇简短的文章，让你了解一些基本的 meta 标签和 Nuxt 相关的概念。

## Nuxt Meta

NuxtJs 使每个页面的 Mete 配置变得非常简单，我们可以通过全局引入和单个页面引入两种方式进行配置。

首先是全局配置，在项目根目录的 nuxt.config.js 文件：

```js
module.exports = {
  head: {
    title: "TITLE GOES HERE",
    meta: [
      { charset: "utf-8" },
      { name: "viewport", content: "width=device-width, initial-scale=1" },
      {
        hid: "description",
        name: "description",
        content: "Tips for having a Nuxt site optimized for SEO",
      },
      {
        hid: "keywords",
        name: "keywords",
        content: "vuejs, nuxtjs, seo, meta, sitemap, modules",
      },
    ],
  },
};
```

Title 值直接映射 HTML 的 `<title></title>` ，然后元数组中的每个值分别创建一个个新的 `<meta></meta>` 标记，其中包含相应的 name 和 content 属性。

对应结果是这样的：

```html
<meta data-n-head="true" charset="utf-8" />
<meta
  data-n-head="true"
  name="viewport"
  content="width=device-width, initial-scale=1"
/>
<meta
  data-n-head="true"
  data-hid="description"
  name="description"
  content="Tips for having a Nuxt site optimized for SEO"
/>
<meta
  data-n-head="true"
  data-hid="keywords"
  name="keywords"
  keywords="vuejs, nuxtjs, seo, meta, sitemap, modules"
/>
<title data-n-head="true">TITLE GOES HERE</title>
```

NuxtJs 在其中添加了 data-n-head，官方文档解释说大多数情况下它是非常标准的 meta 标签。但这是个大坑：当我们要在百度和搜狗进行站长认证时，想通过在 meta 标签放验证信息来验证，就会发现总无法验证通过。此处按下不表，文章最后会详细剖析并给出解决方案。

## Hid

这个属性是用来覆盖 meta 标签用的，如果你想用另一个元标签覆盖任何 Meta 标签，这是必需的。**我们可以通过相同的 Hid 匹配全局定义的相同的 meta 标签，以覆盖标签的描述和关键字。**

## 给单个页面定制 meta 标签和 Hid

在单个 vue 页面文件中，有一个名为 head() 的函数负责返回定制的头部数据。

要点如下：

```js
<script type="text/javascript">
  head () {
    let post = this.post
    return {
      title: post.meta.name,
      meta: [
        {
          hid: `description`,
          name: 'description',
          content: post.meta.content
        },
        {
          hid: `keywords`,
          name: 'keywords',
          keywords: post.meta.keywords
        }
      ]
    }
  }
</script>
```

利用这个基本函数，我们需要做的是 post 请求数据并将其返回结果映射到 head 对象中。其中，相同的 Hid 会覆盖全局定义的相同 Hid 的 meta 标签。

[在这里](https://nuxtjs.org/docs/2.x/components-glossary/pages-head#the-head-method)，官方文献对 head 方法进行了更详细的解释。

基本上就是这些了！有了这个基本的设置，你现在可以为每个页面定制 meta 标签。

但有个遇到的问题不吐不快，就是接下来要说的 data-n-head。

## data-n-head="ssr" 导致站点验证失败的坑

当我们想通过“ HTML 标签验证”的方式验证网站的所有权，以完成提交 sitemap、快速收录等来做 SEO 优化。如下图：

![image.png](https://blog-1304912906.cos.ap-guangzhou.myqcloud.com/images/image_1629183198477.png)

如果 Meta 标签被添加了 data-n-head="ssr" 属性，将会提示“找不到验证的 HTML 标签或者验证的 HTML 标签内容错误”。如下图：

![image.png](https://blog-1304912906.cos.ap-guangzhou.myqcloud.com/images/image_1629183442267.png)

看到“问题分析 & 解决办法： 一般是 html 的 meta 标签没有正确放置，注意 meta 标签必需严格使用搜索资源平台提供的形式，不要做任何修改。”这个提示让我一度**怀疑自己的怀疑**，因为相同结构的 meta 标签，google、bing、360、头条、神马等搜索引擎都可以验证通过，就百度和搜狗出现了验证失败问题。

## 寻求解决方案之：data-n-head 可以“一键”删除光吗？

很遗憾，并不能。

最开始我以为可以在 nuxt 的 hooks 里，直接处理编译后的 html 文件。但我发现在我们的项目中，存在以下一段代码：

```js
function getRouteType() {
  var html = document.documentElement;
  var isSpa = !html.hasAttribute("data-n-head-ssr");
  return isSpa ? "spa.enter" : "ssr.enter";
}
```

这里由于我们的项目太大业务十分的复杂，使用了 spa 和 ssr 混用的方式，所以需要利用 'data-n-head-ssr' 来判断当前页面是否是 spa 单页应用。

没办法，只能想在 NuxtJs 和 Vue-meta 的文档和 issue 里看看有没有参数可以配置不要给我的标签（拿出 She is my girlfriend！ 的气势）添加多余的属性，很可惜，最终结论是官网不支持。

在[not data-n-head="true"? #524](https://github.com/nuxt/vue-meta/issues/524)中，vue-meta 的作者给出的方案是“See the docs: [https://vue-meta.nuxtjs.org/api/#callback](https://vue-meta.nuxtjs.org/api/#callback)”，在 callback 中处理 html。对应 NuxtJs 的解决方案是[https://nuxtjs.org/docs/2.x/internals-glossary/internals-generator](https://nuxtjs.org/docs/2.x/internals-glossary/internals-generator)。

## NuxtJs " generate "和" build "打包方式的区别

在利用 Hooks 处理文件之间，需要分清 " generate "和" build "打包方式

" generate "和" build "打包方式主要有两个：文件打包和发布的区别。

在文件打包的区别上，使用 generate 打包后每个对应的页面都会生成一个 html ，在打包的时候不能关闭后台，它会请求后台数据生成首屏的数据。这样打包有一个弊端，当首屏的数据发生更改的时候，他还是显示的是之前的数据，要想改变的话，需要重新打包发布才行。所以，如果首屏是动态的就不建议使用这种打包方式。

而 build 打包生成的是动态页面，会灵活很多，每次 render 的都会是新的数据，这种方式也是具有 SEO 功能的。

在发布的区别上，使用 generate 打包和之前使用 vue 打包一样，生成一个 dist 文件夹，发布操作和普通 vue 项目没有区别。

而 build 打包会生成 .nuxt 文件夹，目录结构类似下图，发布操作会相对复杂一些。
![image.png](https://blog-1304912906.cos.ap-guangzhou.myqcloud.com/images/image_1629190453784.png)

## 站点验证失败的解决方案

在 nuxt.config.js 添加以下代码：

```js
export default {
  hooks: {
    "render:route": (url, result) => {
      result.html = result.html
        .replace(/ data-n-head=".*?"/gi, "")
        .replace(/ data-hid=".*?"/gi, ""); // 此处我另写了正则针对百度和搜狗做了特殊处理，而不是示例这样之间去除全部
    },
  },
};
```

本解决方案更多细节可见：

[Nuxt hooks](https://nuxtjs.org/docs/2.x/internals-glossary/internals-generator)

[Nuxt meta-tags not scraped by Facebook Debugger](https://stackoverflow.com/questions/62155583/nuxt-meta-tags-not-scraped-by-facebook-debugger)

## 总结

NuxtJs 是个很不错的工具，配置 Meta 标签也很方便，不过在兼容百度、搜狗浏览器做 SEO 优化的过程中，发现 data-n-head 的出现且无法通过配置来限制，感觉有种不受控的可怕感觉！当然这种解决方案看起来也很拉，十分的不优雅。其实我认为这个锅应该由国内的搜索引擎来背，meta 标签多了个无关的属性就不给验证通过，是不是有点“人工智障”？（或者说死板？）。

嗯... 发现一黑黑了俩。
