---
layout: post
title: MAC笔记本配置环境变量
date: 2020-04-07
tags: Mac    
---

## MAC笔记本配置环境变量【操作.bash_profile文件】

### 1. 在命令行中输入
 `export PATH=/usr/bin:/usr/sbin:/bin:/sbin:/usr/X11R6/bin`

这样可以保证命令行命令暂时可以使用。命令执行完之后先不要关闭终端。
 如果你的命令行命令可以使用，请直接跳到第2步。

### 2. 进入当前用户的home目录
 `cd ~/`

### 3. 打开.bash_profile并编辑
 `open .bash_profile`

如果.bash_profile文件不存在，则执行创建命令。

创建.bash_profile文件：
 `touch .bash_profile`

### 4. 这样就打开了一个记事本，会显示你之前配置过的path，修改记事本，强烈建议先备份下，然后根据自己需要配置。### 

```shell
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_191.jdk/Contents/Home
export ANDROID_HOME=/Users/XXXXX/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/platform-tools 
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/build-tools/28.0.3  
export PATH=/usr/local/bin:/usr/local/sbin:~/bin:$PATH
```

如果是配置node，则在配置最后一行加上

```shell
export PATH=$PATH:/usr/local/opt/node@10/bin
```

### 5.command+s保存关闭文件

### 6.使修改后的配置生效命令
 `source .bash_profile`

### 7.验证环境变量是否配置成功

如果是java,在终端输入以下命令
 `java -version`

如果是node,在终端输入以下命令
 `node -v`

***当能正常展示版本号，则表示配置成功！***