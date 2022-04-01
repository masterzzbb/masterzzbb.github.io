---
title: Redis Data Structure
categories:
- Data Structure
tags: 
- Data Structure
- Redis
---

[TOC]

## Redis底层数据结构

### SDS(Simple Dynamic String)

1. 结构

```C
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

### 链表

1. 数据结构

```C
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

### 字典

1. 数据结构

```C
//字典
typedef struct dict {

    dictType *type;

    void *private;

    dictht ht[2];

    int trehashidx;//不再rehash时值为-1

}
//特定类型函数
typedef struct dictType {

    unsigned int (*hashFunction)(const void *key);

    void *(*keyDup)(void *private,const void *key);

    void *(*valDup)(void *private,const void obj);

    int *(*keyCompare)(void *private,const void *key1,const void key2);

    void (*keyDestructor)(void *private,void *key);

    void (*valDestructor)(void *private,void *obj);
}
```

### 跳跃表

1. 数据结构

```C
//节点
typedef struct zskiplistNode {

    struct zskiplistNode *backward;

    double score;

    robj *obj;

    struct zskiplistLevel {

        struct zskiplistNode *forward;

        unsigned int span;

    } level[];

} zskiplistNode;

//跳跃表
typedef struct zskiplist {

    struct skiplistNode *header,*tail;

    unsigned long length;

    int level;

}
```

### 整数集合

1. 数据结构

```C
typedef struct intset {

    uint32_t encoding;

    uint32_t length;

    int8_t contents[];
}
```

### 压缩列表
