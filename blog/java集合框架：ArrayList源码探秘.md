---
title: java集合框架：List源码探秘
tags:
  - java
originContent: >-
  本文主要讲解List源码部分常使用的方法及注意事项

  <!-- more -->


  # 集合与泛型


  ## List,List<Object>,List<?>的区别


  List表示能够放入任意类型的数据，List<Object>表示只能够放入Object类型的数据。<? extends
  T>表示可以赋值任何T及T的子类，上界为T，<? super T>表示可以放入任何T及T的父类，下界为T。


  ## fail-fast机制

  java.util包下的所有集合类都是fail-fast,即当检测到遍历的同时发生了修改，会立即抛出ConcurrentModificationException异常。相对应的就是fail-safe,concurrent包内的都是fail-safe，为了避免此类情况，可以使用并发容器CopyOnWriteArrayList代替ArrayList，实行读写分离，如果是写操作，则复制一个新集合，在新集合内添加或删除元素，待一切修改完成后再将原集合的引用指向新的集合。COW的缺点也很明显，就是读不到最新的数据，这也是CAP理论中C与A的矛盾，即一致性和可用性的矛盾。
categories:
  - java
toc: false
date: 2019-02-27 15:19:16
---

本文主要讲解List源码部分常使用的方法及注意事项
<!-- more -->

# 集合与泛型

## List,List<Object>,List<?>的区别

List表示能够放入任意类型的数据，List<Object>表示只能够放入Object类型的数据。<? extends T>表示可以赋值任何T及T的子类，上界为T，<? super T>表示可以放入任何T及T的父类，下界为T。

## fail-fast机制
java.util包下的所有集合类都是fail-fast,即当检测到遍历的同时发生了修改，会立即抛出ConcurrentModificationException异常。相对应的就是fail-safe,concurrent包内的都是fail-safe，为了避免此类情况，可以使用并发容器CopyOnWriteArrayList代替ArrayList，实行读写分离，如果是写操作，则复制一个新集合，在新集合内添加或删除元素，待一切修改完成后再将原集合的引用指向新的集合。COW的缺点也很明显，就是读不到最新的数据，这也是CAP理论中C与A的矛盾，即一致性和可用性的矛盾。