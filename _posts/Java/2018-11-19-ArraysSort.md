---
layout: blog
project: false
istop: true
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
category: Java
title: Arrays.sort()的实现
tags:
- Java
- 排序
redirect_from:
  - /2018/11/19/ArraysSort/
---

# 概述

使用``java.util.Arrays``中的``sort()``方法，对对象数组进行排序，按照属性。

# 代码

```java
package com.demo;

import java.util.Arrays;
import java.util.Comparator;

import java.util.ArrayList;

class Student{
	//为了方便，不进行封装
	int id;
	int age;
	String name;
		
	Student(int id,int age,String name){
		this.id=id;
		this.age=age;
		this.name=name;
	}

	public String toString() {
		System.out.println("Student's id = "+this.id+" ,age = "+this.age+" ,name = "+this.name);
		return null;
	}
}

public class Main {
	
	public static void main(String[] args) {
		
		//随便给点数据
		Student[] stu = new Student[6];
		stu[0] = new Student(1,25,"Mary");
		stu[1] = new Student(3,22,"Peter");
		stu[2] = new Student(6,18,"Jack");
		stu[3] = new Student(2,36,"James");
		stu[4] = new Student(9,22,"Wade");
		stu[5] = new Student(5,15,"Anna");
		
		System.out.println("原始数据：");
		for(int i=0;i<stu.length;i++) {
			stu[i].toString();
		}
		System.out.println();
		
		Arrays.sort(stu, new MyComparator(1));
		System.out.println("按id升序：");
		for(int i=0;i<stu.length;i++) {
			stu[i].toString();
		}
		System.out.println();
		
		Arrays.sort(stu, new MyComparator(2));
		System.out.println("按id降序：");
		for(int i=0;i<stu.length;i++) {
			stu[i].toString();
		}
		System.out.println();
		
		Arrays.sort(stu, new MyComparator(3));
		System.out.println("按age升序：");
		for(int i=0;i<stu.length;i++) {
			stu[i].toString();
		}
		System.out.println();
		
		Arrays.sort(stu, new MyComparator(4));
		System.out.println("按age降序：");
		for(int i=0;i<stu.length;i++) {
			stu[i].toString();
		}
		System.out.println();
		
		Arrays.sort(stu, new MyComparator(5));
		System.out.println("按name升序：");
		for(int i=0;i<stu.length;i++) {
			stu[i].toString();
		}
		System.out.println();
		
		Arrays.sort(stu, new MyComparator(6));
		System.out.println("按name降序：");
		for(int i=0;i<stu.length;i++) {
			stu[i].toString();
		}
	}
}

class MyComparator implements Comparator<Student> {
	//用以判断以什么形式排序，通过构造函数传参
	int flag;
	
	@Override
	public int compare(Student o1, Student o2) {
		switch(flag) {
			//1为：按id排序，升序
			case 1:{
				if(o1.id>o2.id)
					return 1;
				else if(o1.id<o2.id)
					return -1;
				else
					return 0;
			}
			//2为：按id排序，降序
			case 2:{
				if(o1.id>o2.id)
					return -1;
				else if(o1.id<o2.id)
					return 1;
				else
					return 0;
			}
			//3为：按age排序，升序
			case 3:{
				if(o1.age>o2.age)
					return 1;
				else if(o1.age<o2.age)
					return -1;
				else
					return 0;
			}
			//4为：按age排序，降序
			case 4:{
				if(o1.age>o2.age)
					return -1;
				else if(o1.age<o2.age)
					return 1;
				else
					return 0;
			}
			//5为：按name排序，升序
			case 5:{
				if(o1.name.compareTo(o2.name)>0)
					return 1;
				else if(o1.name.compareTo(o2.name)<0)
					return -1;
				else
					return 0;
			}
			//6为：按name排序，降序
			case 6:{
				if(o1.name.compareTo(o2.name)>0)
					return -1;
				else if(o1.name.compareTo(o2.name)<0)
					return 1;
				else
					return 0;
			}
			//默认为id升序
			default:{
				if(o1.id>o2.id)
					return 1;
				else if(o1.id<o2.id)
					return -1;
				else
					return 0;
			}
		}
		
	}
	
	MyComparator(int flag){
		this.flag=flag;
	}
}
```

运行结果如下：
```
原始数据：
Student's id = 1 ,age = 25 ,name = Mary
Student's id = 3 ,age = 22 ,name = Peter
Student's id = 6 ,age = 18 ,name = Jack
Student's id = 2 ,age = 36 ,name = James
Student's id = 9 ,age = 22 ,name = Wade
Student's id = 5 ,age = 15 ,name = Anna

按id升序：
Student's id = 1 ,age = 25 ,name = Mary
Student's id = 2 ,age = 36 ,name = James
Student's id = 3 ,age = 22 ,name = Peter
Student's id = 5 ,age = 15 ,name = Anna
Student's id = 6 ,age = 18 ,name = Jack
Student's id = 9 ,age = 22 ,name = Wade

按id降序：
Student's id = 9 ,age = 22 ,name = Wade
Student's id = 6 ,age = 18 ,name = Jack
Student's id = 5 ,age = 15 ,name = Anna
Student's id = 3 ,age = 22 ,name = Peter
Student's id = 2 ,age = 36 ,name = James
Student's id = 1 ,age = 25 ,name = Mary

按age升序：
Student's id = 5 ,age = 15 ,name = Anna
Student's id = 6 ,age = 18 ,name = Jack
Student's id = 9 ,age = 22 ,name = Wade
Student's id = 3 ,age = 22 ,name = Peter
Student's id = 1 ,age = 25 ,name = Mary
Student's id = 2 ,age = 36 ,name = James

按age降序：
Student's id = 2 ,age = 36 ,name = James
Student's id = 1 ,age = 25 ,name = Mary
Student's id = 9 ,age = 22 ,name = Wade
Student's id = 3 ,age = 22 ,name = Peter
Student's id = 6 ,age = 18 ,name = Jack
Student's id = 5 ,age = 15 ,name = Anna

按name升序：
Student's id = 5 ,age = 15 ,name = Anna
Student's id = 6 ,age = 18 ,name = Jack
Student's id = 2 ,age = 36 ,name = James
Student's id = 1 ,age = 25 ,name = Mary
Student's id = 3 ,age = 22 ,name = Peter
Student's id = 9 ,age = 22 ,name = Wade

按name降序：
Student's id = 9 ,age = 22 ,name = Wade
Student's id = 3 ,age = 22 ,name = Peter
Student's id = 1 ,age = 25 ,name = Mary
Student's id = 2 ,age = 36 ,name = James
Student's id = 6 ,age = 18 ,name = Jack
Student's id = 5 ,age = 15 ,name = Anna
```

# 结尾

好好学习，天天向上~