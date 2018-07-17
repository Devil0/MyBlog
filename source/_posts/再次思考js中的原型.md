---
title: 再次思考js中的原型
date: 2018-07-17 11:16:25
tags: js
---

> **横看成岭侧成峰 远近高低各不同。**

js 是面向对象设计的语言，而面向对象意味着要先描述对象——规定具体对象的特性与行为。然而在实际中我们可以看到很多对象的特性与行为都可以复用，这意味着这部分代码是可以抽象出来复用的。java 采用了**类**概念，引申自我们对自然事物的抽象概念，譬如生物课本上的**界门纲目科属种**。JS，作为一个‘蠢弟弟’，就不这样子干。就不要类的概念，但是为了解决相同的的问题自己搞了一套对象关联对象的东西（所谓继承）——原型与原型链

<!-- more -->

{% img /assets/prototype.png %}

### js 实现一个对象的方法

#### 字面量方式

```js
let o = {
  a: 1,
  b: 2
}
// 控制台打印可以看到
o._proto_ = Object.prototype
```
这时候对象原型链指向Object，于是乎这个新对象有了Object的hasOwnProperty等方法

#### 构造函数方式

我们知道当使用new操作符调用函数时，做了四步工作——新建对象，指定新对象的原型链是函数原型，绑定this，返回新对象

```js
function nothing(){
  console.log('you got a new object')
}
nothing.prototype = {}
let a = new nothing()
// 控制台打印可以看到
a._proto_ = nothing.prototype = {}
```
原型链再一次被使用：链接到对象的继承——原型对象

#### Object.create()方式

相对好理解的对象间委托属性方式
```js
let cat = {eat: 'people'}
let dog = Object.create(cat)

dog._proto_ = cat
dog.eat = 'people'
```
我们可以看到Object.create方式是直接把新建对象的原型链指向了传入的对象，新对象通过原型链找到了拥有传入对象的属性

#### class 关键字创建的对象

es6新增的语法糖来方便的构建便于理解的‘继承’关系；毕竟底层还是原型链方式，继承也不是


