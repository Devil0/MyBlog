---
title: Vue项目内嵌Cordova
date: 2018-07-10 14:12:04
tags: Hybrid APP 架构
---

{% img /assets/cordova.png %}

> 现下前端可谓是花开满地，在各个地方异彩纷呈，问题也接踵而至（。。。怎么这么多成语）。多端统一是愿景，[cordova](http://cordova.apache.org/)便是其中之一，不巧项目有用到，做一个小 demo 总结吧。

### 小项目介绍

本项目用 vue cli(3.0.0) + vue(2.5.16) + cordova(8.x)构建一个项目原型——Vue 项目内嵌 cordova 安卓平台。按流程走，有坑的地方已经用我的肉体填平了,相关源码地址请参阅[vue-cordova](https://github.com/Devil0/vue-cordova)。
<!-- more -->
### 认识 cordova

Apache Cordova 是一个开源的移动开发框架。允许你用标准的 web 技术-HTML5,CSS3 和 JavaScript 做跨平台开发。 应用在每个平台的具体执行被封装了起来，并依靠符合标准的 API 绑定去访问每个设备的功能

> 跨平台开发；调用底层硬件设备

{% img /assets/cordovaFrame.png %}

### cordova 安装使用

1.  [官网](http://cordova.apache.org/#getstarted)入门 demo
2.  搭建安卓平台环境
3.  添加插件
4.  设置热更新
5.  调试:android studio\google_inspect

##### 安卓开发环境搭建

> 准备工作：下载安装[android studio](http://www.android-studio.org/index.php/download)、[JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)、[Android SDK](http://tools.android-studio.org/index.php/sdk)

1.  JDK——JDK 的全称是 Java SE Development Kit，也就是 Java 开发工具箱。SE 表示标准版。JDK 是 Java 的核心，包含了 Java 的运行环境（Java Runtime Environment），一堆 Java 工具和给开发者开发应用程序时调用的 Java 类库，JDK 包含的基本工具主要有： - javac：Java 编译器，将源代码转成字节码。 - jar：打包工具，将相关的类文件打包成一个文件。 - javadoc：文档生成器，从源码注释中提取文档。 - java：运行编译后的 java 程序。
    配置 JDK 的系统变量环境——JAVA_HOME
2.  android studio——集成的 Android 开发工具用于开发和调试（自带 gradle 构建安卓项目）
3.  Android SDK——Android SDK 提供了开发 Android 应用程序所需的 API 库和构建、测试和调试 Android 应用程序所需的开发工具，环境变量配置：Android SDK 安装目录下的 platform-tools 和 tools——ANDROID_HOME

> tips：
1.  cmd 命令——echo %path%查看修改成功与否（可能需要重新打开 cmd 窗口）
2.  jdk10（最新版本有坑别去踩），jdk8 就行了——影响安卓 sdk 安装（检测不到 jdk 环境）
3.  安装安卓 sdk 可以选择 exe 或文件下载。注意要配置 tools 和 platform-tools，下载安卓不同版本的开发包（Android target）
4.  安卓项目构建需要 gradle，可能还有些依赖，需要下载 android studio 帮忙配置（设置离线 gradle，下载代理加速，这些 Cordova 做不了）
5.  不要 cnpm 安装 cordova，不然只能安装 6.0.0 的否则 create 不了项目（报错文件缺失）

### 初始化 Vue 项目

依次运行如下命令：

```js
npm install -g @vue/cli
vue create vue-cordova
```

相关文档参照[vue cli 文档](https://cli.vuejs.org/guide/creating-a-project.html#installation)——得到如下图（这时候可能大概需要在项目下面创建一个 vue.config.js 用来配置项目打包运行）
{% img /assets/vueInit.png %}

### 在 vue 项目下创建 cordova

1.  安装 cordova 命令

```js
npm install -g cordova
```

2.  创建 cordova 项目

```
cordova create cordovaDemo Apache.myCompany.cordovaDemo cordovaDemo
```

3.  项目配置修改
    - 在 vue 根项目下.ignore 文件中添加如下代码，之后在 cordovaDemo 目录下运行 cordova prepare 会自动安装平台和插件
    ```
    # cordova
    cordovaDemo/platforms
    cordovaDemo/plugins
    cordovaDemo/www
    cordovaDemo/res
    ```
    - index.html 插入 cordova.js
    ```html
    <body>
        <div id="app"></div>
        <script type="text/javascript" src="cordova.js"></script>
        <!-- built files will be auto injected -->
    </body>
    ```
    - vue 项目 main.js 中配置在设备准备后才加载 vue
    ```js
    let app = {
      // Application Constructor
      initialize: function() {
        document.addEventListener(
          'deviceready',
          this.onDeviceReady.bind(this),
          false
        )
      },
      // deviceready Event Handler
      //
      // Bind any cordova events here. Common events are:
      // 'pause', 'resume', etc.
      onDeviceReady: function() {
        new Vue({
          render: h => h(App)
        }).$mount('#app')
      }
    }
    app.initialize()
    ```
    - 修改 Vue 项目打包生成路径，打包引用为相对路径
    ```js
    module.exports = {
      // 设置打包引用为相对路径
      baseUrl: './',
      // 输出构建的文件的地方
      outputDir: 'cordovaDemo/www'
    }
    ```

### 小试牛刀——调用手机拍照 hybrid app

> 到目前为止基本的 Vue 集成 cordova 项目就完成了，可以使用 cordova 进行开发 Hybrid App；在这里我们添加一个安卓平台并调用拍照功能获取我们拍到的照片

1.  添加安卓平台

```js
cordova platform add android --save
```

2.  添加相机插件

```js
cordova plugin add cordova-plugin-camera --save
```

我们可以在 cordova 的项目下的*config.xml*配置文件最后面看·到如下代码，大家可以类比这个*config.xml*相当于我们平常项目里的 package.json 管理依赖，运行**cordoava prepare**即可自动安装项目所需插件与平台

```html
<plugin name="cordova-plugin-camera" spec="^4.0.3" />
<engine name="android" spec="7.0.0" />
```

3.  调用相机的代码——此处应该查看[api 文档](http://cordova.apache.org/docs/en/latest/reference/cordova-plugin-camera/index.html)

```html
<template>
  <div class="app">
    <h1>Apache Cordova</h1>
    <div id="deviceready" class="blink">
      <p class="event listening">Connecting to Device</p>
      <p class="event received">Device is Ready</p>
    </div>
    <button @click="handleCamera()">打开相机</button>
    <button @click="showNavigator()">查看navigator.camera</button>
    <img id="myImage" src="" alt="获取相片">
  </div>
</template>
```

```js
    onSuccess(imageData) {
        var image = document.getElementById('myImage');
        image.src = "data:image/jpeg;base64," + imageData;
    },

    onFail(message) {
        alert('Failed because: ' + message);
    },

    handleCamera() {
        navigator.camera.getPicture(this.onSuccess, this.onFail, {
            quality: 25,
            destinationType: window.Camera.DestinationType.DATA_URL
        });
    }
```

4.  构建项目
    - vue 项目打包——生成 cordova 目录下的 www 文件
    ```js
    npm run build
    ```
    - cordova 项目打包 www 文件(注意**切换目录**)
    ```js
    cordova build android
    ```
    生成 apk 文件，我们安装到手机上——嗯!可以用的
    {% img /assets/cordovaDemo.png %}
5.  貌似到这里就完了，可是。。。 每次都要发布 app 不累吗？而且用户也不喜欢啊！所以热更新来了
> [插件 Cordova Hot Code Push Plugin](https://github.com/nordnet/cordova-hot-code-push),基本原理是为 www 目录下的每个文件加多一个 hash 值追踪(chcp.manifest 文件)，有更改的文件便会被在 apk 启动检查到然后被更新

    1. 添加热更新插件,开发环境部署测试，热更新部署命令
    ```js
    cordova plugin add cordova-hot-code-push-plugin --save
    cordova plugin add cordova-hot-code-push-local-dev-addon --save
    npm install cordova-hot-code-push-cli --save
    ```
    2. 执行热更新发布并开启热更新环境
    ```js
    cordova-hcp server
    // 或者只是执行热更新发布**这时候需要部署到线上环境**
    cordova-hcp build
    ```
    这时候会生成cordovaDemo下的**.chcpenv**、www目录下的**chcp.json**、**chcp.manifest**文件
    3. 根据线上部署或生产调试设置热更新检查的--URL--(项目的地址，确保apk能访问得到配置文件)
    ```html
    <chcp>
        <config-file url="URL/chcp.json" />
    </chcp>
    <plugin name="cordova-plugin-camera" spec="^4.0.3" />
    <plugin name="cordova-hot-code-push-plugin" spec="^1.5.3" />
    <plugin name="cordova-hot-code-push-local-dev-addon" spec="^0.4.2" />
    <engine name="android" spec="7.0.0" />
    ```
    最后cordova打包生成apk并安装（这里我指定URL为本机的局域网IP和对应的端口）
    4. 测试更新效果
        - 修改**Apache Cordova** 为**HotCodePush Cordova**
        - Vue项目重新打包
        - 执行*cordova-hcp server*
        - apk退出重新打开过几秒就可以看到，修改成功
        {% img /assets/cordovaHotCodePush.png %}


### 一路走来好像顺风顺水的，其实并不是啊——调试！！！

> 安卓真机调试我们可以用*chrome://inspect/#devices*或者*android studio*，当然后者更强大不过有时候对前端小伙伴来说 Chrome 的就够了，而且也亲切。

1.  chrome://inspect/#devices 方式
    - 直接在谷歌 url 复制粘贴该地址回车便可以进入该模式，开启*Discover USB devices*发现设备
    - 打开手机的*usb 调试*模式，需要先打开开发者模式
    - 连接手机打开网页，点击设备下出现的网页**inspect**进入调试
      {% img /assets/chromeInspect.png %}
      {% img /assets/inspectCordova.png %}
2.  android studio
    - 支持虚拟设备和真机调试
    - 指定 android sdk
    - 需要 android sdk 下的 ADB——用于连接调试设备
    - gradle 构建，apk 安装到手机，手机 apk 面的日志会打印到 logcat 中
      {% img /assets/studioDeploy.png %}

> tips
1.  inspect 时报错 http1.1 404 Not found——第一次使用 google inspect 需要翻墙下载一些东西
2.  vue 项目 build 到 cordova 源项目；cordova 项目 build 到安卓项目，Android studio run 进行调试（或者直接 cordova 打包）
3.  cordova 项目的 index 中引入 Content-Security-Policy——不允许在 html 中直接添加事件，可以将它注释掉
4.  部分 google 的 inspect 不兼容所有的手机网页所以测试是否连接成功首选手机 google 调试，怀疑和内核有关或者部分 app 内部做了限制
