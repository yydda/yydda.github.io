---
title: ES6 字符串和正则表达式
categories:
  - Code
tags:
  - js
  - es6
abbrlink: 51792
date: 2017-07-17 20:27:20
updated: 2021-09-19 21:41:19
---

## 更好的 Unicode 支持

### UTF-16 码位

- UTF-16 引入了代理对（surrogate pair），其规定用两个 16 位编码单元表示一个码位。也就是说，字符串里的字符有两种，一种是由一个编码单元 16 位表示的 BMP 字符，一种是由两个编码单元 32 位表示的辅助平面字符。
- ES6 强制使用 UTF-16 字符串编码，并按照这种字符编码来标准化字符串操作，在 JavaScript 中增加了专门针对代理对的功能。

### codePointAt() 方法

codePointAt() 方法接受编码单元的位置而非字符串的位置作为参数，返回与字符串中给定位置对应的**码位**，既一个整数值。

对于 BMP 字符集中的字符，codePointAt() 和 charCodeAt() 方法的相同，而对于非 BMP 字符集来说返回值则不同。charCodeAt() 方法返回的只是位置 0 处的第一个编码单元，而 codePointAt() 返回完整的码位，即使这个码位包含多个编码单元。

要检测一个字符占用的编码单元数量，最简单的方法是调用字符的 codePointAt() 方法：

```JavaScript
function is32Bit(c) {
    return c.codePointAt(c) > 0xFFFF
}

console.log('a')    // false
```

> 用 16 位表示的字符集上界为十六进制 FFFF，所以超过这个上界的码位一定由两个编码单元来表示，总共 32 位。

### String.fromCodePoint() 方法

String.fromCodePoint() 方法根据指定的码位生成一个字符：

```JavaScript
console.log(String.fromCodePoint(134071))    // '𠮷'
```

可以将 String.fromCodePoint() 看成是完整版的 String.fromCharCode()。对应 BMP 中所有的字符，两方法执行结果相同。只有传递非 BMP 字符的码位作为参数，二者的执行结果才有可能不同。

### normalize() 方法（国际化应用中常使用）

normalize() 方法提供 Unicode 的标准化形式：

1. 以标准等价方式分解，然后以标准等价方式重组 ('NFC'), 默认选项。
2. 以标准等价方式分解 ('NFD')。
3. 以兼容等价方式分解 ('NFKC')。
4. 以兼容等价方式分解，然后以标准等价方式重组 ('NFKD')。

**需牢记：在对比字符串之前，一定要先把它们标准化为同一种形式：**

```JavaScript
let normalized = values.map(function(text) {
    retrun text.normalize()
})

nomralized.sort(funtion(first, second) {
    if (first < second) {
        return -1
    } else if (first === second) {
        return 0
    } else {
        return 1
    }
})
```

如果相对原始数组进行排序，可以在比较函数中添加 normalize() 方法：

```JavaScript
values.sort(funtion(first, second) {
    let firstNormalized = first.normalize(),
        secondNormalized = second.normalize()

    if (firstNormalized < secondNormalized) {
        return -1
    } else if (firstNormalized === secondNormalized) {
        return 0
    } else {
        return 1
    }
})
```

### 正则表达式 u 修饰符

解决默认将字符串中的每个字符按照 16 位编码单元处理的问题。

#### u 修饰符实例

当一个正则表达式添加了 u 修饰符时，它就从编码单元操作模式切换为字符模式，如此一来正则表达式就不会视代理对为两个字符。

```JavaScript
let text = '𠮷'

console.log(text.length)    // 2
console.log(/^.$/.test(text))    // false /^.$/匹配所有单字符字符串，'𠮷'由两个编码单元组成
console.log(/^.$/u.test(text))    // true
```

#### 计算码位数量

es6 检测字符串的 length 属性仍然返回字符串编码单元的数量。可以通过正则表达式的 u 修饰符来解决：

```JavaScript
function codePointlength(text) {
    let result = text.match(/[\s\S]/gu)
    return result ? result.length : 0
}

console.log(codePointlength('abc'))    // 3
console.log(codePointlength('𠮷bc'))    // 3
```

> 此方法有效，但运行效率低（可以通过字符串迭代器来解决）

#### 检测 u 修饰符支持

检测当前浏览器是否支持 u 修饰符，最安全的方式是通过以下函数：

```JavaScript
function hasRegExpU() {
    try {
        var pattern = new RegExp('.', 'u')
        return true
    } catch (ex) {
        return false
    }
}
```

## 其他字符串变更

### 字符串中的子串识别

**indexOf() 拓展：**

- includes() 方法，如果在字符串中检测到指定文本则返回 true，否则返回 false
- startsWith() 方法，从字符串起始位置检测
- endsWith() 方法，从字符串结束部分检测

以上 3 个方法都接收两个参数 ==> ('要搜索的文本', '开始搜索的位置的索引')

```JavaScript
let msg = 'Hello World!'

console.log(msg.startsWith('Hello'))    // true
console.log(msg.endsWith('!'))    // true
console.log(msg.includes('o'))    // true

console.log(msg.startsWith('o'))    // false
console.log(msg.endsWith('World!'))    // true
console.log(msg.includes('x'))    // false

console.log(msg.startsWith('o', 4))    // true
console.log(msg.endsWith('o', 8))    // true
console.log(msg.includes('o', 8))    // false
```

> 这三个方法如果没有按照要求传入字符串，而是传入正则表达式，会触发错误。indexOf() 和 lastIndexOf() 则不会，它们会把传入的正则表达式当作字符串并搜索它。

#### repeat() 方法

repeat() 方法接受一个 number 类型的参数，表示字符串重复的次数，返回值是当前字符串重复一定次数的新字符串。

