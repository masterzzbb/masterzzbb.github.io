---
title: A Little...
categories: 
- other
---

# 一点点

## 日志

```java
//先判断,再输出,提高性能

    public void test() {
        if(log.isEnableTrace()){//是否允许输出trace级别的日志
            log.trace();
        }
    }
```

## 规范

```java
    //POJO类中任何布尔类型的变量,不以is为前缀,否则部分框架解析会引起序列化错误.
    Caused by: com.fasterxml.jackson.databind.JsonMappingException: (was java.lang.NullPointerException)
```

## 优化

- 线程

```java

/**
 *a
 */
CompletableFuture<A> a = CompletableFuture.supplyAsync(() -> doA());
CompletableFuture<B> b = CompletableFuture.supplyAsync(() -> doB()); CompletableFuture.allOf(futureA,futureB) // 等a b 两个任务都执行完成 C 
c = doC(futureA.join(), futureB.join());
CompletableFuture<D> futureD = CompletableFuture.supplyAsync(() -> doD(c)); CompletableFuture<E> futureE = CompletableFuture.supplyAsync(() -> doE(c)); CompletableFuture.allOf(futureD,futureE) // 等d e两个任务都执行完成 
return doResult(futureD.join(),futureE.join());
```

- 多线程

- maven 版本号设置

```text
    mvn versions:set -DnewVersion=1.0.3-SNAPSHOP

    mvn versions:update-child-modules

    mvn versions:commit
```
