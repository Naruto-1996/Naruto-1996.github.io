---
title: Ubuntu权限详解
date: 2021-03-29 16:40:43
categories:
- Linux
tags:
- ubuntu
---

### Ubuntu权限详解

在Linux系统中，文件的权限控制着文件的所有操作，比如一个文件的读写权限、执行权限、是否为目录等等。
以下操作全部在终端中执行

输入ls -altrh

```
user1@wzjing-win10:/mnt/c/Users/user1$ ls -l
total 458391864
drwxrwxrwx 0 root root       512 May 13 00:51 AppData
drwxrwxrwx 0 root root       512 Apr 25 00:10 CLionProjects
drwxrwxrwx 0 root root       512 May 22 09:32 compile
drwxrwxrwx 0 root root       512 May 28 14:08 Desktop
-rwxrwxrwx 0 root root       512 May 28 14:08 test.apk
```

每一行的含义分别如下:

|  权限   | incode | 所属用户 | 所属用户组 | 文件大小    | 修改时间    | 文件或文件名|
|  ----  | ----   | ----     | ----      | ----   |    ---- | ---- |
| drwxrwxrwx  | 0 |root | root | 512  | May 13 00:51 | AppData |

每一行的第一个字段，如`drwxrwxrwx`代表了这个文件的权限详情，共分为10位，由 d r w x - 五种标识符组成，

* `d` 是否为目录
* `r` 代表用户是否有读取权限
* `w` 代表用户是否有写入权限
* `x` 代表用户是否有执行权限
* `-` 代表此项为空，也就是没有此项权限的意思

| 位置 | 属性 | 含义 |
| ---- | ---- | ----- |
| 第1位 | d   | 代表是否为文件夹 |
| 第2-4位 | rwx | 代表所属用户的读 写 执行权限 | 
| 第5-7位 | rwx | 代表所属用户组的读 写 执行权限 | 
| 第8-10位 | rwx | 代表其他用户的读 写 执行权限 | 

如第一位是`d`代表是文件夹， 第一位如果是-代表不是文件夹（那不就是文件喽）
`rwx`代表有读取、写入、执行权限，如果为`-wd`代表无读取、有写入、有执行权限
好吧，现在来理解这一行

```
drwxrwxrwx 0 root root       512 May 13 00:51 AppData
```

* 第1位 `d` 是文件夹
* 2-4位 `rwx` 所属用户root有 读取、写入、执行 三项权限
* 5-7位 `rwx` 所属用户组root有 读取、写入、执行 三项权限
* 8-10位 `rwx` 其他用户user1有 读取、写入、执行 三项权限

### 使用`chmod`命令更改文件权限

语法`chmod [权限操作] [文件名]`
你不能把一个文件改成文件夹或者把文件夹改成文件，所以你只能改后9位
使用 `u g o a` 代表要更改的权限群组，

* `u` [代表所属用户]
* `g` [代表所属用户组]
* `o` [代表其他用户]
* `a` [代表以上所有三个]
* `-` [代表删除权限]
* `+` [代表增加权限]
* `=` [代表将权限设置为]

**示例**（如果提示你没权限修改的话，就在命令前边增加`sudo`）

`chmod u+x test.apk` 代表增加所属用户对test.apk的可执行权限

`chmod a-w AppData` 代表删除所有人对AppData这个文件夹的写入权限

`chmod -w AppData` a可以省略，此条命令和上边这条完全相同

**也可以一次设置多个权限**

`chmod g+rwx AppData` 代表添加所属用户组可读取、可写入、可执行权限

`chmod g=rwx AppData` 代表把所属用户组的权限设置为可读取、可写入、可执行

**Tip:**  其实`+`和`=`的区别不是很大

**有一种更为简便的写法：**

Linux系统内部设定： `r=4 w=2 x=1 -=0`

`r w x`任意一种组合的三个值相加的结果都不同

如：

rwx=7

-wx=3

r-x=5

rw-=6

所以可以这么写命令：

`chmod 777 test.apk` 设置权限为 -rwxrwxrwx

`chmod 755 test.apk` 设置权限为 -rwxr-xr-x

`chmod 666 test.apk` 设置权限为 -rw-rw-rw-





