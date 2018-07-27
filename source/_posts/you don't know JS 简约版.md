---
title: you don't know JS 简约版（上）
date: 2018-07-20 16:32:03
tags: ['读书笔记', 'js']
---

据说读书厉害的某高考状元说：把书读薄，把书读厚，然后你就会吃透这本书了。好吧我编不下去了，反正技术书不是网页武侠爽文随便瞄几眼就过去了，要理解吃透相关概念。面对一本书我们都需要好好思考怎么去读它才能最大限度从里面汲取养分，而不是看了好几本想起来脑袋空空。

> **把书读薄**：提取书的框架和要点，乍一看去，就回忆起这部分讲的是什么,形成自己的体系脉络
> **把书读厚**：关注书的具体解释，引申意义，细节内容，关联性内容，如同给书做了很多的注释，所以越看越厚

这个系列的书是真滴不错，刷新不少认知也加深很多理解，经典好书当然值得我们好好吸毒。。。细读（这输入法）。但是我觉得它还不够薄，所以再来提炼一波。原书分为上中下三个系列，一口吃不成一个胖子，所以这边也分成三个篇章分别对应。

<!-- more -->

### 作用域

#### 作用域概念

1.  编译：分词/词法分析,解析/语法分析（Abstract Syntax Tree，AST),代码生成
2.  作用域是一套规则，用于确定在何处以及如何查找变量（标识符）。如果查找的目的是对
    变量进行赋值，那么就会使用 LHS 查询；如果目的是获取变量的值，就会使用 RHS 查询

#### 词法作用域

1.  概念：词法作用域就是定义在词法阶段的作用域。换句话说，词法作用域是由你在写
    代码时将变量和块作用域写在哪里来决定的，因此当词法分析器处理代码时会保持作用域
    不变（大部分情况下是这样的）
2.  eval(..)和 with 会在运行时修改或创建新的作用域，以此来欺骗其他在书写时定义的词法作用域。
3.  主要区别：词法作用域是在写代码或者说定义时确定的，而动态作用域是在运行时确定的。（this 也是！）词法作用域关注函数在何处声明，而动态作用域关注函数从何处调用。

#### 函数作用域和块作用域

1.  最小特权原则中引申出来，某个模块或对象的 API 设计，接口清晰明了，私有化，模块化
2.  区分函数声明和表达式最简单的方法是看 function 关键字出现在声明中的位置（不仅仅是一行代码，而是整个声明中的置。如果 function 是声明中的第一个词，那么就是一个函数声明，否则就是一个函数表达式
3.  IIFE，代表立即执行函数表达式（Immediately Invoked Function Expression）——避免变量污染全局
4.  块作用域指的是变量和函数不仅可以属于所处的作用域，也可以属于某个代码块（通常指 { .. } 内部）

#### 提升

`var a = 2`——JavaScript 引擎第一个是编译阶段的任务，而第二个则是执行阶段的任务；
声明（变量和函数）都会被“移动”到各作用域的最顶端，这个过程被称为提升（函数优先）

### 闭包

#### 作用域闭包

_当函数可以记住并访问所在的词法作用域，即使函数是在当前词法作用域之外执行，这时就产生了闭包_

```js
// first
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
// second
for (var i = 1; i <= 5; i++) {
  (function() {
    setTimeout(function timer() {
      console.log(i)
    }, i * 1000)
  })()
}
// third
for (var i = 1; i <= 5; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log(j)
    }, j * 1000)
  })(i)
}
// forth
for (let i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
```

#### 模块化实现

1.  必须有外部的封闭函数，该函数必须至少被调用一次（每次调用都会创建一个新的模块实例）
2.  封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态。
3.  代码实现
    ```js
    var MyModules = (function Manager() {
      var modules = {}
      function define(name, deps, impl) {
        // deps:模块依赖；这里根据模块名取出对应模块
        for (var i = 0; i < deps.length; i++) {
          deps[i] = modules[deps[i]]
        }
        modules[name] = impl.apply(impl, deps)
      }
      function get(name) {
        return modules[name]
      }
      return {
        define: define,
        get: get
      }
    })()
    ```
4.  es6 采用文件模块化机制
    一个文件一个 module，export 导出要暴露的 API，在需要引入的文件里用 import 添加模块并绑定到相应变量上

### this

#### this 概念

