---
title: Maven 配置镜像
date: 2024-07-02 11:50:46
categories:
- Maven
tags:
- Maven
---

### Maven 配置镜像的正确方式

##### 1、如何配置Maven镜像

>因为网络的原因，Maven 默认的镜像加载速度很慢，所以需要使用镜像加快速度。网上关于配置镜像的文章很多，但是大部分都是错误的，甚至阿里云的官方文档也是错误的

##### 错误的做法

```
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>

```

>其实吧，这是个错误的配置。问题在于 <mirrorOf>*</mirrorOf> 这段配置。它使用 * 通配符，说明了任何的 jar 包都去阿里云找。但是有些 jar 包，比如 Spring Cloud 的 jar 包在阿里云的 public 仓库里是找不到的。


##### 正确的做法

```
<mirrors>
    <mirror>
      <id>aliyun-jcenter</id>
      <mirrorOf>jcenter</mirrorOf>
      <name>aliyun-jcenter</name>
      <url>https://maven.aliyun.com/repository/public</url> <!-- 改用 public -->
    </mirror>
    <mirror>
      <id>aliyun-central</id>
      <mirrorOf>central</mirrorOf>
      <name>aliyun-central</name>
      <url>https://maven.aliyun.com/repository/public</url> <!-- 改用 public -->
    </mirror>
    <mirror>
      <id>aliyun-spring</id>
      <mirrorOf>spring</mirrorOf>
      <name>aliyun-spring</name>
      <url>https://maven.aliyun.com/repository/spring</url>
    </mirror>
    <mirror>
      <id>aliyun-spring-plugin</id>
      <mirrorOf>spring-plugin</mirrorOf>
      <name>aliyun-spring-plugin</name>
      <url>https://maven.aliyun.com/repository/spring-plugin</url>
    </mirror>
</mirrors>

```
