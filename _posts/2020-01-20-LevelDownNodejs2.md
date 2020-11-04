---
layout: post
title: 降低node的版本
date: 2020-01-20 
tags: Nodejs    
---

## 降低node的版本

### 首先搜索您所需的软件包

```shell
brew search node
```

这可能会为您带来以下结果：

```shell
heroku/brew/heroku-node ✔
llnode
node@10
nodebrew
leafnode
node ✔
node@8
....
```

### 然后安装所需的版本

```shell
brew install node@8
```

### 还要记住，您可以同时安装多个节点软件包，但不能同时使用它们。因此，如果您`node`已经安装了最新/通用软件包，则需要先取消链接：

```shell
brew unlink node
```

### 然后，您可以链接其他版本

```shell
brew link node@8
```

### 对于某些较旧的节点版本（仅桶），可能需要将它们与`--force`和`--overwrite`选项链接：

```shell
brew link --force --overwrite node@8
```

***特别注意***--对于mac本，在完成以上操作之后，可能需要手动配置或更改环境变量【即.bash_profile文件】







