---
title: 前端基础知识点梳理-JS篇
abbrlink: 54080
date: 2021-08-08 01:50:53
updated: 2021-08-15 21:12:12
categories:
  - Code
tags:
  - js
---

## 变量类型和计算

### 值类型&&引用类型

- 引用类型：堆栈关系
  - object
  - array
  - null（特殊引用类型，指针指向为空地址）
  - 函数（特殊引用类型，但不用于存储数据，所以没有"拷贝、复制函数"这一说）
- 值类型：
  - undefined
  - String
  - number
  - boolean
  - symbol

### typeof 能判断什么类型？

- 识别所有值类型
- 识别函数
- 判断是否是引用类型（不可再细分）--全为 object

### 深拷贝

```javascript
function deepClone(obj) {
	if(typeof obj !== 'object' || obj == null) {
		reture obj
	}

	let result
	if (obj instanceof Array) {
		result = []
	} else {
		result = {}
	}

	for(let key in obj) {
		if (obj.hasOwnProperty(key)) {
			result[key] = deepClone(obj[key])
		}
	}

	return result
}
```

### 何时使用===何时使用==

### 变量计算-类型转换

- 字符串拼接

  ```javascript
  const a = 100 + 10; // 110
  const b = 100 + "10"; // '10010'
  const c = true + "10"; // 'true10'
  ```

- ==运算符

  ```javascript
  // 除了 == null 之外，其他一律中 ===， 例如
  const obj = { x: 100 };
  if (obj.a == null) {
  }

  // 相当于：
  // if (obj.a === null || obj.a === undefined) {}
  ```

- if 语句和逻辑运算

  - 判断的是 truly 或 falsely
  - truly 变量： !!a === true
  - falsely 变量：!!a === false

  ```javascript
  // 以下是falsely变量。除此之外都是truly变量
  !!0 === false;
  !!"" === false;
  !!NaN === false;
  !!null === false;
  !!undefined === false;
  !!false === false;
  ```

## 原型和原型链

js 基于原型来继承

### 题目

- 如何准确判断一个变量是不是数组？

- 手写一个简易的 jQuery，考虑插件和扩展性

- class 的原型本质，怎么理解？

### 知识点

#### class 和继承

> 面向对象的方法

- class

  - constructor
  - 属性
  - 方法

  ```javascript
  // 类
  class Student {
    constructor(name, number) {
      this.name = name;
      this.number = number;
    }
    sayHi() {
      console.log(`姓名 ${this.name}, 学号 ${this.number}`);
    }
  }

  // 通过类声明对象/实例
  const xialuo = new Student("夏洛", 100);
  console.log(xialuo.name);
  console.log(xialuo.number);
  xialuo.sayHi();
  ```

- 继承

  - extends

  ```js
  // 父类
  class People {
    constructor(name) {
      this.name = name;
    }
    eat() {
      console.log(`${this.name} eat something`);
    }
  }

  // 子类
  class Student extends People {
    constructor(name, number) {
      super(name);
      this.number = number;
    }
    sayHi() {
      console.log(`姓名 ${this.name} 学号 ${this.number}`);
    }
  }

  class Teacher extends People {
    constructor(name, major) {
      super(name);
      this.major = major;
    }
    teach() {
      console.log(`${this.name} 教授 ${this.major}`);
    }
  }

  const xialuo = new Student("夏洛", 100);
  console.log(xialuo.name);
  console.log(xialuo.number);
  xialuo.sayHi();
  xialuo.eat();
  const wanglaoshi = new Teacher("王老师", "语文");
  ```

- instanceof 怎么写？

  - 类型判断 - instanceof

  ```js
  xialuo instanceof Student // true
  xialuo instanceof People // true
  xialuo instanceof Object // true Object是所有引用类型的父类（js引擎做的）

  [] instanceof Array // true
  [] instanceof Object // true

  {} instanceof Object // true

  ```

  - instanceof 为匹配最近一层的显示原型

- 原型和原型链

  - 原型

    ```js
    // class 实际上是函数，可见是语法糖
    typeof People; // 'function'
    typeof Student; // 'function'

    // 隐式原型和显示原型
    console.log(xialuo.__proto__); // 隐式
    console.log(Student.prototype); // 显示
    console.log(xialuo.__proto__ === Student.prototype);
    ```

    ![image-20210806231255234](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210806231255234.png)

    - 原型关系
      - 每个 class 都有显示原型`prototype`
      - 每个实例都有隐式原型`__proto__`
      - 实例的`__proto__`指向对应 class 的`prototype`
    - 基于原型的执行规则
      - 获取属性 xialuo.name 或执行方法 xialuo.sayHi()时
      - 先在自身属性和方法寻找
      - 如果找不到则自动去`__proto__`中查找

#### 原型链

