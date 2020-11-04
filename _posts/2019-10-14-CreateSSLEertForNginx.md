---
layout: post
title: 生成SSL自签证书和秘钥
date: 2019-10-14 
tags: Nginx   
---

### 一、所需工具

cfssl和cfssljson(以下是linux64位系统的工具名)

cfssl_linux-amd64和cfssljson_linux-amd64

> 其他系统的可以在这个网站下载https://pkg.cfssl.org/

### 二、使用工具生成证书和秘钥

使用root用户生成

1、 将cfssl_linux-amd64和cfssljson_linux-amd64上传到root用户下并为其添加执行权限chmod +x cfssl_linux-amd64 cfssljson_linux-amd64

2、 mv cfssl_linux-amd64 /usr/local/bin/cfssl

3、 mv cfssljson_linux-amd64 /usr/local/bin/cfssljson

4、 创建一个文件ca-csr.json

```shell
{
	"CN": "192.168.124.130",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "BeiJing",
      "L": "BeiJing",
      "O": "My Organization ",
      "OU": "ops"
    }
  ]
}
```

术语介绍:

> CN: Common Name，浏览器使用该字段验证网站是否合法，一般写的是域名。非常重要。浏览器使用该字段验证网站是否合法

> C: Country， 国家

> L: Locality，地区，城市

> O: Organization Name，组织名称，公司名称

> OU: Organization Unit Name，组织单位名称，公司部门

> ST: State，州，省

5、 生成ca证书和ca私钥和csr(证书签名请求，自签证书不需要)

`# cfssl gencert -initca ca-csr.json | cfssljson -bare ca  ### 初始化ca`

执行完此命令后会生成以下文件，其中ca.pem为证书ca-key.pem为秘钥

使用普通用户生成时无需将cfssl_linux-amd64和cfssljson_linux-amd64工具放到/usr/local/bin目录下，直接在上传目录下执行以下命令也可

`./cfssl_linux-amd64 gencert -initca ca-csr.json | ./cfssljson_linux-amd64 -bare ca`

### 三、将生成的ca.pem和ca-key.pem复制到nginx的配置文件路径下即可

cfssl常用命令：

```shell
cfssl gencert -initca ca-csr.json | cfssljson -bare ca ### 初始化ca
cfssl gencert -initca -ca-key key.pem ca-csr.json | cfssljson -bare ca ### 使用现有私钥,重新生成
cfssl certinfo -cert ca.pem ###查看证书信息
```

