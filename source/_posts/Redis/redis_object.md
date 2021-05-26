---
title: Redis Object
categories:
- Redis Object
tags: 
- Redis Object
- Redis
---

## Redis对象

### redis对象的数据结构

```C++
    typedef struct redisObject {
        unsigned type:4;
        unsigned encoding:4;
        void *ptr;
        int refcount;
        unsigned lru:22;
    } robj;
```

### 类型检查

        类型特定命令所进行的类型检查是通过redisObject结构的type属性来实现的.

### 多态命令

        一个对象有多种编码,对不同的编码有不同的命令,例如,列表对象有ziplist和linkedlist两种编码可以使用,对一个键执行LLEN命令,更具对象所使用的编码来选择正确的命令.
    
### 内存回收

        引用计数技术实现内存回收机制.
    + 创建一个对象时,初始化对象的引用计数值为1
    + 对象被使用,计数值加1
    + 对象不再被使用,计数值减1
    + 对象的计数值为0时,对象所占用的内存被回收

### 引用计数的API

|函数|作用|
|:----:|:----:|
|incrRefCount|将对象的引用计数值加1|
|decrRefCount|将对象的引用计数值减1|
|resetRefCount|将对象的引用计数值设置为0,但是并不释放对象,这个函数通常在需要重新设置对象的引用计数值时使用|

### 对象共享

        为节约内存,相同类型的值会被共享,共享时两部操作:键的值指针指向同一个现有的值,共享的值对象的引用计数值加1.
    **但是由于验证复杂对象相同的复杂度高,Redis只对包含整数值的对象进行共享**

### 对象的空转时长

        记录对象最后一次被命令程序访问的时间,用于计算出对象的空转时间,服务器开启maxmemory选项,
        并且服务器用于回收内存的算法为volatile-lru或者allkeys-lru,那么当服务器占用的内存超过来maxmemoryy选项所设置的上限值时,
        空转时间较长的部分键会被优先释放,从而回收内存.


