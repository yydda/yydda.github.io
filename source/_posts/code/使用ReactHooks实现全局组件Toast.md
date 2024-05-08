---
title: 使用 React Hooks 实现全局组件 Toast
categories:
  - Code
tags:
  - React
abbrlink: 37554
date: 2022-08-18 16:50:54
---

React 是一种非常流行的 JavaScript 库，广泛用于构建 Web 应用程序。在 React 中，你可以轻松地创建自定义组件，其中一种非常有用的组件是 Toast 组件。

Toast 组件通常用于显示短暂的消息或警告，例如在提交表单时显示成功或失败消息。因此，它应该是可以全局访问的，而不需要将其放置在每个组件中。

在这篇文章中，我将向你展示如何使用 React Hooks 来实现全局 Toast 组件。

## 步骤 1：创建 Context

首先，我们需要创建一个 Context，将 Toast 组件与其他组件进行通信。在 createContext 方法中传递一个初始值，这样我们可以避免访问未定义的属性。

```javascript
import React, { createContext } from "react";

export const ToastContext = createContext({ showToast: () => {} });
```

## 步骤 2：创建 Toast 组件

下一步是创建 Toast 组件本身。在本示例中，我们将简单地渲染一些文本。

```javascript
import React from "react";

const Toast = ({ message }) => {
  return (
    <div className="toast">
      <p>{message}</p>
    </div>
  );
};

export default Toast;
```

## 步骤 3：创建全局 ToastProvider

接下来，我们需要一个全局 ToastProvider 组件，它将包装整个应用程序并提供 showToast 方法以触发 Toast 组件。使用 useReducer 钩子管理 Toast 的状态。

```javascript
import React, { useReducer } from "react";
import { ToastContext } from "./context";
import Toast from "./Toast";

const initialState = {
  message: "",
  visible: false,
};

const reducer = (state, action) => {
  switch (action.type) {
    case "SHOW":
      return {
        message: action.payload.message,
        visible: true,
      };
    case "HIDE":
      return {
        ...state,
        visible: false,
      };
    default:
      return state;
  }
};

const ToastProvider = ({ children }) => {
  const [state, dispatch] = useReducer(reducer, initialState);

  const showToast = (message) => {
    dispatch({ type: "SHOW", payload: { message } });
    setTimeout(() => {
      dispatch({ type: "HIDE" });
    }, 3000);
  };

  return (
    <ToastContext.Provider value={{ showToast }}>
      {children}
      {state.visible && <Toast message={state.message} />}
    </ToastContext.Provider>
  );
};

export default ToastProvider;
```

## 步骤 4：在应用程序中使用 Toast

现在，我们已经创建了 Toast 组件和全局 ToastProvider，我们可以在整个应用程序中使用它。只需将组件包装在 ToastProvider 中即可。

```javascript
import React from "react";
import ReactDOM from "react-dom";
import ToastProvider from "./ToastProvider";
import App from "./App";

ReactDOM.render(
  <React.StrictMode>
    <ToastProvider>
      <App />
    </ToastProvider>
  </React.StrictMode>,
  document.getElementById("root")
);
```

现在，我们可以从其他组件中调用 showToast 方法来触发 Toast 组件。

```javascript
import React, { useContext } from "react";
import { ToastContext } from "./context";

const ExampleComponent = () => {
  const { showToast } = useContext(ToastContext);

  const handleSubmit = () => {
    // Some form submission logic

    if (submissionSuccessful) {
      showToast("Form submitted successfully!");
    } else {
      showToast("Form submission failed :(");
    }
  };

  return (
    <div>
      <form onSubmit={handleSubmit}>
        {/* Form inputs */}
        <button type="submit">Submit</button>
      </form>
    </div>
  );
};

export default ExampleComponent;
```

## 结论

在本文中，我们使用 React Hooks 创建了一个全局 Toast 组件。通过创建 Context、Toast 组件以及全局 ToastProvider，并将其包装在应用程序的最外层，我们可以轻松地从其他组件调用 showToast 方法来触发 Toast 组件。这使得显示短暂消息或警告的过程变得简单而方便，而不需要在每个组件中都添加一个单独的 Toast 组件。

这种方法具有可扩展性，你可以根据需要自定义 Toast 组件的样式和持续时间。希望这篇文章对你有所帮助。
