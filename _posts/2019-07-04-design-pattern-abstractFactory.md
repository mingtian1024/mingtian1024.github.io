---
layout: post
title: '【设计模式五】之抽象工厂模式'
date: 2019-06-30
subtitle: '介绍抽象工厂模式的相关概念、实现与应用'
categories: 设计模式
cover: 'https://ae01.alicdn.com/kf/HTB1HEaSe8Kw3KVjSZFOq6yrDVXal.jpg'
tags: 设计模式
---

## 抽象工厂模式

### 前言

在工厂方法模式中，我们有进入了一个误区，即一个工厂只生产一种单一的产品，比如一个汽车工厂只能生产一种型号的汽车，但实际上工厂的功能应该不止于此。举个例子，奥迪汽车厂即生产跑车也生产轿车，即生产奥迪A6也生产奥迪A8。抽象工厂模式则是工厂方法模式的进一步抽象和深入。

### 定义

提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。抽象工厂模式又称为Kit模式。

### 模式结构分析

和工厂方法模式一样，抽象工厂模式涉及到

- AbstractFactory 抽象工厂

- ConcreteFactory 具体工厂

- AbstractProduct 抽象产品

- ConcreteProduct 具体产品

  抽象工厂定义工厂的生产产品的方法。比如一个汽车抽象工厂定义两个方法createSedanCar()生产轿车、createSportsCar()生产跑车；

  具体工厂继承抽象工厂，并重写方法。比如，奔驰汽车厂重写createSedanCar()方法来生产奔驰轿车，重写createSportsCar()生产奔驰跑车；

  抽象产品定义产品共有的属性和方法。比如，轿车有四个门，跑车只有两个门；

  具体产品则继承抽象产品，并定义属于自己的属性和方法。

### 实现

三种工厂模式中，抽象工厂模式名副其实，显然是最复杂、最抽象的。它的实现基本UML图如下。实现代码就不列出来了，结合具体的实现举例再看代码。

![](https://ae01.alicdn.com/kf/HTB1F51Ke8GE3KVjSZFhq6AkaFXa1.jpg)



### 实现举例

车有很多种类，如跑车、轿车、SUV；也有很多品牌，如奔驰、宝马、奥迪。各个品牌都有各种车型。如何用抽象工厂模式来生产它们呢？

首先，肯定先要设计出产品。定义一个抽象汽车类，它是所有车型的基类。

```java
interface Car {
    void run();
}
```

再设计不同类型的汽车，它们都继承自Car

```java
// 轿车
public abstract class SedanCar extends Car{
    @Override
    public abstract void run();
}
// 跑车
public abstract class SportsCar extends Car{
    @Override
    public abstract void run();
}
```

汽车设计好了，然后再建造生产汽车的工厂。汽车工厂大体都差不多，可以定义一个抽象工厂，它可以生产轿车和跑车。它是所有工厂的基类。

```java
public abstract class CarFactory {
    abstract SedanCar createSedanCar();
    abstract SportsCar createSportsCar();
}
```

不同的品牌汽车之间都有所不同，它们有各自的工厂生产自己的汽车。比如，奔驰汽车厂可以生产奔驰轿车C200，奥迪汽车厂可以生产奥迪跑车R8。这就涉及到具体产品和具体工厂。

```java
// 这些都是具体产品
// 奔驰轿车
public class BenzSedanCar extends SedanCar{
    public void run() {
        System.out.println("奔驰轿车");
    }
}
// 奔驰跑车
public class BenzSportsCar extends SportsCar {
    public void run() {
        System.out.println("奔驰跑车");
    }
}
// 奥迪轿车
public class AudiSedanCar extends SedanCar {
    public void run() {
        System.out.println("奥迪轿车");
    }
}
// 奥迪跑车
public class AudiSportsCar extends SportsCar {
    public void run() {
        System.out.println("奥迪跑车");
    }
}
```

不同品牌有自己的工厂，可以生产各自的车型。奥迪有奥迪汽车厂，生产轿车和跑车（奥迪轿车和奥迪跑车）。

```java
// 奔驰汽车厂
public class BenzFactory extends CarFactory{
    SedanCar createSedanCar() {
        SedanCar c = new BenzSedanCar();
        return c;
    }
    SportsCar createSportsCar() {
        SportsCar s = new BenzSportsCar();
        return s;
    }
}
// 奥迪汽车厂
public class AudiFactory extends CarFactory{
    SedanCar createSedanCar() {
        SedanCar c = new AudiSedanCar();
        return c;
    }
    SportsCar createSportsCar() {
        SportsCar s = new AudiSportsCar();
        return s;
    }
}
```

整个实现中，涉及到两个具体工厂，四个具体产品。

![](https://ae01.alicdn.com/kf/HTB1HEaSe8Kw3KVjSZFOq6yrDVXal.jpg)

生产。创建奔驰工厂，生产轿车，最后运行结果显示是奔驰轿车，没毛病。

```java
public class Client {
    public static void main(String[] args){
            CarFactory benzFactory = new BenzFactory();
            Car car = benzFactory.createSedanCar();
            car.run();// 奔驰轿车
    }
}
```

### 模式扩展

> 可扩展性是从简单工厂模式、工厂方法模式到抽象工厂模式都必须关注也是最关键的一个问题。前面两个模式介绍他它们的缺点时，都提到过，不易扩展，或者随着扩展系统越来越复杂。，抽象工厂模式如何解决扩展的问题？ 	

#### 如何扩展工厂？

正当奔驰和奥迪生产的如火如荼的时候，新晋厂商宝马也投入到汽车行业。

#### 如何新增产品？

随着科技的进步，电动汽车已经上市了，各大厂商都开始跟进生产电动汽车。