---
title: Android 知识总结
date: 2021-06-22 14:37:27
category:
- Android
tags:
- 总结
---

### Android 细碎知识总结

##### 1、implementation、api 的区别

> android studio 3.0 版本之后 gradle 也升级为了3.x版本
>
> compile已过时，已被 implementation 和api 取代
>

那么，他们之间的区别在哪？

* implementation: 只能在内部使用此模块，比如我在一个libiary中使用implementation依赖了gson库，然后我的主项目依赖了libiary，那么，我的主项目就无法访问gson库中的方法。
这样的好处是编译速度会加快，推荐使用implementation的方式去依赖，

* api: 如果你需要提供给外部访问，那么就使用api依赖即可

##### 2、Android中为什么不推荐使用 Enum (枚举)

大家都说 Android 中不应该使用 Enum，而且官方文档上也写出不应该使用 Enum，好那我就不用了，
改成 Android 注解。然后这句好就被一传十，十传百，
所有人都记住了 “ Android 中不推荐使用枚举，请使用注解代替”。

谣言 绝对的谣言

JakeWharton 关于 Android 中使用枚举的一些建议：

JW 认为 ProGuard 和 R8 会在编译的时候会将琐碎的枚举优化为整型，不存在效率低下的问题，enum 效率低下只是 Android 团队散布的谣言，同时 Kotlin 和 Java 中的 Enum 在编译成字节码之后是一样的。

更多的时候我们不需要过分关心使用 Enum 带来的内存增长，你要记住 Enum 是一个类，它只是占用了作为一个类来说应有的内存。

所以是可以使用的.
