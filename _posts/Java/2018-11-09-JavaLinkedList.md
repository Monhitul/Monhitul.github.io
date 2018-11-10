---
layout: blog
project: false
istop: true
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
category: Java
title: Java集合之LinkedList
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

# LinkedList的继承关系

<img src="/style/images/passage/JavaLinkedList/继承关系.PNG">

LinkedList的本质是双向链表。

1 LinkedList继承于AbstractSequentialList，并且实现了Dequeue接口。
2 LinkedList包含两个重要的成员：header 和 size。

header 是双向链表的表头，它是双向链表节点所对应的类Entry的实例。Entry中包含成员变量： previous, next, element。其中，previous是该节点的上一个节点，next是该节点的下一个节点，element是该节点所包含的值。

size是双向链表中节点的个数。

# LinkedList遍历方式

LinkedList支持多种遍历方式。建议不要采用随机访问的方式去遍历LinkedList，而采用逐个遍历的方式。

1 第一种，通过迭代器遍历。即通过Iterator去遍历。
```java
for(Iterator iter = list.iterator(); iter.hasNext();)
    iter.next();
```
2 通过快速随机访问遍历LinkedList
```java
int size = list.size();
for (int i=0; i<size; i++) {
    list.get(i);        
}
```
3 通过另外一种for循环来遍历LinkedList
```java
for (Integer integ:list) 
    ;
```
4 通过pollFirst()来遍历LinkedList
```java
while(list.pollFirst() != null)
    ;
```
5 通过pollLast()来遍历LinkedList
```java
while(list.pollLast() != null)
    ;
```
6 通过removeFirst()来遍历LinkedList
```java
try {
    while(list.removeFirst() != null)
        ;
} catch (NoSuchElementException e) {
}
```
7 通过removeLast()来遍历LinkedList
```java
try {
    while(list.removeLast() != null)
        ;
} catch (NoSuchElementException e) {
}
```

遍历LinkedList时，使用removeFist()或removeLast()效率最高。但用它们遍历时，会删除原始数据；若单纯只读取，而不删除，应该使用第3种遍历方式。
**无论如何，千万不要通过随机访问去遍历LinkedList！**

# LinkedList示例

