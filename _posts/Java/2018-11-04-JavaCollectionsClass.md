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

参考来源：
[1]https://www.cnblogs.com/LittleHann/p/3690187.html
[2]https://www.cnblogs.com/nayitian/p/3266090.html#collectionframe

# 总述

集合类存放于java.util包中。

集合类存放的都是对象的引用，而非对象本身，出于表达上的便利，我们称集合中的对象就是指集合中对象的引用（reference)。

集合类型主要有3种：set(集）、list(列表）和map(映射)。其中Set代表无序、不可重复的集合；List代表有序、重复的集合；而Map则代表具有映射关系的集合。Java 5之后，增加了Queue体系集合，代表一种队列集合实现。

集合接口分为：Collection和Map，list、set实现了Collection接口。

<img src="/style/images/passage/JavaCollectionsFramework/架图.PNG">

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

# Collection接口

Collection接口是Set、List和Queue接口的父接口，基本操作包括(JDK源码内容)：

* int size();

//返回集合中元素的数量，如果数量超过了Integer.MAX_VALUE，就直接返回Integer.MAX_VALUE

* boolean isEmpty();

//判断集合是否为空

* boolean contains(Object o);

//是否包含指定元素

* Iterator<E> iterator();

//返回Iterator对象，用于遍历集合中的元素

* Object[] toArray();

//把集合转换为一个数组

* <T> T[] toArray(T[] a);

//把集合转换为一个数组，返回数组的运行时类型是指定数组的运行时类型

* boolean add(E e);

//增加元素

* boolean remove(Object o);

//移除元素

* boolean containsAll(Collection<?> c);

//是否包含集合c中的所有元素

* boolean addAll(Collection<? extends E> c);

//将所有指定集合中的元素添加到此集合的结尾

* boolean removeAll(Collection<?> c);

//清除当前集合中与所有指定集合所包含的元素，也就是说，removeAll实际上是一个先与c求交集然后取补的操作。

* default boolean removeIf(Predicate<? super E> filter){}

//删除集合中符合条件的元素

* boolean retainAll(Collection<?> c);

//相当于求与c的交集

* void clear();

//移除所有元素

* boolean equals(Object o);

//判断集合中有没有元素与指定元素相等

* int hashCode();

//返回集合的哈希值

# Set接口

Set集合类似于一个背包，放进Set集合里的多个对象之间没有明显的顺序。Set继承自Collection接口，不能包含有重复元素(记住，这是整个Set类层次的共有属性)。

Set判断两个对象相同不是使用"=="运算符，而是根据equals方法。也就是说，我们在加入一个新元素的时候，如果这个新元素对象和Set中已有对象进行注意equals比较都返回false，则Set就会接受这个新元素对象，否则拒绝。

因为Set的这个制约，在使用Set集合的时候，应该注意两点：
1 为Set集合里的元素的实现类实现一个有效的equals(Object)方法；
2 对Set的构造函数，传入的Collection参数不能包含重复的元素。

## HashSet

HashSet是Set接口的典型实现，HashSet使用HASH算法来存储集合中的元素，因此具有良好的存取和查找性能。当向HashSet集合中存入一个元素时，HashSet会调用该对象的hashCode()方法来得到该对象的hashCode值，然后根据该HashCode值决定该对象在HashSet中的存储位置。

值得注意的是，HashSet集合判断两个元素相等的标准是两个对象通过equals()方法比较相等，并且两个对象的hashCode()方法的返回值相等。

### LinkedHashSet

LinkedHashSet集合也是根据元素的hashCode值来决定元素的存储位置，但和HashSet不同的是，它同时使用链表维护元素的次序，这样使得元素看起来是以插入的顺序保存的。

当遍历LinkedHashSet集合里的元素时，LinkedHashSet将会按元素的添加顺序来访问集合里的元素。

LinkedHashSet需要维护元素的插入顺序，因此性能略低于HashSet的性能，但在迭代访问Set里的全部元素时(遍历)将有很好的性能(链表很适合进行遍历)。

## SortedSet    

此接口主要用于排序操作，即实现此接口的子类都属于排序的子类。

### TreeSet

TreeSet是SortedSet接口的实现类，TreeSet可以确保集合元素处于排序状态。

## Set总结

1 存入Set的每个元素都必须是唯一的，Set接口不保证维护元素的次序；

2 HashSet类： 为快速查找设计的Set，存入HashSet的对象必须定义hashCode()，它不保证集合的迭代顺序；  

3 LinkedHashSet类： 具有HashSet的查询速度，且内部使用链表维护元素的顺序(插入的次序)。
