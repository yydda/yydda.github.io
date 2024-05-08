---
title: 使用 React.forwardRef、useImperativeHandle 实现 Toast Notification Message
categories:
  - Code
tags:
  - React
abbrlink: 63789
date: 2022-08-18 20:13:14
---

在 React 中，通常情况下我们是通过 Props 来传递父组件和子组件之间的数据和方法的。然而在一些特殊场景下，使用 Props 并不能满足我们的需求。例如，在使用第三方库或者类似于 HOC（Higher Order Component）的方式将组件进行封装时，我们可能无法直接修改子组件的 Props。

## 为什么选择使用 React Portal？

这时候，我们可以使用 `React.forwardRef()` 和 `useImperativeHandle()` 来实现对子组件的访问和控制。

`React.forwardRef()` 是一种高级组件技术，它可以将一个函数式组件转换成一个支持 `ref` 属性的组件。在使用 `React.forwardRef()` 时，需要在函数组件的第二个参数中接收 `ref` 对象，并在组件内部使用 `useImperativeHandle()` 显式地定义需要对外暴露的方法。

`useImperativeHandle()` 钩子函数可以让我们有选择性地暴露给使用者某些组件内部的属性和方法，而不用将所有组件内部属性都暴露出来。

在 `useImperativeHandle(ref, createHandle, [deps])` 中，`ref` 是父组件传递进来的引用，`createHandle` 函数返回一个对象，用于向外暴露属性和方法。[deps] 可选参数则用于描述 `createHandle` 函数依赖的变量，当这些变量发生变化时，`createHandle` 函数会被重新执行。

使用 `React.forwardRef()` 和 `useImperativeHandle()` 的组件可以暴露一个对象接口给父组件，这个对象包含了一些方法和属性，用于对子组件进行控制。通过这种方式，我们可以在不破坏 Props 约定的情况下，对组件进行更加细致的控制。

## 实现 Toast Notification

以下是一个使用 React.forwardRef() 和 useImperativeHandle() 实现 Toast Notification 的示例代码：

```tsx
import React, {
  useState,
  useEffect,
  forwardRef,
  useImperativeHandle,
  Ref,
} from "react";

interface ToastProps {
  message: string;
}

export interface ToastNotificationMethods {
  show: (message: string) => void;
  hide: () => void;
}

const ToastNotification = forwardRef(
  (props: ToastProps, ref: Ref<ToastNotificationMethods>) => {
    const [isVisible, setIsVisible] = useState(false);
    const [message, setMessage] = useState("");

    const show = (text: string) => {
      setMessage(text);
      setIsVisible(true);
    };

    const hide = () => {
      setIsVisible(false);
    };

    useImperativeHandle(ref, () => ({
      show,
      hide,
    }));

    useEffect(() => {
      let timer: NodeJS.Timeout;

      if (isVisible) {
        // 显示2秒后自动隐藏
        timer = setTimeout(() => {
          hide();
        }, 2000);
      }

      return () => {
        clearTimeout(timer);
      };
    }, [isVisible]);

    return (
      <div style={{ display: isVisible ? "block" : "none" }}>
        <p>{message}</p>
      </div>
    );
  }
);

export default ToastNotification;
```

上面的代码中，我们首先定义了一个 `ToastProps` 接口，用于描述 `ToastNotification` 组件需要接受哪些属性。接着，我们定义了一个 `ToastNotificationMethods` 接口，用于描述通过 ref 对象向外暴露的 `show()` 和 `hide()` 方法。

然后，我们在函数式组件中使用 `useState()` 方法来定义两个状态：`isVisible` 和 `message`，分别用于记录当前消息是否可见和消息内容。接着，我们定义了 `show()` 和 `hide()` 方法，它们分别用于显示和隐藏消息，并通过 `useImperativeHandle()` 将它们暴露给使用者。

在 `useEffect()` 钩子函数中，我们使用了 `NodeJS.Timeout` 类型来描述计时器对象，避免 TypeScript 报错。同时，我们也为 `useEffect()` 函数添加了依赖项 [isVisible]，以确保每次 `isVisible` 改变时都会重新启动计时器和清除计时器。

最后，我们使用 forwardRef() 方法将 `ToastNotification` 组件转换为一个可以接受 `ref` 参数的组件，接着将 `ToastProps` 和 `ToastNotificationMethods` 接口传递给它，并导出组件。

您可以像之前一样，在其他组件中使用 `ToastNotification` 组件，并通过 `ref` 对象调用其方法：

```tsx
import React, { useRef } from "react";
import ToastNotification, {
  ToastNotificationMethods,
} from "./ToastNotification";

function App() {
  const toast = useRef<ToastNotificationMethods>(null);

  const handleClick = () => {
    if (toast.current) {
      toast.current.show("Hello world!");
    }
  };

  return (
    <>
      <ToastNotification ref={toast} />
      <button onClick={handleClick}>Show Toast</button>
    </>
  );
}

export default App;
```

这里我们也增加了 `ToastNotificationMethods` 类型声明。在 App 组件中，我们使用 `useRef()` 创建了一个 `toast` 对象，并将其类型定义为 `ToastNotificationMethods`。接着，我们在按钮的点击事件中判断 `toast.current` 是否存在，如果存在则调用 `show()` 方法，否则不做任何操作。

## 结论

React.forwardRef 和 useImperativeHandle 是 React 中用来给函数式组件添加 Ref 的两个 Hook。它们使得父组件可以访问到子组件中的 DOM 节点和方法。

React.forwardRef 将一个函数式组件转换成一个支持 ref 属性的组件，通过第二个参数接收 ref 对象，并在组件内部使用 useImperativeHandle 显式地定义需要对外暴露的方法。

useImperativeHandle 钩子函数可以让我们有选择性地暴露给使用者某些组件内部的属性和方法，而不用将所有组件内部属性都暴露出来。

React.forwardRef 和 useImperativeHandle 的使用场景主要是在高阶组件或第三方库中，用来封装一些常见的组件或解决特殊的需求。但是，在一般情况下，我们应该优先考虑使用 Props 来传递数据和方法。

需要注意的是，由于使用了 Ref，这种方式可能会带来一些副作用和性能问题，因此不应该滥用 Ref。

总之，React.forwardRef 和 useImperativeHandle 提供了一种轻便、灵活的方式，用来在函数式组件中添加 Ref 属性，并暴露给父组件子组件内部的属性和方法。
