---
layout: post
title: java concurrency
tags: java concurrency
categories: java concurrency

---

# 基础概念

## 什么是进程？什么是线程？

**进程**是一个可执行的程序，在windows下打开任务管理器，可以看到很多进程正在运行。

![java-concurrency-exe](..\static\img\java-concurrency-exe.png)



**线程**，比如上面图片里的wechat进程，打开以后，你可以一边打字聊天，一边跟另一个人语音聊天。

## 进程和线程的区别

1. 一个进程里包含多个线程。所以线程又被称为轻量级进程
2. 在java领域，线程之间想要通信，可以使用wait,notify,notifyAll等方法进行通信，但是进程之间要想实现通信，需要第三方软件的支持。
3. 进程都有自己独立的主内存，但是线程可以共享所在进程的主内存，并且拥有独立的堆栈空间。

## 并发和并行的区别

并发：多个线程操作同一个资源，交替执行的过程

并行：多个线程同时执行，只有在多核cpu下才能实现

## 创建线程的几种方式

1. 继承Thread类，重写run方法
2. 实现Runnable接口，重写run方法
3. 实现Callable接口，重写call方法
4. 线程池

## java线程的几种状态

1. NEW

2. RUNNABLE
3. BLOCKED
4. WAITING
5. TIMED_WAITING
6. TERMINATED

```java
public enum State {
    NEW,
    RUNNABLE,
    BLOCKED,
    WAITING,
    TIMED_WAITING,
    TERMINATED;
}
```

## java线程之间的状态转换

* runnable与blocked
  * Synchronized，lock锁，当其中一个线程持有锁，其余线程只能等待。这些等待中的线程的状态是有runnable->blocked。当等待中的线程获取到锁，就从blocked->runnable
* runnable->waiting
  * 获取锁，调用无参数的wait方法
  * 调用join。线程a调用a.join的时候，执行这条语句的线程会等待线程a先执行完，在执行。等待中的线程，其状态就是runnable-wait。当线程a执行完，等待它的线程的状态wait->runnable
  * locksupport.park() locksupport.unpark()
* runnable->timed-waiting
  * 调用有参wait
  * 调用有参join
  * 有参parkutil,parknanos
* new->runnable
  * 刚刚创建出来的线程对象就是new状态
  * 创建出来的线程调用start()方法，new->runnable
* runnable->terminated
  * 当线程执行完，会自动转换为terminated状态
  * 当线程执行中抛出异常，也会导致线程终止
  * 我们调用api手动终止线程

## wait和sleep的区别

1. 类不同，wait是object类的实例方法，sleep是thread类的静态方法

2. wait调用的时候会释放锁，但是sleep调用后会阻塞，不会释放锁

3. wait一般与notifyAll配合使用，且必须写在同步方法或者同步代码块中，完成线程之间的通信，但是sleep可以单独使用

4. sleep方法需要显示的捕获异常

## Synchronized和lock

1. synchronized隐式锁，是java提供的语法糖，自动完成加锁解锁操作，lock需要显示的加锁，解锁

2. synchronized默认是非公平锁，但是lock可以通过参数实现公平锁。

3. synchronized提供更加精细化的操作，比如尝试获取锁，超时机制，中断机制等等

4. 实现线程之间的通信，synchronized需要与wait,notifyAll配合使用。但是lock可以操作condition，实现更加精细化的通信。

## start和run

* 如果在main方法里，创建了一个java线程对象t1.调用t1.start()方法，则会创建一个线程去执行线程t1对应的run方法。

* 如果在main方法里，创建了一个java线程对象t1，直接调用t1.run()。则不会创建新的线程，run()是在main线程里执行的。

<font style='color:red '>一个java进程里至少包含两个线程，一个main线程，一个gc线程</font>

## 线程安全

* cpu缓存引发可见性问题
  * Java内存模型可以解决可见性问题

* 线程切换引发原子性问题
  * 互斥锁
  * 自旋锁
  * 原子类

* 编译优化引发有序性问题
  * volatile

