---
title: 给Linux安装nginx
date: 2020-12-09 14:25:33
categories:
- nginx
tags:
- nginx
- linux
- ubuntu
---

#### 给Linux(ubuntu)服务器安装nginx

##### 1：检查80端口是否被占用。
##### 2：安装nginx

给nginx配置安装目录,就是nginx存放的目录

我一般安装软件都是安装在/usr/local下面的

```
mkdir /usr/local/nginx
```

进入nginx目录`cd /usr/local/nginx`

使用wget命令下载nginx资源包

```
wget http://nginx.org/download/nginx-1.12.2.tar.gz
```

解压

```yaml
tar -zxvf nginx-1.5.9.tar.gz
```


执行` ./configure命令`

```
cd nginx-1.5.9
sudo ./configure
```

执行`./configure`可能会存在一下错误,如果出现请执行一下命令

>错误1
>
>/configure: error: the HTTP rewrite module requires the PCRE library.

解决方法

安装pcre-devel解决问题

```
sudo apt-get update
sudo apt-get install libpcre3 libpcre3-dev
```

>错误2
>
>./configure: error: the HTTP cache module requires md5 functions
from OpenSSL library. You can either disable the module by using
--without-http-cache option, or install the OpenSSL library into the system,
or build the OpenSSL library statically from the source with nginx by using
>--with-http_ssl_module --with-openssl=<path> options.

解决办法：

```
sudo apt-get install openssl libssl-dev
```

>错误3 （如果出现这种检查不通过，则说明缺少某些依赖。）
>
> ubuntu@VM-0-16-ubuntu:/usr/local/nginx/nginx-1.12.2$ sudo ./configure 
> checking for OS
>
> Linux 4.15.0-118-generic x86_64
> checking for C compiler ... not found
>
> ./configure: error: C compiler cc is not found

解决办法：

```yaml
sudo apt-get install build-essential
```

>错误4
>
>error: the HTTP gzip module requires the zlib library.

解决办法：

```
apt-get install zlib1g-dev
```


然后在执行` ./configure命令`

```
cd nginx-1.5.9
sudo ./configure
```

##### 3.编译

`make` 编译 （make的过程是把各种语言写的源码文件，变成可执行文件和各种库文件）

```
cd /usr/local/nginx/nginx-1.5.9
sudo make
```

##### 4.make install安装

`make install` 安装 （`make install`是把这些编译出来的可执行文件和库文件复制到合适的地方）

```
sudo make install
```

##### 5.启动nginx服务

```
cd /usr/local/nginx/sbin
sudo ./nginx
```
##### 6.看nginx服务是否启动

```
ubuntu@VM-0-16-ubuntu:/usr/local/nginx/sbin$ ps -ef | grep nginx
root      7183     1  0 14:53 ?        00:00:00 nginx: master process ./nginx
nobody    7184  7183  0 14:53 ?        00:00:00 nginx: worker process
ubuntu    7252 23084  0 14:53 pts/1    00:00:00 grep --color=auto nginx
```

我们看到服务已经起来了,输入ip即可访问我们nginx目录下面的html文件夹下面的index.html文件

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201209145642.png)

===================华丽的分割线======================

`nginx -s reload `：修改配置后重新加载生效

`nginx -s reopen` ：重新打开日志文件

关闭nginx：

`nginx -s stop` :快速停止nginx

`quit `：完整有序的停止nginx
