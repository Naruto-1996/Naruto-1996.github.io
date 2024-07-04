---
title: 搭建服务器ubuntu系统
date: 2020-12-30 15:59:35
categories:
- Linux
tags:
- ubuntu
- 服务器搭建
---

#### 购买完服务器后应该做哪些配置

##### 一 、安装apache

```
sudo apt-get install apche2 apche2-doc
```

如果你已经安装完了nginx，你需要安装一个这个东西，当你在浏览器地址栏输入服务器公网ip的时候就会出现一个nginx的默认页面 就像这样的

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201230160621.png)

##### 二、安装mysql

```
sudo apt-get install mysql-server mysql-client
```

在安装mysql的时候可能会提醒设置root账户密码

#####  三、更改默认python版本：

修改软连接

1、进入到usr/bin目录下

```
cd /usr/bin
```

2、查看该目录下与python有关的项

```
ls -altrh | grep python
```

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201230174310.png)

3、删除软连接并指向新的版本

```
sudo rm python
sudo ln -s python3.6 python
```

4、最后结果

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201230174848.png)




