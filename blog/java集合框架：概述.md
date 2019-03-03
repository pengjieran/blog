---
title: java集合框架：概述
tags:
  - java
originContent: >-
  本文主要内容是概述java集合框架的所有内容，接下来的篇章会详细介绍各个集合的内容。

  <!-- more -->

  # 概述

  不在沉默中爆发，就在沉默中灭亡，看看去年的学习工作历程，简直惨不忍睹。一方面是因为博主要承担leader的工作，每天开不完的需求会，盯不完的任务进度。另一方面是因为博主确实放松了，没有再更新相关内容，新的一年，继续加油吧！

  今年一年会通过博客的方式分享开发工作中所有的知识点和细节，有感兴趣的小伙伴关注哦！

  # 博客更新顺序

  先来看一张图

  ![集合框架JDK8水印.jpg](http://images.mtoliv.com/FhHysLDyuZyO1oYs86EpffCq_hml)

  这是我花了一天的时间在JDK8的基础上总结的一张集合框架的思维导图，集合框架系列的博客顺序也会按照这个顺序往下写，有错误的地方希望小伙伴指正

  # 集合框架概述

  java原生提供的集合框架有集合和Map,集合包含有序可重复链表，无序不可重复链表和队列，Map包含基于hash表的map,基于二叉树的map,还有一个支持并发的CurrentHashMap

  # List

  List是一个接口，其实现有很多，这里只介绍常用的集合，主要实现有ArrayList,Vector,LinkedList

  ## ArrayList

  ```java

  public class ArrayList<E> extends AbstractList<E>
          implements List<E>, RandomAccess, Cloneable, java.io.Serializable
  ```


  ArrayList继承至AbstractList，实现了List,RandomAccess,Cloneable,Serializable四个接口，使其具有List的通用操作，随机访问能力，可拷贝与可序列化。

  ## Vector

  ```java

  public class Vector<E>
      extends AbstractList<E>
      implements List<E>, RandomAccess, Cloneable, java.io.Serializable
  ```

  ArrayList继承至AbstractList，实现了List,RandomAccess,Cloneable,Serializable四个接口，使其具有List的通用操作，随机访问能力，可拷贝与可序列化。唯一与ArrayList不同的是采用synchronized加锁的方式实现线程安全。

  ## LinkedList

  ```java

  public class LinkedList<E>
      extends AbstractSequentialList<E>
      implements List<E>, Deque<E>, Cloneable, java.io.Serializable
  ```

  继承AbstractSequentialList并实现了List, Deque, Cloneable, Serializable等接口


  # Set

  Set里面存放的都是无序的非重复数据。

  ## HashSet

  ```java

  public class HashSet<E>
      extends AbstractSet<E>
      implements Set<E>, Cloneable, java.io.Serializable
  ```

  继承至AbstractSet并实现了Set,Cloneable,Serializable接口


  ## TreeSet

  ```java

  public class TreeSet<E> extends AbstractSet<E>
      implements NavigableSet<E>, Cloneable, java.io.Serializable
  ```

  继承AbstractSet并实现了NavigableSet，Cloneable和Serializable接口

  ## LinkedHashSet

  ```java

  public class LinkedHashSet<E>
      extends HashSet<E>
      implements Set<E>, Cloneable, java.io.Serializable
  ```

  继承HashSet并实现Set,Cloneable,Serializable接口
categories:
  - java
toc: false
date: 2019-02-27 15:21:46
---

本文主要内容是概述java集合框架的所有内容，接下来的篇章会详细介绍各个集合的内容。
<!-- more -->
# 概述
不在沉默中爆发，就在沉默中灭亡，看看去年的学习工作历程，简直惨不忍睹。一方面是因为博主要承担leader的工作，每天开不完的需求会，盯不完的任务进度。另一方面是因为博主确实放松了，没有再更新相关内容，新的一年，继续加油吧！
今年一年会通过博客的方式分享开发工作中所有的知识点和细节，有感兴趣的小伙伴关注哦！
# 博客更新顺序
先来看一张图
![集合框架JDK8水印.jpg](http://images.mtoliv.com/FhHysLDyuZyO1oYs86EpffCq_hml)
这是我花了一天的时间在JDK8的基础上总结的一张集合框架的思维导图，集合框架系列的博客顺序也会按照这个顺序往下写，有错误的地方希望小伙伴指正
# 集合框架概述
java原生提供的集合框架有集合和Map,集合包含有序可重复链表，无序不可重复链表和队列，Map包含基于hash表的map,基于二叉树的map,还有一个支持并发的CurrentHashMap
# List
List是一个接口，其实现有很多，这里只介绍常用的集合，主要实现有ArrayList,Vector,LinkedList
## ArrayList
```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

ArrayList继承至AbstractList，实现了List,RandomAccess,Cloneable,Serializable四个接口，使其具有List的通用操作，随机访问能力，可拷贝与可序列化。
## Vector
```java
public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```
ArrayList继承至AbstractList，实现了List,RandomAccess,Cloneable,Serializable四个接口，使其具有List的通用操作，随机访问能力，可拷贝与可序列化。唯一与ArrayList不同的是采用synchronized加锁的方式实现线程安全。
## LinkedList
```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```
继承AbstractSequentialList并实现了List, Deque, Cloneable, Serializable等接口

# Set
Set里面存放的都是无序的非重复数据。
## HashSet
```java
public class HashSet<E>
    extends AbstractSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
```
继承至AbstractSet并实现了Set,Cloneable,Serializable接口

## TreeSet
```java
public class TreeSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, java.io.Serializable
```
继承AbstractSet并实现了NavigableSet，Cloneable和Serializable接口
## LinkedHashSet
```java
public class LinkedHashSet<E>
    extends HashSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
```
继承HashSet并实现Set,Cloneable,Serializable接口
