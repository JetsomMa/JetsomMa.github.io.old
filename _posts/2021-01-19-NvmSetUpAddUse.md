---
layout: post
title: Nodejs版本管理工具(nvm安装与使用)
date: 2021-01-19
tags: Nodejs    
---

### 1.nvm 简介

nvm(npm version manager )是 npm 的版本管理工具，可以同时管理多个node版本，便于临时切换使用。

### 2.安装 nvm

#### windows下载安装

github上下载最新版本:https://github.com/coreybutler/nvm-windows/releases

#### mac下载安装

##### （1）install brew

打开终端输入下列命令

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

##### （2）install nvm

如果在本地电脑上已经安装了node的话，可以先写在卸载掉。如果是brew命令安装的话，直接使用下列命令卸载即可

```shell
brew uninstall node
```

下载好brew之后，使用brew 工具下载nvm,在终端输入下列命令

```shell
brew install nvm
```

##### （3）配置环境变量

将下面这段代码添加至你的环境变量，如果用的是base则配置.bash_profile;如果用的是oh-my-zsh则配置.zshrc

```shell
# This loads nvm
export NVM_DIR="$HOME/.nvm"
export PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

使环境变量生效

```shell
source ~/.bash_profile 
or
source ~/.zshrc 
```

### 3.使用nvm命令

```shell
nvm --version  #查询nvm版本信息
nvm ls-remote  #查看服务器上所有node可用的版本
nvm install 12 #安装大版本为12的node的最新版本【一般都是安装LTS的稳定版本】
nvm ls         #查看本地安装的node版本列表
nvm use [XX.xx.xx] #临时切换到指定版本的node【版本号为本地版本号】
nvm alias default [XX.xx.xx] #永久切换到指定版本的node【版本号为本地版本号】
```
