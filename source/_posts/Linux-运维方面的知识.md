---
title: Linux 运维方面的知识
date: 2020-12-02 11:50:46
categories:
- Linux
tags:
- 运维
- 防火墙
- Linux命令
---

### Linux 常用命令

##### 1、怎么看Linux系统版本

登陆Linux，在终端输入

```Linux
cat /proc/version
````
##### 2、查看服务器内存使用情况
```linux
free -m
```
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201202120027.png)

#### 参数解释：
Mem行（单位均为M）：
* total：内存总数
* used：已使用内存数
* free：空闲内存数
* shared：当前废弃不用
* buffers：缓存内存数（Buffer）
* cached：缓存内舒数（Page）

(-/+ buffers/cache)行：
* （-buffers/cache）: 真正使用的内存数，指的是第一部分的 used - buffers - cached
* （+buffers/cache）: 可用的内存数，指的是第一部分的 free + buffers + cached

Swap行指交换分区。

实际上不要看free少就觉得内存不足了，buffers和cached都是可以在使用内存时拿来用的，应该以(-/+ buffers/cache)行的free和used来看。只要没发现swap的使用，就不用太担心，如果swap用了很多，那就要考虑增加物理内存了。

#### 3、 查看CPU使用情况

```
top
```
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201202120433.png)


