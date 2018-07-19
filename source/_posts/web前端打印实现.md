---
title: web前端打印实现
date: 2018-07-16 16:40:34
tags: web打印
---

### 背景需求

> 基于 web 现代浏览器实现批量打印电子面单（规格固定），各种带条码的单据（规格范围不固定）;如下所示

{% img /assets/webPrint1.png 200 %}
{% img /assets/webPrint2.png 200 %}

<!-- more -->

### web 打印技术调研

#### 浏览器的菜单打印功能

这是最简单的，只需点击页面上的打印菜单，但是也是问题最多的，基本上是不能满足用户需要。比如：不能精确分页，有出现打出半行字的风险；改变纸型后打印出的格式和页面显示的格式相差太大；页眉页脚也需要从菜单中去设置，等等等等。这种方案最大的优势就是不需要做任何代码，点击打印就可以了。

#### window.print()

这实际上，是浏览器打印功能菜单的一种程序调用。与点击打印功能菜单一样，不能精确分页，不能设置纸型，套打的问题更加无从谈起，只不过，可以让用户不用去点菜单，直接点击网页中的一个按钮，或一个链接里面调用罢了。
需要指出的是这种方法提供一个打印前和打印后的事件 onbeforeprint、onafterprint。可以在打印前的时候重新编辑一些格式，专门送去打印，打印后又处理回来。

```js
function window.onbeforeprint()
{
//将一些不需要打印的隐藏
}
function window.onafterprint()
{
//放开隐藏的元素
}
```

事实上，很多用户都是采用这种方式打印，但是这种方式最致命的缺点是不能设置打印参数，比如纸型，页边距，选择打印机等等。

#### 导出 excel 导出 pdf 文件的打印

将需要打印的数据导出 excel 文件或者导出 pdf 文件，然后打开 excel 文件或者 pdf 文件重新打印，用这种方案能实现精确的打印，套打也能实现，但是需要客户端安装 excel 和 adobe 软件，操作起来也有些麻烦，并且导出的 excel 文件可以重新修改编辑，一般用户都会要求系统提供这种导出的方案，也同时需要直接打印的功能，所以个人觉得这种方案也不能很好的解决打印的问题。

#### 纯 activex 控件

这种方案其实就是编写一个 c/s 的打印控件，然后嵌入到页面里面，将要打印的数据装入到控件中，然后打印。这种方案的优点是打印精度高，分页，设置打印参数等等都能实现。但是缺点也是很明显的，嵌入 activex 控件破坏了 web 应用的整体 html 风格，且这样的控件通常都比较大，一般都超过 1m，下载很慢。

#### applet 方式

采用 applet 方式，分页或精确打印，都可以做到完美，但缺点也很明显，表现在：
安装 applet 成本巨大。需要下载十几 m 的文件。
applet 本身可能并不大，但运行 applet 所需的 jre 一般>10m。用户需要极大的耐心，来进行打印。
打印报表时，需要重新向服务器检索数据，效率低。
因为 applet 方案，一般采用 html 方式呈现数据，打印时 applet 必须向服务器检索同一张票据的数据，看上去，是打印了当前页的票据，实际上，applet 根本不会用当前 html 页的数据来打印，而是向服务器下载数据到 applet 中来打印。也就是说，打印的话，必须两次请求，一次 html 呈现，一次用来打印。
市场上 java 类的报表工具，一般推荐 applet 方式来实现打印。

#### 轻量级的 activex 插件＋ dhtml ＋ javascript ＋后台代码（动态获取数据）

轻量级 activex 插件：设置打印参数，比如预定义纸型，设置打印方向，打印边距，指定打印机，不弹出打印对话框直接打印等等。
dhtml ＋ javascript 编辑打印数据的格式展现，实现格式的自定义。
后台代码，可以是 java，dotnet 等等的，实现动态获取打印数据。
这种方案是比较理想的，只需要客户端下载一个很小的打印插件，客户端无需安装任何 c/s 的格式设计

