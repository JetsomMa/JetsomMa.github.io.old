---
layout: post
title: php安装踩坑
date: 2021-01-20
tags: php    
---

### 1.安装php的命令

```shell
#安装php8.0.1
./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --enable-fpm --with-fpm-user=www --with-fpm-group=www --enable-mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --enable-mysqlnd-compression-support     --with-zlib  --enable-xml --disable-rpath --enable-bcmath --enable-shmop --enable-sysvsem  --with-curl --enable-mbregex --enable-mbstring --enable-intl   --enable-ftp  --enable-gd-jis-conv  --with-openssl --with-mhash --enable-pcntl --enable-sockets   --enable-soap --with-gettext --disable-fileinfo --enable-opcache --with-pear  --with-ldap=shared --without-gdbm

#安装php7.2以下版本
./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --enable-fpm --with-fpm-user=www --with-fpm-group=www --enable-mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --enable-mysqlnd-compression-support --with-iconv-dir --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir --enable-xml --disable-rpath --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --enable-mbregex --enable-mbstring --enable-intl --with-mcrypt --with-libmbfl --enable-ftp --with-gd --enable-gd-jis-conv --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --enable-sockets --with-xmlrpc --enable-zip --enable-soap --with-gettext --disable-fileinfo --enable-opcache --with-pear --enable-maintainer-zts --with-ldap=shared --without-gdbm

#安装php7.2以下版本
./configure --prefix=/usr/local/php73 --with-curl --with-freetype-dir --with-gd --with-gettext --with-iconv-dir --with-kerberos --with-libdir=lib64 --with-libxml-dir --with-mysqli --with-openssl --with-pcre-regex --with-pdo-mysql --with-pdo-sqlite --with-pear --with-png-dir --with-xmlrpc --with-xsl --with-zlib --enable-fpm --enable-bcmath -enable-inline-optimization --enable-mbregex --enable-mbstring --enable-opcache --enable-pcntl --enable-shmop --enable-soap --enable-sockets --enable-sysvshm --enable-sysvmsg --enable-sysvsem --enable-xml --enable-zip --with-fpm-user=nginx --enable-ftp --enable-exif  --enable-session --with-mhash --with-ldap

#安装
make && make install
```

### 2.php-fpm操作相关命令

```shell
/usr/local/php/bin/php-fpm    #开启 php-fpm
ps aux|grep php-fpm           #查询php-fpm进程
kill -USR2 [PID]              #重启php-fpm
pkill php-fpm                 #杀死php-fpm进程
php -v                        #查看版本
export PATH=/usr/local/php73/bin:$PATH  #切换php版本

# 修改环境变量
vim /etc/profile
# 使环境变量生效
source /etc/profile
```

### 3.PHP编译安装问题解决

> PHP 7+ 版本极大地改进了性能，在一些WordPress基准测试当中，性能可以达到PHP 5.6的3倍。而PHP 5.6版本在2018年底官方已经不再维护，升级PHP 7是必然选择。

*** centos7 编辑安装php遇到的问题：***

#### 解决 configure: error: no acceptable C compiler found in $PATH 错误

```shell
yum install -y gcc gcc-c++
```

#### 解决 error: the HTTP XSLT module requires the libxml2/libxslt 错误

```shell
yum -y install libxml2 libxml2-dev
yum -y install libxslt-devel
```

#### 解决 error: the HTTP image filter module requires the GD library. 错误

```shell
yum -y install gd-devel
```

#### 解决 error: the GeoIP module requires the GeoIP library. 错误

```shell
yum -y install GeoIP GeoIP-devel GeoIP-data
```

#### 解决 error: the Google perftools module requires the Google perftools 错误

```shell
yum -y install gperftools
```

#### 解决 error: libatomic_ops library was not found. 错误

```shell
yum -y install libuuid-devel libblkid-devel libudev-devel fuse-devel libedit-devel libatomic_ops-devel
```

#### 解决 error trying to exec ‘cc1plus’: execvp: No such file or directory 错误

```shell
yum -y install gcc-c++
```

#### 解决 error: [pool www] cannot get uid for user ‘www-data’ 错误

