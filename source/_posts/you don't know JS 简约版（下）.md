---
title: you don't know JS 简约版（下）
date: 2018-07-27 16:22:39
tags: ['读书笔记', 'js']
---

### 深入编程

#### 程序

程序常被称为源码或代码，它是一组特定的指令，用来指示计算机要执行哪些任务

#### 语句

程序就是多个语句的集合，它们合起来描述了程序要执行的所有步骤

#### 表达式

语句由一个或多个表达式组成。一个表达式是对一个变量或值的引用，或者是一组值和变量与运算符的组合。运算符，变量（值与类型），常量。

<!-- more -->

#### 执行程序

解释器或编译器将编写的代码翻译成计算机可以理解的命令

#### 实践

Chrome `about:blank` ，prompt，alert，console

#### 代码块与逻辑语句

{}（词法作用域）、条件判断、循环/迭代、函数

#### 编程小结

- 在值上执行动作需要运算符。
- 执行各种类型的动作需要值和类型，比如，对数字进行数学运算，用字符串输出。
- 在程序的执行过程中需要变量来保存数据（也就是状态）。
- 需要 if 这样的条件判断来作出决策。
- 需要循环来重复任务，直到不满足某个条件。
- 需要函数将代码组织为逻辑上可复用的块。

### 深入 JavaScript

1.  值和类型：弱类型、子类型、装箱、类型转换、对象拷贝
2.  变量：作用域，提升
3.  严格模式的情况
4.  函数：IIFE、闭包（模块）
5.  this 标识符
6.  原型
7.  polyfilling 与 transpiling
8.  宿主对象——document、alert(..)

### ES6

#### js 版本管理

1.  2009 年，ES5 正式发布（然后是 2011 年的 ES5.1）
2.  早期——基于某一组主要特性组合为单位，然后未能按时交付。。。如 ES6：发布日期从 2013 年拖到 2014 年，然后又到 2015 年
3.  ES6 之后——把 JavaScript 未来的版本看成基于单个特性：es2016，es2017
4.  新语法：transpiling——transformation ＋ compiling
    - 新的特性和模式一旦在标准中稳定下来，并且浏览器能够实现它们之后，就能够在开发者的代码中得到应用
    - 利用专门的工具把你的 ES6 代码转化为等价（或近似！）的可以在 ES5 环境下工作的代码
5.  新 API：shim/polyfill
    - [ES6 Shim](https:// github.com/paulmillr/es6-shim/)

#### ES6 新语法

##### 块作用域

1.  ES5 之前只能函数或 IIFE 创建
2.  { let.. }——隐式
3.  var 都是归属于包含它的整个函数作用域。 let/const 声明归属于块作用域
4.  var 有提升，let/const 不会（TDZ）—— let 声明放在其所在作用域的最前面
5.  const 创建常量，必须要有显式的初始化
6.  块作用域函数

##### 函数默认参数

1.  传参形式
    ```js
    // ES5
    x = x || 11
    x = x !== undefined ? x : 11
    x = 0 in arguments ? x : 11
    // ES6
    function foo(x = 11) {}
    ```
2.  接受默认值表达式——惰性求值

##### 解构——结构化赋值

1.  对象属性赋值模式
    - `target: source`解构反转赋值
    - 对象解构形式允许多次列出同一个源属性（持有值类型任意）
2.  数组解构赋值
3.  太多，太少，刚刚好
4.  默认值
    ```js
    var [a = 3, b = 6, c = 9, d = 12] = foo()
    var { x = 5, y = 10, z = 15, w = 20 } = bar()
    ```
5.  嵌套解构
6.  解构参数
    - 解构默认值＋参数默认值（注意*解构默认值和函数参数默认值之间的差别*）eg：
    ```js
    function f6({ x = 10 } = {}, { y } = { y: 10 }) {
      console.log(x, y)
    }
    f6() // 10 10
    f6(undefined, undefined) // 10 10
    f6({}, undefined) // 10 10
    f6({}, {}) // 10 undefined
    f6(undefined, {}) // 10 undefined
    f6({ x: 2 }, { y: 3 }) // 2 3
    ```
    - 嵌套默认：解构并重组——关键在于属性默认赋值与重新解构赋值结合起来。
    ```js
    // 把defaults合并进config
    {
      // (带默认值赋值的)解构
      let {
        options: {
          remove = defaults.options.remove,
          enable = defaults.options.enable,
          instance = defaults.options.instance
        } = {},
        log: { warn = defaults.log.warn, error = defaults.log.error } = {}
      } = config
      // 重组
      config = {
        options: { remove, enable, instance },
        log: { warn, error }
      }
    }
    ```

##### 对象字面量扩展

1.  简洁属性`{a:a,b:b}`=>`{a,b}`
2.  简洁方法`{a:function(){},b:function(){}}`=>`{a(){},b(){}}`(!**不适用于递归或者事件绑定 / 解绑定**)
3.  计算属性名
4.  设置[[prototype]]
    - Object.setPrototypeOf(..)
    - super.xxx
5.  模板字符量`${}`
    - 字符串插入表达式——词法作用域内
    - 标签模板字面量
      ```js
      function foo(strings, ...values) {
        cosole.log(string)
        cosole.log(values)
      }
      var desc = 'awesome'
      foo`Everything is ${desc}!`
      // 获取并操作原始的变量和字符串，输出如下
      // [ "Everything is ", "!"]
      // [ "awesome" ]
      ```
6.  箭头函数——简短、绑定 this 词法作用域（优先级最高）

> 待完善
