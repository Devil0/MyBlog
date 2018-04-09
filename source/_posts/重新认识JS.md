---
title: 重新认识JS
date: 2018-04-08 10:07:42
tags: JS
categories: JS体系
---
{% img [class names] /assets/javascript.png 600 350 JavaScript %}
## 概述

高中读化学时一直觉得知识点好零碎，徘徊门外分数也上不去；后来高一舍友告诉我要形成自己的体系，闭上眼睛都能想象得出化学的课本章节，可以从元素周期表或是有机无机开始为基点打造自己的**学习体系脉络**；说来也神奇，有了这个想法后学习真的轻松不少，打怪升级分数也上去了。废话有点多，谁让 js 是一门不需要学习就能直接先用的语言呢？导致不少人压根就迷迷糊糊的或者雾里看花似懂非懂；那个什么鬼前端知识图片就是坑，算了，入正题。。。

### 何为程序

作为一个电子信息科学与技术专业，学硬件又学软件什么的，大学算是有过了一遍从基础电信号——数电——汇编——C 语言的大致历史，个人理解的电子世界是这样的：01 电信号构成基石——排列组合逻辑运算构成数字芯片实现一定功能（输入输出及存储），排列组合构成功能指令——为编程方便简化指令成为汇编语言（assembling）——进一步简化为各个分支`c、c++、c#、java、.net`......(高级语言)

### 定位——轻量级的脚本语言

脚本语言的主要特征是：程序代码即是脚本程序，亦是最终可执行文件。脚本语言可分为独立型和嵌入型，独立型脚本语言在其执行时完全依赖于解释器，而嵌入型脚本语言通常在编程语言中（如 C，C++，VB，Java 等）被嵌入使用

{% blockquote 阮一峰 http://javascript.ruanyifeng.com/introduction/intro.html#toc2 什么是 JavaScript 语言%}
JavaScript 是一种嵌入式（embedded）语言。它本身提供的核心语法不算很多，只能用来做一些数学和逻辑运算。JavaScript 本身不提供任何与 I/O（输入/输出）相关的 API，都要靠宿主环境（host）提供，所以 JavaScript 只合适嵌入更大型的应用程序环境，去调用宿主环境提供的底层 API。
{% endblockquote %}
JS 在 web 方面应用，目前主要在宿主环境有 NODE 和浏览器，客户端与服务端。

### js 的核心语法

> 类对象编程语法

1. 基本的语法构造
2. 标准库
3. 宿主环境提供的 API

#### 基础语法

> 基于规则的一门语言，类似我们学的自然语言，从最简单的拼音字母到词乃至句子表达的意思——对于程序来说则意味着命令

1. 变量
2. 标识符（保留字）
3. 注释
4. 区块
    - 作用域
    - `var const 与 let`
5. 逻辑语句
    - 条件语句——if、switch、? :
    - 循环语句——for、do until、while（`break跳出当前循环、continue用于终止循环`）
    
#### 数据类型
>js作为一门动态类型语言，其实是没有所谓类型可言，对语言引擎和开发人员来说，类型是值的内部特征，它定义了值的行为，以使其区别于其他值

1. 空类型
    * null
    * undefined
2. 原始类型/值类型——变量存值新开内存
    * string——base64（字符编码二进制转换用于传输）btoa()、atob()
    * number——64位浮点数、精度、范围、parseInt（string,formats）、parseFloat
    * boolean——自动转为false的情况：undefined、null、NaN、0、''
3. 复合类型/引用类型——变量存对象地址新开内存，属性值另外存
    * object——子类有 `array、function、object`

>对象深、浅拷贝
>null undefined区别
>值类型确定：typeof、instanceof、Object.prototype.toString
>NaN（Not a Number）——任何运算都是自身，属于Number；Infinity——无穷大/小，非0数值除以0；两者运算求导，不可导为NaN

**typeof**
```
typeof a === 'undefined' // 用于检测变量、函数是否定义
!a && typeof a === 'object' // 检测null，可检测出的为除null基本数据类型+function
```

#### 对象

概念： 数据集或功能集——面向对象编程
```
    抽象性：抽取我们所需要的数据信息等
    封装性
    继承性
```
增删查改
```
    // 增改
    obj.key
    obj['key'] 
    // 遍历
    key in obj
    Object.keys(obj) // 自身属性
    for (let key in obj) // enumerable属性（结合hasOwnProperty修改自身key）
    // 删除
    delete obj.key // configurable为true的属性，否则为false
```
#### 数组

继承对象的特性，需要区分类数组（arguments）
类数组转为数组或使用数组方法：

```
var arr = Array.prototype.slice.call(arrayLike)
Array.prototype.forEach.call(arrayLike, fn)
```