---
title: 解决服务器连不上github的问题
date: 2021-03-29 09:24:26
categories:
- github
tags:
- 腾讯云服务器
---

### 解决服务器连不上github的问题

报错信息:
```
fatal: unable to access 'https://github.com/ohmyzsh/ohmyzsh.git/': Empty reply from server
Error: git clone of oh-my-zsh repo failed
```

本来想改变一下服务器终端的主题，一直都是黑白色 有些单调，就想着安装一个 `on my zsh` 结果死活连不上`github`

一向贫穷的我怎么买的起香港的服务器当跳板呢，所以绞尽脑汁看各种资料 终于还是发现了

具体步骤如下:

1、首先我们去查一下`github.com`的ip地址

[https://www.ipaddress.com/ip-lookup](https://www.ipaddress.com/ip-lookup)

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210329093225.png)

2、我们进入自己的服务器的`/etc` 目录下

```
cd /etc
sudo vim hosts
```

在`hosts`文件最后一行 加上下面这行，并保存退出服务器 重新进一下

```
GitHub的IP   github.com
```

GitHub的IP 是我们刚才查到的

发现可以连上了。