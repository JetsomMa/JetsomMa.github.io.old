---
layout: post
title: centos修改yum镜像地址
date: 2020-03-08 
tags: Linux    
---

> 163 镜像是国内最好的yum 镜像之一，这里使用的是163的yum 镜像

### 1、备份镜像

```shell
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

### 2、进入 镜像目录

```shell
cd /etc/yum.repos.d/
```

### 3、备份原来镜像配置

```shell
mv CentOS-Base.repo CentOS-Base.repo.backup
```

### 4、下载163 镜像

```shell
wget http://mirrors.163.com/.help/CentOS7-Base-163.repo
```

### 5、修改163镜像文件名

```shell
mv CentOS7-Base-163.repo CentOS-Base.repo
```

### 6、最后一步，生成缓存

```shell
yum clean all
yum makecache
```

