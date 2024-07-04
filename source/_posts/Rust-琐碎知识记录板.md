---
title: Rust 琐碎知识记录板
date: 2020-10-21 11:03:59
categories:
- Rust
tags:
- Rust
---

### 下面是Rust的一些琐碎知识

##### 1、format!宏
* format!: write formatted text to String  这个宏可以将一个文本格式化成String类型（可变字符串，在堆上面分配空间），类似于C#中的String.Format方法。

``` rust
let world = "world";
let text = format!("Hello {}!", world);
```
这样text的类型就会是String
