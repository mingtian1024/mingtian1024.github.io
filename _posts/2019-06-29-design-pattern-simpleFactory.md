---
layout: post
title: '【设计模式三】之简单工厂模式'
date: 2019-06-29
subtitle: '介绍简单工厂模式的相关概念与实现'
categories: 设计模式
cover: 'https://ae01.alicdn.com/kf/HTB1KbsQev1H3KVjSZFHq6zKppXal.jpg'
tags: 设计模式
---
## 简单工厂模式

### 定义

简单工厂模式，又称静态工厂方法模式。简单工厂模式中，定义一个工厂类，来负责创建其他类的实例，这些被创建的类通常都有共同的父类或继承自同一个接口。

### 结构

![](https://ae01.alicdn.com/kf/HTB1KbsQev1H3KVjSZFHq6zKppXal.jpg)

简单工厂模式中有三个角色：

* Factory 工厂角色
  * 工厂角色实现了创建实例的逻辑
* Product 抽象产品角色
  * 是所有被创建的类的父类，描述这些类的接口
* ConcreteProduct 具体产品角色
  * 被创建的类

### 实现

```java
// 抽象产品
public interface Product {}
// 3个具体产品
public class ConcreteProduct implements Product {}
public class ConcreteProduct1 implements Product {}
public class ConcreteProduct2 implements Product {}
// 简单工厂类,实现了产生哪个产品的逻辑
public class SimpleFactory {
    public static Product createProduct(int param){
        if (param == 1){
            return new ConcreteProduct1();
        }else if (param == 2){
            return new ConcreteProduct2();
        }
        return new ConcreteProduct();
    }
}
```

实现了基本的角色，就可以在客户端中使用

```java
public static void main(String[] args){
        Product p1 = SimpleFactory.createProduct(1);
        Product p2 = SimpleFactory.createProduct(2);
}
```

### 优点

1. 将类的实例化与对象的操作分开，**解耦**。
2. 一般创建对象实例的代码比较繁琐，工厂模式中客户端仅需知道必要的参数就可以创建需要的具体产品。**降低代码复杂度**。

### 缺点

1. 工厂类的职责过重，所有实例的创建都依赖于工厂类中的逻辑
2. 扩展困难，每次添加新产品都要修改工厂类的代码，违背“开放-关闭原则”。工厂中的代码势必会越来越复杂
3. 使用了静态工厂方法，造成工厂角色无法形成基于继承的等级结构

### 应用

* JDK中的java.text.DateFormat

### 总结

简单工厂模式其实并不属于GOF设计模式23种之一。它实现比较简单，适用于需要创建的类并不多，后续也不需要太多扩展的情况。

### 参考

 [简单工厂模式( Simple Factory Pattern )](https://design-patterns.readthedocs.io/zh_CN/latest/creational_patterns/simple_factory.html#id14)
