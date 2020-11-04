---
layout: post
title: Nginx服务器搭建（支持https）
date: 2019-10-13 
tags: Nginx   
---

## 一、安装nginx

### 1、编译环境依赖

 gcc和 gcc-c++

### 2、安装包及依赖包

nginx-1.13.12.tar.gz(安装包)、

openssl-1.0.1t.tar.gz(https请求依赖)、

pcre-8.39.tar.gz、(nginx的rewrite依赖)

zlib-1.2.11.tar.gz

### 3、新建nginx用户

使用root用户执行useradd nginx

为nginx用户设置密码 passwd nginx

### 4、上传安装包及依赖包到nginx用户并解压

tar -zxvf nginx-1.13.12.tar.gz

tar -zxvf openssl-1.0.1t.tar.gz

tar -zxvf pcre-8.39.tar.gz

tar -zxvf zlib-1.2.11.tar.gz

### 5、编译依赖环境

`cd openssl-1.0.1t`

执行`./config`

执行`make`

执行`sudo make install` (需要root权限执行此命令)

`cd pcre-8.39`

执行`./configure`

执行`make`

执行`sudo make install` (需要root权限执行此命令)

完成之后执行`./pcre-config –-version`查看pcre版本

`cd zlib-1.2.11`

执行`./configure`

执行`make`

执行`sudo make install`(需要root权限执行此命令)

### 6、安装nginx

`cd nginx-1.13.12`

执行

```shell
./configure --prefix=/home/nginx/nginx   				//指定nginx的安装目录
	--with-http_stub_status_module    					 	//使ngx_stub_status_module 模块可用
	--with-http_ssl_module    										//使用https协议模块
	--with-http_gzip_static_module 								//使nginx支持gzip资源的部署
	--with-http_realip_module 
	--with-http_sub_module 
	--with-openssl=/home/nginx/openssl-1.0.1t 		//读取openssl库的源码路径
	--with-pcre=/home/nginx/pcre-8.39    					//读取pcre库的源码路径
	--user=nginx    															//设置nginx工作进程的用户
	--group=nginx   															//设置nginx的工作进程的用户组
```

执行`make`

执行`make install`

## 二、配置nginx

### 1、配置证书和秘钥

在nginx/conf下新建文件夹cfssl，将证书和秘钥文件放在此目录下

### 2、修改配置文件

`vi nginx/conf/nginx.conf`

