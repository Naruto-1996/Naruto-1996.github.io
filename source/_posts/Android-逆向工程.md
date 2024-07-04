---
title: Android 逆向工程
date: 2021-05-19 17:30:53
categories: 
- Android
tags:
- 逆向工程
- Mac
---

### Android 逆向工程

#### 一、需要的工具

1、Apktool
 
>是一款应用在Apk上的逆向工程的工具，它有编译、反编译、签名等功能

使用HomeBrew安装Apktool 

[这里给出其他安装方式](https://ibotpeaches.github.io/Apktool/install/)

```
cd ~
brew install apktool
```
安装完以后输入

```
apktool
```

如果出现 下方的东西 安装成功

```
Apktool v2.5.0 - a tool for reengineering Android apk files
with smali v2.4.0 and baksmali v2.4.0
Copyright 2010 Ryszard Wiśniewski <brut.alll@gmail.com>
Copyright 2010 Connor Tumbleson <connor.tumbleson@gmail.com>

usage: apktool
 -advance,--advanced   prints advance information.
 -version,--version    prints the version then exits
usage: apktool if|install-framework [options] <framework.apk>
 -p,--frame-path <dir>   Stores framework files into <dir>.
 -t,--tag <tag>          Tag frameworks using <tag>.
usage: apktool d[ecode] [options] <file_apk>
 -f,--force              Force delete destination directory.
 -o,--output <dir>       The name of folder that gets written. Default is apk.out
 -p,--frame-path <dir>   Uses framework files located in <dir>.
 -r,--no-res             Do not decode resources.
 -s,--no-src             Do not decode sources.
 -t,--frame-tag <tag>    Uses framework files tagged by <tag>.
usage: apktool b[uild] [options] <app_path>
 -f,--force-all          Skip changes detection and build all files.
 -o,--output <dir>       The name of apk that gets written. Default is dist/name.apk
 -p,--frame-path <dir>   Uses framework files located in <dir>.

For additional info, see: https://ibotpeaches.github.io/Apktool/ 
For smali/baksmali info, see: https://github.com/JesusFreke/smali
```

2、dex2jar
 
>这款工具的作用主要是将dex文件转换成jar文件，转换成jar后我们才好借助JD-GUI来查看反编译dex后的代码

[下载路径](https://github.com/geilige/dex2jar/releases)

请下载最新的压缩包

下载完后直接解压就行了 后面我们再说用途

3、JD-GUI 

>一款Java反编译器GUI，通过它我们能查看到反编译后的dex的代码，通常需要配合dex2jar使用;

[下载路径](http://java-decompiler.github.io/)

下载完后解压 进入到文件夹双击图标的时候 如果提示打不开就去  系统偏好设置-> 安全性和隐私 -> 通用 

设置一下允许

#### 二、具体操作

1、首先我们找到一个apk文件

```
apktool d test.apk
```

运行完成后，得到一个包含资源文件和代码的文件：

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210519180706.png)

##### 注意：

此时 dex 文件直接反编译成了 smali 文件，而我们需要的是 .dex 文件。

此时再运行：

```
apktool d -s -f test.apk
```

>-d 反编译 apk 文件
>
>-s 不反编译 dex 文件，而是将其保留
>
>-f 如果目标文件夹存在，则删除后重新反编译

此时得到这样的文件夹：

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210519180931.png)

2、将上一步得到的 classes.dex 文件(可能是多个) copy到dex2jar 解压好的目录中 

运行代码

```
sh d2j-dex2jar.sh classes.dex
```

如果提示：

```
d2j-dex2jar.sh: line 36: ./d2j_invoke.sh: Permission denied
```

执行
```
sudo chmod +x d2j_invoke.sh
```

然后在执行

```
sh d2j-dex2jar.sh classes.dex
```

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210519181313.png)

运行成功 会在当前目录下生成对应的 .jar文件

3、 我们打开 可视化工具 jd-gui

将刚才生成的 .jar 文件拖进去 就能看到源码了

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210519181524.png)



[参考文章1](https://www.zhihu.com/question/29370382)

[参考文章2](https://blog.csdn.net/fengyuzhengfan/article/details/80286704)
