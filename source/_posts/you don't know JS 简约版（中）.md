---
title: you don't know JS 简约版（中）
date: 2018-07-26 14:54:55
tags: ['读书笔记', 'js']
---

### 类型和语法

#### 类型

1.  概念：类型是值的内部特征，它定义了值的行为，以使其区别于其他值
2.  内置类型：null、undefined、string、number、boolean、object、symbol
    1.  null 判断：`(!null && typeof null === "object"); // true`
    2.  其他采用 typeof 判断
    3.  `Object.prototype.tostring.call()`进一步判断对象子类型
3.  undefined 与 undeclared：
    1.  JavaScript 中的变量是没有类型的，只有值才有。变量可以随时持有任何类型的值
    2.  js 中将 undefined 与 undeclared 混为一谈，typeof 均返回 undefined
    3.  typeof 的安全防范机制——用于避免重复定义

<!-- more -->

#### 值

1.  数组
    1.  “稀疏”数组
    2.  类数组`Array.prototype.slice.call()`与`Array.from()`转换
2.  字符串
    类数组的结构，本身不可变，常用来与数组相互转换做数据处理：`split`、`join`
3.  数字
    1.  JavaScript 中的“整数”就是没有小数的十进制数。所以 42.0 即等同于“整数” 42。
    2.  JavaScript 中的数字类型是基于 IEEE754 标准来实现的，该标准通常也被称为“浮点数”。 JavaScript 使用的是“双精度”格式（即 64 位二进制）
    3.  API
        - 指数形式：toExponential()
        - 小数位数：tofixed(..)
        - 有效位数：toPrecision(..)
        - 整数：Number.isInteger(..)
        - 安全整数：Number.isSafeInteger(..)
        - Number.MAX_VALUE 与 Number.MIN_VALUE
        - Number.MAX_SAFE_INTEGER 与 Number.MIN_SAFE_INTEGER
    4.  0o（八进制）与 0x（十六进制）
    5.  `0.1 + 0.2 === 0.3; // false`与机器精度
        ```js
        if (!Number.EPSILON) {
          Number.EPSILON = Math.pow(2, -52)
        }
        ```
4.  特殊数值
    1.  null 与 undefined
        - null 指空值（empty value），undefined 指没有值（missing value）
        - null 是一个特殊关键字，不是标识符，我们不能将其当作变量来使用和赋值。然而 undefined 却是一个标识符，可以被当作变量来使用和赋值
    2.  void 运算符
        void 并不改变表达式的结果，只是让表达式不返回值（undefined）
    3.  NaN——错误的数值
        Number.isNaN：`return n !== n`
    4.  无穷数
        - `1 / 0; // Infinity`
        - `-1 / 0; // -Infinity`
        - `Infinity/Infinity; // NaN`
        - `1 / Infinity; 0`
        - `-1 / Infinity; -0` 保留矢量方向
    5.  `Object.is(..)`判断 NaN 或-0
        ```js
        Object.is(a, NaN)
        Object.is(b, -0)
        ```
5.  值与引用
    1.  简单值（即标量基本类型值）总是通过值复制的方式来赋值传递，包括 null、 undefined、字符串、数字、布尔和 ES6 中的 symbol。
    2.  复合值——对象（包括数组和封装对象）和函数，则总是通过引用复制的方式来赋值 / 传递。
    3.  我们无法自行决定使用值复制还是引用复制，一切由值的类型来决定

#### 原生函数

1.  内部属性 [[Class]]
    - typeof 返回值为"object"的对象（如数组）都包含一个内部属性[[Class]]，eg：`[object Array]`
    - 对象的内部 [[Class]] 属性和创建该对象的内建原生构造函数相对应
    - null 与 undefined
    ```js
    Object.prototype.toString.call(null)
    // "[object Null]"
    Object.prototype.toString.call(undefined)
    // "[object Undefined]"
    ```
2.  封装对象包装
    - 基 本 类 型 值没有.length 和.toString()这样的属性和方法，需要通过封装对象才能访问，此时 JavaScript 会自动为基本类型值包装（box 或者 wrap）一个封装对象
    - 不提倡用 new 构造封装对象，繁琐容易出错
    - Date(..) 和 Error(..)没有对应的常量形式来作为它们的替代
    - 使用 Symbol(..) 原生构造函数来自定义符号。但它比较特殊，不能带 new 关键字，否则会出错
    - 符号是具有唯一性的特殊值（并非绝对），用它来命名对象属性不容易导致重名

