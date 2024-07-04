---
title: Android intent.setFlags方法中参数值的含义
date: 2020-10-21 17:17:58
categories:
- Android
tags:
- Intent
- setFlags
---

#### Android中intent.setFlags方法中参数值的含义

##### 一、FLAG_ACTIVITY_NEW_TASK:

例如现在栈一的情况是：A    B   C(C位于栈顶)，C通过intent跳转到D，并且这个Intent添加了FLAG_ACTIVITY_NEW_TASK标记，
如果D这个Activity在Manifest.xml中声明了添加Task affinity，系统首先会查找有没有和D的Task affinity相同的task栈存在，
如果存在，就将D压入那个栈，如果不存在则会新建一个D的affinity的栈将其压入。如果D的Task affinity默认没有设置，则会将其压入栈1，变成A B C D，
这样就和不加FLAG_ACTIVITY_NEW_TASK标记效果是一样的了。但如果试图从非Activity的非正常途径启动一个activity，
比如从一个service、BroadcastReceiver等中启动一个Activity，则intent要设置Intent.FLAG_ACTIVITY_NEW_TASK标记。
Activity要存在于Activity栈中，而非Activity的途径启动Activity时必然不存在一个Activity的栈，
所以要新建一个Activity栈来存放要启动的Activity。

##### 二、FLAG_ACTIVITY_CLEAR_TOP:

例如现在的栈情况为A B C D, D此时通过intent跳转到B，如果这个intent设置FLAG_ACTIVITY_CLEAR_TOP标记，则栈情况变为:A B。如果没有添加这个标记，则栈的情况将会变为：A B C D B 。
也就是说，如果设置了FLAG_ACTIVITY_CLEAR_TOP标记，并且目标Activity在栈中已存在，
则会把位于该目标Activity之上的Activity从栈中弹出销毁。

##### 三、FLAG_ACTIVITY_NO_HISTORY:

例如现在栈的情况为：A B C 。C通过intent跳转到D，这个intent添加FLAG_ACTIVITY_NO_HISTORY标志，此时界面显示D的内容，但是它并不会压入栈中。
如果按返回键，返回到C，栈的情况是：A  B  C。如果D中又跳转到E，栈的情况为：A B C E,此时按返回键会回到C，因为D根本就没有被压入栈中。

##### 四、FLAG_ACTIVITY_SINGLE_TOP:

和Activity的Launch mode的singleTop类似。如果某个intent设置了这个标志，并且这个intent的目标Activity就是栈顶的Activity，那么将不会新建一个实例压入栈中。
简言之，目标Activity已在栈顶则跳转过去，不在栈顶则在栈顶新建Activity。

* 如果遇到这种错误

``` java

Caused by: android.util.AndroidRuntimeException: Calling startActivity() from outside of an Activity  context requires the FLAG_ACTIVITY_NEW_TASK flag. Is this really what you want?

```


Context中有一个startActivity方法，Activity继承自Context，覆写了startActivity方法。
如果使用Activity的startActivity方法，不会有任何问题，而如果使用Context的startActivity方法，
就必须新建一个Activity栈，遇到上面的异常，是因为使用了Context的startActivity方法。

解决的办法是：为intent设置一个flag，即

``` java
intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
```
这样就可以在新的Activity栈里启动这个Activity了。
