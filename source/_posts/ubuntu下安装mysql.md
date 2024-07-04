---
title: ubuntu下安装mysql
date: 2021-02-02 11:05:08
categories:
- mysql
tags:
- ubuntu
---

#### ubuntu下安装mysql

> MySQL 的安装比较简单，只需要简单的几步就可以搞定，但它的一些配置却比较繁琐。下面以 Ubuntu 18.04（其他版本类似）为例，详细介绍 MySQL 数据库的安装和配置。

##### 1、安装mysql

在 Ubuntu 中，默认只有最新版本的 MySQL 包含在 APT 软件包存储库中。

要安装它，需要使用 apt 更新服务器上的软件包索引：

``` linux
$ sudo apt update
```

然后安装默认的 MySQL 软件包：

``` 
$ sudo apt install mysql-server
```

这一步不会进行一些配置相关的提示（例如：设置密码），因为会使 MySQL 的安装不安全，我们将在下一步解决该问题。

##### 2、配置mysql

在安装完 MySQL 之后，应该运行一下包含的安全脚本：

```
sudo mysql_secure_installation
```

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210202115006.png)

重点说一下第一个提示，这会询问我们是否愿意设置验证密码插件，该插件可用于测试 MySQL 密码的强度。

##### 3、更改用户验证方式

虽然上面设置了 root 用户的密码，但当通过 MySQL 终端登录时，并不能通过密码进行认证：

```
$ mysql -u root -p
Enter password:
ERROR 1698 (28000): Access denied for user 'root'@'localhost'
```

这是因为在 MySQL 5.7 及之后的版本中，root 用户被默认设置为通过 auth_socket 插件（而非密码）认证，其主要原因是出于对数据库的安全性考虑。

话虽如此，但偶尔也需要外部程序来访问，这时就会很麻烦了。为了使 root 用户能通过密码方式连接 MySQL，先通过终端打开 MySQL 的提示符：

```
sudo mysql
```

然后通过如下命令，检查 MySQL 中每个用户的认证方式：

```
mysql> SELECT user, authentication_string, plugin, host FROM mysql.user;
+------------------+-------------------------------------------+-----------------------+-----------+
| user             | authentication_string                     | plugin                | host      |
+------------------+-------------------------------------------+-----------------------+-----------+
| root             |                                           | auth_socket           | localhost |
| mysql.session    | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
| mysql.sys        | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
| debian-sys-maint | *ABA968D18E3A0B6DEB02F9D5FBDA21415A86977B | mysql_native_password | localhost |
+------------------+-------------------------------------------+-----------------------+-----------+
4 rows in set (0.00 sec)
```

显而易见，root 用户的认证方式是 auth_socket。现在运行如下命令，将认证方式更改为密码认证（即：mysql_native_password）：

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
```

可以看到，已经修改成功了，现在退出数据库：

```
mysql> exit
```

再来尝试一下，让 root 用户以密码形式登录：

```
$ mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 7
Server version: 5.7.29-0ubuntu0.18.04.1 (Ubuntu)
 
Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
 
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
 
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
 
mysql>
```

##### 4、配置远程访问

默认情况下，MySQL 只监听本地主机（localhost）的连接。若要启用远程连接，需要进行以下配置。

1. 编辑 MySQL 的 mysqld.cnf 配置文件：

```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

注释掉下面这行配置并保存退出：

```
bind-address          = 127.0.0.1
```

2. 更改 user 表中的 host 项，将“localhost”改称“%”（表示所有用户都可以访问），并给 root 用户授权：

```

$ mysql -u root -p
 
mysql> use mysql;
mysql>
mysql> update user set host = '%' where user = 'root';
mysql>
mysql> grant all on *.* to root@'%' identified by '123456' with grant option;
mysql>
mysql> flush privileges;  # 刷新权限
mysql>
mysql> exit

```

3. 执行如下命令，重启 mysql 服务：

```
$ sudo systemctl restart mysql
```

现在，就可以远程连接（下图为 Navicat 截图） MySQl 数据库了：

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210202120829.png)
