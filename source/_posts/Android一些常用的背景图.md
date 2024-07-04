---
title: Android一些常用的背景图
date: 2020-12-18 16:52:39
categories:
- Android
tags:
- Java
- 背景图
- Drawable
- xml
---

#### Android中一些常用的xml背景图


##### 1、绿色背景 圆角为4dp的xml背景(颜色可自行修改)

res/drawable 目录下 common_bg_4_radius_green.xml

```xml

<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
  android:shape="rectangle">

  <solid android:color="@color/绿色"/>
  <corners android:radius="4dp"/>
</shape>

```

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201218170312.png)

##### 2、绿色border边框背景

```xml

<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
android:shape="rectangle">

<stroke
  android:width="0.6dp"
  android:color="@color/绿色" />
<corners android:radius="2dp" />

</shape>

```

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201218170216.png)

##### 3、背景为圆形

```xml

<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
  android:shape="oval">

  <solid android:color="@color/绿色" />
  <corners android:radius="8.3dp" />

  <size
    android:width="17dp"
    android:height="17dp" />
</shape>

```

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20201218170648.png)
