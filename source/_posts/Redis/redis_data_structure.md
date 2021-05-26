---
title: Redis Data Structure
categories:
- Data Structure
tags: 
- Data Structure
- Redis
---

## Redis底层数据结构

* SDS(Simple Dynamic String)
1. 结构
```C++
struct sdshdr {
    int len;
    int free;
    char buf[];

};
```
2. 与C字符串比较

- 常数复杂度获取字符串长度
- 杜绝缓冲区溢出
---
* 链表
1. 数据结构
```c++
//链表节点
typedef struct listNode {
    struct listNode *prev;
    struct listNode *next;
    void *value;
}listNode;

//链表
typedef struct list {
    listNode *head;
    listNode *tail;
    unsigned long len;
    void *(*duo)(void *ptr);
    void (*free)(void *ptr);
    int (*match)(void *ptr,void *key);
}list;
```