1.  作用：隐式传递上下文对象——精准而优雅
2.  误区：函数内部调用自身与 this 调用自身区别。具名函数可通过函数名访问自身（词法作用域），this 方式则要 call、applay、bind 指向函数。
3.  学习 this 的第一步是明白 this 既不指向函数自身也不指向函数的词法作用域；this 实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用
4.  绑定规则
    1.  默认绑定：全局，strict 模式下为 undefined
    2.  隐式绑定：对象属性引用链中只有最顶层或者说最后一层会影响调用位置
    3.  显式绑定：call(..) 和 apply(..)
        ```js
        function bind(fn, obj) {
          return function() {
            return fn.apply(obj, arguments)
          }
        }
        ```
        为了避免函数作为参数传入调用时丢失上下文，新增硬绑定`bind`,然后它还能进行柯里化（固定参数）
    4.  new 绑定：
        实际上并不存在所谓的“构造函数”， 只有对于函数的“构造调用”
        1.  创建（或者说构造） 一个全新的对象。
        2.  这个新对象会被执行 [[ 原型 ]] 连接。
        3.  这个新对象会绑定到函数调用的 this。
        4.  如果函数没有返回其他对象， 那么 new 表达式中的函数调用会自动返回这个新对象。
5.  绑定优先级
    new 绑定>显式绑定>隐式绑定>默认绑定
6.  =>箭头函数
    使用词法作用域取代了传统的 this 机制；相当于我们之前的`let self = this`,最好不要混用两种风格。
7.  有些调用可能在无意中使用默认绑定规则。 如果想“更安全” 地忽略 this 绑
    定， 你可以使用一个 DMZ 对象， 比如 ø = Object.create(null)， 以保护全局对象

### 对象

#### 内置对象

1.  内置函数可以当作构造函数来使用，从而可以构造一个对应子类型的新对
    象。在必要时语言会自动把字符串字面量转换成一个 String 对象
2.  String、Number、Boolean、Object、Function、Array、Date、RegExp、Error
3.  Object.prototype.tostring.call(obj)=== [object 对象子类型]

#### 对象内容

1.  对象的内容是由一些存储在特定命名位置的（任意类型的）值组成的，
    我们称之为属性（引用——指向这些值真正的存储位置）
2.  .a 语法通常被称为“属性访问”， ["a"] 语法通常被称为“键访问”；区别在于**标识符的命名规范、键访问可接受变量并将变量转为字符串**
3.  数组有一套更加结构化的值存储机制，期望的是数值下标，所以谨慎使用可转为数字的属性添加至数组，最好不要以对象形式修改数组属性

#### 对象复制

1.  浅复制
    ```js
    var newObj = Object.assign({}, myObject)
    ```
2.  深复制
    ```js
    // JSON 安全的对象
    var newObj = JSON.parse(JSON.stringify(myObject))
    ```

#### 属性描述符

1.  定义与查看
    ```js
    Object.defineProperty(obj,a,{
        value: 1,
        writable: true,
        configurable: true,
        enumerable: true
        }
    )，
    Object.getOwnPropertyDescriptor(obj,a)
    // {
    // value: 2,
    // writable: true,
    // enumerable: true,
    // configurable: true
    // }
    ```
2.  writable （可写）、enumerable （可枚举）和 configurable （可配置）
3.  严格模式报错，非严格模式静默不生效
4.  设置对象不可变
    ```js
    // 禁止添加新属性
    Object.preventExtensions(obj)
    // 禁止配置或删除已有属性（在上面基础上）
    Object.seal(obj)
    // 禁止修改属性值（在上面基础上）
    Object.freeze(obj)
    ```

#### 对象增删查改——[[get]] [[put]]

1.  [[get]]：对象中查找是否有名称相同的属性——原型链查找——返回值 undefined
2.  [[Put]]：属性是否是访问描述符？存在 setter 就调用 setter：按照属性描述符设置

#### 访问描述符 Getter Setter

访问描述符优于属性描述符
两种定义方式

```js
var myObject = {
  // 给 a 定义一个 getter
  get a() {
    return this._a_
  },
  // 给 a 定义一个 setter
  set a(val) {
    this._a_ = val * 2
  }
}

Object.defineProperty(
  myObject, // 目标对象
  'a', // 属性名
  {
    // 描述符
    get: function() {
      return this._a_ * 2
    },
    set: function(val) {
      this._a_ = val * 2
    }
  }
)
```

#### 检测属性存在

1.  in 操作符会检查属性是否在对象及其[[Prototype]]原型链中；hasOwnProperty(..) 只会检查属性是否在 myObject 对象中
2.  Object.keys(..) 会返回一个数组，包含所有可枚举属性
3.  propertyIsEnumerable(..) 会检查给定的属性名是否直接存在于对象中
4.  Object.getOwnPropertyNames(..)会返回一个数组，包含所有属性，无论它们是否可枚举

#### 遍历对象