![image-20210806232121897](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210806232121897.png)

![image-20210806232251151](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210806232251151.png)

#### 题目解答

- 如何准确判断一个变量是不是数组？

  - a instanceof Array

- 手写一个简易的 jQuery，考虑插件和扩展性

  ```js
  class jQuery {
    constructor(selector) {
      // dom选择器
      const result = document.querySelectorAll(selector);
      const length = result.length;
      for (let i = 0; i < length; i++) {
        this[i] = result[i];
      }
      this.length = length;
    }
    get(index) {
      return this[index];
    }
    each(fn) {
      for (let i = 0; i < this.length; i++) {
        const elem = this[i];
        fn(elem);
      }
    }
    on(type, fn) {
      return this.each((elem) => {
        elem.addEventListener(type, fn, false);
      });
    }
    // 更多扩展dom操作api
  }

  // 插件和扩展性
  // 方式一：原型链
  jQuery.prototype.dialog = function (info) {
    alert(info);
  };
  //方式二：“造轮子”--复写
  class myJQuery extends jQuery {
    constructor(selector) {
      super(selector);
    }
    // 扩展自己的方法
    addClass(calssName) {}
    style(data) {}
  }

  // const $p = new jQuery('p')
  ```

- class 的原型本质，怎么理解？

  - 原型和原型链的图示
  - 属性和方法的执行规则

### 作用域和闭包

#### 题目

- this 的不同应用场景，如何取值？
- 手写 bind 函数
- 实际开发中闭包的应用场景，举例说明
- ![image-20210807000358876](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210807000358876.png)

#### 知识点

##### 作用域和自由变量

- 作用域

  ![image-20210807000718726](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210807000718726.png)

  - 全局作用域

  - 函数作用域

  - 块级作用域（ES6 新增）

    ![image-20210807000832167](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210807000832167.png)

- 自由变量

  - 一个变量在当前作用域没有定义，但被使用了
  - 向上级作用域，一层层依次查找，直到找到为止
  - 如果在全局作用域都没找到，则报错 xx is not defined

##### 闭包

> 闭包：自由变量的查找，是在函数定义的地方，向上级作用域查找，而不是在执行的地方

- 作用域应用的特殊情况，有两种表现：
- 函数作为参数被传递
- 函数作为返回值被返回
- ![image-20210807001251201](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210807001251201.png)

##### this

> - 场景多
> - this 取什么值是在执行的时候确定的，而不是在定义的时候确定的

- 作为普通函数

- 使用 call apply bind

- 作为对象方法被调用

- 在 class 方法中调用

- 箭头函数

  ```js
  function fn1() {
    console.log(this);
  }
  fn1(); // window

  f1.call({ x: 100 }); // { x: 100 }

  const fn2 = fn1.bind({ x: 200 }); // bind会返回新的函数去执行
  fn2(); // { x: 200 }
  ```

  ![image-20210807002419812](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210807002419812.png)

##### 原型中的 this

#### 题目解答

- this 的不同应用场景，如何取值？

- 手写 bind 函数

  > 扩展考法：手写 call、apply

  ```js
  Function.prototype.bind1 = function () {
    // arguments是列表，不是数组
    // Array.prototype.slice.call可以将列表转为数组
    const args = Array.prototype.slice.call(arguments);

    // 获取第一项，为要绑定的作用域
    const t = args.shift();

    // 谁调用，指向谁
    const self = this;

    // bind是返回一个可执行的参数
    return function () {
      // 返回执行结果
      return self.apply(t, args);
    };
  };

  function fn1(a, b, c) {
    console.log("this", this);
    console.log(a, b, c);
    return "this is fn1";
  }

  const fn2 = fn1.bind1({ x: 100 }, 10, 20, 30);
  const res = fn2();
  console.log(res);
  ```

- 实际开发中闭包的应用场景，举例说明

  - 隐藏数据

  - 如做一个 cache 工具

    ```js
    // 闭包隐藏数据，只提供api
    function createCache(key, val) {
      const data = {}; // 闭包中的数据，被隐藏，不被外界访问
      return {
        get: function (key) {
          return data[key];
        },
        set: function (key, val) {
          data[key] = val;
        },
      };
    }

    const c = createCache();
    c.set("a", 100);
    c.get("a");
    ```

- ![image-20210807000358876](/Users/yeyuanda/Library/Application Support/typora-user-images/image-20210807000358876.png)

### 异步和单线程

#### 题目

- 同步和异步的区别是什么？
- 手写用 Promise 加载一张图片
- 前端使用异步的场景有什么？
- ![image-20210807134212103](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210807134212103.png)

#### 知识点

- 单线程和异步

  - js 是单线程语言，只能同时做一件事
  - 浏览器和 nodejs 已支持 js 启动进程，如 web worker
  - js 和 dom 渲染共用同一个线程，因为 js 可修改 dom 结构
  - 遇到等待（网络请求，定时任务）不能卡住
  - 需要异步
  - callback 回调函数形式

