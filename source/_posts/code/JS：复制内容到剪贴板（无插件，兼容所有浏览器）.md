---
title: JS：复制内容到剪贴板（无插件，兼容所有浏览器）
categories:
  - Code
tags:
  - js
abbrlink: 20301
date: 2020-12-16 14:53:29
updated: 2021-09-19 21:39:34
---

## HTML 部分

```html
<button onclick="copyToClip('内容')">Copy</button>
```

## JS 部分

```js
/**
 * 复制单行内容到粘贴板
 * content : 需要复制的内容
 * message : 复制完后的提示，不传则默认提示"复制成功"
 */
function copyToClip(content, message) {
  var aux = document.createElement("input");
  aux.setAttribute("value", content);
  document.body.appendChild(aux);
  aux.select();
  document.execCommand("copy");
  document.body.removeChild(aux);
  if (message == null) {
    alert("复制成功");
  } else {
    alert(message);
  }
}
```

### 【补充】

```js
/**
 * 复制多行内容到粘贴板
 * contentArray: 需要复制的内容（传一个字符串数组）
 * message : 复制完后的提示，不传则默认提示"复制成功"
 */
function copyToClip(contentArray, message) {
  var contents = "";
  for (var i = 0; i < contentArray.length; i++) {
    contents += contentArray[i] + "\n";
  }
  const textarea = document.createElement("textarea");
  textarea.value = contents;
  document.body.appendChild(textarea);
  textarea.select();
  if (document.execCommand("copy")) {
    document.execCommand("copy");
  }
  document.body.removeChild(textarea);
  if (message == null) {
    alert("复制成功");
  } else {
    alert(message);
  }
}
```