{% blockquote 马开东博客 http://www.makaidong.com/Web%E5%89%8D%E7%AB%AF1/224425.shtml 几种web表格打印方案的比较 %}
以上来源:
{% endblockquote %}

主要有如下主流控件：

- ScriptX
- PAZU
- jatoolsPrinter
- lodop
  国内主要是 jatoolsPrinter 和 lodop，对比后 lodop 最合适——技术支持，免费的开放功能丰富

### lodop 实现打印

#### 打印工具说明

Web 打印控件[Lodop](http://www.c-lodop.com/index.html)
以插件形式扩展 JavaScript 打印编程接口，使得 web 可以操作控制打印,支持超文本打印或自定义指令打印

#### 打印设计流程

1.  页面检测打印控件安装状态并提示

2.  用户设置打印机、打印格式、字体等

3.  勾选表格数据并点击打印弹框预览

4.  打印状态返回并修改单据打印状态

#### 项目实现

##### 基本流程

> 你可能需要教程资料：[C-Lodop 技术手册下载](http://www.c-lodop.com/download/C-LodopNoteBook20170904.zip)、[入门样例及说明](http://www.c-lodop.com/LodopDemo.html)

1.  打印控件引入及引导安装控件 JS 文件——LodopFuncs.js
    对该文件的主函数 getLodop 模块化导出，修改相关文件里安装、调用和升级的程序代码为项目的弹框提示，去除升级检测。

    ```js
    export function getLodop(oOBJECT, oEMBED) {}
    ```

2.  控件在页面内以 object 元素形式被引用。设置元素的 width 和 height 等于 0 来隐藏控件。

3.  采用 js 动态拼接字符串生成 html+css 的超文本并发送到打印插件进行打印

    ```js
    LODOP.ADD_PRINT_HTM('0mm', '0mm', '100%', '100%', strFormHtml)
    ```

4.  设置定时器监听回调捕获打印状态

    ```js
    LODOP.On_Return_Remain = true
    LODOP.On_Return = function(TaskID, Value) {
      // 根据TaskID和Value判断打印状态
    }
    ```

##### 模块化封装并设置打印回调

1.  模块化各个打印单据类型
    项目里面有多个单据，打印的排版，指定打印机，内容等都有所不同，通过定义一个打印主函数，模块化封装各个单据及对应对应参数。我们只需在页面中调用函数并传入单据类型、打印数据及相关参数就可以实现具体单据的打印。
    ```js
    export printDemo1 = function(data){
        printStyle = `样式控制`
        printHtml = `填入数据的html模板`
        const params = {
            type: `Demo1`,
            printStyle: printStyle,
            printHtml: printHtml,
            size: {
                width: 800,
                height: 600
            }
        }
        return params
    }
    ```
2.  主函数流程控制
    根据传入不同单据类型做处理并执行打印指令
    ```js
    import printDemo1 from './printModule/demo1'
    export function print(type, data) {
        switch (type) {
            case 'Demo1':
            {
                let params = printDemo1(data)
                return doPrint(params)
            }
            case 'Demo2': ...
        }
    }
    ```
    doPrint 函数执行打印返回一个 promise 对象，打印取消、成功或失败后的回调
    ```js
    function doPrint(params) {
        return new Promise(function (resolve, reject) {
            // 打印取消
            reject('cancel')
            // 打印成功
            resolve('success')
            // 打印失败
            reject('error')
        }
    }
    ```
3.  实际调用

```js
print(data)
  .then(res => {
    console.info('打印成功')
  })
  .catch(err => {
    if (err === 'cancel') {
      console.warning('打印取消')
    } else {
      console.error('打印失败')
    }
  })
```

### 电子面单打印——菜鸟打印组件

#### 打印工具说明

1.  菜鸟打印组件——模板设计器、打印客户端以及相应的接口和协议(**菜鸟物流接入的电子面单都可以打除了顺丰**)
2.  模板设计可自定义设置电子面单模板并生成相关代码
3.  打印机客户端用于 web 交互通讯打印相关电子面单
4.  相关接口协议用于前端 web 调用并控制打印设置

#### 打印流程

[菜鸟打印 demo 文档](http://cloudprint-docs-resource.oss-cn-shanghai.aliyuncs.com/printTest.html)
大致流程如下，相关代码均可写在一个 cainiaoChat.js 文件中并像 lodop 打印那样子模块化并做相关回调处理，打印的实现效果也和上文一致。相关的接口文档比较完善这里就大致讲解一下。
{% img /assets/cainiaoPrint.png %}

1. 连接打印机
打印组件客户机soket接口为13528（https的话，端口是13529）
新建websoket连接打印组件并设置相关websoket监听事件

2. 配置打印机,可配置项如下：
打印机名称
模板上下联logo
水平和垂直偏移量
页面有无空边
打印机纸张宽高

3. 获取电子面单数据并构建打印请求
异步请求获取到要打印的电子面单数据=>按照格式要求构造request对象=>把request用websoket发送至菜鸟组件生成电子面单=>打印或预览设置