- 异步和同步

  - 基于 js 是单线程语言
  - 异步不会阻塞代码执行
  - 同步会阻塞代码执行

- 应用场景

  - 网络请求，如 ajax、图片加载

    ```js
    // 图片加载
    console.log("start");
    let img = document.createElement("img");
    img.onload = function () {
      console.log("loaded");
    };
    img.src = "/xxx.png";
    console.log("end");
    ```

  - 定时任务，如 setTimeout

- callback hell 和 promise

  - ![image-20210807141921795](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210807141921795.png)
  - ![image-20210807142045229](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210807142045229.png)
  - ![image-20210807142157823](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210807142157823.png)

#### 题目解答

- 同步和异步的区别是什么？

  - 基于 js 是单线程语言
  - 异步不会阻塞代码执行
  - 同步会阻塞代码执行

- 手写用 Promise 加载一张图片

  ```js.
  function loadimg(src) {
  	return new Promise((resolve, reject) => {
  		const img = document.createElement('img')
  		img.onload = () => {
  			resolve(img)
  		}
  		img.onerror = () => {
  			const err = new Error('图片加载失败')
  			reject(err)
  		}
  		img.src = src
  	})
  }

  const url = 'https://www.imooc.com/static/img/index/logo2020.png'
  loadimg(url).then(img => {
  	console.log(img.width)
  	// 作为下一个then的参数传入
  	return img
  }).then(img => {
  	console.log(img.height)
  }).catch(err => console.error(err))

  const url1 = '1.png'
  const url2 = '2.png'
  loadimg(url1).then(img1 => {
  	console.log(img1.width)
  	return img1 // 普通对象
  }).then(img1 => {
  	console.log(img1.height)
  	return loadimg(url2) // Promise 实例
  }).then(img2 => {
  	console.log(img1.width)
  })
  ```

- 前端使用异步的场景有什么？

  - 网络请求，如 ajax 图片加载
  - 定时任务，如 setTimeout

#### js 异步-进阶

##### 题目