#### 强制类型转换

1.  概念：将值从一种类型转换为另一种类型通常称为类型转换（type casting），这是显式的情况；隐式的情况称为强制类型转换（coercion
2.  类型转换发生在静态类型语言的编译阶段，而强制类型转换则发生在动态类型语言的运行时
3.  JSON
    - 不安全的 JSON 值：undefined、function、symbol（ES6+）和包含循环引用
    - JSON.stringify(..) 在对象中遇到 undefined、 function 和 symbol 时会自动将其忽略，在数组中则会返回 null（以保证单元位置不变）
    - 对包含循环引用的对象执行 JSON.stringify(..) 会出错
    - 如果传递给 JSON.stringify(..)的对象中定义了 toJSON()方法，那么该方法会在字符串化前调用，以便将对象转换为安全的 JSON 值。
4.  类型装换要先获取简单基本类型值（**ToPrimitive 原则**）：先是 valueOf()得到简单基本类型值，得不到时转而调用 toString()
    ```js
    // 坑????
    var i = 2
    Number.prototype.valueOf = function() {
      return i++
    }
    if (a == 2 && a == 3) {
      // ..
    }
    ```
5.  宽松相等（loose equals） == 和严格相等（strict equals） === ——**== 允许在相等比较中进行强制类型转换，而===不允许。”**；不建议也不提倡用`==`
6.  `[] == ![] // true`——运行顺序问题，ToPrimitive 原则
7.  以下原则可以让我们有效地避免出错。（**我靠，干脆不用！！！还能开开心心撸代码吗？**）
    - 如果两边的值中有 true 或者 false，千万不要使用 ==。
    - 如果两边的值中有 []、 "" 或者 0，尽量不要使用 ==。
    - typeof 判断可以使用 ==。

#### 语法

1.  语句和表达式
    - 语句都有一个返回结果值——代码块的结果值就如同一个隐式的返回，即返回最后一个语句的结果值（函数返回另外）
    - 上下文？介绍{}代码块的一些场景：对象，JSON，代码块，else if 之类的，所以这里是想干嘛？？
2.  运算符优先级
    - && 和 || 运算符的“短路”
3.  自动分号插入（Automatic SemicolonInsertion， ASI）
    - break、 continue、 return 和 yield（ES6）等关键字
4.  错误——并非 词法 错误，而是 语法 错误，发生在编译阶段，无法 try...catch
5.  TDZ（Temporal DeadZone，暂时性死区）——指的是由于代码中的变量还没有初始化而不能被引用的情况
6.  函数参数：在 ES6 之前，获得函数所有参数的唯一途径就是 arguments 数组，然后说废就废。。。不要混用 es6 的参数方式和 argument，argument 只和传入的有关。
7.  try..finally
    - finally 中的 return 会覆盖 try 和 catch 中 return 的返回值
    - finally 中的代码总是会在 try 之后执行，如果有 catch 的话则在 catch 之后执行。也可以将 finally 中的代码看作一个回调函数，即无论出现什么情况最后一定会被调用

#### 混合环境的 js

1.  宿主环境（浏览器等）创建并提供给 JavaScript 引擎的变量“宿主对象”（包括内建对象和函数）
2.  创建带有 id 属性的 DOM 元素时也会创建同名的全局变量
3.  不要扩展原生原型——无法兼容和考虑未来发展
4.  shim/polyfill——为不符合最新规范的老版本浏览器填补缺失的功能

### 异步与回调

#### 异步

程序中现在运行的部分和将来运行的部分之间的关系就是异步编程的核心

1.  分块的程序
    1.  程序代码分块执行，当前指向与未来触发执行任务的便有了同步异步之分
    2.  控制台是异步的
