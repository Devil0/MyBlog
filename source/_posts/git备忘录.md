---
title: Git备忘录
date: 2018-04-10 11:01:46
tags: git
---

{% img [class names] /assets/gitCommand.png 600 350 %}

> Git 理解与常用命令汇总

### git 工作流

作为一个**版本控制管理系统**工具，如何做到的？分布式管理仓库，分为当前工作区和.git 文件保存的文件版本管理区；一个版本统一 hash 值作为版本 ID；文件在工作区修改之后都需要提交更改到本地 git 的版本管理中才能推送更改到远程仓库中。具体的操作看图片
<!-- more -->
{% img /assets/gitWorkFlow.jpg %}

1. 任何对象都是在工作区中诞生和被修改;
2. 任何修改都是从进入 index 区才开始被版本控制;
3. 只有把修改提交到本地仓库，该修改才能在仓库中留下痕迹;
4. 与协作者分享本地的修改，可以把它们 push 到远程仓库来共享。

### 本地版本管理

* HEAD，它始终指向当前所处分支的最新的提交点。你所处的分支变化了，或者产生了新的提交点，HEAD 就会跟着改变，Git 允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`
* 版本标签:

```
命令git tag <name>用于新建一个标签，默认为HEAD，也可以指定一个commit id；

git tag -a <tagname> -m "blablabla..."可以指定标签信息；

git tag -s <tagname> -m "blablabla..."可以用PGP签名标签；

命令git tag可以查看所有标签。
```

### 远程仓库同步

* 关联远程仓库：`git remote add origin git@server-name:path/repo-name.git`、`git clone git@github.com:Devil0/repository.git`
* 同步操作：`push`、`pull`、`fetch`

### 分支管理

brach用于开发协同管理，涉及展示分支，切换分支，创建分支，删除分支这四种操作