- 请描述 event loop（事件循环/事件轮询）的机制，可画图
- 什么事宏任务和微任务，两者有什么区别？
- Promise 有哪三种状态？怎么变化？
- 场景题-![image.png](https://blog-1304912906.cos.ap-guangzhou.myqcloud.com/images/image_1628349801714.png)![image-20210807232232027](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210807232232027.png)
- ![image.png](https://blog-1304912906.cos.ap-guangzhou.myqcloud.com/images/image_1628349900583.png)
- ![image.png](https://blog-1304912906.cos.ap-guangzhou.myqcloud.com/images/image_1628350168074.png)
- 场景题-外加 async/await 的顺序问题

![image-20210807233158019](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210807233158019.png)

##### event loop（事件循环/事件轮询）的机制

- js 是单线程运行的
- 异步要基于回调来实现
- event loop 就是异步回调的实现原理

###### js 如何执行？

- 从前到后，一行行执行

- 如果某一行执行报错，则停止下面代码的执行

- 先把同步代码执行完，再执行异步

  ```js
  console.log("Hi");

  setTimeout(function cb1() {
    console.log("cb1");
  }, 5000);

  console.log("Bye");
  ```

  - Browser console
  - Call Stack（调用栈）
  - Web APIs（setTimeout、dom 操作、bom 操作之类，浏览器定义的相关的 api）
  - Callback Queue（回调函数的队列）-->event loop

##### 总结 event loop 过程

- 同步代码，一行一行放在 Call Stack 执行
- 遇到异步，会先“记录”下，等待时机（定时、网络请求等）
- 时机到了，就移动到 Callback Queue
- 如 Call Stack 为空（即同步代码执行完），Event Loop 开始工作
- 轮询查找 Call Queue，如有则移动到 Call Stack 执行
- 然后继续轮询查找（永动机一样）

##### DOM 事件和 event loop

- js 是单线程的
- 异步（setTimeout， ajax 等）使用回调，基于 event loop
- DOM 事件也使用回调，基于 event loop
- 和 setTimeout 的触发时机不一样，DOM 事件不是异步

![image-20210808001336360](/Users/yeyuanda/Documents/blog/blog/Test.assets/image-20210808001336360.png)

#### Promise

##### 三种状态

- pending resolved rejected
- pending --> resolved 或 pending --> rejected
- 状态不可逆

##### 状态的表现和变化

- pending 状态，不会触发 then 和 catch
- resolved 状态，会触发后续的 then 回调函数
- rejected 状态，会触发后续的 catch 回调函数

##### then 和 catch 对状态的影响

- then 正常返回 resolved，里面有报错则返回 rejected

- catch 正常返回 resolved，里面有报错则返回 rejected

  ```js
  // then正常返回resolved，里面有报错则返回rejected [start]
  const p1 = Promise.resolve().then(() => {
    return 100;
  });
  console.log("p1", p1); // resolved 触发后续 then 回调函数
  p1.then(() => {
    console.log("123"); // 执行
  });

  const p2 = Promise.resolve().then(() => {
    throw new Error("then error");
  });
  console.log("p2", p2); // rejected 触发后续 catch 回调函数
  p2.then(() => {
    console.log("456"); // 不执行
  }).catch((err) => {
    console.error("err", err); // 执行
  });
  // then正常返回resolved，里面有报错则返回rejected [end]

  // catch正常返回resolved，里面有报错则返回rejected
  const p3 = Promise.reject("my error").catch((err) => {
    console.error(err);
  });
  console.log("p3", p3); // resolved 注意⚠️ 触发then回调
  p3.then(() => {
    console.log(100); // 可以执行
  });

  const p4 = Promise.reject("my error").catch((err) => {
    throw new Error("catch error");
  });
  console.log("p4", p4); // rejected 触发catch回调
  p4.then(() => {
    console.log(200); // 不执行
  }).catch(() => {
    console.log("some err");
  }); // resolved
  ```

## async/await

### 基本语法和应用

- 异步回调 callback hell

- Promise then catch 链式调用，但也是基于回调函数

- async/await 是同步语法，彻底消灭回调函数

  ```js
  !(async function () {
    const img1 = await loadImg(src1);
    console.log(img1.width);
  })();
  ```

### async/await 和 Promise 的关系

- async/await 是消灭异步回调的终极武器
- 但和 Promise 不互斥
- 反而，两者相辅相成

- 执行 async 函数，返回的是 Promise 对象

- await 相当于 Promise 的 then

- try...catch 可捕获异常，代替了 Promise 的 catch

  ```js
  // async 返回Promise对象
  async function fn1() {
  	return 100 // 相当于return Promise.resolve(100)
  }

  const res1 = fn1() // 执行async函数，返回的是一个Promise对象
  res1.then((data) => {
  	console.log('data', data) // 100
  })

  // -----
  // await相当于Promise then
  !(function() {
    const p1 = Promise.resolve(300)
    const data = await p1 // await相当于Promise then
    console.log('data', data) // 300
  })()

  !(function() {
    const p1 = Promise.resolve(300)
    const data = await 400 // 相当于await Promise.resolve(400)
    console.log('data', data) // 400
  })()

  // ------
  // try...catch相当于
  !(function () {
    const p4 = Promise.reject('err1') // rejected 状态
    try {
      const res = await p4
      console.log(res)
    } catch(ex) {
      console.error(ex) // try...catch 相当于 promise catch
    }
  })()
  ```

#### 异步的本质

- async/await 是消灭异步回调的终极武器
- js 还是单线程，还得是有异步，还得是基于 event loop
- async/await 只是一个语法糖

#### for...of

- for...in(forEach for)是常规的同步遍历

- for...of 常用于异步的遍历

  ```js
  function muti(num) {
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve(num * num);
      }, 1000);
    });
  }

  const nums = [1, 2, 3];

  // 要1s之后才同时打印出1、4、9
  nums.forEach(async (i) => {
    const res = await muti(i);
    console.log(res);
  });

  // "排队"的效果
  !(async function () {
    for (let i of nums) {
      const res = await muti(i);
      console.log(res);
    }
  })();
  ```

### 宏任务 macroTask 和微任务 microTask

#### 什么是宏任务，什么是微任务

- 宏任务：setTimeout，setInterval，Ajax，DOM 事件
- 微任务：Promise，async/await
- **微任务执行时机比宏任务早**

#### event loop 和 DOM 渲染

- 每次 call stack 清空（即每次轮询结束），即同步任务执行完
- 都是 DOM 重新渲染的机会，DOM 结构如有改变则重新渲染
- 然后再去触发下一次 Event Loop

- ![image-20210808205437375](/Users/yeyuanda/Documents/blog/blog/前端基础知识点梳理-JS篇.assets/image-20210808205437375.png)

#### 宏任务和微任务的区别

- 宏任务：DOM 渲染后触发，如 setTimeout
- 微任务：DOM 渲染前触发，如 Promise

#### 从 event loop 解释，为何微任务执行更早

- 微任务是 ES6 语法规定的
- 宏任务是由浏览器规定的

![image-20210808210607821](/Users/yeyuanda/Documents/blog/blog/前端基础知识点梳理-JS篇.assets/image-20210808210607821.png)

![image-20210808210715015](/Users/yeyuanda/Documents/blog/blog/前端基础知识点梳理-JS篇.assets/image-20210808210715015.png)
