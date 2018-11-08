---
layout: blog
project: false
istop: true
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
category: Java
title: Java集合之Collection
tags:
- Java
- 集合框架
redirect_from:
  - /2018/11/08/JavaCollection/
---

参考来源：
[1]http://www.cnblogs.com/skywang12345/p/3308513.html

# 概述

由下图可看到Collection的架构如下：

<img src="/style/images/passage/JavaCollectionsFramework/框图.PNG">

Collection的定义如下：
```java
public interface Collection<E> extends Iterable<E> {}
```
它是一个接口，是高度抽象出来的集合，它包含了集合的基本操作：添加、删除、清空、遍历(读取)、是否为空、获取大小、是否保护某元素等等。

Collection接口的所有子类(直接子类和间接子类)都必须实现2种构造函数：不带参数的构造函数 和 参数为Collection的构造函数。带参数的构造函数，可以用来转换Collection的类型。

```java
// Collection的API
abstract boolean         add(E object)
abstract boolean         addAll(Collection<? extends E> collection)
abstract void            clear()
abstract boolean         contains(Object object)
abstract boolean         containsAll(Collection<?> collection)
abstract boolean         equals(Object object)
abstract int             hashCode()
abstract boolean         isEmpty()
abstract Iterator<E>     iterator()
abstract boolean         remove(Object object)
abstract boolean         removeAll(Collection<?> collection)
abstract boolean         retainAll(Collection<?> collection)
abstract int             size()
abstract <T> T[]         toArray(T[] array)
abstract Object[]        toArray()
```

# AbstractCollection
AbstractCollection的定义如下：
```java
public abstract class AbstractCollection<E> implements Collection<E> {}
```
AbstractCollection是一个**抽象类**，它实现了Collection中除iterator()和size()之外的函数。

**AbstractCollection的主要作用：它实现了Collection接口中的大部分函数。从而方便其它类实现Collection，比如ArrayList、LinkedList等，它们这些类想要实现Collection接口，通过继承AbstractCollection就已经实现了大部分的接口了。**

# AbstractList

AbstractList的定义如下：
```java
public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E> {}
```

AbstractList是一个继承于AbstractCollection，并且实现List接口的**抽象类**。它实现了List中除size()、get(int location)之外的函数。

**AbstractList的主要作用：它实现了List接口中的大部分函数。从而方便其它类继承List。
另外，和AbstractCollection相比，AbstractList抽象类中，实现了iterator()接口。**

# AbstractSet

AbstractSet的定义如下： 
```java
public abstract class AbstractSet<E> extends AbstractCollection<E> implements Set<E> {}
```

AbstractSet是一个继承于AbstractCollection，并且实现Set接口的**抽象类**。由于Set接口和Collection接口中的API完全一样，Set也就没有自己单独的API。和AbstractCollection一样，它实现了List中除iterator()和size()之外的函数。

**AbstractSet的主要作用：它实现了Set接口中的大部分函数。从而方便其它类实现Set接口。**

# Iterator

Iterator的定义如下：
```java
public interface Iterator<E> {}
```

Iterator是一个接口，它是集合的迭代器。集合可以通过Iterator去遍历集合中的元素。Iterator提供的API接口，包括：是否存在下一个元素、获取下一个元素、删除当前元素。

注意：Iterator遍历Collection时，是fail-fast机制的。即，当某一个线程A通过iterator去遍历某集合的过程中，若该集合的内容被其他线程所改变了；那么线程A访问集合时，就会抛出ConcurrentModificationException异常，产生fail-fast事件。

```java
// Iterator的API
abstract boolean hasNext()
abstract E next()
abstract void remove()
```

# ListIterator

ListIterator的定义如下：
```java
public interface ListIterator<E> extends Iterator<E> {}
```

ListIterator是一个继承于Iterator的接口，它是队列迭代器。专门用于便利List，能提供向前/向后遍历。相比于Iterator，它新增了添加、是否存在上一个元素、获取上一个元素等等API接口。

```java
// ListIterator的API
// 继承于Iterator的接口
abstract boolean hasNext()
abstract E next()
abstract void remove()
// 新增API接口
abstract void add(E object)
abstract boolean hasPrevious()
abstract int nextIndex()
abstract E previous()
abstract int previousIndex()
abstract void set(E object)
```