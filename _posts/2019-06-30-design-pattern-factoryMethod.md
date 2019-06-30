---
layout: post
title: '【设计模式四】之工厂方法模式'
date: 2019-06-30
subtitle: '介绍工厂方法模式的相关概念、实现与应用'
categories: 设计模式
cover: 'https://ae01.alicdn.com/kf/HTB1mN5eb2Bj_uVjSZFpq6A0SXXaa.jpg'
tags: 设计模式
---
## 工厂方法模式

### 前言

在简单工厂模式中，所有类的创建都在一个工厂类中实现，但是这会导致工厂类代码太过复杂，工厂方法模式可以解决这个问题。它创建一个抽象工厂类，每个产品都有自己的具体工厂类，在具体工厂类中创建产品。

### 定义

**工厂方法**模式也叫**工厂模式**或**虚拟构造器模式**或**多态工厂模式**。工厂方法模式中，定义一个创建对象的接口（抽象工厂），但由子类决定要实例化的类是哪一个。工厂方法让类吧实例化推迟到子类。

### 结构

![](https://ae01.alicdn.com/kf/HTB1qLURdkxz61VjSZFtq6yDSVXaj.jpg)

工厂方法模式包含四个角色：

* Factory 抽象工厂
* ConcreteFactory 具体工厂
* Product 抽象产品
* ConcreteProduct 具体产品

### 实现

![](https://ae01.alicdn.com/kf/HTB1mN5eb2Bj_uVjSZFpq6A0SXXaa.jpg)

定义一个抽象工厂类，描述了创建产品的方法factoryMethod()，该方法返回Product抽象类。

```Java
public abstract class Factory {
    public abstract Product factoryMethod();
    public void doSomething(){
        Product product = factoryMethod();
        System.out.println("doSomething");
    }
}
```

根据需要定义若干个具体工厂类，继承抽象工厂方法，并重写factoryMethod()方法，用来创建具体产品类

```Java
public class ConcreteFactory extends Factory{
    @Override
    public Product factoryMethod() {
        return new ConcreteProduct();
    }
}
```

产品类可直接依赖简单工厂方法模式中所定义的类。每个具体工厂可以创建对应的具体产品。

```java
// 抽象产品
public interface Product {}
// 若干个具体产品
public class ConcreteProduct implements Product {}
```

客户端中测试一下

```java
public class Client {
    Factory factory = new ConcreteFactory1();
    Product p1 = factory.factoryMethod();
}
```

### 优点

* 基于多态是工厂方法模式的关键，工厂角色和产品角色都各自有同一抽象父类。**工厂完全自主决定如何创建何种对象**，这个过程都封装在具体工厂内部。
* 向客户端隐藏产品细节，只需关注工厂。
* 向系统加入新产品时，无需修改抽象工厂和抽象产品和其他类，只需要关注要新增的具体产品和具体工厂。**系统的扩展性较好**，也符合**开闭原则**。

### 缺点

* 增加系统复杂度，会有很多的小类，扩展时类的个数成对增加。

### 应用

* java.util.Calendar

* ``` Connection conn=DriverManager.getConnection（"driverName"）```

  使用JDBC获取数据库连接时，无论是Mysql还是Oracle，只需要指定驱动名称即可。

* [Collection中的iterator()方法](<https://www.hollischuang.com/archives/1408>)

### 参考

 [工厂方法模式(Factory Method Pattern)](https://design-patterns.readthedocs.io/zh_CN/latest/creational_patterns/factory_method.html#id15)

[工厂方法](https://cyc2018.github.io/CS-Notes/)

[设计模式（六）——JDK中的那些工厂方法](https://www.hollischuang.com/archives/1408)