```shell
#运行用户
user nginx;

#启动进程,通常设置成和cpu的数量相等
worker_processes  1;

#全局错误日志及PID文件
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

#工作模式及连接数上限
events {
  #epoll是多路复用IO(I/O Multiplexing)中的一种方式,
  #仅用于linux2.6以上内核,可以大大提高nginx的性能
	use   epoll; 
	
  #单个后台worker process进程的最大并发链接数    
  worker_connections  1024;

  # 并发总数是 worker_processes 和 worker_connections 的乘积
  # 即 max_clients = worker_processes * worker_connections
  # 在设置了反向代理的情况下，max_clients = worker_processes * worker_connections/4
  # 为什么上面反向代理要除以4，应该说是一个经验值
  # 根据以上条件，正常情况下的Nginx Server可以应付的最大连接数为：4 * 8000 = 32000
  # worker_connections 值的设置跟物理内存大小有关
  # 因为并发受IO约束，max_clients的值须小于系统可以打开的最大文件数
  # 而系统可以打开的最大文件数和内存大小成正比，一般1GB内存的机器上可以打开的文件数大约是10万左右
  
  # 我们来看看360M内存的VPS可以打开的文件句柄数是多少：
  # $ cat /proc/sys/fs/file-max
  # 输出 34336
  # 32000 < 34336，即并发连接总数小于系统可以打开的文件句柄总数，这样就在操作系统可以承受的范围之内

  # 所以，worker_connections 的值需根据 worker_processes 进程数目和
  # 系统可以打开的最大文件总数进行适当地进行设置
  # 使得并发总数小于操作系统可以打开的最大文件数目
  # 其实质也就是根据主机的物理CPU和内存进行配置

	# 当然，理论上的并发总数可能会和实际有所偏差，因为主机还有其他的工作进程需要消耗系统资源。
 	# ulimit -SHn 65535
}

http {
	#设定mime类型,类型由mime.type文件定义
	include    mime.types;
	default_type  application/octet-stream;

	#设定日志格式
	log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
  access_log  logs/access.log  main;

  #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，
  #对于普通应用，必须设为 on,
  #如果用来进行下载等应用磁盘IO重负载应用，可设置为 off，
  #以平衡磁盘与网络I/O处理速度，降低系统的uptime.
  sendfile     on;
  #tcp_nopush     on;

	#连接超时时间
	#keepalive_timeout  0;
	keepalive_timeout  65;
	tcp_nodelay     on;

 	#开启gzip压缩
	gzip  on;
	gzip_disable "MSIE [1-6].";

 	#设定请求缓冲
	client_header_buffer_size    128k;
	large_client_header_buffers  4 128k;

	map $http_upgrade $connection_upgrade {
		default upgrade;
		'' close;
	}

	#出口（短连接）
	upstream hostIp {
		server 3.1.11.76:8869;
		server 3.1.11.73:8869;
	}

	#出口（长连接）    
	upstream websocket {
		server 3.1.11.76:53005;
		server 3.1.11.73:53005;
	}

 	#排队机资源更新出口
	upstream gengxingIp {
		server 3.1.11.76:53006;
		server 3.1.11.73:53006;
	}
	
	# location 匹配规则
	# ~ 波浪线表示执行一个正则匹配，区分大小写
	# ~* 表示执行一个正则匹配，不区分大小写
  # ^~ 表示普通字符匹配，如果该选项匹配，只匹配该选项，不匹配别的选项，一般用来匹配目录
  # = 进行普通字符精确匹配

	#pad调用服务的https请求入口，对应pad的C端配置的hostIp端口（短连接）
	server {
		listen 8870 ssl;
   	ssl_certificate      /home/nginx/nginx/conf/cfssl/server.crt;
		ssl_certificate_key  /home/nginx/nginx/conf/cfssl/rsa_private_key.pem;
		ssl_session_cache    shared:SSL:1m;
		ssl_session_timeout  5m;
		ssl_ciphers  HIGH:!aNULL:!MD5;
		ssl_prefer_server_ciphers  on;

			location / {
				root   html;
				index  index.html index.htm;
				#proxy_pass [http://hostIp](http://hostip/);
				#proxy_http_version 1.1;
			}
		}

		#pad调用服务的http请求入口，对应pad的C端配置的websocket端口（长连接）
		server {
			listen 54005;
			location / {
			proxy_pass [http://websocket](http://websocket/);
			proxy_http_version 1.1;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection $connection_upgrade;
		}
	}

	#排队机http请求(短连接)
	server {
		listen 8871;
		location / {
			root   html;
			index  index.html index.htm;
			#proxy_pass [http://hostIp](http://hostip/);
			#proxy_http_version 1.1;
			#proxy_set_header Upgrade $http_upgrade;
			#proxy_set_header Connection $connection_upgrade;
		}
	}

	#排队机资源更新请求(短连接) 
	server {
		listen 54006;
		location / {
			proxy_pass [http://gengxingIp](http://gengxingip/);
			proxy_http_version 1.1;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection $connection_upgrade;
		}
	}
} 
```

### 3、校验配置文件的正确性，并启动nginx

执行命令进去nginx控制文件路径

 `cd nginx/sbin`

执行`./nginx -t` 校验配置是否正确

如果提示

```shell
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /usr/local/nginx/conf/nginx.conf:2
```

可以设置nginx脚本仅 root 用户和 nginx用户可以运行，建议将nginx用户加入到root权限来启动

`chown root.nginx nginx`

`chmod 775 nginx`

`chmod u+s nginx `  

\#-u：User，即文件或目录的拥有者 ;-s :在文件执行时把进程的属主或组ID置为该文件的文件属主

在sbin目录下执行常用命令：

`./nginx`	启动nginx

`./nginx -s reload`	 重启nginx

`./nginx -s quit`	关闭nginx
