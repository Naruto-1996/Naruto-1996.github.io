---
title: Android 中的单例模式
date: 2021-06-22 15:01:37
category:
- Android
tags:
- 单例模式
---


### Android 中的单例模式

#### 单例模式的优缺点

1、主要优点:

* 提供了对唯一实例的受控访问。
* 由于在系统内存中只存在一个对象，因此可以节约系统资源，对于一些需要频繁创建和销毁的对象单例模式无疑可以提高系统的性能。
* 允许可变数目的实例

2、主要缺点:

* 由于单利模式中没有抽象层，因此单例类的扩展有很大的困难。
* 单例类的职责过重，在一定程度上违背了“单一职责原则”。
* 滥用单例将带来一些负面问题，如为了节省资源将数据库连接池对象设计为的单例类，可能会导致共享连接池对象的程序过多而出现连接池溢出；如果实例化的对象长时间不被利用，系统会认为是垃圾而被回收，这将导致对象状态的丢失。

#### Java中的几种单例模式

> 懒汉式、 饿汉式、 DCL双重校验模式、 静态内部类单例模式、 枚举单例、 使用容器实现单例模式

#### 懒汉式(线程不安全)

```java

//懒汉式单例类.在第一次调用的时候实例化自己   
public class Singleton {  
    //私有的构造函数
    private Singleton() {} 
    //私有的静态变量 
    private static Singleton single = null;  
    //暴露的公有静态方法   
    public static Singleton getInstance() {  
         if (single == null) {    
             single = new Singleton();  
         }    
        return single;  
    }  
}

```

* 如果多个线程同时调用 getInstance 方法，可能存在同时判断 instance 变量是否为空的情况，上面的代码中很容易导致重复创建多个实例，这违背了单例模式的目的。
* 这种方式创建的缺点是存在线程不安全的问题，解决的办法就是使用synchronized 关键字，便是单例模式的第二种写法了。

#### 懒汉式(线程安全)

```java

public class Singleton {  
    //私有的静态变量
    private static Singleton instance;  
    //私有的构造方法
    private Singleton (){};
    //公有的同步静态方法
    public static synchronized Singleton getInstance() {  
    if (instance == null) {  
        instance = new Singleton();  
    }  
    return instance;  
    }  
}

```

* 这种单例实现方式的getInstance（）方法中添加了synchronized 关键字，也就是告诉Java（JVM）getInstance是一个同步方法。
* 同步的意思是当两个并发线程访问同一个类中的这个synchronized同步方法时，一个时间内只能有一个线程得到执行，另一个线程必须等待当前线程执行完才能执行，因此同步方法使得线程安全，保证了单例只有唯一个实例。
* 缺点也很明显 每次每次调用getInstance（）都进行同步，造成了不必要的同步开销。这种模式一般不建议使用。效率低

#### 饿汉式(天生线程安全)

```java

//饿汉式单例类.在类初始化时，已经自行实例化   
public class Singleton {  
    //static修饰的静态变量在内存中一旦创建，便永久存在
    private static Singleton instance = new Singleton();  
    private Singleton (){}  
    public static Singleton getInstance() {  
    return instance;  
    }  
}

```

* 饿汉式的例子一看就懂，不管三七二十一先创建了对象再说，不同的进程通过 getInstance 获取的都是同一个对象，所以是线程安全的。
* 缺点: 如果某个类创建过程会消耗很多资源，但程序运行中没有调用过 getInstance 方法，那么就存在资源浪费的情况，如果一个系统存在非常多此类情况那么这个系统可能存在性能上的问题。
* 所以，我们需要的是一种延迟加载的功能。

#### DCL双重校验模式(推荐使用)

```java

public class Singleton {  
    private volatile static Singleton singleton;  //静态变量
    private Singleton (){}  //私有构造函数
    public static Singleton getInstance() {  
      if (singleton == null) {  //第一层校验
          synchronized (Singleton.class) {  
          if (singleton == null) {  //第二层校验
              singleton = new Singleton();  
          }  
        }  
      }  
    return singleton;  
    }  
}

```
* 这种写法也是用的最多的 增加了效率的同时 也是线程安全的

#### 静态内部类(推荐使用)

```java

public class Singleton {  
    private Singleton (){} ;//私有的构造函数
    public static final Singleton getInstance() {  
        return SingletonHolder.INSTANCE;  
    }  
    //定义的静态内部类
    private static class SingletonHolder {  
        private static final Singleton INSTANCE = new Singleton();  //创建实例的地方
    }  
}

```

* 这种方法也可以做到延迟加载，但是它又不同于饿汉式。
* 因为只有调用 getInstance 时，SingletonHolder 才会进行初始化。
* Android中涉及到图片缓存加载的时候经常会看到一些开源组件有各类 ImageHolder 的代码，原理正是如此。

#### 枚举类型

```java

public enum Singleton {  //enum枚举类
    INSTANCE;  
    public void whateverMethod() {  

    }  
}

```

* 枚举单例模式最大的优点就是写法简单，枚举在java中与普通的类是一样的，不仅能够有字段，还能够有自己的方法，最重要的是默认枚举实例是线程安全的，并且在任何情况下，它都是一个单例。即使是在反序列化的过程，枚举单例也不会重新生成新的实例。

我们只需要 Singleton.INSTANCE.whateverMethod() 来访问实例的方法，这比调用getInstance()方法简单多了

#### 容器式

```

public class SingletonManager { 
　　private static Map<String, Object> objMap = new HashMap<String,Object>();//使用HashMap作为缓存容器
　　private Singleton() { }
　　public static void registerService(String key, Objectinstance) {
　　　　if (!objMap.containsKey(key) ) {
　　　　　　objMap.put(key, instance) ;//第一次是存入Map
　　　　}
　　}
　　public static ObjectgetService(String key) {
　　　　return objMap.get(key) ;//返回与key相对应的对象
　　}
}

```

* 在程序的初始，将多种单例模式注入到一个统一的管理类中，在使用时根据key获取对应类型的对象。

总结 推荐使用 DCL 和 静态内部类的方式



