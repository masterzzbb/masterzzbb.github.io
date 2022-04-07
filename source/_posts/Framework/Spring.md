---
title: Spring
categories:
- Spring
tags: 
- Spring
---
# Spring

## Feature

- Core technologies:dependency injection, event, resources,i18n,validation,data binding, type conversion,SpEL,AOP.
- Testing:mock objects,TestContext framework,Spring MVC Test,WebTestClient.
- Data Access: transactions,DAO support,JDBC,ORM,Marshalling XML.
- Spring MVC and Spring WebFlux web frameworks.
- Integration:remoting.JMS,JCA,JMX,email,tasks,scheduling,cache.
- Languages:Kotlin,Groovy,dynamic languages.

## Spring事务

### Spring事务的配置方式

1. 编程式事务	

> 「侵入式」  TransactionTemplate（推荐） 或者 PlatformTransactionManager。粒度可以到代码块

2. 声明式事务

> 「非侵入式」声明式事务是建立在AOP上的。粒度是方法级的

### Spring事务特性

1. 事物传播机制
2. 事物隔离级别

#### 事物传播机制

> 事务的传播一般出现在事务嵌套的场景：一个事务方法调用了另一个事务方法。

- PROPAGATION_REQUIED

  默认传播机制，如果有外层事务，当前事务加入到外层事务，一块提交，一块回滚，没有则创建。

- PROPAGATION_REQUIES_NEW

  每次开启一个新的事务，同时把外部事务挂起，当前事务执行完毕，恢复上层事务的执行。没有外部事务，执行当前。

- PROPAGATION_SUPPORT

  依赖于外层事务。有外层事务，加入到外层事务中，没有则使用非事务提交。

- PROPAGATION_NOT_SUPPORT

  不支持事务，存在外部事务，挂起外部事务，执行完成当前代码后恢复外层事务，无论是否异常都不会回滚当前代码。

- PROPAGATION_NEVER

  不支持外层事务，存在外层事务抛出异常。

- PROPAGATION_MANDATORY

  与NEVER相反，如果外层没有事务，抛出异常。

- PROPAGATION_NESTED

  保存当前事务的状态保存点，当前事务回滚到某一个点，从而避免所有的嵌套事务都回滚，即各自回滚各自的，如果子事务没有把异常吃掉，基本会引起全部的回滚

### @Transactional事务失效

- 未开启事务管理
- 内部调用
- 方法权限非public
- 方法修饰为final、static
- 捕获异常未抛出
- rollback指定异常不包含抛出异常
- 多线程调用：数据库连接变化
- 数据库表不支持事务：myisam
- 不被Spring管理

