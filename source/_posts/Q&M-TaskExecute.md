---
title: TaskExecute-Q&M
categories:
- JAVA
tags: 
- 多线程
---

## 多个任务共同执行

> 场景1:excel导出,多个sheet.

> 系统A中提供三个API供获取三个表数据,每个表的数据对应一个sheet,系统B调用A的API获取数据,在B系统中做了模版请求