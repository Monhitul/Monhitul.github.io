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

# List接口

List集合代表一个元素有序、可重复的集合，集合中每个元素都有其对应的顺序索引。List集合允许加入重复元素，因为它可以通过索引来访问指定位置的集合元素。List集合默认按元素的添加顺序设置元素的索引。

## ArrayList

ArrayList是基于数组实现的List类，它封装了一个动态的增长的、允许再分配的Object[]数组。

## Vector

Vector和ArrayList在用法上几乎完全相同，但由于Vector是一个古老的集合，所以Vector提供了一些方法名很长的方法，但随着JDK1.2以后，java提供了系统的集合框架，就将Vector改为实现List接口，统一归入集合框架体系中。

## Stack

Stack是Vector提供的一个子类，用于模拟"栈"这种数据结构(LIFO后进先出)。

## LinkedList

implements List<E>, Deque<E>。实现List接口，能对它进行队列操作，即可以根据索引来随机访问集合中的元素。同时它还实现Deque接口，即能将LinkedList当作双端队列使用。自然也可以被当作"栈来使用"。

## List总结

1 List按对象进入的顺序保存对象，不做排序等操作；

2 ArrayList类：由数组实现的List，允许对元素进行快速随机访问，但是向List中间插入与移除元素的速度很慢；

3 LinkedList类：对顺序访问进行了优化，向List中间插入与删除的开销并不大，随机访问则相对较慢。

# Queue接口

Queue用于模拟"队列"这种数据结构(先进先出 FIFO)。队列的头部保存着队列中存放时间最长的元素，队列的尾部保存着队列中存放时间最短的元素。新元素插入(offer)到队列的尾部，访问元素(poll)操作会返回队列头部的元素，队列不允许随机访问队列中的元素。

## PriorityQueue(优先队列)

PriorityQueue并不是一个比较标准的队列实现，PriorityQueue保存队列元素的顺序并不是按照加入队列的顺序，而是按照队列元素的大小进行重新排序，这点从它的类名也可以看出来。

## Deque

Deque接口代表一个"双端队列"，双端队列可以同时从两端来添加、删除元素，因此Deque的实现类既可以当成队列使用、也可以当成栈使用。

### ArrayDeque

是一个基于数组的双端队列，和ArrayList类似，它们的底层都采用一个动态的、可重分配的Object[]数组来存储集合元素，当集合元素超出该数组的容量时，系统会在底层重新分配一个Object[]数组来存储集合元素。

### LinkedList

## Queue总结

1 Queue用于模拟队列这种数据结构，实现“FIFO”等数据结构。通常，队列不允许随机访问队列中的元素。

2 ArrayDeque类：为Queue子接口Deque的实现类，数组方式实现。

3 LinkedList类：是List接口的实现类，同时它也实现了Deque接口（Queue子接口）。因此它也可以当做一个双端队列来用，也可以当作“栈”来使用。
