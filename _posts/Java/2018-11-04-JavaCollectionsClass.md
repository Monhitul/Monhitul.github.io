---
layout: blog
project: false
istop: true
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
category: Java
title: Java集合类
tags:
- Java
- 集合框架
redirect_from:
  - /2018/11/04/JavaCollectionsClass/
---

# 总述

集合类存放于java.util包中。

集合类存放的都是对象的引用，而非对象本身，出于表达上的便利，我们称集合中的对象就是指集合中对象的引用（reference)。

集合类型主要有3种：set(集）、list(列表）和map(映射)。其中Set代表无序、不可重复的集合；List代表有序、重复的集合；而Map则代表具有映射关系的集合。Java 5之后，增加了Queue体系集合，代表一种队列集合实现。

集合接口分为：Collection和Map，list、set实现了Collection接口。

<img src="/style/images/passage/JavaCollectionsClass/集合框架图.jpg">

常用的集合框架如下图：
<img src="/style/images/passage/JavaCollectionsClass/常用集合框架1.PNG">

# 继承结构

Java API中所用的集合类，都是实现了Collection接口，他的一个类继承结构如下：

Collection<--List<--Vector<--Stack

Collection<--List<--ArrayList

Collection<--List<--LinkedList

Collection<--Set<--HashSet

Collection<--Set<--HashSet<--LinkedHashSet

Collection<--Set<--SortedSet<--TreeSet

**Vector**: 基于Array的List，其实就是封装了Array所不具备的一些功能方便我们使用，它不可能走出Array的限制。性能也就不可能超越Array。所以，在可能的情况下，我们要多运用Array。另外很重要的一点就是Vector“synchronized”的，这个也是Vector和ArrayList的唯一的区别。

**ArrayList**：同Vector一样是一个基于数组实现的，但是不同的是ArrayList不是同步的。所以在性能上要比Vector优越一些，但是当运行到多线程环境中时，可需要自己在管理线程的同步问题。

**LinkedList**：LinkedList不同于前面两种List，它不是基于Array的，所以不受Array性能的限制。它每一个节点（Node）都包含两方面的内容：1.节点本身的数据（data）；2.下一个节点的信息（nextNode）。所以当对LinkedList做添加，删除动作的时候就不用像基于Array的List一样，必须进行大量的数据移动。只要更改nextNode的相关信息就可以实现了。这就是LinkedList的优势。