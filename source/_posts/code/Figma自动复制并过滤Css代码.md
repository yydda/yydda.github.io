---
title: Figma 自动复制并过滤 Css 代码
abbrlink: 42777
date: 2021-10-28 10:27:38
categories:
  - Code
tags:
  - Css
  - Figma
---

使用方法：、

1. 复制以下代码
2. 打开浏览器控制台粘贴后回车运行
3. 在 Figma 设计稿中选择元素，就会自动复制并过滤当前元素 Css 代码

```js
function getMiniCss(rawStr) {
  result = rawStr.replace(/([\d\.]+)px/g, (pxStr, number) => {
    return number * 2 + "rpx";
  });

  let str = ``;
  [
    "width",
    "height",
    "font-weight",
    "font-size",
    "color",
    "line-height",
    "border",
    "border-radius",
    "background",
    "background-color",
  ].forEach((key) => {
    if (key === "font-weight") {
      const fontWeightRegx = /font-weight:\s(\d+)/m;
      const fontWeightMatch = result.match(fontWeightRegx);
      if (fontWeightMatch && fontWeightMatch[1] === "500") {
        str += `font-weight: 700;\n`;
      }
    } else {
      const match = result.match(new RegExp(`^${key}:.+;`, "m"));
      if (match) {
        str += match[0] + "\n";
      }
    }
  });

  console.log(str);
  str = str.trim().replace(/[^\S]$/gm, "");
  return str;
}

function setClipboardText(event) {
  var result = window.getSelection(0).toString();

  if (result.match(/[a-z-]+:\s.+;/)) {
    event.preventDefault();
    str = getMiniCss(result);
    if (event.clipboardData) {
      // event.clipboardData.setData("text/html", htmlData);
      event.clipboardData.setData("text/plain", str);
    } else if (window.clipboardData) {
      return window.clipboardData.setData("text", str);
    }
  }
}
if (typeof setClipboardText !== undefined) {
  document.removeEventListener("copy", setClipboardText);
}
document.addEventListener("copy", setClipboardText);
```
