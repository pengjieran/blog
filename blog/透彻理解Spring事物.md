---
title: 透彻理解Spring事物
tags:
  - Spring
originContent: >-
  本文将带你透彻理解，看完此篇，相信你能对Spring事物有一个透彻的理解与掌握。

  <!-- more -->

  # 什么是事物？

  事物是逻辑上的一组操作组合，遮住操作要么都执行，要么都不执行。


  # 事物的四大特性（ACID）：

  - 原子性（Atomicity）：事物是最小的执行单位，不允许分割。事物的原子性确保动作要么全部成功，要么全部失败。

  - 一致性（Consistency）：执行事物前后，数据保持一致

  - 隔离性（Isolation）：各并发事物之间是独立的，不会相互影响

  - 持久性（Persistence）：一个事物被提交后，对数据库的改变是持久的，即使数据库发生故障也不会有任何影响。

  # Spring事物管理接口介绍

  ## Spring事物管理接口

  - PlatformTransactionManager：（平台）事物管理器

  - TransactionDefinition：事物定义信息（事物隔离级别，传播行为，超时，只读，回滚策略）

  - TransactionStatus：事物运行状态

  ### PlatformTransactionManager

  Spring并不直接管理事物，而是提供了多种事物管理器，他们将事物管理的职责委托给Hibernate或者JTA等持久化机制所提供的相关平台框架来实现。通过PlatformTransactionManager接口，Spring为各个平台框架都提供了对应的事物管理器，但是具体的实现就是各个平台框架自己的事情了。

  PlatformTransactionManager接口代码如下：

  ```java

  Public interface PlatformTransactionManager()...{  
      // Return a currently active transaction or create a new one, according to the specified propagation behavior（根据指定的传播行为，返回当前活动的事务或创建一个新事务。）
      TransactionStatus getTransaction(TransactionDefinition definition) throws TransactionException; 
      // Commit the given transaction, with regard to its status（使用事务目前的状态提交事务）
      Void commit(TransactionStatus status) throws TransactionException;  
      // Perform a rollback of the given transaction（对执行的事务进行回滚）
      Void rollback(TransactionStatus status) throws TransactionException;  
      } 
  ```

  几个比较常见的接口实现如下：

  |事物|说明|

  |-|-|

  |DataSourceTransactionManager|使用Spring JDBC或者Mybatis进行持久化数据时使用|

  |HibernateTransactionManager|使用Hibernate进行持久化数据时使用|

  |JtaTransactionManager|使用JTA实现来管理事物，在一个事物跨越多个资源时使用|

  ### TransactionDefinition

  事物管理器接口PlatformTransactionManager通过getTransaction(TransactionDefinition
  definition)方法来得到一个事物，这个方法里面的参数是TransactionDefinition类，类里面定义了一些基本的事物属性。事物属性包含了五个方面：

  - 隔离级别

  - 传播行为

  - 回滚规则

  - 是否只读

  - 事物超时

  #### 隔离级别

  在谈事物隔离级别之前，先来谈谈并发事物带来的问题：

  - 脏读（Dirty
  read）：当一个事物正在访问数据并且对数据进行了修改，而这种修改还没有提交到数据库中，这时另外一个事物也访问了这个数据，然后使用了这个数据。因为这个数据是还没有提交的，另外一个事物读到的这个数据就是脏数据，依据脏数据所做的操作可能是不正确的。

  - 丢失修改（Lost to
  modify）：一个事物读取到一个数据时，另外一个事物也读取了这个数据，第一个事物修改了这个数据，第二个事物也修改了这个数据，这样第一个事物修改的数据就会丢失，称为丢失修改。

  -
  不可重复读（Unrepeatableread）：在一个事务内多次读同一个数据，在这个事物还没有结束时，另一个事物也访问该数据。那么，在第一个事物两次读数据中间，由于第二个事物修改了数据导致第一个事物两次读出的数据不一致，这样就发生了两次读到的数据是不一样的，因此称为不可重复读。

  - 幻读（Phantom
  read）：发生在一个事物读取了几行数据，接着另外一个事物插入了几行数据，在随后的查询中，第一个事物就会发现多了一些数据，就像发生了幻视一样，所以称为幻读。

  不可重复读的重点是修改，幻读的重点在于新增或者删除。

  ##### TransactionDefinition 接口中定义了五个表示隔离级别的常量：

  -
  TransactionDefinition.ISOLATION_DEFAULT：使用后端数据库默认的隔离级别，mysql默认采用REPEATABLE_READ隔离级别，Oracle默认采用READ_COMMITTED隔离级别。

  -
  TransactionDefinition.ISOLATION_READ_UNCOMMITTED：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读，幻读，不可重复读。

  -
  TransactionDefinition.ISOLATION_READ_COMMITTED：允许读取并发事物已经提交的数据，可以阻止脏读，但是幻读和不可重复读仍有可能发生。

  -
  TransactionDefinition.ISOLATION_REPEATABLE_READ：对同一字段的多次读取结果都是一致的，除非数据是被本身事物自己修改的，可以阻止脏读和不可重复读，但幻读仍有可能发生。

  -
  TransactionDefinition.ISOLATION_SERIALIZABLE：最高的隔离级别，完全服从ACID的隔离级别，所有的事物依次逐个执行，这种事物之间就完全不可能产生干扰，但是会严重影响程序性能。通常情况下也不会用到该级别。

  #### 事物传播行为（为了解决业务层方法之间互相调用的事物问题）

  当事物方法被另外一个事物方法调用时，必须指定事物应该如何传播，方法可能继续在现有事物中运行，也可能开启一个新事物，并在自己的事物中运行。在TransactionDefinition定义中包括了如下几个表示传播行为的常量：

  - TransactionDefinition.PROPAGATION_REQUIRED：如果当前存在事物，则加入该事物，否则创建一个新事物

  - TransactionDefinition.PROPAGATION_SUPPORTS：如果当前存在事物，则加入该事物，否则以非事物的方式继续运行

  - TransactionDefinition.PROPAGATION_MANDATORY：如果当前存在事物，则加入该事物，否则强制性抛出异常