```shell
groupadd www-data
useradd -g www-data www-data
```

#### 解决configure: error: mbed TLS libraries not found. 错误。

```shell
# 需要安装mbedtls
# 1、下载并解压
wget https://tls.mbed.org/download/mbedtls-2.16.3-gpl.tgz
tar -xf mbedtls-2.16.3-gpl.tgz
cd mbedtls-2.16.3
# 备用下载地址：https://down.24kplus.com/linux/mbedtls/mbedtls-2.16.3-gpl.tgz

# 2、编译安装
make
make DESTDIR=/usr install
ldconfig

# 如果出现 make[1]: python2: Command not found 错误，执行：
yum -y install python2

# 如果出现 /usr/bin/env: ‘perl’: No such file or directory 错误，执行：
yum -y install perl
```

#### 解决 error: Cannot find OpenSSL’s <evp.h> 错误

```shell
yum install openssl openssl-devel
ln -s /usr/lib64/libssl.so /usr/lib/
```

#### 解决 error: Libtool library used but ‘LIBTOOL’ is undefined 错误

```shell
yum install libtool
```

#### 解决 exec: g++: not found 错误

```shell
yum -y update gcc
yum -y install gcc+ gcc-c++
```

#### 解决 configure: error: tss lib not found: libtspi.so 错误

```shell
yum install trousers-devel
```

#### 解决 Can’t exec “autopoint”: No such file or directory 错误

```shell
yum install gettext gettext-devel gettext-common-devel
```

#### 解决 configure: error: libcrypto not found. 错误

```shell
yum remove openssl-devel
yum -y install openssl-devel
```

#### 解决 configure: error: Package requirements (libffi >= 3.0.0) were not met: No package ‘libffi’ found 错误

```shell
yum install libffi-devel
```

#### 解决 fatal error: uuid.h: No such file or directory 错误

```shell
yum install e2fsprogs-devel uuid-devel libuuid-devel
```

#### 解决 configure: error: openssl lib not found: libcrypto.so 错误

```shell
yum install openssl-devel
```

#### 解决 tar (child): lbzip2: Cannot exec: No such file or directory 错误

```shell
yum -y install bzip2
```

#### 解决 configure: error: C++ preprocessor “/lib/cpp” fails sanity check 错误

```shell
yum install gcc-c++
```

#### 解决 configure: error: Please reinstall the BZip2 distribution 错误

```shell
yum install bzip2 bzip2-devel
```

#### 解决 configure: error: cURL version 7.15.5 or later is required to compile php with cURL support 错误

```shell
yum install curl-devel
```

#### 解决 configure: error: not found. Please provide a path to MagickWand-config or Wand-config program 错误

```shell
yum install ImageMagick-devel
```

#### 解决 configure: error: no acceptable C compiler found in $PATH 错误

```shell
yum install gcc
```

#### 解决 configure: error: Package requirements (icu-uc >= 50.1 icu-io icu-i18n) were not met: 错误

```shell
yum install libicu-devel
```

#### 解决 configure: error: Package requirements (sqlite3 > 3.7.4) were not met: No package ‘sqlite3’ found 错误

```shell
yum install sqlite-devel
```

#### 解决 configure: error: Package requirements (oniguruma) were not met: No package ‘oniguruma’ found 错误

```shell
yum install oniguruma oniguruma-devel

#使用源代码安装。
wget https://github.com/kkos/oniguruma/archive/v6.9.4.tar.gz -O oniguruma-6.9.4.tar.gz 
tar -zxvf oniguruma-6.9.4.tar.gz
cd oniguruma-6.9.4/
./autogen.sh
./configure
make
sudo make install

# 解决 bash: make: 未找到命令…
# 需要安装gcc，执行一下就可以了：
yum -y install gcc automake autoconf libtool make
yum install gcc gcc-c++
```

#### 解决 configure: error: Package requirements (libzip >= 0.11 libzip != 1.3.1 libzip != 1.7.0) were not met:
Package ‘libzip’, required by ‘virtual:world’, not found
Package ‘libzip’, required by ‘virtual:world’, not found
Package ‘libzip’, required by ‘virtual:world’, not found

