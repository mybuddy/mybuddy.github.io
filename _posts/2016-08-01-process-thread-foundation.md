---
layout: post
title:  "进程、线程基础"
date:   2016-08-01 20:32:01 +0800
categories: 并发编程
tags: 并发编程 java
---

* content
{:toc}

# 进程、线程基础

## 1. 基本概念

进程：每个进程对应一个程序，**对应一定的内存地址空间**，且只能使用其自己的内存空间，进程间互不干扰。进程保存了程序每个时刻的运行状态，进程暂停时会保存其当前的状态（进程标识、进程使用的资源等），下次切换回来时，根据之前保存的状态进行恢复，然后继续执行。

线程：一个进程包含多个线程，但这些**线程共享进程占用的资源和地址空间**。

**进程**是操作系统进行**资源分配**的基本单位，而**线程**是操作系统进行**调度**的基本单位。

进程使得操作系统的并发性成为可能，而线程使得进程内部的并发成为可能。

## 2. Java中的进程、线程

Java中，**一个应用程序对应一个JVM实例**（亦称JVM进程），名字一般默认为java.exe或javaw.exe。

Java采用**单线程编程模型**，即程序中没有主动创建线程的话，默认只会创建一个线程，称为主线程，但不代表JVM中只有一个线程，JVM实例在创建的时候，会同时创建很多其他线程（如垃圾收集器线程）。

### Java中创建线程

2种方式，1. 继承Thread类；2. 实现Runnable接口。

### Java中创建进程

2种方式：

1. 通过ProcessBuilder.start方法创建；
ProcessBuilder.start方法中调用了ProcessImpl.start方法，ProcessImpl类本身继承了Process类，而在ProcessImpl.start方法中new了一个ProcessImpl实例并返回。

2. 通过Runtime.exec方法创建。
Runtime.exec方法中调用了ProcessBuilder.start方法。**Runtime，即运行时，表示当前进程所在的虚拟机实例。**

用法：

1. 通过ProcessBuilder.start方法

    ```java
    ProcessBuilder pb = new ProcessBuilder("cmd","/c","ipconfig/all");
    Process process = pb.start();
    Scanner scanner = new Scanner(process.getInputStream());
        
    while(scanner.hasNextLine()){
      System.out.println(scanner.nextLine());
    }
    scanner.close();
    ```

2. 通过Runtime.exec方法

    ```java
    String cmd = "cmd "+"/c "+"ipconfig/all";
    Process process = Runtime.getRuntime().exec(cmd); // 单例方式获取Runtime实例
    Scanner scanner = new Scanner(process.getInputStream());
        
    while(scanner.hasNextLine()){
      System.out.println(scanner.nextLine());
    }
    scanner.close();
    ```

## 3. Thread类的使用

线程的状态及对应的方法
![线程的状态及对应的方法](http://images.cnitblog.com/blog/288799/201409/061046391107893.jpg)

### 线程的状态

**线程包括5个状态：创建（new）、就绪（runnable）、运行（running）、阻塞（blocked）[time waiting、waiting]、消亡（dead）。**

线程的创建状态，new一个线程。

**线程创建后，不会立即进入就绪状态**，因为线程运行需要一些条件（如内存资源，线程私有的程序计数器、Java栈、本地方法栈），只有条件满足了才进入就绪状态。调用start方法会为线程分配需要的资源。

线程进入就绪状态后，不代表立即能获取CPU执行时间，当得到CPU执行时间后，才进入运行状态。

线程运行过程中，可能有多个原因导致线程无法继续运行，如sleep()、join()/wait()、同步块阻塞。对应time waiting、waiting、blocked状态。

### 上下文切换

线程的上下文切换实际上就是**存储和恢复CPU状态的过程**，它使得线程能够从中断点恢复执行。

### Thread类中的方法

线程优先级：最大10，最小1，默认5。

线程运行相关方法：

1. start。  
启动一个线程，为线程分配需要的资源。
2. run。
3. sleep。  
交出CPU，进入**阻塞状态，不释放锁**。
4. yield。  
交出CPU，重回**就绪状态，不释放锁**。
5. join。  
等待thread执行完毕或等待指定时间，内部使用wait实现。交出CPU，进入**阻塞状态，释放锁**。
6. interrupt。  
**中断处于阻塞状态的线程**（使其抛出中断异常）。

线程属性相关方法：

1. getId。
2. getName、setName。
3. getPriority、setPriority。
4. setDaemon、isDaemon。
**守护线程依赖于创建它的线程，而用户线程不依赖。**创建守护线程的线程运行完毕后，守护线程也会随之消亡，而用户线程会一直运行到其结束。

获取当前线程的静态方法Thread.currentThread()。

## 参考

[Java多线程基础：进程和线程之由来](http://www.cnblogs.com/dolphin0520/p/3910667.html)
[Java并发编程：如何创建线程？](http://www.cnblogs.com/dolphin0520/p/3913517.html)
[Java并发编程：Thread类的使用](http://www.cnblogs.com/dolphin0520/p/3920357.html)


