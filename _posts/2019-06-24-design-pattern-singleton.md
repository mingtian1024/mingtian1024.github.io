---
layout: post
title: '【设计模式二】之单例模式'
date: 2019-06-24
subtitle: '介绍单例模式的相关概念、实现方式及应用举例'
categories: 设计模式
cover: 'https://cy-pic.kuaizhan.com/g3/01/ce/f000-060f-4fa9-8144-42672b657d4f47'
tags: 设计模式
---
## 单例模式 Singleton Pattern
### 定义
单例模式确保某一个类只有一个实例，而且自行实例化，并向整个系统提供这个实例。这个类称为单例类。
关键点：
* 单例类只有一个实例
* 此实例自行创建
* 提供全局访问方法

### 实现
单例模式有多种实现方式，但都是基于上文提到的三个要点，实现时也有三个要点：
* 单例类有一个自身的**静态私有**成员变量 ———— 存储唯一实例
* 单例类的构造方法为**私有**private ———— 确保无法通过new直接实例化
* 提供一个public**公有**的静态工厂方法 ———— 校验实例是否存在并实例化它  
下面介绍几种单例模式的经典实现方法
![](https://cy-pic.kuaizhan.com/g3/01/ce/f000-060f-4fa9-8144-42672b657d4f47)  


#### 1. 懒汉模式（线程不安全）
* 懒加载初始化
* 线程不安全  
类加载时没有初始化类，获取时才初始化，属于懒加载。
多线程下，会产生多个实例，线程不安全。
```Java
public class SingletonLazy {
    private static SingletonLazy instance;
    private SingletonLazy() {
    }
    public static SingletonLazy getInstance(){
        if (instance == null){
            instance = new SingletonLazy();
        }
        return instance;
    }
}
```

#### 2. 懒汉模式（线程安全）
* 懒加载初始化
* 线程安全  
能够在多线程下工作，synchronized可以保证单例，但是效率较低。
```Java
public class SingletonLazy2 {
    private static SingletonLazy2 instance;
    private SingletonLazy2() {
    }
    public static synchronized SingletonLazy2 getInstance(){
        if (instance == null){
            instance = new SingletonLazy2();
        }
        return instance;
    }
}
```
两种懒汉方式总结:  
优点：第一次调用时才初始化，避免浪费内存
缺点：多线程支持不好

#### 3. 饿汉模式
* 非懒加载
* 线程安全  
这种方式类加载时较慢，但获取对象较快。基于类加载机制避免了多线程的同步问题，但是导致类加载的方式有很多
优点：不用加锁就支持多线程，执行效率较高
缺点：**类加载时就初始化**，容易产生垃圾对象，浪费内存。有时类Singleton类被加载了，不需要instance被立刻初始化。这点在[方式5](#5-静态内部类)中有改进
```Java
public class Singleton3 {
    private static Singleton3 instance = new Singleton3();
    private Singleton3() {
    }
    public static Singleton3 getInstance() {
        return instance;
    }
}
```

#### 4. 双重校验
* 懒加载初始化
* 多线程安全  
这种方式采用双重校验，多线程下安全且具有较高性能（JDK1.5起才支持）
volatile关键字是十分必要的，因为：
    ```instance = new Singleton4()```这行代码分三步执行
    > 1. 为instance分配内存空间 ```memory =allocate(); ```
    > 2. 初始化instance ```ctorInstance(memory);```
    > 3. 将instance指向分配的内存空间 ```instance = memory;```

    但由于JVM存在**指令重排**，执行顺序可能变成1>3>2。单线程环境下没有问题，但多线程环境下，如果线程A执行了1、3,线程B正好执行```if (instance == null)```判断为true，if里语句会执行，多次实例化instance，显然不符合单例模式。此时线程A其实正在初始化instance，判断结果应该为false，if里语句不应该执行。
    > volatile关键字阻止变量访问前后的指令重排，保证了指令执行顺序  

```Java
public class Singleton4 {
    private volatile static Singleton4 instance;
    private Singleton4() {
    }
    public static Singleton4 getInstance() {
        if (instance == null){  // 判断1
            synchronized (Singleton4.class){
                if (instance == null){  // 判断2
                    instance = new Singleton4();
                }
            }
        }
        return instance;
    }
}
```

#### 5. 静态内部类 
* 懒加载初始化（Jvm加载类后，并没有将其初始化。直到静态变量被访问时，才使类被初始化（静态变量被赋值））
* 线程安全  
这种方式利用classloader的加载机制来实现懒加载。单例类Singleton5在加载时，INSTANCE并不会被初始化，只有显式调用getInstance方法时才会初始化。  
> 类的静态变量被初次访问会触发 Java 虚拟机对该类进行初始化，即该类的静态变量的值会变为其初始值而不是默认值。因此，静态方法 getlInstance()被调用的时候Jvm会初始化这个方法所访问的内部静态类 SingletonHolder ，这使得 SingletonHolder的静态变量 INSTANCE 被初始化，从而使 Singleton5  类的唯一实例得以创建。由于类的静态变量只会创建一次，因此 Singleton5（单例类）只会被创建一次。


```Java
public class Singleton5 {
    private static class SingletonHolder {
        private static final Singleton5 INSTANCE = new Singleton5();
    }
    private Singleton5() {
    }
    private static final Singleton5 getInstance(){
        return SingletonHolder.INSTANCE;
    }
}
```

#### 6. 枚举
* 非懒加载
* 线程安全  

> Singleton6.INSTANCE 初次被引用的时候才 被初始化的仅访问 Singleton 本身（比如上述的 Singleton.class.getNa me（）调用）并不会导致 S ingleton 的唯一 实例被初始化 。

避免多线程同步问题，而且还自动支持序列化机制，防止反序列化重新创建新的对象，绝对防止多次实例化。
```Java
public enum Singleton6 {
    INSTANCE;
    public void method(){}
}
```
### 优点
引用[图说设计模式](https://design-patterns.readthedocs.io/zh_CN/latest/creational_patterns/singleton.html#id10)

> * 提供了对唯一实例的受控访问。
   因为单例类封装了它的唯一实例，所以它可以严格控制客户怎样以及何时访问它，并为设计及开发团队提供了共享的概念。
> * 由于在系统内存中只存在一个对象，因此可以节约系统资源，对于一些需要频繁创建和销毁的对象，单例模式无疑可以提高系统的性能。
> * 允许可变数目的实例。我们可以基于单例模式进行扩展，使用与单例控制相似的方法来获得指定个数的对象实例。


### 缺点
> * 由于单例模式中没有抽象层，因此单例类的扩展有很大的困难。
> * 单例类的职责过重，在一定程度上违背了“单一职责原则”。因为单例类既充当了工厂角色，提供了工厂方法，同时又充当了产品角色，包含一些业务方法，将产品的创建和产品的本身的功能融合到一起。
> * 滥用单例将带来一些负面问题:
    如为了节省资源将数据库连接池对象设计为单例类，可能会导致共享连接池对象的程序过多而出现连接池溢出；
    现在很多面向对象语言(如Java、C#)的运行环境都提供了自动垃圾回收的技术，因此，如果实例化的对象长时间不被利用，系统会认为它是垃圾，会自动销毁并回收资源，下次利用时又将重新实例化，这将导致对象状态的丢失。  

### 应用
**java.lang.Runtime**
```Java
// JDK1.8中getRuntime（）的实现，采用饿汉模式
public class Runtime {
    private static Runtime currentRuntime = new Runtime();
    private Runtime() {}
    public static Runtime getRuntime() {
        return currentRuntime;
    }
}
```

### 参考
[菜鸟教程-单例模式](https://www.runoob.com/design-pattern/singleton-pattern.html)
[漫画：什么是单例模式？](https://zhuanlan.zhihu.com/p/33102022)
[设计模式（三）——JDK中的那些单例](https://www.hollischuang.com/archives/1383)  
[Graphic Design Patterns - Signleton Pattern(中文)](https://design-patterns.readthedocs.io/zh_CN/latest/read_uml.html#id1)  

