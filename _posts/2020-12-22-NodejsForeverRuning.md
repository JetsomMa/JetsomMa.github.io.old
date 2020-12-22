\---

layout: post

title: nodejs服务后台持久化运行

date: 2020-12-22

tags: MarkDown    

\---

>  forever是一个nodejs守护进程，完全由命令行操控。forever会监控nodejs服务，并在服务挂掉后进行重启。

### 1、安装 forever

```shell
npm install forever -g
```

### 2、配置环境变量

a、查找forever服务路径

```shell
find / -name forever
```

b、打开环境变量配置文件

```shell
vim ~/.bash_profile
```

c、添加环境变量配置

```shell
PATH=$PATH:$HOME/bin
###新添加代码
PATH=$PATH:/root/nodejs/bin/
###新添加代码完毕

export PATH
```

d、使环境变量配置生效

```shell
source ~/.bash_profile
```

### 3、启动服务

```shell
service forever start
```

### 4、使用 forever 启动 js 文件

```shell
forever start index.js
```

### 5、停止 js 文件

```shell
forever stop index.js
```

### 6、启动js文件并输出日志文件

```shell
forever start -l forever.log -o out.log -e err.log index.js
```

### 7、重启js文件

```shell
forever restart index.js
```

### 8、查看正在运行的进程

```shell
forever list
```

