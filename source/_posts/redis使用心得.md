---
title: redis使用心得
date: 2020-12-02 12:09:05
categories:
- Redis
tags:
- Redis
- Mac
---

### Redis 使用

#### 1、在本地使用

进入到redis的src目录中

```
./redis-cli 
```

* 一些常规操作

```
LPUSH 是把新的元素从右侧插入到数组中
LPUSH fruits apple   # => ["apple"]   把元素从左边插入到数组中
> LPUSH  fruits banana   # => ["banana", "apple"]  

RPUSH 是把新的元素从右侧插入到数组中
> LINDEX fruits 0     # => "banana"
> LINDEX fruits 1    # =>  "apple"
> LRANGE fruits 0 1  #=> ['banana', 'apple']   ， 注意只有lrange, 没有 rrange. 
```

* 获取所有的key

```
keys *   这个*号是匹配所有的key 可以拼接 比如 fruits*
```

```
127.0.0.1:6379> keys *
1) "fruits"
```

* 查看某个key下的值:

```
lrange fruits 0 -1
```

```
127.0.0.1:6379> lrange fruits 0 -1
1) "def"
2) "abc"
3) "banana"
4) "apple"
```

* 截断 保留trim中参数部分

```
ltrim fruits 0 2
```

```
我们看到 这里只保留了0-2剩下的都删除了
127.0.0.1:6379> lrange fruits 0 -1
1) "def"
2) "abc"
3) "banana"
```
 
