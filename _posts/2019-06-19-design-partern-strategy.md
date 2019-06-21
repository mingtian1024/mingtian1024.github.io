---
layout: post
title: '【设计模式】之策略模式'
date: 2019-06-19
categories: 设计模式
cover: 'https://ae01.alicdn.com/kf/HTB1clLadBKw3KVjSZTEq6AuRpXab.jpg'
tags: 设计模式
---

#### 定义
策略模式定了算法族（即一系列算法），分别封装起来，让它们之间可以互相替换。此模式让算法的变法独立于使用算法的客户。
策略模式中，一个类的行为或其算法可以在运行时更改。

#### 实现

策略模式涉及到三个角色：

* 环境Context：持有一个Strategy的引用

* 策略接口Strategy：此角色定义具体策略需要实现的接口方法

* 策略实现类：实现具体算法

  ![](https://ae01.alicdn.com/kf/HTB1clLadBKw3KVjSZTEq6AuRpXab.jpg)



#### 实现举例：

一个动作冒险游戏中，一名骑士有多件武器，弓箭、盾牌、宝剑等，所有的装备都可以使用，但一次只能使用一件装备。
定义一个接口，表示使用装备的效果，由具体的装备实现

```Java
public interface WeaponBehavior {
    void useWeapon();
}
```
宝剑、弓箭继承武器接口
```Java
public class SwordBehavior implements WeaponBehavior{
    @Override
    public void useWeapon() {
        System.out.println("挥舞宝剑");
    }
}
public class ArrowBehavior implements WeaponBehavior {
    @Override
    public void useWeapon() {
        System.out.println("远程射箭");
    }
}
```
定义游戏角色，他有一个武器属性，setWeapon()方法可以设置此属性，fight()方法调用武器的userWeapon()方法
```Java
public class Character {
    WeaponBehavior weapon;
    public void setWeapon(WeaponBehavior w){
        this.weapon = w;
    }
    public void fight(){
        if (weapon != null){
            weapon.useWeapon();
        }
    }
}
```
模拟游戏过程
```Java
public class GameClient {
    public static void main(String[] args){
        Character kight = new Character();
        // 挥舞宝剑
        kight.setWeapon(new SwordBehavior());
        kight.fight();
        // 弓箭远程射击
        kight.setWeapon(new ArrowBehavior());
        kight.fight();
    }
}
// 输出
// 挥舞宝剑
// 远程射箭
```
发现，骑士只有攻击技能，没有防御技能。添加一个ShieldBehavior防御行为，
并调用kight.setWeapon(new ShieldBehavior())，kignt.fight()
```Java
public class ShieldBehavior implements WeaponBehavior {
    @Override
    public void useWeapon() {
        System.out.println("盾牌格挡");
    }
}
```


#### 设计原则
* 封装变化：
* 多用组合，少用继承
* 针对接口编程，而不是针对实现编程

#### 优点与缺点
* 优点
1. 完美支持**开闭原则**，用户可以在不修改原有系统的基础上增加、选择算法或行为；
2. 提供了替换继承关系的方法
3. 提供了管理相关算法的族的方法
4. 可以避免多重条件语句
* 缺点
1. 使用者必须知道所有的策略类，并知晓选择算法的逻辑
2. 策略模式会产生很多的策略类，可以通过**享元模式**减少对象的数量

#### 应用


#### 参考
[设计模式-策略模式](https://cyc2018.github.io/CS-Notes/#/notes/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F?id=_9-%E7%AD%96%E7%95%A5%EF%BC%88strategy%EF%BC%89)  
[5.策略模式](https://design-patterns.readthedocs.io/zh_CN/latest/behavioral_patterns/strategy.html)