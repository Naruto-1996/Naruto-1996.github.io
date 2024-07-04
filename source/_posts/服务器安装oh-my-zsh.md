---
title: 服务器安装oh_my_zsh
date: 2021-03-29 09:58:30
categories:
- oh my zsh
tags:
- linux
- ubuntu
---

### 给自己的服务器安装 oh my zsh

#### 1、安装zsh

`oh my zsh` 需要先安装`zsh` 所以如果你没有装`zsh`的话需要先装一下


```
cd ~
sudo apt install zsh
```

### 2、安装git

需要安装`git`因为在装`oh my zsh`的时候会去clone它的仓库，所以需要装个`git`

```
sudo apt install git
```

### 3、安装`oh my zsh`

这个过程中可能会出现无法连接github的问题，我之前的博客也有解决方案 可以搜一下关键字 `解决服务器连不上github的问题`

```
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210329100744.png)


在安装的过程中会问你是否设为默认终端 选择`y`就行了


### 4、更换主题

```
cd ~
sudo vim .zshrc
```

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210329101040.png)

这里去改一下主题名称，保存并退出  然后 `source .zshrc` 就可以了