```JavaScript
// 缩进指定数量的空格
let indent = ' '.repeat(4),
    indentLevel = 0

// 当需要增加缩进时
let newIndent = indent.repeat(++indentLevel)
```

## 其他正则表达式语法变更

### 正则表达式 y 修饰符

y 修饰符会影响正则表达式的搜索过程中的 sticky 属性，当在字符串中开始字符匹配时，它会通知搜索从正则表达式的 lastIndex 属性开始进行，如果在指定位置没能成功匹配，则停止继续匹配。
记住两点：

1. 只有调用 exec() 和 test() 这些正则表达式对象的方法时才会涉及 lastIndex 属性；
2. 调用字符串方法，如 match()，不会触发粘滞行为。

**检测 y 修饰符是否存在（通过属性名来检测）：**

```JavaScript
let pattern = /hello\d/y

console.log(pattern.sticky)    // true
```

**检测引擎的支持程度：**

```JavaScript
function hasRegExpY () {
    try {
        var pattern = new RegExp('.', 'y')
        return true
    } catch (ex) {
        return false
    }
}

hasRegExpY()
```

### 正则表达式的复制

es5 中通过给 RegExp 构造函数传递正则表达式作为参数来复制这个正则表达式，如：

```JavaScript
var re1 = /ab/i
    re2 = new RegExp(re1)
```

但是给 RegExp 构造函数提供第二个参数，为正则表达式指定一个修饰符，则代码无法运行：

```JavaScript
var re1 = /ab/i

    // es5中抛出错误，es6中正常
    re2 = new RegExp(re1, 'g')    // es6中第二个参数修改第一个参数的修饰符
```

### flags 属性

ES6 中用来获取正则表达式的修饰符
ES5 和 ES6 中通过 source 属性获取正则表达式的文本

ES5 中这样获取：

```JavaScript
function getFlags(re) {
    var text = re.toString()
    return text.substring(text.lastIndexOf('/')+1, text.length)
}

var re = /ab/g
console.log(getFlags(re))    // 'g'
```

flags 属性和 source 属性都是**只读的原型属性访问器**，对齐只设置了 getter 方法。访问 flags 属性会返回所有应用于当前正则表达式的修饰符字符串。如：

```JavaScript
let re = /ab/g

console.log(re.source)    // 'ab'
console.log(re.flags)    // 'g'
```

### 模板字面量

ES6 模板字面量语法支持创建专用领域语言（DSL, 是与 JavaScript 概念相反的编程语言，通常是指某些具体且有限的目标设计的语言）。

> 模板字面量是拓展 ECMAScript 基础语法的语法糖，其提供一套生成、查询并操作来自其他语言里的内容的 DSL，且可以免受注入攻击。

新特性：

1. 多行字符串
2. 基本的字符串格式化（将变量的值嵌入字符串的能力）
3. HTML 转义（向 HTML 插入经过安全转换后的字符串的能力）

#### 基础语法

- 用反撇号（`）替换单、双引号
- 在字符串中使用反撇号，要用反斜杠（\）将其转义
- 在模板字面量中，不需要转义单、双引号

#### 多行字符串

在 ES6 之前，通常依靠数组或字符串拼接的方法来创建多行字符串，如：

```JavaScript
var message = [
    'Multiline ',
    'string'
].join('\n')

// 或者
let message = 'Multiline \n' +
    'string'
```

#### 简化多行字符串

- 需要在字符串中添加新的一行，只需在代码中直接换行
- 在反撇号中所有的空白符都属于字符串的一部分，所以要千万小心缩进。如果要通过适当的缩进来对齐文本，可以考虑在多行模板字面量的第一行留白，并在后面几行中缩进。如：

```JavaScript
let html = `
<div>
    <h1>Title</h1>
</div>`.trim()
```

- 也可以在模板字面量中显式的使用、n, 来指明插入新行的位置

#### 标签模板

标签指在模板字面量第一个反撇号（`）前方标注的字符串：

```JavaScript
let message = tag`Hello World`
```

在这个示例中，应用于模板字面量`Hello World`的模板标签是 tag。

##### 定义标签

标签可以是一个函数，调用时传入加工过的模板字面量各部分数据，但必须结合每个部分来创建结果。第一个参数是一个数组，包含 JavaScript 解释过后的**字面量字符串**，第一个参数后的所以参数都是每一个**占位符的解释值**。

标签函数通常使用不定参数特性来定义占位符，从而简化数据处理的过程，如：

```JavaScript
function tag (literals, ...substitutions) {
    // 返回一个字符串
}
```

literals[0] 总是字符串的始端，literals[literals.length - 1] 总是字符串的结尾。这样，substitutions 的数量总比 literals 的数量少一个，也意味着 substitutions.length === literals.length-1 总为 true。、

模拟模板字面量的默认行为：

```JavaScript
function passthru (literals, substitutions) {
    let result = ''

    // 根据substitutions的数量来确定循环的执行次数(用literals常会越界)
    for (let i = 0; i < substitutions.length; i++) {
        result += literals[i]
        result += substitutions[i]
    }

    // 合并最后一个literal
    result += literals[literals.length - 1]

    return result
}

let count = 10,
    price = 0.25
    message = passthru`${count} items cost $${(count*price).toFixed(2)}.`

console.log(message)    // '10 items cost $2.50.'
```

##### 在模板字面量中使用原始值

通过模板标签可以访问到原生字符串信息（转换之前的原生字符串），最简单的方法是使用内建的 String.row 标签：

```JavaScript
let message1 = `Multiline\nstring`
    message2 = String.row`Multiline\nstring`

console.log(message1)    // 'Multiline
                         // string'
console.log(message2)    // 'Multiline\nstring'
```
