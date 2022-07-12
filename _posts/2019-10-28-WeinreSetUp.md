---
layout: post
title: Weinre移动调试安装使用
date: 2019-10-28 
tags: Weinre    
---

### 安装weinre

weinre可以通过npm进行全局安装

`cnpm install -g weinre`

 安装完成后可通过命令查看版本号，命令如下：

`weinre -v`

### 启动weinre

执行下面命令启动weinre，端口号可根据自己的情况来定

`weinre --httpPort [端口号] --boundHost -all-`

例如：`weinre --httpPort 8080 --boundHost -all-`

### 开始调试

打开谷歌浏览器（-webkit内核）输入：http://127.0.0.1:8080/   会看到以下界面：

将截图中2标注的位置代码复制到移动前端项目的index.html页面中，并将ip改为电脑在就局域网中的IP地址，点击标注1进入移动调试页面。

<img src="https://mashaojie.cn/download/source/typora-user-images/image-20190925183204538.png" alt="image-20190925183204538" style="zoom:50%;" />

<img src="https://mashaojie.cn/download/source/typora-user-images/image-20190925210702586.png" alt="image-20190925210702586" style="zoom:50%;" />

<img src="https://mashaojie.cn/download/source/typora-user-images/image-20190925210726246.png" alt="image-20190925210726246" style="zoom:50%;" />

<img src="https://mashaojie.cn/download/source/typora-user-images/image-20190925210740719.png" alt="image-20190925210740719" style="zoom:50%;" />

<img src="https://mashaojie.cn/download/source/typora-user-images/image-20190925210753405.png" alt="image-20190925210753405" style="zoom:50%;" />

<img src="https://mashaojie.cn/download/source/typora-user-images/image-20190925210809205.png" alt="image-20190925210809205" style="zoom:50%;" />

<img src="https://mashaojie.cn/download/source/typora-user-images/image-20190925210836384.png" alt="image-20190925210836384" style="zoom:50%;" />

<img src="https://mashaojie.cn/download/source/typora-user-images/image-20190925210849264.png" alt="image-20190925210849264" style="zoom:50%;" />

### 参考文献

> https://blog.csdn.net/swimming_in_it_/article/details/78234262
>
> https://blog.csdn.net/seanxwq/article/details/80763861