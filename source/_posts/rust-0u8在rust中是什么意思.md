---
title: rust 0u8在rust中是什么意思
date: 2020-10-19 11:00:27
categories:
- Rust
tags:
- Rust
- 0u8
- 字面量
---

#### rust 0u8在rust中是什么意思  
###### 当我在学习[Rust 编程语言](https://learnku.com/docs/rust-lang/2018/ch06-03-if-let/4515)  
* 遇到了如下这种问题：  
``` rust
let some_u8_value = Some(0u8);
match some_u8_value {
    Some(3) => println!("three"),
    _ => (),
}
```
0u8 是什么意思，在我的印象中应该是这么写    
``` rust
let some_u8_value = Some(0 :u8)
```  
然而并没有这种写法，直到我在 [rust_by_example](https://rustwiki.org/zh-CN/rust-by-example/types/literals.html) 中看到了答案，

### 字面量

* 对数值字面量，只要把类型作为后缀加上去，就完成了类型说明。比如指定字面量 42 的 类型是 i32，只需要写 42i32。

* 无后缀的数值字面量，其类型取决于怎样使用它们。如果没有限制，编译器会对整数使用 i32，对浮点数使用 f64。

``` rust
fn main() {
    // 带后缀的字面量，其类型在初始化时已经知道了。
    let x = 1u8;
    let y = 2u32;
    let z = 3f32;

    // 无后缀的字面量，其类型取决于如何使用它们。
    let i = 1;
    let f = 1.0;

    // `size_of_val` 返回一个变量所占的字节数
    println!("size of `x` in bytes: {}", std::mem::size_of_val(&x));
    println!("size of `y` in bytes: {}", std::mem::size_of_val(&y));
    println!("size of `z` in bytes: {}", std::mem::size_of_val(&z));
    println!("size of `i` in bytes: {}", std::mem::size_of_val(&i));
    println!("size of `f` in bytes: {}", std::mem::size_of_val(&f));
}
```

