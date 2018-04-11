---
title: hexo+next建站踩坑记
date: 2018-04-11 16:45:32
tags: blog
---
{% img /assets/myBlog.png %}

>HEXO: A fast, simple & powerful blog framework
>NEXT: A high quality elegant Hexo theme. It is crafted from scratch, with love

### hexo搭建网站
丢上[官网文档](https://hexo.io/zh-cn/docs/index.html)，按照官网的步骤走
这里要了解一下各个目录文件的作用：
* public——最终生成的网站文件
* scaffolds——生成文件模板，可定制个性化
* source——顾名思义，我们对项目的内容编辑都在这里进行
    1. 博文——分为草稿、发布
    2. 静态资源存放地
    3. 其他页文件
* thems——主题目录，对应得博客主题就存放在这里，当然也可以放多个随意切换

<!-- more -->

###  next主题引入与配置
在引入next主题之前我是用yalia主题的，奈何发现各种小bug，虽然可以自定义修改，但是安装了npm包后，hexo编译主题文件不是一堆内存溢出就是打开文件太多错误提示。这个问题怀疑是hexo编译主题的时候把node_module一起......想想都可怕；移除了node_module之后就不会了，然后移进来又会了，oh shit！再去看着yalia一堆open的issue果断转投next。

还是丢[官网文档](http://theme-next.iissnan.com/getting-started.html)，感觉官网文档很不错，不过坑还是有的，或者有一些雾里看花压根就没怎么和你讲。
* 侧边栏的各种关于、标签页都需要在配置新增之后显示，而后用hexo新增对应的页面，博客url管理是日期或者对应文件目录形式，可以看看public文件下面就知道了
* 查找功能
    1. 注意版本
    2. 本地查找一般没啥毛病——原理是基于本地搜索引擎全局查找web站点页面
    3. algolia插件——远程备份需要查找匹配列表的关键信息（json形式），查找时去请求匹配到对应的数据，以便达到快速定位的查找项
```
创建注册algolia账号
安装hexo algolia用于生成推送文档关键词
配置站点配置文件
配置主题配置文件
推送更新
打开搜索功能
```