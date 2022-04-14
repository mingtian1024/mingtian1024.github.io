---
layout: post
title: '详解Java多线程'
date: 2021-12-04
subtitle: '介绍Java多线程核心知识，包括创建线程、线程池、线程安全、线程同步、死锁、JUC等'
cover: 'https://cdn.nlark.com/yuque/0/2021/jpeg/111742/1616482786410-4a3becf1-fc59-40f7-84f6-a52cfd204730.jpeg'
categories: 多线程
tags: Java 多线程

---


![](https://cdn.nlark.com/yuque/0/2021/jpeg/111742/1616482786410-4a3becf1-fc59-40f7-84f6-a52cfd204730.jpeg)
# 线程基础
## 线程相关概念
### 并行与并行
并行：某一**时间点**，多个事件同时发生。
并发：某一**时间段**，多个事件交替发生。
> 以唱K举例，一个话筒，几个人同时对着它唱，就是并行；大家一人一句轮流唱，就是并发

### 进程与线程
线城是CPU调度资源的最小单位
## 创建线程的方式
> 创建线程形式上有多种方式，但基本的方式只有两种。实现Callable接口、线程池等方式，本质上还是实现Runnable接口

### 继承Thread类
```java
public class MyThread impliments Thread{
	@Override
    public void run() {
        // 获取线程名字
        String threadName = this.getName();
        System.out.println(threadName);
    }

    public static void main(String[] args) {
        Thread t1 = new MyThread();
        t1.start();
    }
}
```
### 实现Runnable接口
```java
public class MyRaunnable extends Runable{
    @Override
    public void run() {
        // 获取线程名字
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName);
    }

    public static void main(String[] args) {
        // 创建线程时，传入一个实现了Runable接口的类的对象
        Runnable myRunnable = new ThreadRunable();
        Thread t2 = new Thread(myRunnable);
        Thread t3 = new Thread(myRunnable,"自定义线程名字");
        t2.start();
        t3.start();
    }
}
```
### 其他写法
这几种写法并不是新的线程创建方式，只是利用Java语法糖的一些便捷写法。
##### 匿名内部类
不需要事先自定义一个线程类，创建线程的时候直接传入匿名内部类
```java
public static void main(String[] args) {
        Thread t1 = new Thread(){
            @Override
            public void run() {
                System.out.println(this.getName());
            }
        };
        t1.start();
    }
```
##### Lamda表达式
```java
    public static void main(String[] args) {
        // Lamda表达式创建一个线程实例
        Thread t = new Thread(() -> {
            // run()方法体
            String threadName = Thread.currentThread().getName();
            System.out.println(threadName);
        });
        t.start();
    }
```
### 其他创建方式
还有一些创建线程的方式，本质上还是在Runable接口的基础上进行封装
##### Callable接口和Future接口
##### 线程池创建线程

## 线程的声明周期、状态与转换
线程的状态有的地方说是五种，有的地方说是六种；
个人认为，从理解的角度，线程有五种状态；
从Java源码的定义看，线程有六种状态，即State枚举类中定义的六种状态
### 线程有五种状态
![多线程的状态及转换.jpg](https://cdn.nlark.com/yuque/0/2020/jpeg/111742/1600701164226-d698b5a3-5876-419a-9f98-ac5bf70f42ef.jpeg#crop=0&crop=0&crop=1&crop=1&height=541&id=hamYN&margin=%5Bobject%20Object%5D&name=%E5%A4%9A%E7%BA%BF%E7%A8%8B%E7%9A%84%E7%8A%B6%E6%80%81%E5%8F%8A%E8%BD%AC%E6%8D%A2.jpg&originHeight=541&originWidth=1444&originalType=binary&ratio=1&rotation=0&showTitle=false&size=89885&status=done&style=none&title=&width=1444)
### 线程的六种状态
JDK Thread类源码中，有一个State枚举类，里面完整地列举了线程的6种状态。
A thread state. A thread can be in one of the following states:
#### NEW
> A thread that has not yet started is in this state.

一个已创建而未启动的线程处于该状态。一个线程只能被创建一次，一次一个线程也只能有一次处于该状态
#### RUNNABLE
> A thread executing in the Java virtual machine is in this state.
> 一个正在被JVM执行的线程的状态

该状态可被看成一个复合状态，包括两个子状态

- READY
   - 已启动，但是等待被线程调度器调度的状态，一旦被调度，就处于RUNNING状态
- RUNNING
   - 正在运行的状态，即线程正在执行run方法代码。

调用 yield() 方法，可能使RUNNING装换到READY
#### BLOCKED
> A thread that is blocked waiting for a monitor lock is in this state.
> 等待获取监视器锁，被阻塞的线程

申请一个由其他线程持有的独占资源时，线程处于BLOCKED状态，此状态的线程不占用处理器资源
#### WAITING
> A thread that is waiting _indefinitely_ for another thread to perform a particular action is in this state.
> 无限期等待另一个线程执行特定操作的线程处于此状态。


#### TIMED_WAITING
> A thread that is waiting for another thread to perform an action for up to a specified waiting time is in this state.
> 等待另一个线程执行操作，等待时间是指定的，处于此状态。

#### TERMINATED
A thread that has exited is in this state.
> 线程已退出

已经执行结束的线程处于该装啊提。run()正常返回或抛出异常而提前终止都会导致线程进入状态

> A thread can be in only one state at a given point in time. These states are virtual machine states which do not reflect any operating system thread states.
> 在给定的时间点，线程只能处于一种状态。这些状态是不反映任何操作系统线程状态的虚拟机状态。



```java
   public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
    }
```
# 线程安全
多个线程同时处于运行状态时，线程的调度由操作系统决定，程序无法决定。任何一个线程都有可能在任何指令处被操作系统暂停，之后某个时间再继续。
对于下面这个语句，从Java语法上来说它是一行语句，但实际上对应了3行指令
```java
n = n + 1;
```
对应了以下3条指令：
```markdown
ILOAD   // 
IADD    // 
ISTORE  // 
```
多线程情况下，任意语句处都有可能被中断，Java用关键字 `synchronized` 保证代码块任意时刻只能被一个线程执行。
`synchronized` 会在代码块对应的指令开始前上锁，指令结束后解锁。

`synchronized` 的缺点：

1. `synchronized` 代码块无法并发执行，导致性能下降；
1. 加锁、解锁需要消耗资源与时间

## 竞态
访问同一组共享变量的多个线程所执行的操作，相互交错导致的干扰、冲突的结果
两种模式：

1. read-modiy-write 读-改-写
1. check-then-act 检测后行动

## 线程安全的三个方面
原子性、可见性、有序性
### 原子性 Atomic
一组操作不可分割，执行过程中不会被其他线程打断。
在外部来看，原子操作只有未开始和已结束两个状态，不能被读取到中间状态

- 原子操作时针对访问共享变量的操作而言。
- 原子操作是从操作的执行线程以外的线程来描述的。

如何实现原子性：

1. 使用锁，Lock，所具有排他性，保证一个共享变量任意时刻只能被一个线程访问。
1. CAS，在处理器和内存这一层次实现的，是硬件级锁
> Java中，long、double基础型的变量和写操作不是原子操作。其他类型的是原子操作

### 可见性 Visibility
一个线程对共享变量的修改是否可以立即被其他线程看到。

> 扩展1
> 单处理器系统也存在可见性问题
> 单处理器系统中虽虽然多个线程是远行在同一个处理器上的，但是由于在发生上下文切换（Context Switch）的时候，一个线程对寄存器（Register）变量的修改会被作为该线程的线程上下文保存起来，这导致另外一个线程无法“看到”该线程对这个变量的修改，因此，单处理器系统中实现的多线程编程也可能出现可见性问题

> 扩展2
> 原子性与可见性的联系与区别
> 1. 原子性描述的是一个线程对共享变量的更新，从另外一个线程的角度来看，它要么完 成了，要么尚未发生，而不是进行中的一种状态。因此，原子性可以保证一个线程所读取 到的共享变量的值要么是该变量的初始值要么是该变量的相对新值，而不是更新过程中的一个相当于“半成品”的值。
> 
_紧靠原子性还不能保障一个线程能正确看到其他线程对共享变量所做的更新_
> 2. 可见性描述的是一个线程对共享变量的更新对于另外一个线程而言是否可见（或者说 什么情况下可见)的问题，保障可见性意味着个线程可以读取到相应共享变量的相对新值
> 
![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1646806146685-2cec7a96-fb3e-4c6a-bb73-9e112f123741.png#clientId=u021cbf02-0ea0-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=333&id=u1238f39d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=666&originWidth=2276&originalType=binary&ratio=1&rotation=0&showTitle=false&size=2717514&status=done&style=none&taskId=u5f9c2c4d-2942-4bdd-be95-4378e769eaa&title=&width=1138)
> 原子性可以保证，t3时刻，线程p2读取到的a的值，要么为0，要么为1或者2，但不能确定是1还是2。
> 到底是1还是2，这要依靠可见性，即线程p1、p2对a值修改后，其他线程立刻可读取到最新值

### 有序性 Ordering
![](https://cdn.nlark.com/yuque/0/2022/jpeg/111742/1648797497245-89fb3039-47d8-4a8c-988e-112d0d4cbb18.jpeg)
指令重排序
> 在源代码顺序与程序顺序不一致，或者程序顺序与执行顺序不一致的情况下，我们就 说发生了指令重排序(Instruction Reorder)。指令重排序是一种动作，它确确实实地对指 令的顺序做了调整，其重排序的对象是指令。

存储子系统重排序
> 即使在处理器严格依照程序顺序执行两个内存访问操作的情况下’，在存储子系统的 作用下其他处理器对这两个操作的感知顺序仍然可能与程序顺序不一致，即这两个操作的 执行顺序看起来像是发生了变化。这种现象就是存储子系统重排序，也被称为内存重排序 (Memory Ordering)。

指令重排序的重排序对象是指令，它实实在在地对指令的顺序进行调整，而存储子系 统重排序是一种现象而不是一种动作，它并没有真正对指令执行顺序进行调整，而只是造 成了一种指令的执行顺序像是被调整过一样的现象，其重排序的对象是内存操作的结果。 习惯上为了便于讨论，在论及内存重排序问题的时候我们往往采用指令重排序的方式来表述，即我们也会用“内存操作X被重排序到内存操作Y之后”这样的表述称呼内存重排序

**As-if-serial 貌似串行**
尽管在编译、执行过程可能发生重排序，但是重排都遵循貌似串行原则。貌似串行原则使得重排序后的执行结果，和没有重排是一样的，看起来像是串行一样。
但是貌似串行保障的是单线程环境下的重排序不应程序运行结果，而如何将这个貌似串行扩展到多线程环境就是值如何保障多线程的有序性。
**保障有序性**
通过逻辑上部分禁止一部分重排序来实现有序性。从底层来说，禁止重排序是调用处理器提供的指令（内存屏障）来实现。
Java作为跨平台的语言，会提我们与这类指令打交道。如`volatile`、`synchronized`。

> 扩展
> 有序性和可见性的联系与区别
> 可见性是有序性的基础。可见性描述的是一个线程对共享变量的更新对于另外一个线 程是否可见，或者说什么情况下可见的问题。有序性描述的是，一个处理器上运行的线程 对共享变量所做的更新，在其他处理器上运行的其他线程看来，这些线程是以什么样的顺序 观察到这些更新的问题。因此，可见性是有序性的基础。另一方面，二者又是相互区分的。
> 有序性影响可见性。由于重排序的作用，一个线程对共享变量的更新对于另外一个线
> 程而言可能变得不可见。



## 线程安全产生的原因

1. 多个线程操作共享的数据
1. 操作共享数据的线程代码有多条

# 线程的活性故障
由于资源稀缺性或程序问题导致线程一直处于非RUNNABLE状态或线程虽然处于RUNNABLE状态但是其执行的任务却没有进展，这就是`_线程活性故障_`
常见的活性故障：

- 死锁 DeadLock

多个线程相互持有资源

- 锁死 Lockout

无法达到唤醒线程的条件

- 活锁 LiveLock

线程可能处于RUNNABLE状态，但是任务没有进展，即线程一直在做无用功

- 饥饿 Starvation

资源都倾向于某些线程，而另一部分总是无法分配到资源，就处于饥饿状态
## 死锁 DeadLock
### 什么是死锁？
互相持有对方需要的资源，又互相等待对方持有的资源，想成一种僵持的局面
### 死锁形成的条件
两个或多个线程构成互相等待的状况。

- 资源互斥
- 资源不可抢夺
- 占用并等待资源
- 循环等待资源（其实这也包含了”占用并等待资源“的意思）
> 这些条件是死锁产生的必要条件 而非充分条件， 也就是说只要产生了死锁， 那么上面 这些条件一定同时成立， 但是上述条件即使同时成立也不 一定就能产生死锁。
> 即，死锁不是必然会出现的

### 如何规避死锁
规避死锁破坏任一条件即可，但是资源时不可改变的，所以规避死锁需要从“等待资源”的两点出发。
即破坏“占用并等待资源”、“循环等待资源”这这两点入手
#### 粗锁法
使用粗粒度的锁代替多个锁，只需要一个范围较广的锁。所有的线程都去申请这**同一个锁。**
缺点很明显：降低了并发性并可能导致资源浪费。本来多个线程并发执行，加了粗锁后一次只能有一个线程执行，相当于用粗俗强制线程串行
#### 锁排序法
将锁按照一定规则排序，全局所有线程都按照同一个顺序申请、释放锁。
#### tryLock(long timeout, TimeUnit unit)
`ReentrantLock.tryLock(long timeout, TimeUnit unit)`
申请锁，此方法再没有申请到锁时，不会一直等待，从而破坏“循环等待条件”

1. 如果在 tryLock(long,TimeUnit) 执行的那一刻相应的锁正被其他线程持有，
那么该方法会使当前线程暂停
1. 直到这个锁被申请成功（ 此时该方法返回 true ）
1. 或者等待时间超过指定的超时时间（ 此时该方法返回 false ）

#### 开放调用（ Open Call 
在调用外部方法时不加锁，通过无锁方式改造方法。不加锁，用volatile、threadlocal等代替synchronized
#### 锁的替代
### 出现死锁如何处理
常见的死锁处理方式大致分为两类：

- 事前的预防措施：包括锁的顺序化、资源合并、避免锁嵌套等等。
   - 顺序化：如果需要获取多个锁，所有线程都必须按指定顺序获取
   - 资源合并：将过个资源合并成一个资源，这样获取多个锁就变成了只需要获取一个锁
   - 避免嵌套：获取一个锁后，必须释放了这个锁，才能获取其他锁。这样避免锁嵌套，防止形成循环等待
- 事后的处理措施：包括锁超时机制、抢占资源机制、撤销线程等等。
   - 锁超时机制：线程获取锁时，设定最长等待时间，这样不需要无限等待下去
   - 抢占资源机制：
   - 撤销线程机制：
### 分析Thread Dump查看死锁
```bash
// mac下的操作

1. 获取要分析的Java进程Id
jps -v 

2. 获取dump信息，会在当前目录下生成日志文件
jstack -l 9894 | tee -a jstack001.log

```
# 线程同步 - 解决线程安全的问题
> 这部分参考《Java多线程编程实战指南（核心篇）》第三章

线程同步机制，协调线程间的数据访问和活动，保障线程安全。

需要线程同步机制来解决线程安全问题，Java引入了6种线程同步机制

1. 内部锁`synchronized` 关键字
   1. 同步代码块 
   1. 同步方法
2. 显式锁/同步锁/可重入锁 `j.u.c.Lock接口`(默认实现是`Reentrantlock`)
2. 特殊域变量 `volatile`
2. 局部变量 `ThreadLocal`
2. 阻塞队列 `LinkedBlockingQueue`
2. 原子变量 `Atomic`
## 锁
保障线程执行的原子性、可见性、有序性。
按Jvm的实现方式，分为内部锁和显示锁
内部锁：通过synchronized关键字实现
显示锁：通过j.u.c.Lock接口实现，如ReentrantLock
### 锁的一些概念
#### 锁的可重入性
可重入锁：一个线程持有一个锁的实话还能够继续成功申请该锁
> 是 同一个线程、同一个锁

#### 锁的争用与调度
锁的调度由Jvm负责，调度策略分为`公平策略`和`非公平策略`，对应的锁被称为`公平锁`和`非公平锁`
> 内部锁，属于非公平锁
> 显示锁，既支持公平锁也支持非公平锁

#### 锁的粒度
表示一个锁实例的保护的共享数据的数据量大小

### 内部锁 synchronized 
Java平台中的任何 一 个对象都有唯 一一 个与之关联的锁 。 这种锁被称为`监视器 ( Monitor ）`或者`内部锁（ Intrinsic Lock ）`。 
内部锁是一种排他锁， 它能够保障原子性 、可见 性和有序性 。
> synchronized 关键字可以加在方法和代码块：
> - 加在方法上，就是同步方法
> - 加在代码块上，就是同步块
> 
同步方法和同步块的内容就是临界区

#### 同步块
> 锁句柄是一个对象的引用（或者能 够返回对象的表达式）。 例如，锁句柄可以填写为 this 关键字（表示当前对象）。 
> 习惯上我们也直接称锁句柄为锁 。 
> 锁句柄对应的监视器就被称为相应同步块的引导锁 。 
> 相应地，我们称呼相应的同步块为该锁引导的同步块 。


> 作为锁句柄的变量通常采用 private final 修饰，
> 如：
> private final Object lock =  new Object();
> 保证锁句柄变量的值不变

```java
private final Object lock =  new Object();
synchronized(lock){
  
  	// 临界区
 		 
}
```
#### 同步方法（普通方法）
```java
public class TestClassB{
    public synchronized void methodB(){

      // 临界区

  }
}

// 这里的锁是 this 对象
// 相当于

public  class TestClassB{
    public void methodB(){
        // 锁句柄是 this 对象
		synchronized(this){
            
        	// 临界区
            
        }
  }
}

```
#### 同步静态方法
synchronized 加在静态方法上
```java
public static class TestClassC{
    public static synchronized void methodC(){

      // 临界区

  }
}

// 这里的锁是 当前类对象
// 相当于

public  class TestClassC{
    public void methodC(){
        // 锁句柄是 this 对象
		synchronized(TestClassC.class){
            
        	// 临界区
            
        }
  }
}
```
#### 内部锁的调度
> 《Java多线程编程实战指南》P89

![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1646895539657-d66ac0b5-7158-430d-b9d3-183ee4349e4b.png#clientId=u147b5ea4-a853-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=448&id=ubcfb344a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=896&originWidth=1638&originalType=binary&ratio=1&rotation=0&showTitle=false&size=945281&status=done&style=none&taskId=ud03a86fc-53bc-40db-b5c0-8236dd871e8&title=&width=819)
#### jdk对内部锁的优化
Java 1. 6/ 1. 7 对内部锁做了 一些优化， 这些优化在特定情况 下可以减少锁的开销 。 
这些优化包括锁消除（ Lock Elimination ）、锁粗化（ Lock Coarsening ） 、 偏向锁 ( Biased Lock ）和适配性锁（ Adaptive Lock ）

### 显示锁 - j.u.c.Lock接口

1. 是排他锁
1. ReentrantLock()  是 Lock接口的默认实现
1. lock.lock(); 和 lock.unlock();之间的部分就是临界区

```java
public class LockBasedSeqGenerator {
    private short sequence = -1;
    private final Lock lock = new ReentrantLock();
    public short nextSeq(){
        lock.lock();
        try {
            sequence = sequence>999 ? 0 : sequence++;
            return sequence;

        }finally {
            lock.unlock();
        }
    }
}
```
#### 显示锁的调度
ReentrantLock() 同时支持公平锁和非公平锁
他有两个构造器

- 默认使用非公平锁
- 传入参数，fair == true, 表示公平锁
> 公平锁适合于锁被持有 的时间相对长或者线程申请锁的平均间 隔时间相对长的情形 。
>  总的来说使用公平锁的开销比使用非公平锁的开销要大， 因 此显式 锁默认使用的是非公平调度策略

```java

    /**
     * Creates an instance of {@code ReentrantLock}.
     * This is equivalent to using {@code ReentrantLock(false)}.
     * 默认的是非公平锁
     */
    public ReentrantLock() {
        sync = new NonfairSync();
    }

    /**
     * Creates an instance of {@code ReentrantLock} with the
     * given fairness policy.
     *
     * @param fair {@code true} if this lock should use a fair ordering policy
     *
     *fair == true, 表示公平锁
     */
    public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
    }
```

### 显式锁的一种：读写锁 - j.u.c.ReadWriteLock
#### 读/写锁
> 对于同步在同一个锁之上的线程而言， 对共享变量仅进行读取而没有进行更新的线程被称为只读 线程 ，简称读线程。 
> 对共享变量进行更新 （包括先读取后更新 ）的线程就被称为写线程。

- 读锁对于读线程来说起到保护其访问的共享变量在其访问期间不被修改的作 用，并使多个读线程可以同 \时读取这些变量从而提高了并发性；
- 写锁保障了写线程能够以独占的方式安全地更新共享变量 。写线程对共享变量的更新对读线程是可见的 。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1646897976511-86095a4c-c0f6-4e9e-b162-9ce1e9cb4358.png#clientId=u147b5ea4-a853-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=314&id=u21347576&margin=%5Bobject%20Object%5D&name=image.png&originHeight=628&originWidth=1652&originalType=binary&ratio=1&rotation=0&showTitle=false&size=526031&status=done&style=none&taskId=u21c060ae-0529-4cee-83d1-59a893acb15&title=&width=826)
#### 读写锁的使用
```java
ReentrantReadWriteLock readWriteLock = new ReentrantReadWriteLock();
Lock readLock = readWriteLock.readLock();
Lock writeLock = readWriteLock.writeLock();
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1646898171346-f5703c9e-2749-436c-b9bb-439a41b4dee3.png#clientId=u147b5ea4-a853-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=711&id=u0fcc5d0e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1422&originWidth=884&originalType=binary&ratio=1&rotation=0&showTitle=false&size=208177&status=done&style=none&taskId=u66396509-bf46-4a1a-873a-eb74a9c930b&title=&width=442)
#### 读写锁的降级
ReentrantReadWriteLock支持锁的降级：
即一个线程持有读写锁的写锁的情况下，可以继续获得相应的读锁；
不支持锁的升级：读线程想要获得写锁，不能直接申请，要先释放读锁，再申请写锁

### 轻量级锁 volatile
> volatile
> 1. adj. 爆炸性的；不稳定的；挥发性的；反覆无常的
> 1. n. 挥发物；有翅的动物

引申开来就是 volatile 修饰的变量不稳定、易变。

1. volatile 修饰的变量不会被编译器分派到寄存器中进行操作，他的读、写操作都是直接主内存中读取的
1. 保障了可见性和有序性，原子性方面仅仅保障了volatile变量操作的原子性（没有锁的排他性）
1. volatile 开销小，因为它不会引起上下文的切换

综上，volatile也被称为轻量级锁
#### 主要作用

- 保障可见性
- 保障有序性
- 保障long/double型变量读写操作的原子性（针对32位虚拟机）
> 访问同一个 volatile 变量的线程被称为同步在这个变量之上的线程

##### volatile如何保障有序性？
##### volatile如何保障可见性？
写线程的存储屏障 + 读线程的加载屏障

Java 虚拟机通过 在 volatile 变量写操作之前插入一个释放屏障， 在 volatile 变量读操作之后插入一个获取屏障，这种成对的释放屏障和获取屏障的使用实现了 volatile 对有序性的保障 。 

类似地， Java 虚拟机在 volatile 变量写操作之后插入一个存储屏 障， 在 volatile 变量读操作之前插入一个 加载屏障，这种成对的存储屏障与 加载屏障的使用实现了 volatile 对可见性的保障。
![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1646901242114-21b63d44-2278-4e78-9e7b-2d53fa5a4ca4.png#clientId=u147b5ea4-a853-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=319&id=ua99a1491&margin=%5Bobject%20Object%5D&name=image.png&originHeight=638&originWidth=1630&originalType=binary&ratio=1&rotation=0&showTitle=false&size=319628&status=done&style=none&taskId=uf081dc6d-c4e1-488c-bc24-7f2d8bc50c0&title=&width=815)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1646901269288-8025defb-8caf-4f45-af24-fc8abfb6d9e7.png#clientId=u147b5ea4-a853-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=330&id=u395d5223&margin=%5Bobject%20Object%5D&name=image.png&originHeight=660&originWidth=1638&originalType=binary&ratio=1&rotation=0&showTitle=false&size=379514&status=done&style=none&taskId=ud85d2044-dcd0-4398-a453-90c5e49d2cf&title=&width=819)
##### 

#### volatile 应用场景
一， 使用 volatile 变量作为状态标志；二， 使用 volatile 保障可见性 ； 三 ，使用 volatile 变量替代锁 ；囚， 使用 volatile 实现简易版读写锁。

### 内存屏障
> 参考《Java多线程编程实战指南》P100

![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1646899580523-e601ce58-e99f-4957-acb6-73e6258a76c9.png#clientId=u147b5ea4-a853-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=292&id=u69f32638&margin=%5Bobject%20Object%5D&name=image.png&originHeight=584&originWidth=920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=205794&status=done&style=none&taskId=ud8424813-7ac0-42fd-a853-7eec1593fd7&title=&width=460)
# 线程通信-线程间的协作机制
## 为什么需要线程通信？
多个线程并发时，cpu默认随机切换，线程通信可以使线程按人为规律执行。
## 线程通信的实现方式
### 1.休眠唤醒机制 - Object.wait、notify/notifyAll

等待线程：执行object.wati() 方法的线程叫做_等待线程_。
在当前线程thread1下执行object1.wait()，就说thread1是object1对象的等待现场。object1.wait()可以被多个线程执行，那object1也可以有多个等待现场
通知线程：执行Object.notify()/notifyAll() 方法的线程叫做 _通知线程_
保护条件：一般将 wait、notify操作放在循环中，进入循环的判断条件就是 _保护条件，_保护条件一般是包含共享                    变量布尔表达式（这个共享变量在等待线程和通知线程之间共享）
目标动作：唤醒线程后要执行的动作
同质等待线程：

#### wait()方法
调用object.wait()方法，会以原子操作 的方式使其执行线程（ 当前线程）暂停（**进入**`**WAITING**`**状态**），并使该线程**释放**其持有的 object 对应的内部锁
```java
// 假设当前线程为t1，在t1线程执行以下代码
// 调用wait() 前要获取内部锁
synchronized (object){
    // 条件不成立时暂停线程（线程进入WAITING状态）
    while (保护条件不成立){
        object.wait();
    }
    // 能执行到这说明条件已满足（一般是另一个线程修改了保护条件，使其成立）
    // 执行目标动作
    doAction();
}
```
这是wait()方法调用的模板方法，可以看到：
> - 等待线程对保护条件的判断、 Object.wait() 的调用总是应该放在相应对象所引导的**临界区**中的一个**循环语句**之中 。
> 
> - 等待线程对保护条件的判断、 Object.wait() 的执行以及目标动作的执行必须放在 同一个对象（内部锁）所引导的**临界区**之中 。
> 
> - Object.wait（）暂停当前线程时释放的锁只是与该 wait 方法所属对象的内部锁 。 当前线程所持有的其他内部锁、显式锁并不会因此而被释放


#### notify()方法
```java
synchronized (object){
    // 更新等待线程保护条件涉及的贡献变量
    updateSharedState();
    // 唤醒等待线程
    object.notify();
}
```


#### wait/notify机制存在的问题
过早唤醒
信号丢失
欺骗性唤醒
 上线文切换问题

#### 基于 wait/notify 的 Thread.join() 方法
join()方法是Thread类的方法
作用：在t1线程内调用t2线程，会使当前线程（t1）暂停，t2线程结束后，t1线程继续执行。
join(long millis)
还有一个重载方法join(millis)，表示t2只会执行millis长时间，之后t1唤醒，t1、t2进入竞争状态

重点❗️❗️❗️

- 搞清楚当前线程是谁？
- 调用哪个线程的join()方法，哪个线程就执行
```java
     //示例：t2线程连续字母a，打印5个后，调用t1.join()
		Thread t1 = new Thread(() -> {
            String name = Thread.currentThread().getName();
            for (int i = 0; i < 10; i++) {
                Thread.sleep(1000);
                System.out.println(name+ ":" + i);
            }
        },"t1");

        Thread t2 = new Thread(() -> {
            String name = Thread.currentThread().getName();
            for (int i = 0; i < 10; i++) {
                Thread.sleep(100);
                if (i==5){
                    System.out.println("t2线程暂停");
                    t1.join();
                }
                    
                System.out.println(name+ ":" + "a");
            }

        },"t2");
```
join方法底层使用wait方法实现的
```java
    public final synchronized void join(long millis)
    throws InterruptedException {
        long base = System.currentTimeMillis();
        long now = 0;

        if (millis < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }

        if (millis == 0) {
            // isAlive()是一个native方法
            while (isAlive()) {
                wait(0);
            }
        } else {
            while (isAlive()) {
                long delay = millis - now;
                if (delay <= 0) {
                    break;
                }
                wait(delay);
                now = System.currentTimeMillis() - base;
            }
        }
    }
```

### 2. 休眠唤醒机制 - 条件变量 Condition 和 await、signle

1. Condition由ReentrantLock创建
1. await() /signal() 方法必须在显式锁（lock）内部使用
> Condition 实例也被称为**条件变量**（ Condition Variable ）或者**条件队列**( Condition Queue ）， 每个 Condition 实例内部都维护了一 个用于 存储等待线程的队列（ 等待队列）
> 设 condl 和 con但 是两个不同的 Condition 实例， 一个 线程执行 cond I .await（）会导致其被暂 停（线程生命周期状 态变更为 WAITING ）并被存入 condl 的 等待 队列 。 condtion. signal（）会使 condl 的等待队列中的 一个任 意线程被唤醒 。 cond l .signalAll（）会使 condl 的等待 队列中的所有线程被 唤醒， 而 cond2 的等待 队列中的任 何一个等待线程不受 此影响 。

**signalAll()**
会唤醒此condition上的所有线程
boolean await(long time, TimeUnit unit)
 boolean awaitUntil(Date deadline) - 返回true，表示等待尚未到达最后期限，是由其他线程的signal()唤醒的

```java
    ReentrantLock lock = new ReentrantLock();
    Condition condition = lock.newCondition();

    public void methodAwait(){
        lock.lock();
        try {
            while (保护条件不成立) {
                condition.await();
            }
            // 执行目标动作
            doAction();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void methodSignal(){
        lock.lock();
        try {
            // 更新共享变量状态
            updateState();
            // 唤醒
            condition.signal();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
```


#### 两种机制的区别
wait、notify必须在`synchronized` 修饰的代码块里执行
signal、await必须和`Lock`互斥锁/共享锁配合使用

### 3. 倒计时协调器 CountDowLatch
`CountDowLatch`类表示一定数量的线程运行结束后，唤醒线程
只有一个构造函数 `public CountDownLatch(int count)`，count表示参与的线程数
countDown()：计数方法，调用一次方法就使count减1
await()：唤醒方法，所有线程都完成（count减至0）时，唤醒执行await()的线程
> 示例：
> 需求：7个线程同时寻找7颗龙珠，都找齐后宣布找到了

```java
    static class MyThread implements Runnable{
        CountDownLatch latch;
        public MyThread(CountDownLatch latch) {
            this.latch = latch;
        }
        @Override
        public void run() {
            String name = Thread.currentThread().getName();
            System.out.println(name + "开始寻找");
            try {
                Thread.sleep(2*new Random().nextInt(1000));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(name + "找到了");

            latch.countDown();

        }
    }
    public static void main(String[] args) {
        CountDownLatch latch = new CountDownLatch(7);
        for (int i = 0; i < 7; i++) {
            int no = i+1;
            Thread t = new Thread(new MyThread(latch),"T-"+no);
            t.start();
        }
        try {
            latch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        // main线程会在这里阻塞，直到7个子线程都结束，main线程才继续执行
        System.out.println("找到所有龙珠了");
    }
```


### 4. CyclicBarrier
CyclicBarrier可以实现多个线程之间相互等待。
> 使用CyclicBarrier的多个线程被称为**参与方**
> 

> 最后一个



**如何实现？**
目标线程：

### Semaphore 信号量
用来控制访问特殊的**_虚拟资源，_**对虚拟资源的访问进行流量控制。

Semaphoe.acquire()
成功获得配额后，立即返回。如果当前的可用配额不足， 那么 Semaphore.acquire() 会使其执行线程暂停 。 Semaphore 内部会维护一个**等待队列**用于存储这些被暂停的线程。使配额减1
Semaphore.release()
会 使 当前可用配额增加1 ，井唤醒相应 Semaphore 实例 的等待队列中的一个任意等待线程。release()方法总是在finnaly中调用，保证配额被释放。

> 配额调度 默认是非公平策略。



### 线程中断机制
> **中断 ( Interrupt ）**可被看作由一个线程（**发起线程 Originator** ） 发送给另外一个线程（**目标线程 Target **） 的一种指示（ Indication ），该指示用于表示发起线程希望目标线程停止其正在执行 的操作 。


- Thread. currentThread(). islnterrupted()

目标线程获取该线程的中断标记值

- Thread. interrupted()

目标线程获取并重置（清空）中断标记值为false

- interrupt()

目标线程的中断标记为true

> 目标线程检查中断标记后所执行的操作， 被称为目标线程对中断的响应， 简称**中断响应**



interrupt()
将**指定线程**的中断标志置为true，表示此线程被中断。

isInterrupted()
获取**指定线程**的中断标志置位，true或false，不做其他处理

interrupted()：
此方法是静态方法，由Thread调用，判断的是当前正在执行的线程的中断标志（判断并清空）
如果线程是中断的，则返回true；
并且清除中断标记（即，标记置为false）

> 注意：
> 「**指定线程**」是指 `Thread t1 = new Thread()；`，t1这样的线程实例
> t1.interrupt() 表示将t1线程中断标志置为 true
> t1.isInterrupted() 获取t1线程的中断标志
> 
> 「interrupted()方法是静态方法，由Thread调用
> Thread.interrupted()  Thread是指执行此行代码的线程

源码中可以看到 isInterrupted 和 interrupted，调用的都是 native方法 isInterrupted(boolean ClearInterrupted)
ClearInterrupted 表示是否清除线程中断标志位
```java
    public static boolean interrupted() {
        return currentThread().isInterrupted(true);
    }
  
    public boolean isInterrupted() {
        return isInterrupted(false);
    }

    private native boolean isInterrupted(boolean ClearInterrupted);
```
```java
 /**
     * Tests whether the current thread has been interrupted.  The
     * <i>interrupted status</i> of the thread is cleared by this method.  In
     * other words, if this method were to be called twice in succession, the
     * second call would return false (unless the current thread were
     * interrupted again, after the first call had cleared its interrupted
     * status and before the second call had examined it).
     *
     * <p>A thread interruption ignored because a thread was not alive
     * at the time of the interrupt will be reflected by this method
     * returning false.
     *
     * @return  <code>true</code> if the current thread has been interrupted;
     *          <code>false</code> otherwise.
     * @see #isInterrupted()
     * @revised 6.0
     */
    public static boolean interrupted() {
        return currentThread().isInterrupted(true);
    }

    /**
     * Tests whether this thread has been interrupted.  The <i>interrupted
     * status</i> of the thread is unaffected by this method.
     *
     * <p>A thread interruption ignored because a thread was not alive
     * at the time of the interrupt will be reflected by this method
     * returning false.
     *
     * @return  <code>true</code> if this thread has been interrupted;
     *          <code>false</code> otherwise.
     * @see     #interrupted()
     * @revised 6.0
     */
    public boolean isInterrupted() {
        return isInterrupted(false);
    }

    /**
     * Tests if some Thread has been interrupted.  The interrupted state
     * is reset or not based on the value of ClearInterrupted that is
     * passed.
     */
    private native boolean isInterrupted(boolean ClearInterrupted);

```
```java
/**
* Interrupts this thread.
*
* <p> Unless the current thread is interrupting itself, which is
* always permitted, the {@link #checkAccess() checkAccess} method
* of this thread is invoked, which may cause a {@link
* SecurityException} to be thrown.
*
* <p> If this thread is blocked in an invocation of the {@link
* Object#wait() wait()}, {@link Object#wait(long) wait(long)}, or {@link
* Object#wait(long, int) wait(long, int)} methods of the {@link Object}
* class, or of the {@link #join()}, {@link #join(long)}, {@link
* #join(long, int)}, {@link #sleep(long)}, or {@link #sleep(long, int)},
* methods of this class, then its interrupt status will be cleared and it
* will receive an {@link InterruptedException}.
*
* <p> If this thread is blocked in an I/O operation upon an {@link
* java.nio.channels.InterruptibleChannel InterruptibleChannel}
* then the channel will be closed, the thread's interrupt
* status will be set, and the thread will receive a {@link
* java.nio.channels.ClosedByInterruptException}.
*
* <p> If this thread is blocked in a {@link java.nio.channels.Selector}
* then the thread's interrupt status will be set and it will return
* immediately from the selection operation, possibly with a non-zero
* value, just as if the selector's {@link
* java.nio.channels.Selector#wakeup wakeup} method were invoked.
*
* <p> If none of the previous conditions hold then this thread's interrupt
* status will be set. </p>
*
* <p> Interrupting a thread that is not alive need not have any effect.
*
* @throws  SecurityException
*          if the current thread cannot modify this thread
*
* @revised 6.0
* @spec JSR-51
*/
public void interrupt() {
    if (this != Thread.currentThread())
        checkAccess();
    
    synchronized (blockerLock) {
        Interruptible b = blocker;
        if (b != null) {
            interrupt0();           // Just to set the interrupt flag
            b.interrupt(this);
            return;
        }
    }
    interrupt0();
    }
```

### 线程停止
#### thread.stop()方法 已作废
存在的问题：

1. 暴力停止线程，使得线程有可能使一些清理性的工作得不到完成
1. 对锁定的对象进行了解锁，导致数据得不到同步的处理，出现数据不一致的问题。

#### 正确停止线程的方式
应该使用 中断标志+return，使线程判断中断标志后自然退出
```java
/**
 * 线程每隔1秒打印一个数字，直到指定时间，主动中断自己，经过判断后，退出循环，线程结束;
 * 注意interrupt()方法 ，如果在线程sleep的时候调用
 * 将产生 InterruptedException: sleep interrupted 异常
 */
class MyThread1 extends Thread{
        @SneakyThrows
        @Override
        public void run() {
            Thread thread = Thread.currentThread();
            String threadName = thread.getName();
            int cnt = 1;
            while (true){
                if (thread.isInterrupted()){
                    System.out.println("线程中断，停止运行");
                    break;
                }
                System.out.println(threadName + " - " + "第"+ cnt++ +"秒");
                Thread.sleep(1000);
                if (cnt == 5){
                    this.interrupt();
                }
            }
        }
    }
```
```java
    public static void main(String[] args) throws InterruptedException {
        MyThread1 t1 = new MyThread1();
        t1.start();
        Thread.sleep(2000);
        System.out.println("2秒到，线程被中断");
        t1.interrupt();
    }

    /**
     * 线程不停打印数字，主线程在2秒后将其中断
     */
    static class MyThread1 extends Thread{
        @SneakyThrows
        @Override
        public void run() {
            Thread thread = Thread.currentThread();
            String threadName = thread.getName();
            int cnt = 0;
            while (true){
                if (thread.isInterrupted()){
                    System.out.println("线程中断，停止运行");
                    break;
                }
                System.out.println(threadName + " - " + cnt++);
            }
        }
    }
```
## 线程通信常见问题
### 1. wait和sleep方法的区别
wait会释放锁，供其他线程使用资源；sleep不释放锁，仍然占用资源。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/111742/1615563255509-a348214d-d30a-4571-829b-8a0c6385c8dc.png#crop=0&crop=0&crop=1&crop=1&height=368&id=PicaW&margin=%5Bobject%20Object%5D&name=image.png&originHeight=736&originWidth=2236&originalType=binary&ratio=1&rotation=0&showTitle=false&size=688810&status=done&style=none&title=&width=1118)
sleep
interrupte
join
# 线程池
## 创建线程时存在的问题

- 创建、销毁线程耗时、耗资源
- 多个线程切换耗时、耗资源

引入线程池可解决，线程池内创建了一批线程，可供调用、重复使用
## ThreadExecutorPool
```java
    /**
     * Creates a new {@code ThreadPoolExecutor} with the given initial
     * parameters.
     *
     * @param corePoolSize the number of threads to keep in the pool, even
     *        if they are idle, unless {@code allowCoreThreadTimeOut} is set
     * @param maximumPoolSize the maximum number of threads to allow in the
     *        pool
     * @param keepAliveTime when the number of threads is greater than
     *        the core, this is the maximum time that excess idle threads
     *        will wait for new tasks before terminating.
     * @param unit the time unit for the {@code keepAliveTime} argument
     * @param workQueue the queue to use for holding tasks before they are
     *        executed.  This queue will hold only the {@code Runnable}
     *        tasks submitted by the {@code execute} method.
     * @param threadFactory the factory to use when the executor
     *        creates a new thread
     * @param handler the handler to use when execution is blocked
     *        because the thread bounds and queue capacities are reached
     * @throws IllegalArgumentException if one of the following holds:<br>
     *         {@code corePoolSize < 0}<br>
     *         {@code keepAliveTime < 0}<br>
     *         {@code maximumPoolSize <= 0}<br>
     *         {@code maximumPoolSize < corePoolSize}
     * @throws NullPointerException if {@code workQueue}
     *         or {@code threadFactory} or {@code handler} is null
     */
	/**
     * corePoolSize: 核心线程数
	 * maximumPoolSize：线程池内最大线程数
	 * keepAliveTime：线程池中空闲（Idle）线程的最大存活时间
	 * unit：keepAliveTime的单位
	 * workQueue：阻塞队列，可以用了链表实现，也可以用队列实现
	 * threadFactory：指定创建线程的工厂
	 * handler：线程池满且阻塞队列也满时，拒绝添加新任务时的拒绝策略
     */
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler)
```
### 主要参数
#### 核心线程数 corePoolSize
线程池中长期工作的线程数
#### 最大线程数 maximumPoolSize
线程池的大小：

- 当前线程池大小（正在运行的线程）
- 核心线程池大小 (设置的一个合理的大小)
- 最大线程池大小（线程池中允许存在的工作线程的数量上限）
#### 存活时间 keepAliveTime
#### 时间单位 unit

#### 阻塞队列 workQueue
用来传输和保存提交的任务，实现了BlockingQueue接口的实现类都可以。
三种排队策略
> 1. 直接传递 
> 
工作队列的一个很好的默认选择是 SynchronousQueue，它将任务交给线程而不用其他方式持有它们。一个新的任务尝试排队时，如果没有可供使用的线程运行它时将会创建一个新的线程。该策略避免了锁定处理可能具有内部依赖关系的请求集，直接传递通常需要无界的最大线程池来避免新的任务提交。这反过来又承认了当命令的平均到达速度快于它们的处理速度时，线程无限增长的可能性。
> 2. 无界队列 
> 
无界队列是一个没有预定义容量的队列，使用无界队列例如LinkedBlockingQueue将导致新任务一直在等待，当核心线程数的线程处于工作状态时。因此，不会有超过核心线程数的线程被创建，也就是说最大线程数是不起作用的。当任务之间互相独立，互不影响的时候这个选择可能是挺合适的。例如，在web服务器中，这种队列在消除短暂的高并发方面很有作用，它允许无界队列增长的平均速度比处理的平均速度快。
> 3. 有界队列 无界队列例如ArrayBlockingQueue，它能在有限的最大线程数内防止资源耗尽，但是它也更难调整和控制。 队列的大小和最大线程数可以互相替换：使用更大的队列数量和小的线程池数量能够最小化CPU的使用、系统资源和上下文切换的开销，但也人为的导致了低吞吐量。如果一个任务频繁的阻塞，例如频繁I/O，系统更多的时间是在频繁的调度而不是运行任务。使用小的队列通常需要大的线程池数量，这会让CPU更能充分利用，但是也会遇到不可接受的调度开销，也会降低吞吐量。

#### 创建线程的工厂
ThreadFactory用来创建线程。如果没有指定ThreadFactory的话，默认会使用Executors#defaultThreadFactory 来创建线程
#### 
#### 拒绝策略handler
在调用execute(Runnable)提交任务时，在Executor已经关闭或者有界队列的最大线程数和队列满的情况下任务会被拒绝。
![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1647328997013-365b8667-afd4-4d69-8c2f-a6e2ff69b161.png#clientId=u9c4e27f9-df03-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=580&id=u6ffe4e83&margin=%5Bobject%20Object%5D&name=image.png&originHeight=580&originWidth=1774&originalType=binary&ratio=1&rotation=0&showTitle=false&size=572243&status=done&style=none&taskId=u11699829-85c1-4309-b7aa-6cc91e7995c&title=&width=1774)

### 线程池如何工作
#### 向线程池提交任务
```java
给线程池提交一个任务
1. 如果 工作线程数<核心线程数,则创建一个新线程执行此任务
2. 如果 已到达核心线程数，则将任务加入阻塞队列
3. 如果 则阻塞队列已满，则继续创建新线程，直到达到最大线程数
4. 如果 达到最大线程数，最执行拒绝策略
```
```java
    /**
     * Executes the given task sometime in the future.  The task
     * may execute in a new thread or in an existing pooled thread.
     *
     * If the task cannot be submitted for execution, either because this
     * executor has been shutdown or because its capacity has been reached,
     * the task is handled by the current {@code RejectedExecutionHandler}.
     *
     * @param command the task to execute
     * @throws RejectedExecutionException at discretion of
     *         {@code RejectedExecutionHandler}, if the task
     *         cannot be accepted for execution
     * @throws NullPointerException if {@code command} is null
     */
    public void execute(Runnable command) {
        if (command == null)
            throw new NullPointerException();
        /*
         * Proceed in 3 steps:
         *
         * 1. If fewer than corePoolSize threads are running, try to
         * start a new thread with the given command as its first
         * task.  The call to addWorker atomically checks runState and
         * workerCount, and so prevents false alarms that would add
         * threads when it shouldn't, by returning false.
         *
         * 2. If a task can be successfully queued, then we still need
         * to double-check whether we should have added a thread
         * (because existing ones died since last checking) or that
         * the pool shut down since entry into this method. So we
         * recheck state and if necessary roll back the enqueuing if
         * stopped, or start a new thread if there are none.
         *
         * 3. If we cannot queue task, then we try to add a new
         * thread.  If it fails, we know we are shut down or saturated
         * and so reject the task.
         */
        int c = ctl.get();
        if (workerCountOf(c) < corePoolSize) {
            if (addWorker(command, true))
                return;
            c = ctl.get();
        }
        if (isRunning(c) && workQueue.offer(command)) {
            int recheck = ctl.get();
            if (! isRunning(recheck) && remove(command))
                reject(command);
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        }
        else if (!addWorker(command, false))
            reject(command);
    }

```


### 使用线程池
```java
// 创建线程池
ExecutorService fixedThreadPool = Executors.newFixedThreadPool(1);
// 执行线程池execut()方法，传入Runnable接口实现
fixedThreadPool.execute(new Runnable() {
        @Override
        public void run() {
            System.out.println();
        }
	});
```
## Executor 框架
### Executor 接口
### Executors 工具类
### Completion Service
批量执行异步任务

# Java集合的并发安全问题

j.u.c中的并发容器
![image.png](https://cdn.nlark.com/yuque/0/2021/png/111742/1615617954784-ed53a349-1a7a-4713-80e3-04319bfca5dd.png#crop=0&crop=0&crop=1&crop=1&height=653&id=aurJb&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1306&originWidth=1522&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1662272&status=done&style=none&title=&width=761)
# 多线程编程的硬件基础
## 高速缓存
处理器处理能力远胜于主内存（DRAM）访问速率，了弥补处理器 与主 内存处理能力之 间的鸿沟， 硬件设计者在主 内存和处理器之 间引入 了高速缓存（ Cache )。
高速缓存分为多个层次，
距离处理器越近的高速缓存，存取速率越快
![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1647583752544-d2a52ac9-3cf7-4483-abd1-f4470dacefd1.png#clientId=uc345e735-129b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=804&id=u4ddad0c8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=804&originWidth=1660&originalType=binary&ratio=1&rotation=0&showTitle=false&size=358082&status=done&style=none&taskId=uf70928f6-c5c6-4406-aeb9-ab423533128&title=&width=1660)
## 缓存一致性协议
多个线程访问一个共享变量时，这些线程的执行处理器会在各自的高速缓存中各自保留一份共享变量的副本，为确保一个处理处理器更新共享变量值后，其他处理器共享变量副本的值也能得到更新，就需要缓存一致性协议。
> MESI(Modified-.Exclusive-Shared-Invalid)协议是一种广为使用的缓存一致性协议


## 写缓冲器与无效化队列
## 内存屏障
## Java 内存模型 JMM
> 缓存一致性协议确保了一个处理器对某个内存地址进行的写 操作的结果**最终**能够被 其他处理器所读取。

即，缓存一致性协议不能保证其他处理器立刻能读取到最新值，只能保障最终的结果一致。因此还需要解决
这个问题：
> 一个处理器对共享变量所做的更新在什么时候或者说什么情况下才能够被其他处理器 所读取，
>  即，可见性问题。 
> 可见性问题又衍生出一个新的问题：一个处理器先后更新多个共享变量的情况下， 其他处理器是以何种顺序读取到这些更新的， 即，有序性问题。

因此，需要引入**内存一致性模型**（Memory Consistency Model），也称为**内存模型**（Memory Model）。
> Java 作为一个跨平台（跨操作系统和硬件）的语言， 为了屏蔽不同处理器的内存模型 ，以便 Java 应用开发人员不必根据不同的处理器编写不同的代码， 它必须定义自己
> 的内存模型， 这个模型就被称为 Java 内存模型。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1647583788172-54343d0d-338c-47af-8fcc-e1f3875530ae.png#clientId=uc345e735-129b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=804&id=u1e6b7265&margin=%5Bobject%20Object%5D&name=image.png&originHeight=804&originWidth=1644&originalType=binary&ratio=1&rotation=0&showTitle=false&size=346429&status=done&style=none&taskId=u776ba33d-bc71-4f27-81ce-21ff0d943b1&title=&width=1644)
### 什么是 Java内存模型 JMM
Java内存模型是Java语言规范的一部分，定了final、synchronized、volatile关键字的行为，并确保正确同步的Java程序能正确运行在不同架构的处理器上。

#### JMM结构
![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1648796644184-f10bb943-96fc-4270-bd15-ff8eab581cf2.png#clientId=u188f9ee9-dd1c-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=1446&id=u2c1b2027&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1446&originWidth=1664&originalType=binary&ratio=1&rotation=0&showTitle=false&size=537208&status=done&style=none&taskId=u38806dfe-b552-4d1f-b1b7-217ec73d92d&title=&width=1664)
#### 内存指令
![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1648796711835-1de4b11e-ae6c-432e-b5aa-9a8d2e393b9e.png#clientId=u188f9ee9-dd1c-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=1038&id=u243bdfc8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1038&originWidth=1390&originalType=binary&ratio=1&rotation=0&showTitle=false&size=558104&status=done&style=none&taskId=ufa3f1db2-e7e8-4df7-a28f-87f90d83636&title=&width=1390)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1648796729449-1bc0a7a0-fe52-4873-89dc-d0cc8a9cdf50.png#clientId=u188f9ee9-dd1c-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=1438&id=uef47ac9f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1438&originWidth=2040&originalType=binary&ratio=1&rotation=0&showTitle=false&size=737464&status=done&style=none&taskId=ub14797f3-28bf-4a61-bc19-0cf398454cc&title=&width=2040)
### happens-before 规则
Java内存模型定义了一些动作，包括：

- 变量的读、写
- 锁的申请lock与释放unlock
- 线程的启动start与加入join等

`happens-before` 规则就是规定了这些动作的执行先后关系。
JMM还规定了什么情况写两个动作具有happens-before关系
#### 程序顺序规则
同一个线程中，每一个动作都 happens-before 此动作后的每一个动作
> 这句话看起来像多此一举，但其实不是。因为 happens-before关系与时间上的先后关系并无必然联系。
> 此规则下，这些动作看起来像是完全依照程序顺序执行的，但是其实只要动作间不存在数据依赖，是有可能发生重排序的

#### 内部锁规则
内部锁的unlock happens-before **后续**每一个对该锁的lock
重点：

- 申请和释放的必须是同一个锁
- **“后续”**指的是时间上的先后关系

总结就是：一个线程释放锁后另一个线程再来申请这个锁的情况下，这两个线程的“释放”和“申请”存在happens-before关系
> !!!!
> 程序锁规则和内部锁规则一起确保了锁对可见性和有序性的保障

#### volatile变量规则
对同一个volatile变量的写操作 happens-before 后续每一个针对改变量的读操作
注意：

- 必须是针对同一个volatile变量
- 写、读操作具有时间上的先后关系
####  线程启动规则
调用一个线程的 start 方法 happens-before 被**启动的这个钱程**中的任何一个动作

#### 线程终止规则
一个线程中的任何一个动作都 happens-before 该线程的 join 方法的执行线程在 join 方法返回之后所执行的任意 一个动作 。


以上五个规则是Java内存模型定义的happens-before规则，同时Java类库也定义了一些规则：
例如： 
对于任意的 CountDownLatch 实例 countDownLatch ，一个线程在 countDownLatch.countDown（） 调用前所执行的所有动作与另 外 一 个线程在 countDownLatch.await（）调用 成功返回之后所执行的所有动 作之间存在 happen-before 关系


# 


## Java对象的内存布局
一般以HotSpot虚拟机为例。
### 对象头
Java对象头包括三部分：

- Mark Word
- Klass Pointer
- 对其填充
#### Mark Word
> 默认存储对象的HashCode，分代年龄和锁标志位信息。这些信息都是与对象自身定义无关的数据，所以Mark Word被设计成一个非固定的数据结构以便在极小的空间内存存储尽量多的数据。它会根据对象的状态复用自己的存储空间，也就是说在运行期间Mark Word里存储的数据会随着锁标志位的变化而变化。

| **锁状态** | **存储内容** | **标志位** |
| --- | --- | --- |
| 无锁 | 对象的hashCode、对象分代年龄、是否是偏向锁（0） | 01 |
| 轻量级 | 指向栈中锁记录的指针 | 00 |
| 重量级 | 指向互斥量（重量级锁）的指针 | 10 |
| GC标记 | （空） | 11 |
| 偏向锁 | 偏向线程ID、偏向时间戳、对象分代年龄、是否是偏向锁（1） | 01 |


![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1647590726511-7b8fe59a-9d54-4b00-8e5f-840e25fa1f20.png#clientId=u07555b4a-0a9a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=704&id=u23ce862c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=704&originWidth=1696&originalType=binary&ratio=1&rotation=0&showTitle=false&size=243087&status=done&style=none&taskId=ue476765c-7968-4d3e-a0be-7895aa23b42&title=&width=1696)


# 多线程控制类
## ThreadLoacl
> 各个线程创建各自的实例， 一个实例只能被一个线程访问的对象
> 就被称为线程特有对象（ TSO, Thread Specific Object ），相对应的线程就被称为该 线程特有对象的持有线程

多个线程使用同一个 ThreadLocal<T＞实例所访问到的对象是类型 T 的不同实例，即这些 钱程各自的线程特有对象实例

ThreadLocal<T>，并不是线程持有对象，而是T

![image.png](https://cdn.nlark.com/yuque/0/2022/png/111742/1647244487213-76d95364-e219-4212-9fac-f8a5570535e4.png#clientId=u9df361e2-8026-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=944&id=ggTLN&margin=%5Bobject%20Object%5D&name=image.png&originHeight=944&originWidth=1660&originalType=binary&ratio=1&rotation=0&showTitle=false&size=563666&status=done&style=none&taskId=u7e028e92-4578-4724-9929-0a7e6458f1b&title=&width=1660)
> ThreadLoc al 实例为每 个访问它 的线程（ 即当前线 程）都关 联了 一个该线 程的线程 特有对象 。 换句话说 ， 每个 ThreadLocal<T＞实 例都有一个 （且只有一 个） 当前线程 的特有对 象 T 的实例与 之关联， 这种关联关系就像 一个变量总是有一 个（且只 有 一个）值 与之关联 一样（尽 管变量的 值是可以 改变的） ， 因此 ThreadLocal 实例也被 称为线程局部变量（ Thread-local Variable）。


**实现方式：**
**ThreadLocal里都存有一个ThreadLocalMap，里面存有键值对**

- 键key为ThreadLocal实例
- 值value为对应线程下该变量的引用

![image.png](https://cdn.nlark.com/yuque/0/2021/png/111742/1615604646308-5977372a-61ef-4b6f-92f4-3cf93510f0ff.png#crop=0&crop=0&crop=1&crop=1&height=416&id=WpBr6&margin=%5Bobject%20Object%5D&name=image.png&originHeight=832&originWidth=2036&originalType=binary&ratio=1&rotation=0&showTitle=false&size=618912&status=done&style=none&title=&width=1018)
**可能导致的问题**

- **退化与数据错乱**
- **内存泄漏、为内存泄漏**
> 内存泄漏 （ Memory Leak ）
> 指由于对象永远无法被垃圾回收导致其占用的 Java 虚拟机内存无法被释放。 持续的内存泄漏会导致 Java 虚拟机可用内存逐渐减少， 并最终 可能导致 Java 虚拟机内存溢出（ Out of Memory ），直到 Java 虚拟机宕机。
> 
> 伪内存泄漏 （ Memory Pseudo-leak ）
> 类似于内存泄漏 。 所不同的是， 伪内存泄漏中对象所占用的内存在其不再被使用后的相当长时间仍然无法被 回收， 甚至可能永远无法被回收 。 也就是说， 伪内存泄漏中对象占用的内存空间可能会被回收， 也可能永远无法被回收（此时，就变成了内存泄漏｝。


## 原子类
j.u.c包的原子类主要用于**原子性**地更新数据。
主要有四个类型：

1. 基本类型： 
1. 数组类型：
1. 引用类型：
1. 属性类型：

### 原子类实现原理
#### CAS
```java
// 定义一个原子类
private static AtomicInteger n ;

// 执行 n++ 操作
n.getAndIncrement();

// getAndIncrement()方法源码分析

public final int getAndIncrement() {
	return unsafe.getAndAddInt(this, valueOffset, 1);
}

/**
 * unsafe类中的源码
 * var1 调用的对象
 * var2 地址偏移量
 * var4 增加步长（1）
 */
public final int getAndAddInt(Object var1, long var2, int var4) {
	int var5;
    /**
     * var5!!!! 是期望的值 
     *var5 = this.getIntVolatile(var1, var2) 通过对象和地址偏移量直接去读取堆中的值
     * 
     * 然后在进行CAS操作！
     * compareAndSwapInt:
     * 1. 比较当前值（var1）和期望值（v5）
     * 2. 相同，说明没有线程更改过，当前值（var1）= 期望值（var5）+ 递增间隔（var4）返回true
     * 3. 不相同，说明有其他线程更改过，当前值（var1）= 期望值（var5），返回false
     */
	do {
		var5 = this.getIntVolatile(var1, var2);
        // CAS!操作
	} while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
    
  return var5;
}

```
















# 参考

1. Java锁详解 https://tech.meituan.com/2018/11/15/java-lock.html
2. 《Java多线程编程实战指南（核心篇）》
2. 【Java内存模型这块彻底玩儿明白了】by B站up 寒食君


