---
title: Java基础
categories:
- JAVA
tags: 
- thread

---



# JAVA

[TOC]



## 线程

### **volatile**

> JAVA允许线程访问共享变量，为确保变量能被准确和一致地更新，线程应该确保通过排他锁单独获得这个变量

***CPU术语定义***

|    术语    |          英文          | 描述                                                         |
| :--------: | :--------------------: | :----------------------------------------------------------- |
|  内存屏障  |    memery barriers     | 一组处理器指令，用于实现对内存操作的顺序限制。               |
|   缓冲行   |       cache line       | cpu高速缓存中可以分配的最小处理单元，<br />填写缓存行时会加载整个缓存行 |
|  原子操作  |   atomic operations    | 不可中断的一个或一系列操作                                   |
| 缓存行填充 |    Cache line fill     | 当处理器识别到从内存中读取操作数是可缓存的，<br />处理器读取整个高速缓存行到适当的缓存（L1,L2,L3或所有） |
|  缓存命中  |       cache hit        |                                                              |
|   写命中   |       write hit        |                                                              |
|   写缺失   | write misses the cache |                                                              |

***实现原则***

> 1.Lock前缀指令会引起处理器缓存回写到内存

​		

> 2.一个处理器的缓存回写到内存会导致其他处理器的缓存无效

### **synchronized**

> JVM基于进入和退出Monitor对象来实现方法同步和代码同步，适用monitorenter和monitorexit指令实现。任何对象都有一个monitor与之关联，当且一个monitor被持有后，它将处于锁定状态。线程执行到monitorenter指令时，将尝试获取对象所对应的monitor的所有权，即尝试获取对象的锁。

> **Java中每一个对象都可以是一把锁**

1. 对于普通同步方法，锁是当前实例对象
2. 对于静态同步方法，锁是当前类的Class对象
3. 对于同步方法块，锁是Synchronized括号里配置的对像	

### **CAS**

> Comparea And Swap ,CAS操作需要输入两个数值，一个旧值，一个新值，在操作期间线比较旧值有没有发生变化，如果没有发生变化，才交换成新值，发生了变化则不交换。

> Java实现原子操作的方法：使用循环CAS实现原子操作（自旋CAS）；使用锁机制实现原子操作，JVM内部锁机制中除了偏向锁，其他的锁实现方式都是使用了循环CAS。

​		CAS带来的问题

​		***ABA***

​		***循环时间长开销大***

​		***只能保证一个共享变量的原子操作***

### **线程**

> 线程是现代操作系统中调度的最小单元，也叫轻量级进程，在一个进程里可以创建多个线程，这些线程都拥有各自的计数器、堆栈和局部变量等属性，并且能够访问共享的内存变量。

​		***线程的状态***

|   状态名称   | 说明                                                         |
| :----------: | ------------------------------------------------------------ |
|     NEW      | 初始状态，线程被构建，但是还没有调用start()方法。            |
|   RUNNABLE   | 运行状态，JAVA线程将操作系统中的就绪和运行两种状态笼统地称作“运行中” |
|   BLOCKED    | 阻塞状态                                                     |
|   WAITING    | 等待状态，需要其他线程通知或者中断                           |
| TIME_WAITING | 超时等待，在指定时间自行返回                                 |
|  TERMINATED  | 终止状态，表示当前线程已经执行完毕                           |

### **AQS**（AbstractQueuedSynchronizer）

​		***可重写方法***

|                  方法名称                   | 描述                                                         |
| :-----------------------------------------: | ------------------------------------------------------------ |
|    protected boolean tryAcquire(int arg)    | 独占式获取同步状态                                           |
|    protected boolean tryRelease(int arg)    | 独占式释放同步状态                                           |
|   protected int tryAcquireShared(int arg)   | 共享式获取同步状态，返回值大于等于0表示获取成功              |
| protected boolean tryReleaseShared(int arg) | 共享式释放同步状态                                           |
|    protected boolean isHeldExclusively()    | 当前同步器是否在独占模式下被线程占用，一般方法表示是否被当前线程所独占 |
