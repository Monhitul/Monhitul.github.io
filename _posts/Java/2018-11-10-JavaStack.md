---
layout: blog
project: false
istop: true
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
category: Java
title: Java集合之Stack
tags:
- Java
- 集合框架
redirect_from:
  - /2018/11/09/JavaStack/
---

参考来源：
[1]http://www.cnblogs.com/skywang12345/p/3308852.html

# 概述

Stack是栈。它的特性是：先进后出(FILO, First In Last Out)。

Java工具包中的Stack是继承于Vector(矢量队列)的，由于Vector是通过数组实现的，这就意味着，Stack也是通过数组实现的，而非链表。

当然，我们也可以将LinkedList当作栈来使用！

## Stack的继承关系

<img src="/style/images/passage/JavaStack/继承关系.PNG">

## Stack的构造函数

Stack只有一个默认构造函数，如下：
```java
Stack()
```
 
## Stack的API

Stack是栈，它常用的API如下：
```java
             boolean       empty()
synchronized E             peek()
synchronized E             pop()
             E             push(E object)
synchronized int           search(Object o)
```
由于Stack和继承于Vector，因此它也**包含Vector中的全部API。**

## 小结

1 Stack实际上也是通过数组去实现的。

执行push时(即，将元素推入栈中)，是通过将元素追加的数组的末尾中。

执行peek时(即，取出栈顶元素，不执行删除)，是返回数组末尾的元素。

执行pop时(即，取出栈顶元素，并将该元素从栈中删除)，是取出数组末尾的元素，然后将该元素从数组中删除。

2 Stack继承于Vector，意味着Vector拥有的属性和功能，Stack都拥有。

# Stack示例

```java
import java.util.Stack;
import java.util.Iterator;
import java.util.List;

/**
 * @desc Stack的测试程序。测试常用API的用法
 *
 * @author skywang
 */
public class StackTest {

    public static void main(String[] args) {
        Stack stack = new Stack();
        // 将1,2,3,4,5添加到栈中
        for(int i=1; i<6; i++) {
            stack.push(String.valueOf(i));
        }

        // 遍历并打印出该栈
        iteratorThroughRandomAccess(stack) ;

        // 查找“2”在栈中的位置，并输出
        int pos = stack.search("2");
        System.out.println("the postion of 2 is:"+pos);

        // pup栈顶元素之后，遍历栈
        stack.pop();
        iteratorThroughRandomAccess(stack) ;

        // peek栈顶元素之后，遍历栈
        String val = (String)stack.peek();
        System.out.println("peek:"+val);
        iteratorThroughRandomAccess(stack) ;

        // 通过Iterator去遍历Stack
        iteratorThroughIterator(stack) ;
    }

    /**
     * 通过快速访问遍历Stack
     */
    public static void iteratorThroughRandomAccess(List list) {
        String val = null;
        for (int i=0; i<list.size(); i++) {
            val = (String)list.get(i);
            System.out.print(val+" ");
        }
        System.out.println();
    }

    /**
     * 通过迭代器遍历Stack
     */
    public static void iteratorThroughIterator(List list) {

        String val = null;
        for(Iterator iter = list.iterator(); iter.hasNext(); ) {
            val = (String)iter.next();
            System.out.print(val+" ");
        }
        System.out.println();
    }

}
```

结果如下：

```
1 2 3 4 5 
the postion of 2 is:4
1 2 3 4 
peek:4
1 2 3 4 
1 2 3 4 
```