```shell
# 第一步：
rpm -q libzip    rpm -q libzip-devel 查看是否有安装过，如果有，卸载它。yum remove libzip-devel libzip

# 第二步:
# 下载
wget https://libzip.org/download/libzip-1.7.3.tar.gz

# 解压
tar -xvf libzip-1.7.3.tar.gz

# 安装cmake3
yum install -y cmake3
sudo ln -sf /usr/bin/cmake3 /usr/bin/cmake

#安装libzip
cd libzip-1.7.3/
mkdir build && cd build
cmake ..
make && make install
# 源码地址
https://pkgs.org/search/?q=libzip
#通过源码安装
yum install http://mirror.centos.org/centos/8/AppStream/x86_64/os/Packages/libzip-1.5.2-1.module_el8.2.0+314+53b99e08.x86_64.rpm
yum install http://mirror.centos.org/centos/8/AppStream/x86_64/os/Packages/libzip-devel-1.5.2-1.module_el8.2.0+314+53b99e08.x86_64.rpm
```

#### 解决 No package ‘libxml-2.0’ found缺失libxml2.0 库，错误：

```shell
yum -y install libxml2
yum -y install libxml2-devel
```

#### 解决 configure: error: Package requirements (libxslt >= 1.1.0) were not met:No package ‘libxslt’ found 错误

```shell
yum install libxslt-devel
```

#### 解决 configure: error: Package requirements (libpng) were not met:No package ‘libpng’ found 错误

```shell
yum install libpng-devel
```

#### 解决 configure: error: Cannot find ldap.h 错误

```shell
yum install openldap
yum install openldap-devel
```

#### 解决 configure: error: Cannot find ldap libraries in /usr/lib 错误

```shell
cp -frp /usr/lib64/libldap* /usr/lib/
```

#### 解决 configure: error: Package requirements (libcurl >= 7.15.5) were not met: 错误

```shell
yum install libcurl-devel
```

#### 解决 configure: error: Package requirements (krb5-gssapi krb5) were not met:

```shell
yum -y install krb5-devel
```

#### 解决 configure: error: Package requirements (libjpeg) were not met:

```shell
yum install -y libjpeg-devel
```

#### 解决 configure: error: Package requirements (freetype2) were not met:

```shell
yum -y install freetype freetype-devel
```

#### 解决 configure: error: Package requirements (libpcre2-8 >= 10.30) were not met:

```shell
wget https://ftp.pcre.org/pub/pcre/pcre2-10.35.tar.gz
tar xzvf pcre2-10.35.tar.gz
cd pcre2-10.35

./configure --prefix=/usr/local/pcre2 \
--enable-pcre2-16 \
--enable-pcre2-32 \
--enable-jit \
--enable-jit-sealloc

make && make install

export PKG_CONFIG_PATH=/usr/local/pcre2/lib/pkgconfig/
```

#### 解决 configure: error: Please reinstall the libzip distribution

```shell
# A.使用老版本 （长期合作战略合作伙伴提供技术支持）libzip-1.2.0
wget https://nih.at/libzip/libzip-1.2.0.tar.gz
tar -zxvf libzip-1.2.0.tar.gz
cd libzip-1.2.0
./configure
make -j4 && make install

# B.使用最新版 （长期合作战略合作伙伴不提供技术支持**） libzip-1.5.2
wget https://libzip.org/download/libzip-1.5.2.tar.gz
tar -zxf libzip-1.5.2.tar.gz
cd libzip-1.5.2
mkdir build 
cd build 
cmake ..        （#注意：cmake后面有两个小数点）
make -j4
make test
make install
```

#### cmake: 未找到命令解决方案

```shell
# 1. 官网下载cmake-3.12.2.tar.gz
https://github.com/Kitware/CMake/releases/download/v3.13.2/cmake-3.13.2.tar.gz

# 2. 解压到指定目录
tar -zxvf cmake-3.13.2.tar.gz

# 3、进入解压后的目录，依次运行指令
cd cmake-3.13.2
./bootstrap && make && make install

# 4、查看版本信息：
cmake -version
```
