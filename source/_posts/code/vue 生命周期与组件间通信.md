---
title: vue 生命周期与组件间通信
categories:
  - Code
tags:
  - vue
  - 生命周期
abbrlink: 35436
date: 2020-12-19 17:06:01
updated: 2021-09-19 21:39:14
---

## Vue 生命周期

> [生命周期图示](https://cn.vuejs.org/v2/guide/instance.html#%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%9B%BE%E7%A4%BA)

![image.png](https://blog-1304912906.cos.ap-guangzhou.myqcloud.com/images/image_1629882393948.png?imageMogr2/format/webp)

### Vue 父子组件生命周期执行顺序

#### 加载渲染过程

父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> 子 created -> 子 beforeMount -> 子 mounted -> 父 mounted

父组件先 beforeCreate => created => beforeMount , 然后子组件开始 beforeCreate => created => beforeMount => mounted 子组件挂载完成了，父组件再 mounted。

#### 子组件更新过程

父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated

#### 销毁过程

父 beforeDestroy -> 子 beforeDestroy -> 子 destroyed -> 父 destroyed

## 组件间通信

### 组件间通信的分类

Vue 组件间通信大体可以分为以下几类：

- 父子组件之间的通信
- 兄弟组件之间的通信
- 祖孙与后代组件之间的通信
- 非关系组件间之间的通信

### 组件间通信的方案

父子组件通信：父组件通过 props 的方式向子组件传递数据，而通过 $emit 子组件可以向父组件通信。

#### 通过 props 传递

- 适用场景：父组件传递数据给子组件
- 子组件设置 props 属性，定义接收父组件传递过来的参数
- 父组件在使用子组件标签中通过字面量来传递值

```js
// Children.vue
props:{
    // 字符串形式
    name:String // 接收的类型参数
    // 对象形式
    age:{
        type:Number, // 接收的类型为数值
        defaule:18,  // 默认值为18
       require:true // age属性必须传递
    }
}
```

```js
// Father.vue
<Children name="jack" age="18" />
```

> prop 只可以从上一级组件传递到下一级组件（父子组件），即所谓的单向数据流。而且 prop 只读，不可被修改，所有修改都会失效并警告。

#### $emit 触发自定义事件

- 适用场景：子组件传递数据给父组件
- 子组件通过 $emit 触发自定义事件，$emit 第二个参数为传递的数值
- 父组件绑定监听器获取到子组件传递过来的参数

```js
// Children.vue
this.$emit("add", good);
```

```js
// Father.vue
<Children @add="cartAdd($event)" />
```

#### ref

- 使用场景：父组件获取子组件的实例
- 父组件在使用子组件的时候设置 ref
- 父组件通过设置子组件 ref 来获取数据

```js
// Father.vue
<Children ref="foo" />;

this.$refs.foo; // 获取子组件实例，通过子组件实例我们就能拿到对应的数据
```

#### EventBus

- 使用场景：兄弟组件传值
- 创建一个中央时间总线 EventBus
- 兄弟组件通过 $emit 触发自定义事件，$emit 第二个参数为传递的数值
- 另一个兄弟组件通过 $on 监听自定义事件

```js
// Bus.js
// 创建一个中央时间总线类
class Bus {
  constructor() {
    this.callbacks = {}; // 存放事件的名字
  }
  $on(name, fn) {
    this.callbacks[name] = this.callbacks[name] || [];
    this.callbacks[name].push(fn);
  }
  $emit(name, args) {
    if (this.callbacks[name]) {
      this.callbacks[name].forEach((cb) => cb(args));
    }
  }
}

// main.js
Vue.prototype.$bus = new Bus(); // 将$bus挂载到vue实例的原型上
// 另一种方式
Vue.prototype.$bus = new Vue(); // Vue已经实现了Bus的功能
```

```js
// Children.vue
this.$bus.$emit("foo");
```

```js
// Father.vue
this.$bus.$on("foo", this.handle);
```

#### $parent 或 $ root

- 通过共同祖辈 $parent 或者 $root 搭建通信侨联

兄弟组件

`this.$parent.emit('add')`

另一个兄弟组件

`this.$parent.on('add',this.add)`

#### $attrs 与 $listeners

[$attrs](https://cn.vuejs.org/v2/api/#vm-attrs)

- 适用场景：祖先传递数据给子孙
- 设置批量向下传属性 $attrs 和 $listeners
- 包含了父级作用域中不作为 prop 被识别 （且获取） 的特性绑定 ( class 和 style 除外）。
- 可以通过 v-bind="$attrs" 传⼊内部组件

```js
// child：并未在props中声明foo
<p>{{$attrs.foo}}</p>

// parent
<HelloWorld foo="foo"/>
```

```js
// 给Grandson隔代传值，communication/index.vue
<Child2 msg="lalala" @some-event="onSomeEvent"></Child2>

// Child2做展开
<Grandson v-bind="$attrs" v-on="$listeners"></Grandson>

// Grandson使⽤
<div @click="$emit('some-event', 'msg from grandson')">
{{msg}}
</div>
```

#### provide 与 inject

[provide-inject](https://cn.vuejs.org/v2/api/#provide-inject)

这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在其上下游关系成立的时间里始终生效。如果你熟悉 React，这与 React 的上下文特性很相似。

provide 选项应该是一个对象或返回一个对象的函数。该对象包含可注入其子孙的 property。在该对象中你可以使用 ES2015 Symbols 作为 key，但是只在原生支持 Symbol 和 Reflect.ownKeys 的环境下可工作。

inject 选项应该是：

- 一个字符串数组，或
- 一个对象，对象的 key 是本地的绑定名，value 是：
  - 在可用的注入内容中搜索用的 key （字符串或 Symbol)，或
  - 一个对象，该对象的：
    - from property 是在可用的注入内容中搜索用的 key （字符串或 Symbol)
    - default property 是降级情况下使用的 value

```js
// 父级组件提供 'foo'
var Provider = {
  provide: {
    foo: "bar",
  },
  // ...
};

// 子组件注入 'foo'
var Child = {
  inject: ["foo"],
  created() {
    console.log(this.foo); // => "bar"
  },
  // ...
};
```

#### vuex

- 适用场景：复杂关系的组件数据传递
- Vuex 作用相当于一个用来存储共享变量的容器

![image.png](https://blog-1304912906.cos.ap-guangzhou.myqcloud.com/images/image_1629882509052.png?imageMogr2/format/webp)

- state 用来存放共享变量的地方
- getter，可以增加一个 getter 派生状态，（相当于 store 中的计算属性），用来获得共享变量的值
- mutations 用来存放修改 state 的方法。
- actions 也是用来存放修改 state 的方法，不过 action 是在 mutations 的基础上进行。常用来做一些异步操作

### 小结

- 父子关系的组件数据传递选择 props 与 $emit 进行传递，也可选择 ref
- 兄弟关系的组件数据传递可选择 $bus，其次可以选择 $parent 进行传递
- 祖先与后代组件数据传递可选择 attrs 与 listeners 或者 Provide 与 Inject
- 复杂关系的组件数据传递可以通过 vuex 存放共享的变量
