---
layout: post
title: Nodejs镜像源管理工具(nrm安装与使用)
date: 2019-07-21 
tags: Nodejs    
---

### 1.nrm 简介

nrm(npm registry manager )是 npm 的镜像源管理工具，有时候国外资源太慢，使用这个就可以快速地在 npm 源间切换

### 2.安装 nrm

在命令行执行命令，全局安装 nrm。

```shell
npm install -g nrm
```

### 3.增加 npm 的镜像源

你可以增加定制的源，特别适用于添加企业内部的私有源，执行命令，其中 reigstry 为源名，url 为源的路径。

```shell
nrm add <registry> <url>
```

例如：新增 goose 镜像源

> nrm add goose http://goose.nineheaven.top:4873/

### 4.查看 npm 的镜像源

执行命令，查看可选的源。其中，带\*的是当前使用的源，上面的输出表明当前源是官方源。

```shell
nrm ls
```

例如：

> nrm ls
>
> npm -------- https://registry.npmjs.org/
>
> yarn ------- https://registry.yarnpkg.com/
>
> cnpm ------- http://r.cnpmjs.org/
>
> \* taobao ----- https://registry.npm.taobao.org/
>
> nj --------- https://registry.nodejitsu.com/
>
> npmMirror -- https://skimdb.npmjs.com/registry/
>
> edunpm ----- http://registry.enpmjs.org/
>
> goose ------ http://goose.nineheaven.top:4873/

### 5.切换 npm 的镜像源

如果要切换镜像源，执行命令。其中 reigstry 为源名

```shell
nrm use <registry>
```

例如：切换到 goose 镜像源

> nrm use goose

### 6.测试镜像源速度

你还可以通过 nrm test 测试相应源的响应时间。其中 reigstry 为源名

```shell
nrm test <registry>
```

例如：测试 goose 镜像源速度

> nrm test goose

### 7.删除 npm 的镜像源

执行命令，删除对应的源。其中 reigstry 为源名

```shell
nrm del <registry>
```

例如：删除 goose 的镜像源

> nrm del goose
