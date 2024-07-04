---
title: 'zsh: permission denied: 解决办法'
date: 2020-11-18 17:40:48
categories:
- Mac
tags:
- zsh
- chmod
---

### 1、出现原因
``` java
用户没有权限，所以才出现了这个错误，所以只需要用chmod修改一下权限
```

### 2、解决办法
```
chmod u+x *.sh
```

### 3、说明

```
chmod是权限管理命令change the permissions mode of a file的缩写。
u代表所有者。x代表执行权限。’+’ 表示增加权限。
chmod u+x file.sh 就表示对当前目录下的file.sh文件的所有者增加可执行权限。
```