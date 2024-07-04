---
title: ubuntu下安装redis
date: 2021-02-02 13:57:48
categories:
- redis
tags:
- linux
- ubuntu
---

#### ubuntu下安装redis

##### 1、使用apt安装

在 Ubuntu 系统安装 Redi 可以使用以下命令:

```
$sudo apt-get update
$sudo apt-get install redis-server
```

##### 2、启动redis

```
$ redis-server
```

##### 3、查看 redis 是否启动？

```
$ redis-cli
```

以上命令将打开以下终端：

```
redis 127.0.0.1:6379>
```

127.0.0.1 是本机 IP ，6379 是 redis 服务端口。现在我们输入 PING 命令。

```
redis 127.0.0.1:6379> ping PONG
```
