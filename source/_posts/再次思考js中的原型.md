---
title: 再次思考js中的原型
date: 2018-07-17 11:16:25
tags: js
---

> **横看成岭侧成峰 远近高低各不同。**

### 前言

js 是面向对象设计的语言，而面向对象意味着要先描述对象——规定具体对象的特性与行为。然而在实际中我们可以看到很多对象的特性与行为都可以复用，这意味着这部分代码是可以抽象出来复用的。java 采用了**类**概念，引申自我们对自然事物的抽象概念，譬如生物课本上的**界门纲目科属种**。JS，作为一个‘蠢弟弟’，就不这样子干。就不要类的概念，但是为了解决相同的的问题自己搞了一套对象关联对象的东西（所谓继承）——原型与原型链

<!-- more -->

{% img /assets/prototype.png %}

### 让我们来new一个对象

> 为了解决从哪里来到哪里去这个问题，我们可以看对象是怎么定义出来的。看完之后对于这个原型，原型链问题也就迎刃而解了。

#### 字面量方式

```js
let o = {
  a: 1,
  b: 2
}
// 控制台打印可以看到
o._proto_ = Object.prototype
```

这时候对象原型链指向 Object，于是乎这个新对象有了 Object 的 hasOwnProperty 等方法

#### 构造函数方式

我们知道当使用 new 操作符调用函数时，做了四步工作——新建对象，指定新对象的原型链是函数原型，绑定 this，返回新对象

```js
function nothing() {
  console.log('you got a new object')
}
nothing.prototype = {}
let a = new nothing()
// 控制台打印可以看到
a._proto_ = nothing.prototype = {}
```

原型链再一次被使用：链接到对象的继承——原型对象;可能细心的你会发现 a 的 constructor 变成了 Object()，因为这里把函数默认的实例原型对象自带的 constructor 给重置了。实际上 constructor 并不一定表示被 xx 函数构造的意味,它只是函数原型对象的一个默认对象充话费送的，指向当前函数的属性，实际上也没啥卵用。

#### Object.create()方式

相对好理解的对象间委托属性方式

```js
let cat = { eat: 'people' }
let dog = Object.create(cat)

dog._proto_ = cat
dog.eat = 'people'
```

我们可以看到 Object.create 方式是直接把新建对象的原型链指向了传入的对象，新对象通过原型链找到了拥有传入对象的属性

#### class 关键字创建的对象

es6 新增的语法糖来方便的构建便于理解的‘继承’关系；毕竟底层还是原型链方式,写法倒是和 java 类继承基本一样，`class`的本质是构造函数

```js
class Person {
  constructor(name) {
    this.name = name
  }
  say() {
    console.log(`I am ${this.name}`)
  }
}
// 这里相当于
Person.prototype = {
  constructor(name) {
    this.name = name
  },
  say() {
    console.log(`I am ${this.name}`)
  }
}
let a = new Person('a')
```

清晰很多有木有，‘继承’对象的基本属性，方法定义都不再割裂开来，我们可以很方便的定义一个原型对象并初始化它的子对象。

**以上，我们了解到一个对象是如何诞生的，也知道，对象的行为、属性来源于另一个所谓的原型对象，以此串联而成的便是原型链，所有的源头的均会回到 js 基本类型 Object.prototype 或 null**

> 就像*你不知道的 JS 上*一书所阐明的，JS 中对象的描述采用属性**委托**设计方式而非**类继承**设计方式。对象间的关联是自由的，也更加强大。再上个图理凑字数也是不错滴：

{% img /assets/prototype.jpg %}
