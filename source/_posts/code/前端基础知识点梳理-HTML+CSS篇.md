---
title: 前端基础知识点梳理-HTML+CSS篇
abbrlink: 40579
date: 2021-08-02 00:19:25
updated: 2021-08-15 21:12:42
categories:
  - Code
tags:
  - html
  - css
---

高效学习：找准知识体系；刻意练习；及时反馈。

## HTML

### 如何理解 HTML 语义化？

- 让人更易读懂（增加代码可读性）
- 让搜索引擎更易读懂（SEO）

### 哪些标签是块级元素，哪些是内联元素？

- display:block/table;有 div h1 h2 table p ul ol li 等
- display:inline/inline-block;有 span label i input button 等

---

## CSS

### 布局

#### 盒子模型的宽度如何计算？

- offsetWidth=（内容宽度+padding+边框）,无外边距
- box-sizing:border-box;

#### magin 纵向重叠的问题

- 相邻元素的 margin-top 和 margin-bottom 会发送重叠
- 空白内容的`<p></p>`也会重叠

#### margin 负值的问题

- margin-top 和 margin-left 负值，元素向上/向左移动
- margin-right 负值，右侧元素左移，自身不受影响
- margin-bottom 负值，下侧元素上移，自身不受影响

#### BFC 的理解和应用

- Block format context，块级格式化上下文
- 一块独立渲染区域，内部元素的渲染不会影响边界以外的元素
- 形成 BFC 的常见条件：
  - float 不是 none
  - position 是 absolute 或 fixed
  - overflow 不是 visible
  - display 是 flex inline-block 等
- BFC 的常见应用
  - 清除浮动

#### float 布局的问题，以及 clearfix

- 圣杯布局和双飞翼布局

1. 特点
   - 三栏布局，中间一栏先加载和渲染（内容最重要）
   - 两侧内容固定，中间内容随宽度自适应
   - 一般用于 PC 网页
2. 实现方式
   - 使用 float 布局
   - 两侧使用 margin 负值，以便和中间内容横向重叠
   - 防止中间内容被两侧覆盖，一个用 padding 一个用 margin

- 手写 clearfix

```css
.clearfix:after {
  content: "";
  display: table;
  clear: both;
}
.clearfix {
  *zoom: 1; // ie
}
```

#### flex 画色子

- flex-direction 方向
- justify-content 水平对齐
- align-items 水平对齐
- flex-wrap 换行
- align-self 子元素

### 定位

#### absolute 和 relative 分别依据什么定位？

- relative 依据自身定位
- absolute 依据最近一层的定位元素（absolute relative fixed body）定位

#### 居中对齐有哪些实现方式？

- text-align: center;
- margin: 0 auto;

- lineheight 等于 height
- absolute 和 margin
- absolute 和 transform

### 图文样式

#### line-height 的继承问题

- 写具体数值，则继承该值
- 写比例，也继承该比例
- 写百分百则继承计算出来的值

### 响应式

#### rem 是什么？

- 长度单位，相对于根元素，常用于响应式布局

#### 如何实现响应式

- 媒体查询，media-query，根据不同屏幕宽度设置根元素 font-size
- js 动态设置根元素 font-size

#### vw/vh

- rem 弊端:“阶梯”性
- 网页视口尺寸
- vw 网页视口宽度的 1/100
- vmax 取两者最大值；vmin 取两者最小值
