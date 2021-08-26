---
title: A Little About log
categories:
- log
tags: 
- A Little
---

## 关于日志的一点点

* 日志

```java
//先判断,再输出,提高性能

    public void test() {
        if(log.isEnableTrace()){//是否允许输出trace级别的日志
            log.trace();
        }
    }
```
* 规范

```java
    //POJO类中任何布尔类型的变量,不以is为前缀,否则部分框架解析会引起序列化错误.
    Caused by: com.fasterxml.jackson.databind.JsonMappingException: (was java.lang.NullPointerException)
```
* maven 版本号设置

```
mvn versions:set -DnewVersion=1.0.3-SNAPSHOP

mvn versions:update-child-modules

mvn versions:commit
```