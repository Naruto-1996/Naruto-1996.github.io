---
title: picture_bed
date: 2020-10-18 15:36:35
categories:
- 图床
tags:
- github
- jsDelivr
- PicGo
---
### Mac环境--如何做一个自用的图床 
#### 1、下载PicGo
PicGo[下载地址](https://github.com/Molunerfinn/picgo/releases)
#### 2、创建一个Github仓库并进行设置
##### （1）创建仓库
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201018155437.png)
##### (2) 点击头像 点击Settings
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201018160141.png)
##### (3) 再点 Developer settings
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201018160243.png)
##### (4) 再点 Personal access tokens
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201018160353.png)
##### (5) 填写内容
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201018160722.png)
##### (6) 复制生成的token (注意：网页关闭后就再也看不到这个token了) 
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201018161000.png)
#### 3、在刚才创建的仓库中新增一个项目
    git init
    git add .
    git commit -m "备注"
    git remote add origin 仓库地址
    git push -u origin master
下面是我的项目：
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201018162357.png)
#### 4、配置刚才下载的PicGo
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201018161711.png)

设置自定义域名：  
    https://cdn.jsdelivr.net/gh/Naruto-1996/picture  
    https://cdn.jsdelivr.net/gh/用户名/仓库名
    
配置完就可以在PicGo中上传图片了。