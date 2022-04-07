---
title: TaskExecute-Q&M
categories:
- Java
tags: 
- 线程
---

## 线程

* 阻塞

CountDownLatch

```JAVA

    ThreadPoolTaskExecutor taskExecutor;//global varialble

    CountDownLatch latch = new CountDownLatch(1);
    Future<Response> future = taskExecutor.submitListenable(()->{
        //task
    });
    future.addCallback(success->{
        //处理结果
        latch.countDown();
    },exception->{
        //处理异常
        latch.countDown();
    });
    latch.await();//

```

