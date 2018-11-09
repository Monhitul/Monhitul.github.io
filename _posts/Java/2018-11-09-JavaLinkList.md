---
layout: blog
project: false
istop: true
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
category: Java
title: Java集合之ArrayList
tags:
- Java
- 集合框架
redirect_from:
  - /2018/11/09/JavaLinkedList/
---

参考来源：
[1]http://www.cnblogs.com/skywang12345/p/3308807.html

# 概述

* LinkedList 是一个继承于AbstractSequentialList的双向链表。它也可以被当作堆栈、队列或双端队列进行操作。

* LinkedList 实现 List 接口，能对它进行队列操作。

* LinkedList 实现 Deque 接口，即能将LinkedList当作双端队列使用。

* LinkedList 实现了Cloneable接口，即覆盖了函数clone()，能克隆。

* LinkedList 实现java.io.Serializable接口，这意味着LinkedList支持序列化，能通过序列化去传输。

LinkedList构造函数：
```java
// 默认构造函数
LinkedList()

// 创建一个LinkedList，保护Collection中的全部元素。
LinkedList(Collection<? extends E> collection)
```

## AbstractSequentialList

LinkedList是AbstractSequentialList的子类。

AbstractSequentialList 实现了get(int index)、set(int index, E element)、add(int index, E element) 和 remove(int index)这些函数。这些接口都是随机访问List的，LinkedList是双向链表；既然它继承于AbstractSequentialList，就相当于已经实现了“get(int index)这些接口”。

此外，我们若需要通过AbstractSequentialList自己实现一个列表，只需要扩展此类，并提供 listIterator() 和 size() 方法的实现即可。若要实现不可修改的列表，则需要实现列表迭代器的 hasNext、next、hasPrevious、previous 和 index 方法即可。