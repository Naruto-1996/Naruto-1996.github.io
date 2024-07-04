---
title: Android ImageView设置background和src的区别
date: 2020-10-21 17:03:01
categories:
- Android
tags:
- ImageView
---

#### 一、ImageView设置background和src的区别。

* 1.src是图片内容（前景），bg是背景，可以同时使用。
* 2.background会根据ImageView组件给定的长宽进行拉伸，而src就存放的是原图的大小，不会进行拉伸 。
* 3.scaleType只对src起作用；bg可设置透明度。

#### 二、ImageView几种不同的设置图片的方式。

设置background：

``` java
image.setBackground(getResources().getDrawable(R.drawable.black));//变形
image.setBackgroundResource(R.drawable.black);//变形 
image.setBackgroundDrawable(getResources().getDrawable(R.drawable.black));////变形

```

设置src:
 
``` java
image.setImageDrawable(getResources().getDrawable(R.drawable.blackk)); //不会变形

Stringpath = Environment.getExternalStorageDirectory()+File.separator+"test1.jpg";
Bitmap bm = BitmapFactory.decodeFile(path);
image.setImageBitmap(bm);//不会变形

image.setImageResource(R.drawable.blackk);//不会变形

```
