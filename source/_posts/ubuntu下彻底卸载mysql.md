---
title: ubuntu下彻底卸载mysql
date: 2021-02-02 09:26:49
categories:
- Ubuntu
tags:
- mysql
---

#### Ubuntu下彻底卸载mysql

> 采用sudo apt install mysql-server命令的方式默认安装的是MySQL5.7，MySQL5.7版本最高只适配到Ubuntu17.04，
> 不支持Ubuntu18.04，MySQL8.0可适配到Ubuntu18.04故如果系统使用的Ubuntu18.04，
> 只能安装MySQL8.0，而且加密方式需要选择5.x的加密，因为有兼容性问题，如果你已经执行了上边的命令，安装了MySQL5.7，需要先卸载。


##### 1、首先在系统终端中查看MySQL的依赖项，运行命令：`dpkg --list|grep mysql`

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210202093257.png)

##### 2、卸载 `sudo apt-get remove mysql-common`

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210202093512.png)

卸载 `sudo apt-get autoremove --purge mysql-server-5.7`

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210202093713.png)

#### 3、清除残留数据，运行命令：`dpkg -l|grep ^rc|awk '{print$2}'|sudo xargs dpkg -P`

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210202093832.png)

#### 4、再次查看MySQL的剩余依赖项，运行命令：`dpkg --list|grep mysql`

>  如果还有依赖项 就继续删除剩余依赖项，比如：sudo apt-get autoremove --purge mysql-apt-config
>  如果没有那就表示删除干净了