2.  事件循环
    - JavaScript 引擎基于浏览器，JavaScript 引擎本身并没有时间的概念，只是一个按需执行 JavaScript 任意代码片段的环境。“事件”（JavaScript 代码执行）调度总是由包含它的环境进行
    - 事件循环：一种机制来处理程序中多个块的执行，且执行每块时调用 JavaScript 引擎，排队执行程序
    - 伪代码
      ```js
      // eventLoop是一个用作队列的数组
      // （先进，先出）
      var eventLoop = []
      var event
      // “永远”执行
      while (true) {
        // 一次tick
        if (eventLoop.length > 0) {
          // 拿到队列中的下一个事件
          event = eventLoop.shift()
          // 现在，执行下一个事件
          try {
            event()
          } catch (err) {
            reportError(err)
          }
        }
      }
      ```
3.  并行线程
    1.  线程：最小的执行单元；进程：资源管理配置单元
    2.  事件循环把自身的工作分成一个个任务并顺序执行，不允许对共享内存的并行访问和修改。通过分立线程中彼此合作的事件循环，并行和顺序执行可以共存。
    3.  **js 是单线程的**，不跨线程共享数据但是存在竞态条件（异步执行顺序）
4.  并发
    - 此并发是指任务触发上的‘并发’，而非执行上的并发
    - 非交互——不影响彼此——不需要对代码处理
    - 交互——竞态（race）——为后续的执行代码添加**门闩**（或者说**一次性通过钥匙**）
    - 协作——互相关联
5.  任务
    - 一个建立在事件循环队列之上，叫作任务队列（job queue）
    - 在事件循环的每个 tick 中，可能出现的异步动作不会导致一个完整的新事件添加到事件循环队列中，而会在当前 tick 的任务队列末尾添加一个项目（一个任务）
    - 事件循环列队就像普通游客，任务队列就是 vip 插队游客
6.  语句顺序
    代码中语句的顺序和 JavaScript 引擎执行语句的顺序并不一定要一致

#### 回调

_回调函数是 JavaScript 异步的基本单元_

1.  函数、任务的执行依赖于另一个任务的执行，时序上有先后连锁关系
2.  嵌套回调与链式回调—— - 我们的顺序阻塞式的大脑计划行为无法很好地映射到面向回调的异步代码。这就是回调方式最主要的缺陷 - 大脑对于事情的计划方式是线性的、阻塞的、单线程的语义，但是回调表达异步流程的方式是非线性的、非顺序的，这使得正确推导这样的代码难度很大,难于理解的代码
    是坏代码，会导致坏 bug
3.  回调的不确定性导致的信任问题：_信任，但要核实_
4.  解决回调问题：
    - 分离回调（一个用于成功通知，一个用于出错通知）
      ```js
      function success(data) {
        console.log(data)
      }
      function failure(err) {
        console.error(err)
      }
      ajax('http://some.url', success, failure)
      ```
    - “error-first 风格”（“Node 风格”）
      ```js
      function response(err, data) {
        // 出错？
        if (err) {
          console.error(err)
        }
        // 否则认为成功
        else {
          console.log(data)
        }
      }
      ajax('http://some.url.1', response)
      ```

#### Promise

_回调表达程序异步和管理并发的两个主要缺陷：缺乏顺序性和可信任性_

promise 规范：不把自己程序的 continuation 传给第三方，而是希望第三方给我们提供了解其任务何时结束的能力，然后由我们自己的代码来决定下一步做什么

1.  理解 promise 概念：Promise 是一种封装和组合未来值的易于复用的机制
2.  promise 的类型判断
    ```js
    if (
      p !== null &&
      (typeof p === 'object' || typeof p === 'function') &&
      typeof p.then === 'function'
    ) {
      // 假定这是一个thenable!
    } else {
      // 不是thenable
    }
    ```
3.  promise 信任问题
    - 调用回调过早——必定异步
    - 调用回调过晚——回调注册独立依次运行不互相影响
    - 回调未调用——超时竞态，错误回调
    - 调用回调次数过少或过多—— Promise 只能被决议一次
    - 未能传递所需的环境和参数——保持其定义所在的作用域的闭包
    - 吞掉可能出现的错误和异常——内部异常捕获机制
    - Promise.resolve(..)为所有函数的返回值（不管是不是 thenable）都封装一层，保证总会返回一个 Promise 结果
