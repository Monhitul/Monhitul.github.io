---
layout: blog
project: false
istop: true
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
category: Java
title: Java集合之Vector
tags:
- Java
- 集合框架
redirect_from:
  - /2018/11/10/JavaVector/
---

参考来源：
[1]http://www.cnblogs.com/skywang12345/p/3308833.html

# 概述

Vector 是矢量队列，它是JDK1.0版本添加的类。继承于AbstractList，实现了List, RandomAccess, Cloneable这些接口。
Vector 继承了AbstractList，实现了List；所以，**它是一个队列，支持相关的添加、删除、修改、遍历等功能。**
Vector 实现了RandmoAccess接口，即**提供了随机访问功能**。RandmoAccess是java中用来被List实现，为List提供快速访问功能的。在Vector中，我们即可以通过元素的序号快速获取元素对象；这就是快速随机访问。
Vector 实现了Cloneable接口，即实现clone()函数。它能被克隆。

和ArrayList不同，**Vector中的操作是线程安全的。**

## Vector的构造函数
```java
//Vector共有4个构造函数
// 默认构造函数
Vector()

// capacity是Vector的默认容量大小。当由于增加数据导致容量增加时，每次容量会增加一倍。
Vector(int capacity)

// capacity是Vector的默认容量大小，capacityIncrement是每次Vector容量增加时的增量值。
Vector(int capacity, int capacityIncrement)

// 创建一个包含collection的Vector
Vector(Collection<? extends E> collection)
```

## Vector的API
```java
synchronized boolean        add(E object)
             void           add(int location, E object)
synchronized boolean        addAll(Collection<? extends E> collection)
synchronized boolean        addAll(int location, Collection<? extends E> collection)
synchronized void           addElement(E object)
synchronized int            capacity()
             void           clear()
synchronized Object         clone()
             boolean        contains(Object object)
synchronized boolean        containsAll(Collection<?> collection)
synchronized void           copyInto(Object[] elements)
synchronized E              elementAt(int location)
             Enumeration<E> elements()
synchronized void           ensureCapacity(int minimumCapacity)
synchronized boolean        equals(Object object)
synchronized E              firstElement()
             E              get(int location)
synchronized int            hashCode()
synchronized int            indexOf(Object object, int location)
             int            indexOf(Object object)
synchronized void           insertElementAt(E object, int location)
synchronized boolean        isEmpty()
synchronized E              lastElement()
synchronized int            lastIndexOf(Object object, int location)
synchronized int            lastIndexOf(Object object)
synchronized E              remove(int location)
             boolean        remove(Object object)
synchronized boolean        removeAll(Collection<?> collection)
synchronized void           removeAllElements()
synchronized boolean        removeElement(Object object)
synchronized void           removeElementAt(int location)
synchronized boolean        retainAll(Collection<?> collection)
synchronized E              set(int location, E object)
synchronized void           setElementAt(E object, int location)
synchronized void           setSize(int length)
synchronized int            size()
synchronized List<E>        subList(int start, int end)
synchronized <T> T[]        toArray(T[] contents)
synchronized Object[]       toArray()
synchronized String         toString()
synchronized void           trimToSize()
```

## Vector的继承关系

<img src="/style/images/passage/JavaVector/继承关系.PNG">

Vector的数据结构和ArrayList差不多，它包含了3个成员变量：elementData , elementCount， capacityIncrement。

1 ``elementData``是"Object[]类型的数组"，它保存了添加到Vector中的元素。elementData是个动态数组，如果初始化Vector时，没指定动态数组的大小，则使用默认大小10。随着Vector中元素的增加，Vector的容量也会动态增长，capacityIncrement是与容量增长相关的增长系数，具体的增长方式，请参考源码中的ensureCapacity()函数。

2 ``elementCount``是动态数组的实际大小。

3 ``capacityIncrement``是动态数组的增长系数。如果在创建Vector时，指定了capacityIncrement的大小；则，每次当Vector中动态数组容量增加时，增加的大小都是capacityIncrement。

