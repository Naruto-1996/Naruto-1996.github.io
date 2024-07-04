---
title: 给Linux服务器设置登录提示
date: 2020-12-08 18:06:02
categories:
- Linux
tags:
- Linux
---

##### 1、给Linux设置登录提示
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201208180823.png)

```
sudo vim /etc/motd
```

然后把你想要的内用拷贝进去 退出服务器,重新登录就可以看到了

```
< Hi, How are you >
 -----------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||  

```

##### 2、给Linux设置彩色界面

##### 查看当前终端支持集中颜色

使用`tput colors`命令即可查看当前终端支持几种颜色

##### 查看当前终端类型

`$TERM`变量代表当前终端类型，可使用`echo $TERM`命令查看。

##### 输出当前支持的颜色

运行以下命令，若输出了完整的256种颜色，就说明当前终端支持256色：

```
(x=`tput op` y=`printf %76s`;for i in {0..256};do o=00$i;echo -e ${o:${#o}-3:3} `tput setaf $i;tput setab $i`${y// /=}$x;done)
```

若只有前8种颜色，说明当前配置是8色模式，默认情况下，Ubuntu中的Gnome-Terminal就只开启了8色支持。此时可通过修改`~/.bashrc`文件将其改为256色，在`.bashrc`文件最后中加入以下代码即可：

```
export TERM=vte-256color
```

如果没有`.bashrc`文件 可以选择`sudo vim .bashrc` 创建一个 也可以使用`sudo cp /etc/skel/.bashrc ./` 命令拷贝到home目录下

最后输入命令source ~/.bashrc，一定要输入，不然配置文件无法生效(虽然source了但是退出登录进来后有没有了颜色)

##### 解决.bashrc文件每次打开终端都需要source的问题

`sudo vim ~/.bash_profile`在文件内部输入

```
# 加载.bashrc文件
if test -f .bashrc ; then
source .bashrc 
fi
```