4.  链式流
    - this 链式流（jquery 方式），操作对象都为当前对象
    - thenable 链式流（promise），持续展开 thenable 的同时递归地前进
    - 顺序流程控制——符合思维角度，避免出错
    - 理解 resolve，fulfilled 区别：**A Promise can either be pending (unresolved), fulfilled (resolved successfully) or rejected (resolved unsuccessfully)**
    ```js
    // 函数命名标准规范
    let p = new Promise(function(resolve, reject) {})
    p.then(onFulfilled, onRejected)
    ```
5.  错误处理
    - try..catch 无法处理跨异步操作
    - Promise 链的一个最佳实践就是最后总以一个 catch(..) 结束
    - 遗留的 catch 里的异常坑
6.  promise 模式
    - Promise.all([ .. ])
    - Promise.race([ .. ])
      ```js
      // 超时竞赛
      Promise.race([
        foo(), // 启动foo()
        timeoutPromise(3000) // 给它3秒钟
      ]).then(
        function() {
          // foo(..)按时完成！
        },
        function(err) {
          // 要么foo()被拒绝，要么只是没能够按时完成，
          // 因此要查看err了解具体原因
        }
      )
      ```
    - 变体自定义模式：none([ .. ])、any([ .. ])、first([ .. ])、last([ .. ])、map([..])并发迭代
7.  Promise 的 API
    - new Promise(..) 构造器
    - Promise.resolve(..) 和 Promise.reject(..)——创建一个状态已确定的 Promise
    - then(fulfilled, rejected ) 和 catch(rejected)（等同于 then(null, rejected )）
    - Promise.all([ .. ]) 和 Promise.race([ .. ])；_向 Promise.all([ .. ]) 传入空数组，它会立即完成，但 Promise.race([ .. ]) 会挂住，且永远不会决议_
    - Promise 库处理其他复杂情况
8.  Promise 的局限性
    - 顺序错误处理——内部错误可能无法捕获
    - 返回单一值——数组、对象形式或 promise.all
    - 单决议——一次性用品，多次复用时需要重新 new 一个
    - 惯性——现有回调函数风格整改为 promise
      ```js
      // ajax("http://some.url.1,cb);异步函数为例的整改
      // polyfill安全的guard检查
      if (!Promise.wrap) {
        Promise.wrap = function(fn) {
          return function() {
            var args = [].slice.call(arguments)
            return new Promise(function(resolve, reject) {
              fn.apply(
                null,
                // 把拆分的参数整合，回调函数插入决议
                args.concat(function(err, v) {
                  if (err) {
                    reject(err)
                  } else {
                    resolve(v)
                  }
                })
              )
            })
          }
        }
      }
      ```
    - 性能问题探讨——Promise 非常好，请使用

#### 生成器

基于回调的异步：不符合大脑对任务步骤的规划方式；由于控制反转，不是可信任或可组合的

1.  生成器基本概念与写法;yield/next(..)这一对不只是一种控制机制，实际上也是一种双向消息传递机制。 yield 表达式本质上是暂停下来等待某个值，接下来的 next(..) 调用会向被暂停的 yield 表达式传回
    一个值（或者是隐式的 undefined ）。
2.  生成器与迭代器
    1.  生成器——闭包方式保存状态与值
    2.  iterable 迭代器——需提供 Symbol.iterator 接口
    3.  生成器把 while..true 带回了 JavaScript 编程的世界！
        ```js
        function* something() {
          var nextVal
          // 生成器就是要永远运行
          while (true) {
            // 闭包
            if (nextVal === undefined) {
              nextVal = 1
            } else {
              nextVal = 3 * nextVal + 6
            }
            yield nextVal
          }
        }
        ```
3.  异步迭代生成器
    - 我们把异步作为实现细节抽象了出去，使得我们可以以同步顺序的形式追踪流程控制
    - 生成器 yield 暂停的特性意味着我们不仅能够从异步函数调用得到看似同步的返回值，还可以同步捕获来自这些异步函数调用的错误