## 小结
1 Vector实际上是通过一个**数组**去保存数据的。当我们构造Vecotr时；若使用默认构造函数，则Vector的**默认容量大小是10**。
2 当Vector容量不足以容纳全部元素时，Vector的容量会增加。**若容量增加系数 >0，则将容量的值增加“容量增加系数”；否则，将容量大小增加一倍。**
3 Vector的克隆函数，即是将全部元素克隆到一个数组中。

# Vector遍历方式

Vector支持4种遍历方式。建议使用下面的第二种去遍历Vector，因为效率问题。

1 第一种，通过迭代器遍历。即通过Iterator去遍历。
```java
Integer value = null;
int size = vec.size();
for (int i=0; i<size; i++) {
    value = (Integer)vec.get(i);        
}
```

2 第二种，随机访问，通过索引值去遍历。

由于Vector实现了RandomAccess接口，它支持通过索引值去随机访问元素。
```java
Integer value = null;
int size = vec.size();
for (int i=0; i<size; i++) {
    value = (Integer)vec.get(i);        
}
``` 

3 第三种，另一种for循环。如下：
```java
Integer value = null;
for (Integer integ:vec) {
    value = integ;
}
```
 
4 第四种，Enumeration遍历。如下： 
```java
Integer value = null;
Enumeration enu = vec.elements();
while (enu.hasMoreElements()) {
    value = (Integer)enu.nextElement();
}
```

**遍历Vector，使用索引的随机访问方式最快，使用迭代器最慢。**

# Vector示例

```java
import java.util.Vector;
import java.util.List;
import java.util.Iterator;
import java.util.Enumeration;

/**
 * @desc Vector测试函数：遍历Vector和常用API 
 *
 * @author skywang
 */
public class VectorTest {
    public static void main(String[] args) {
        // 新建Vector
        Vector vec = new Vector();
            
        // 添加元素
        vec.add("1");
        vec.add("2");
        vec.add("3");
        vec.add("4");
        vec.add("5");

        // 设置第一个元素为100
        vec.set(0, "100");
        // 将“500”插入到第3个位置
        vec.add(2, "300");
        System.out.println("vec:"+vec);

        // (顺序查找)获取100的索引
        System.out.println("vec.indexOf(100):"+vec.indexOf("100"));
        // (倒序查找)获取100的索引
        System.out.println("vec.lastIndexOf(100):"+vec.lastIndexOf("100"));
        // 获取第一个元素
        System.out.println("vec.firstElement():"+vec.firstElement());
        // 获取第3个元素
        System.out.println("vec.elementAt(2):"+vec.elementAt(2));
        // 获取最后一个元素
        System.out.println("vec.lastElement():"+vec.lastElement());

        // 获取Vector的大小
        System.out.println("size:"+vec.size());
        // 获取Vector的总的容量
        System.out.println("capacity:"+vec.capacity());

        // 获取vector的“第2”到“第4”个元素
        System.out.println("vec 2 to 4:"+vec.subList(1, 4));

        // 通过Enumeration遍历Vector
        Enumeration enu = vec.elements();
        while(enu.hasMoreElements())
            System.out.println("nextElement():"+enu.nextElement());
            
        Vector retainVec = new Vector();
        retainVec.add("100");
        retainVec.add("300");
        // 获取“vec”中包含在“retainVec中的元素”的集合
        System.out.println("vec.retain():"+vec.retainAll(retainVec));
        System.out.println("vec:"+vec);
            
        // 获取vec对应的String数组
        String[] arr = (String[]) vec.toArray(new String[0]);
        for (String str:arr)
            System.out.println("str:"+str);

        // 清空Vector。clear()和removeAllElements()一样！
        vec.clear();
//        vec.removeAllElements();

        // 判断Vector是否为空
        System.out.println("vec.isEmpty():"+vec.isEmpty());
    }   
}
```

运行结果：
```
vec:[100, 2, 300, 3, 4, 5]
vec.indexOf(100):0
vec.lastIndexOf(100):0
vec.firstElement():100
vec.elementAt(2):300
vec.lastElement():5
size:6
capacity:10
vec 2 to 4:[2, 300, 3]
nextElement():100
nextElement():2
nextElement():300
nextElement():3
nextElement():4
nextElement():5
vec.retain():true
vec:[100, 300]
str:100
str:300
vec.isEmpty():true
```