---
title: 虚拟 DOM 与 diff 算法
categories:
  - Code
tags:
  - 原理
abbrlink: 55509
date: 2017-10-02 22:02:10
updated: 2021-10-02 22:09:06
---

## 虚拟 DOM 实现原理

通过 JS 的 Object 对象来模拟 DOM 中的节点，然后通过特定的 render 方法，将其渲染成真实的 DOM 节点。

dom diff 则是通过 JS 层面的计算，返回 patch 对象（打补丁的方式），通过特定的操作解析 patch 对象，完成页面的重现渲染。

渲染过程如下：

1. 用 JS 模拟 DOM 树，并渲染这个 DOM 树
2. 比较新老 DOM 树，得到比较的差异对象
3. 把差异对象应用到渲染的 DOM 树

## 用 JS 模拟 DOM 结构

以下是真实的 DOM 节点：

```html
<div id="div1" class="container">
  <p>v dom</p>
  <ul style="font-size: 20px">
    <li>a</li>
  </ul>
</div>
```

用 JS 对象来模拟 DOM 节点如下：

```js
{
    tag: 'div',
    props: {
        id: 'div1'
        class: 'container'
    },
    children: [
        {
            tag: 'p',
            children: 'v dom'
        },
        tag: 'ul',
        props: {
            style: 'font-size: 20px'
        },
        children: [
            {
                tag: 'li',
                children: 'a'
            }
        ]
    ]
}
```

用 JS 对象模拟 DOM 节点的好处是，页面的更新可以先全部反映在 JS 对象上，操作内存中的 JS 对象的速度显然要快多了。等更新完后，再将最终的 JS 对象映射成真实的 DOM，交由浏览器去绘制。

## diff 算法

简单的说就是新旧虚拟 DOM 的比较, 如果有差异就以新的为准, 然后再插入的真实的 DOM 中, 重新渲染。

两棵树如果完全比较时间复杂度是 O(n^3)，1000 个节点，要计算 1 亿次，算法不可用，而 O(n^2)以上的复杂度就基本上意味着是不可用的。

依据《深入浅出 React 和 Redux》一书中的介绍，React 的 Diff 算法的时间复杂度是 O(n)。

从 O(n^3)到 O(n)，主要做了以下三件事：

- 只比较统一层级，不跨级比较
- tag 不相同，则直接删掉重建，不再深度比较
- tag 和 key 都相同，则认为是相同节点，不再深度比较

### h 函数

返回的是 vnode(sel, data,children, text, elm, key) 函数，最后返回 vnode 对象。

### patch 函数

tag 不相同，则直接删掉重建，不再深度比较；tag 和 key 都相同，则认为是相同节点，不再深度比较。

涉及的相关操作有 addvodes、removeVnodes、undateChildren。

### someVnode 函数

对比 tag 和 key。

## key 的重要性

无论是 vue 或者 react，当我们遍历数组生成 dom 元素的时候，都会建议我们给每一个 dom 元素加上 key 值，而且 key 值最好用每一项的唯一 id，而不用 index 值(索引)。

key 值是追踪列表中哪些元素被添加、被修改、被移除的辅助标志。通俗点来说，就是它可以帮助我们快速对比两个虚拟 dom 对象，找到虚拟 dom 对象被修改的元素。然后仅仅替换掉被修改的元素，然后再生成新的真实 dom。

如果没有 key 值，就会根据就地复用的原则，一个一个对比，然后修改渲染。

如果 key 值用 index(索引)，假如我在数组中间插入一项的时候，此时从这一项开始的 key 值就全部都变了，都需要重新对比渲染。

如果有 key，diff 算法就可以通过对比找到正确的位置插入新节点，而 key 值相同的 dom 节点就不要去比较。