4.  生成器结合 Promise
    - ES7： async 与 await
    - 支持 Promise 的 Generator Runner
    ```js
    function run(gen) {
      var args = [].slice.call(arguments, 1),
        it
      // 在当前上下文中初始化生成器
      it = gen.apply(this, args)
      // 返回一个promise用于生成器完成
      return Promise.resolve().then(function handleNext(value) {
        // 对下一个yield出的值运行
        var next = it.next(value)
        return (function handleResult(next) {
          // 生成器运行完毕了吗？
          if (next.done) {
            return next.value
          }
          // 否则继续运行
          else {
            return Promise.resolve(next.value).then(
              // 成功就恢复异步循环，把决议的值发回生成器
              handleNext,
              // 如果value是被拒绝的 promise，
              // 就把错误传回生成器进行出错处理
              function handleErr(err) {
                return Promise.resolve(it.throw(err)).then(handleResult)
              }
            )
          }
        })(next)
      })
    }
    ```
5.  生成器委托——yield \*fun
    - yield 委托的主要目的是代码组织，保持生成器分离有助于程序的可读性、可维护性和可调试性
    - 消息委托，异步委托及递归委托
6.  生成器并发
7.  形实转换程序（thunk）
    ```js
    thunkify(foo, 3, 4)
    function thunkify(fn) {
      return function() {
        var args = [].slice.call(arguments)
        return function(cb) {
          args.push(cb)
          return fn.apply(null, args)
        }
      }
    }
    ```

#### 程序性能

1.  web worker 概念
    - JavaScript 是单线程运作，异步并发的
    - 多线程带来的问题：共享的作用域和资源、通信
    - 浏览器启动一个新的线程
      ```js
      var w1 = new Worker('http://some.url.1/mycoolworker.js')
      ```
    - 不会共享任何作用域或资源,基于事件消息机制相互联系
      ```js
      // 一对一的关系：message要么来自这个Worker，要么来自主页面
      w1.addEventListener('message', function(evt) {
        // evt.data
      })
      w1.postMessage('something cool to say')
      ```
2.  web worker 工作
    1.  环境：
        - 完全独立的线程，不共享 window
        - 网络操作（Ajax、WebSockets）以及设定定时器
        - navigator 、 location 、 JSON 和 applicationCache
    2.  加载脚本
        ```js
        // 在Worker内部
        importScripts('foo.js', 'bar.js')
        ```
    3.  应用场景——把长时间的或资源密集型的任务卸载到不同的线程中，以提高主 UI 线程的响应性。
        - 处理密集型数学计算
        - 大数据集排序
        - 数据处理（压缩、音频分析、图像处理等）
        - 高流量网络通信
    4.  数据传递优化——Transferable 对象、结构化克隆算法
    5.  SharedWorker
        ```js
        // 在共享Worker内部
        addEventListener( "connect", function(evt){
            // 这个连接分配的端口
            var port = evt.ports[0];
            port.addEventListener( "message", function(evt){
                // ..
                port.postMessage( .. );
                // ..
            } );
            // 初始化端口连接
            port.start();
        } );
        ```
3.  SIMD：CPU 级的并行数学运算映射到 JavaScriptAPI，以获得高性能的数据并行运算，比如在大数据集上的数字处理。
4.  asm.js 模式——描述了 JavaScript 的一个很小的子集，它避免了 JavaScript 难以优化的部分（比如垃圾收集和强制类型转换），并且让 JavaScript 引擎识别并通过激进的优化运行这样的代码。
5.  异步编码模式使我们能够编写更高效的代码，通常能够带来非常大的改进。但是，异步特性只能让你走这么远，因为它本质上还是绑定在一个单事件循环线程上。

#### 性能测试与调优

1.  正确的测试解决方案（基于统计学）——Benchmark.js
2.  检查测试环境——引擎优化带来的未知
3.  jsPerf.com——尽可能多的真实环境下进行实际测试；编写更好更清晰的测试：确保测试的公平，真实环境。
4.  微性能
    - 不要试图和 JavaScript 引擎比谁聪明。对性能优化来说
    - 不要过早优化，权衡大局（考虑可读性等）
5.  尾调用优化——（Tail Call Optimization，TCO）
    - 重用已有的 Fun(..) 的栈帧
    - `return fun（）`的函数——递归回调
    - TCO 允许一个函数在结尾处调用另外一个函数来执行，不需要任何额外资源。这意味着，对递归算法来说，引擎不再需要限制栈深度