categories: []
toc: false
date: 2019-03-07 15:51:56
---

本文将带你透彻理解，看完此篇，相信你能对Spring事物有一个透彻的理解与掌握。
<!-- more -->
# 什么是事物？
事物是逻辑上的一组操作组合，遮住操作要么都执行，要么都不执行。

# 事物的四大特性（ACID）：
- 原子性（Atomicity）：事物是最小的执行单位，不允许分割。事物的原子性确保动作要么全部成功，要么全部失败。
- 一致性（Consistency）：执行事物前后，数据保持一致
- 隔离性（Isolation）：各并发事物之间是独立的，不会相互影响
- 持久性（Persistence）：一个事物被提交后，对数据库的改变是持久的，即使数据库发生故障也不会有任何影响。
# Spring事物管理接口介绍
## Spring事物管理接口
- PlatformTransactionManager：（平台）事物管理器
- TransactionDefinition：事物定义信息（事物隔离级别，传播行为，超时，只读，回滚策略）
- TransactionStatus：事物运行状态
### PlatformTransactionManager
Spring并不直接管理事物，而是提供了多种事物管理器，他们将事物管理的职责委托给Hibernate或者JTA等持久化机制所提供的相关平台框架来实现。通过PlatformTransactionManager接口，Spring为各个平台框架都提供了对应的事物管理器，但是具体的实现就是各个平台框架自己的事情了。
PlatformTransactionManager接口代码如下：
```java
Public interface PlatformTransactionManager()...{  
    // Return a currently active transaction or create a new one, according to the specified propagation behavior（根据指定的传播行为，返回当前活动的事务或创建一个新事务。）
    TransactionStatus getTransaction(TransactionDefinition definition) throws TransactionException; 
    // Commit the given transaction, with regard to its status（使用事务目前的状态提交事务）
    Void commit(TransactionStatus status) throws TransactionException;  
    // Perform a rollback of the given transaction（对执行的事务进行回滚）
    Void rollback(TransactionStatus status) throws TransactionException;  
    } 
```
几个比较常见的接口实现如下：
|事物|说明|
|-|-|
|DataSourceTransactionManager|使用Spring JDBC或者Mybatis进行持久化数据时使用|
|HibernateTransactionManager|使用Hibernate进行持久化数据时使用|
|JtaTransactionManager|使用JTA实现来管理事物，在一个事物跨越多个资源时使用|
### TransactionDefinition
事物管理器接口PlatformTransactionManager通过getTransaction(TransactionDefinition definition)方法来得到一个事物，这个方法里面的参数是TransactionDefinition类，类里面定义了一些基本的事物属性。事物属性包含了五个方面：
- 隔离级别
- 传播行为
- 回滚规则
- 是否只读
- 事物超时
#### 隔离级别
在谈事物隔离级别之前，先来谈谈并发事物带来的问题：
- 脏读（Dirty read）：当一个事物正在访问数据并且对数据进行了修改，而这种修改还没有提交到数据库中，这时另外一个事物也访问了这个数据，然后使用了这个数据。因为这个数据是还没有提交的，另外一个事物读到的这个数据就是脏数据，依据脏数据所做的操作可能是不正确的。
- 丢失修改（Lost to modify）：一个事物读取到一个数据时，另外一个事物也读取了这个数据，第一个事物修改了这个数据，第二个事物也修改了这个数据，这样第一个事物修改的数据就会丢失，称为丢失修改。
- 不可重复读（Unrepeatableread）：在一个事务内多次读同一个数据，在这个事物还没有结束时，另一个事物也访问该数据。那么，在第一个事物两次读数据中间，由于第二个事物修改了数据导致第一个事物两次读出的数据不一致，这样就发生了两次读到的数据是不一样的，因此称为不可重复读。
- 幻读（Phantom read）：发生在一个事物读取了几行数据，接着另外一个事物插入了几行数据，在随后的查询中，第一个事物就会发现多了一些数据，就像发生了幻视一样，所以称为幻读。
不可重复读的重点是修改，幻读的重点在于新增或者删除。
##### TransactionDefinition 接口中定义了五个表示隔离级别的常量：
- TransactionDefinition.ISOLATION_DEFAULT：使用后端数据库默认的隔离级别，mysql默认采用REPEATABLE_READ隔离级别，Oracle默认采用READ_COMMITTED隔离级别。
- TransactionDefinition.ISOLATION_READ_UNCOMMITTED：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读，幻读，不可重复读。
- TransactionDefinition.ISOLATION_READ_COMMITTED：允许读取并发事物已经提交的数据，可以阻止脏读，但是幻读和不可重复读仍有可能发生。
- TransactionDefinition.ISOLATION_REPEATABLE_READ：对同一字段的多次读取结果都是一致的，除非数据是被本身事物自己修改的，可以阻止脏读和不可重复读，但幻读仍有可能发生。
- TransactionDefinition.ISOLATION_SERIALIZABLE：最高的隔离级别，完全服从ACID的隔离级别，所有的事物依次逐个执行，这种事物之间就完全不可能产生干扰，但是会严重影响程序性能。通常情况下也不会用到该级别。
#### 事物传播行为（为了解决业务层方法之间互相调用的事物问题）
当事物方法被另外一个事物方法调用时，必须指定事物应该如何传播，方法可能继续在现有事物中运行，也可能开启一个新事物，并在自己的事物中运行。在TransactionDefinition定义中包括了如下几个表示传播行为的常量：
- TransactionDefinition.PROPAGATION_REQUIRED：如果当前存在事物，则加入该事物，否则创建一个新事物
- TransactionDefinition.PROPAGATION_SUPPORTS：如果当前存在事物，则加入该事物，否则以非事物的方式继续运行
- TransactionDefinition.PROPAGATION_MANDATORY：如果当前存在事物，则加入该事物，否则强制性抛出异常