1.  传统：for..in 循环可以用来遍历对象的可枚举属性列表（包括 [[Prototype]] 链）
2.  ES5：forEach(..) 、 map(..)、every(..) 和 some(..)
3.  ES6： for..of （for..of 循环首先会向被访问对象请求一个迭代器对象，然后通过调用迭代器对象的 next() 方法来遍历所有返回值。） @iterator 本身并不是一个迭代器对象，而是一个返回迭代器对象的函数
    ```js
    // 给任何想遍历的对象定义 @@iterator
    Object.defineProperty(myObject, Symbol.iterator, {
      enumerable: false, // 不可枚举
      writable: false,
      configurable: true,
      value: function() {
        var o = this
        var idx = 0
        var ks = Object.keys(o)
        return {
          next: function() {
            return {
              value: o[ks[idx++]],
              done: idx > ks.length
            }
          }
        }
      }
    })
    // 手动遍历 myObject
    var it = myObject[Symbol.iterator]()
    it.next()
    // 用 for..of 遍历 myObject
    for (var v of myObject) {
      console.log(v)
    }
    ```

### 混合对象——类

类相关的面向对象编程

#### 类理论

1.  类 / 继承描述了一种代码的组织结构形式——一种在软件中对真实世界中问题领域的建模方法
2.  类、继承、实例化、多态概念
    - 类意味着复制。
    - 传统的类被实例化时，它的行为会被复制到实例中。类被继承时，行为也会被复制到子类中。
    - 多态（在继承链的不同层次名称相同但是功能不同的函数）看起来似乎是从子类引用父类，但是本质上引用的其实是复制的结果。

#### 类机制

1.  类是概念，实例化复制相关特性行为后获得副本才是我们的对象
2.  类构造函数属于类，而且通常和类同名。此外，构造函数大多需要用 new 来调，这样语言引擎才知道你想要构造一个新的类实例——对象

#### 类继承

1.  面向类的语言中，你可以先定义一个类，然后定义一个继承前者的类
2.  定义好一个子类之后，相对于父类来说它就是一个独立并且完全不同的类。子类会包含父类行为的原始副本，但是也可以重写所有继承的行为甚至定义新行为
3.  多态：相同方法可在类继承中重写，多态并不表示子类和父类有关联，子类得到的只是父类的一份副本。类的继承其实就是复制。
4.  有些面向类的语言允许你继承多个“父类”。多重继承意味着所有父类的定义都会被复制到子类中。

### 原型

1.  对象内置属性[[Prototype]]
2.  in、for..in 等查找属性：[[Get]]——对象本身属性——查找 [[Prototype]] 链——Object.prototype
3.  设置属性`myObject.foo = "bar"`：
    1.  如果在 [[Prototype]] 链上层存在名为 foo 的普通数据访问属性并且没
        有被标记为只读（`writable:true`），那就会直接在 myObject 中添加一个名为 foo 的新属性， 它是屏蔽属性。
    2.  如果在 [[Prototype]]链上层存在 foo，但是它被标记为只读（`writable:false`）， 那么无法修改已有属性或者在 myObject 上创建屏蔽属性如果运行在严格模式下， 代码会抛出一个错误。 否则， 这条赋值语句会被忽略。 总之， 不会发生屏蔽。
    3.  如果在 [[Prototype]] 链上层存在 foo 并且它是一个 setter（参见第 3 章）， 那就一定会调用这个 setter。 foo 不会被添加到（或者说屏蔽于） myObject， 也不会重新定义 foo 这个 setter。
4.  理解 js 中的‘类’：
    1.  JavaScript 中只有对象，但是 js 却一直在模仿类
    2.  继承意味着复制操作， JavaScript（默认） 并不会复制对象属性。 相反， JavaScript 会在两个对象之间创建一个关联，这样一个对象就可以通过委托访问另一个对象的属性和函数。
5.  构造函数与原型继承
    1.  构造函数只是`new`调用函数，`new`操作的四个步骤？
    2.  原型继承在于原型链的指向
    3.  constructor 是函数原型的自带属性，指向函数，可被修改不靠谱
6.  关联[[prototype]]:
    ```js
    // 直接引用原型，Bar相当于没有用
    Bar.prototype = Foo.prototype
    // Foo函数的属性初始化操作可能带来副作用：this.name=name
    Bar.prototype = new Foo()
    // 正确做法
    // ES6 之前需要抛弃默认的 Bar.prototype
    Bar.ptototype = Object.create(Foo.prototype)
    // ES6 开始可以直接修改现有的 Bar.prototype
    Object.setPrototypeOf(Bar.prototype, Foo.prototype)
    ```
7.  检测原型链关系：
    ```js
    // 函数（‘类’）与对象
    obj instanceof Fun // true
    // 对象与对象
    Fun.prototype.isPrototypeOf(Obj) // true
    ```
8.  `.__proto__`的实现
    ```js
    Object.defineProperty(Object.prototype, '__proto__', {
      get: function() {
        return Object.getPrototypeOf(this)
      },
      set: function(o) {
        // ES6 中的 setPrototypeOf(..)
        Object.setPrototypeOf(this, o)
        return o
      }
    })
    ```
9.  class 并没有解决“类”机制的问题，而是隐藏并假装自己是静态类，本质上还是行为委托机制，只是在“类”模仿上走得更远
