---
title: 在linux服务器上安装vim
date: 2020-12-08 16:56:02
categories:
- Linux
tags:
- Vim
- Linux
---

#### 在Linux服务器上 安装 vim 及 vundle ( install vim and vundle)

##### 1、 直接下载 [这个文件](http://siwei.me/system/resources/W1siZiIsIjIwMTQvMTAvMjEvMDlfNTdfMDdfODA3X2RvdF92aW1fZm9sZGVyLnppcCJdXQ/dot_vim_folder.zip) ,  并且解压,成为 home目录下的 .vim 即可.

##### 2、 下载  [vimrc](http://siwei.me/system/resources/BAhbBlsHOgZmSSIjMjAxNC8wMS8xOS8wNV81MV8zOF82NjVfLnZpbXJjBjoGRVQ/.vimrc), 保存到 home目录下。 


接下来使用scp 命令将下载的两个文件传到Linux服务器上

```
scp ~/Downloads/dot_vim_folder.zip ubuntu@85.136.62.11:~
scp ~/Downloads/vimrc ubuntu@85.136.62.11:~
```

传上去后进入到Linux服务器 这个 ~ 目录 

```
cd ~
unzip dot_vim_folder.zip
``` 

然后将vimrc文件重命令一下 改为.vimrc 就好了

```
mv vimrc .vimrc
```

#### .conf文件代码不高亮  这里做一下配置 按照下面的命令进行配置就行

```
#!/bin/bash
#
# Highligh Nginx config file in Vim

# Download syntax highlight
mkdir -p ~/.vim/syntax/
wget http://www.vim.org/scripts/download_script.php?src_id=19394 -O ~/.vim/syntax/nginx.vim

# Set location of Nginx config file
cat > ~/.vim/filetype.vim <<EOF
au BufRead,BufNewFile /etc/nginx/*,/etc/nginx/conf.d/*,/usr/local/nginx/conf/* if &ft == '' | setfiletype nginx | endif
EOF
```