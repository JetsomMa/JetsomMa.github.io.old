---
layout: post
title: Linux系统中Docker服务的安装
date: 2022-06-29 
tags: docker    
---


## Linux系统中Docker服务的安装

#### 一、查询系统内核版本

```shell
# 需要CentOS系统的内核版本高于 3.10
uname -r
```

<img src="/Users/mashaojie/Library/Application Support/typora-user-images/image-20220629181617651.png" alt="image-20220629181617651"  />

#### 二、更新yum

```shell
yum update
```

#### 三、执行 sudo yum install -y yum-utils device-mapper-persistent-data lvm2

目前尚不清楚这一步是在干嘛

#### 四、设置docker版本列表信息查询路径

```shell
# 从docker官网拉取版本列表
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

#### 五、安装

###### 方式一：安装默认最新稳定版本

```shell
 yum -y install docker-ce
```

###### 方式二：指定版本安装

```shell
# 从docker官网拉取版本列表
yum list docker-ce --showduplicates | sort -r
```

<img src="/Users/mashaojie/Library/Application Support/typora-user-images/image-20220629183750586.png" alt="image-20220629183750586" style="zoom: 33%;" />

```shell
# 安装指定版本的docker
yum install docker-ce-17.03.3.ce-1.el7
```

#### 六、验证安装结果

```shell
# 查询所安装的docker版本
docker version
```

<img src="/Users/mashaojie/Library/Application Support/typora-user-images/image-20220629184233539.png" alt="image-20220629184233539" style="zoom:50%;" />

###### 七、启动docker

```shell
# 启动docker服务
systemctl start docker
# 查看docker版本信息
docker version
```

当看到下面截图中的内容，则表示已启动成功

<img src="/Users/mashaojie/Library/Application Support/typora-user-images/image-20220629185539344.png" alt="image-20220629185539344" style="zoom:50%;" />

#### 八、docker使用命令

```shell
# 查看docker镜像
docker images
# 查询开源镜像
docker search mysql
# 拉取docker镜像
docker pull localhost:5000/nuclein-sampling-queue-phone:0.1.2
# 查看正在运行的docker容器
docker ps
# 查看所有docker容器【包含正在运行的和已经停止运行的】
docker container ls --all
# 停止某个docker容器运行
docker stop f79a36d37e51
# 删除某个docker容器
docker container rm f79a36d37e51
# 启动运行docker容器
docker run --name nuclein-sampling-queue-phone -p 8080:80 -d localhost:5000/nuclein-sampling-queue-phone:0.1.2
```



#### 九、docker安装mysql数据库

```shell
# 查看mysql服务镜像
docker search mysql
# 拉取镜像
docker pull mysql
# 启动mysql服务容器
docker run --name mysql -v [数据存储地址]:/var/lib/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=[数据库密码] -d mysql:latest
```

#### 十、docker安装mysql数据库

```shell
# 拉取镜像
docker pull nginx
# 先第一次启动nginx服务容器
docker run --name nginx -p 80:80 -d nginx
# 查询nginx容器服务Id
docker ps
# 复制关键配置到宿主机
mkdir /home/jetsom/
mkdir /home/jetsom/nginx/
mkdir /home/jetsom/nginx/etc/
mkdir /home/jetsom/nginx/etc/conf.d/
mkdir /home/jetsom/nginx/log
mkdir /home/jetsom/nginx/html
docker cp 8051e5beaa2f:/etc/nginx/nginx.conf /home/jetsom/nginx/etc/nginx.conf
docker cp 8051e5beaa2f:/etc/nginx/conf.d/default.conf  /home/jetsom/nginx/etc/conf.d/default.conf
# docker cp 8051e5beaa2f:/var/log/nginx/access.log  /home/jetsom/nginx/log/access.log
# docker cp 8051e5beaa2f:/var/log/nginx/error.log  /home/jetsom/nginx/log/error.log
# docker cp 8051e5beaa2f:/usr/share/nginx/html/index.html /home/jetsom/nginx/html/index.html
# 停止运行现在的nginx容器
docker stop nginx
# 查询所有docker容器，找到已经停止运行的nginx容器的Id，进行删除
docker ps -a # 等同于 docker container ls --all 
docker container rm nginx/[containerId]
# 按照文件夹挂载方式重新启动nginx服务
docker run --name nginx -e TZ="Asia/Shanghai" -p 80:80 -v /home/jetsom/nginx/etc/nginx.conf:/etc/nginx/nginx.conf -v /home/jetsom/nginx/etc/conf.d/default.conf:/etc/nginx/conf.d/default.conf -v /home/jetsom/nginx/html:/usr/share/nginx/html -v /home/jetsom/nginx/log:/var/log/nginx --privileged=true -d nginx
# 容器启停指令
docker restart nginx  # 重启nginx
docker stop nginx
docker start nginx
# 进入容器内部命令
docker exec -it [containerId/name] /bin/bash  # docker exec -it nginx /bin/bash
# 退出容器内部命令
exit
# 查看docker容器启动日志
docker logs nginx
```

#### 十一、安装docker镜像仓库

```shell
# 查看registry服务镜像
docker search registry
# 拉取registry镜像
docker pull registry
# 查看镜像历史记录
docker history registry:latest
# 启动registry容器
docker run -d -p 5000:5000 --restart=always --name registry registry

### 解决pull镜像不成功问题
  # 调用命令: docker pull 118.195.236.91:5000/spatial-modelling-data-server:0.1.5
  # 报错信息: Error response from daemon: Get "https://118.195.236.91:5000/v2/": http: server gave HTTP response to HTTPS client
### 解决办法: 
  # 1. 编辑docker daemon.json：
 	vim etc/docker daemon.json
	# 2. 在文件中添加如下内容：
  {
    "insecure-registries": ["192.168.1.26:5000"]
  }
	# 3. 重新加载daemon：
	systemctl daemon-reload
	# 4. 重启docker：
	systemctl restart docker
```

