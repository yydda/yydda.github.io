---
title: 使用 React Portal 实现全局组件 Toast
categories:
  - Code
tags:
  - React
abbrlink: 37517
date: 2022-08-18 17:15:14
---

React 是一种非常受欢迎的前端框架，它为开发人员提供了许多工具和功能来创建高效、可重用、易于维护的应用程序。其中一个功能是 React Portal，它允许我们将组件渲染到 DOM 中的任何位置，甚至是文档的根节点之外。在本篇文章中，我将介绍如何使用 React Portal 实现全局组件 Toast。

## 为什么选择使用 React Portal？

在传统的 React 应用中，所有组件都是在其父组件内部渲染的。这意味着，如果你想要在应用程序的不同部分显示相同的组件，你就必须在每个需要使用该组件的父组件上进行渲染。这可能会导致代码冗余和维护问题。

React Portal 解决了这个问题。它允许我们将组件渲染到 DOM 结构中的任何位置，这使得全局组件的实现变得更加容易和优雅。

## 实现全局组件 Toast

现在，我们来看一下如何使用 React Portal 实现全局组件 Toast。

首先，我们需要创建一个 Toast 组件。Toast 可以是简单的文本信息或者是一个复杂的自定义组件。在这里，我们将创建一个基本的文本 Toast。

```jsx
import React from "react";
import "./Toast.css";

const Toast = ({ message }) => {
  return (
    <div className="toast-container">
      <div className="toast-message">{message}</div>
    </div>
  );
};

export default Toast;
```

这个组件仅仅是渲染传入的 message 属性到 DOM 中，样式文件可自行编写。

接下来，我们需要创建一个包含 Toast 组件的 Portal。为了实现全局组件，我们需要将 Portal 渲染到文档的根节点之外。我们可以通过使用 React 自带的 `ReactDOM.createPortal()` 方法将 Toast 组件渲染到一个新的 DOM 节点中，并将该节点挂载到文档的 body 上。

```jsx
import React from "react";
import ReactDOM from "react-dom";
import "./ToastPortal.css";
import Toast from "./Toast";

const ToastPortal = ({ message }) => {
  const toastContainer = document.createElement("div");
  document.body.appendChild(toastContainer);

  React.useEffect(() => {
    return () => {
      document.body.removeChild(toastContainer);
    };
  }, [toastContainer]);

  return ReactDOM.createPortal(<Toast message={message} />, toastContainer);
};

export default ToastPortal;
```

在这里，我们创建了一个新的根节点 toastContainer，并将其附加到文档的 body 上。我们还使用 `useEffect` 钩子在组件卸载时删除这个节点，以避免内存泄漏。

最后，我们只需要在应用程序的其他组件中使用 `ToastPortal` 即可全局展示 Toast。

```jsx
import React from "react";
import ToastPortal from "./ToastPortal";

const App = () => {
  return (
    <div>
      <button onClick={() => ToastPortal({ message: "Hello, World!" })}>
        Show Toast
      </button>
    </div>
  );
};

export default App;
```

当用户点击按钮时，ToastPortal 组件将被呈现为包含传递给它的 message 属性的新 DOM 元素。由于我们已经将 Portal 渲染到文档的根节点之外，因此该组件将显示在浏览器窗口的顶部。

## 结论

React Portal 是一个非常有用的工具，它允许我们将组件渲染到应用程序中的任何位置，使全局组件的实现更加容易和优雅。在这篇文章中，我介绍了如何使用 React Portal 实现全局组件 Toast，并解释了为什么 Portal 是一个好的选择。希望这篇文章对你有所帮助！