```java
import java.util.List;
import java.util.Iterator;
import java.util.LinkedList;
import java.util.NoSuchElementException;

public class LinkedListTest {
    public static void main(String[] args) {
        // 测试LinkedList的API
        testLinkedListAPIs() ;

        // 将LinkedList当作 LIFO(后进先出)的堆栈
        useLinkedListAsLIFO();

        // 将LinkedList当作 FIFO(先进先出)的队列
        useLinkedListAsFIFO();
    }
    
    /*
     * 测试LinkedList中部分API
     */
    private static void testLinkedListAPIs() {
        String val = null;
        //LinkedList llist;
        //llist.offer("10");
        // 新建一个LinkedList
        LinkedList llist = new LinkedList();
        //---- 添加操作 ----
        // 依次添加1,2,3
        llist.add("1");
        llist.add("2");
        llist.add("3");

        // 将“4”添加到第一个位置
        llist.add(1, "4");
        

        System.out.println("\nTest \"addFirst(), removeFirst(), getFirst()\"");
        // (01) 将“10”添加到第一个位置。  失败的话，抛出异常！
        llist.addFirst("10");
        System.out.println("llist:"+llist);
        // (02) 将第一个元素删除。        失败的话，抛出异常！
        System.out.println("llist.removeFirst():"+llist.removeFirst());
        System.out.println("llist:"+llist);
        // (03) 获取第一个元素。          失败的话，抛出异常！
        System.out.println("llist.getFirst():"+llist.getFirst());


        System.out.println("\nTest \"offerFirst(), pollFirst(), peekFirst()\"");
        // (01) 将“10”添加到第一个位置。  返回true。
        llist.offerFirst("10");
        System.out.println("llist:"+llist);
        // (02) 将第一个元素删除。        失败的话，返回null。
        System.out.println("llist.pollFirst():"+llist.pollFirst());
        System.out.println("llist:"+llist);
        // (03) 获取第一个元素。          失败的话，返回null。
        System.out.println("llist.peekFirst():"+llist.peekFirst());
    

        System.out.println("\nTest \"addLast(), removeLast(), getLast()\"");
        // (01) 将“20”添加到最后一个位置。  失败的话，抛出异常！
        llist.addLast("20");
        System.out.println("llist:"+llist);
        // (02) 将最后一个元素删除。        失败的话，抛出异常！
        System.out.println("llist.removeLast():"+llist.removeLast());
        System.out.println("llist:"+llist);
        // (03) 获取最后一个元素。          失败的话，抛出异常！
        System.out.println("llist.getLast():"+llist.getLast());


        System.out.println("\nTest \"offerLast(), pollLast(), peekLast()\"");
        // (01) 将“20”添加到第一个位置。  返回true。
        llist.offerLast("20");
        System.out.println("llist:"+llist);
        // (02) 将第一个元素删除。        失败的话，返回null。
        System.out.println("llist.pollLast():"+llist.pollLast());
        System.out.println("llist:"+llist);
        // (03) 获取第一个元素。          失败的话，返回null。
        System.out.println("llist.peekLast():"+llist.peekLast());

         

        // 将第3个元素设置300。不建议在LinkedList中使用此操作，因为效率低！
        llist.set(2, "300");
        // 获取第3个元素。不建议在LinkedList中使用此操作，因为效率低！
        System.out.println("\nget(3):"+llist.get(2));


        // ---- toArray(T[] a) ----
        // 将LinkedList转行为数组
        String[] arr = (String[])llist.toArray(new String[0]);
        for (String str:arr) 
            System.out.println("str:"+str);

        // 输出大小
        System.out.println("size:"+llist.size());
        // 清空LinkedList
        llist.clear();
        // 判断LinkedList是否为空
        System.out.println("isEmpty():"+llist.isEmpty()+"\n");

    }

    /**
     * 将LinkedList当作 LIFO(后进先出)的堆栈
     */
    private static void useLinkedListAsLIFO() {
        System.out.println("\nuseLinkedListAsLIFO");
        // 新建一个LinkedList
        LinkedList stack = new LinkedList();

        // 将1,2,3,4添加到堆栈中
        stack.push("1");
        stack.push("2");
        stack.push("3");
        stack.push("4");
        // 打印“栈”
        System.out.println("stack:"+stack);

        // 删除“栈顶元素”
        System.out.println("stack.pop():"+stack.pop());
        
        // 取出“栈顶元素”
        System.out.println("stack.peek():"+stack.peek());

        // 打印“栈”
        System.out.println("stack:"+stack);
    }

    /**
     * 将LinkedList当作 FIFO(先进先出)的队列
     */
    private static void useLinkedListAsFIFO() {
        System.out.println("\nuseLinkedListAsFIFO");
        // 新建一个LinkedList
        LinkedList queue = new LinkedList();

        // 将10,20,30,40添加到队列。每次都是插入到末尾
        queue.add("10");
        queue.add("20");
        queue.add("30");
        queue.add("40");
        // 打印“队列”
        System.out.println("queue:"+queue);

        // 删除(队列的第一个元素)
        System.out.println("queue.remove():"+queue.remove());
    
        // 读取(队列的第一个元素)
        System.out.println("queue.element():"+queue.element());

        // 打印“队列”
        System.out.println("queue:"+queue);
    }
}
```

运行结果：
```
Test "addFirst(), removeFirst(), getFirst()"
llist:[10, 1, 4, 2, 3]
llist.removeFirst():10
llist:[1, 4, 2, 3]
llist.getFirst():1

Test "offerFirst(), pollFirst(), peekFirst()"
llist:[10, 1, 4, 2, 3]
llist.pollFirst():10
llist:[1, 4, 2, 3]
llist.peekFirst():1

Test "addLast(), removeLast(), getLast()"
llist:[1, 4, 2, 3, 20]
llist.removeLast():20
llist:[1, 4, 2, 3]
llist.getLast():3

Test "offerLast(), pollLast(), peekLast()"
llist:[1, 4, 2, 3, 20]
llist.pollLast():20
llist:[1, 4, 2, 3]
llist.peekLast():3

get(3):300
str:1
str:4
str:300
str:3
size:4
isEmpty():true


useLinkedListAsLIFO
stack:[4, 3, 2, 1]
stack.pop():4
stack.peek():3
stack:[3, 2, 1]

useLinkedListAsFIFO
queue:[10, 20, 30, 40]
queue.remove():10
queue.element():20
queue:[20, 30, 40]
```