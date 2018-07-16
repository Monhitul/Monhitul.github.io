---
layout: blog
book: false
istop: true
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
category: Jsp&Servlet
title: JSP的编译指令
tags:
- Jsp
- 编译指令
redirect_from:
  - /2018/07/13/Jsp_compiler_directive/
---

JSP的编译指令是通知JSP引擎的消息，它不直接生成输出。

#  3个编译指令
编译指令都有默认值，开发人员无须为每个指令设置默认值。
主要介绍``page``和``include``，``taglib``用于定义和访问自定义标签，不介绍。
使用编译指令的语法格式如下：
```
<%@ 编译指令名 属性名="属性值"... %>
```
##  page指令
page指令通常位于JSP页面的顶部，一个JSP页面可以使用多条page命令。

###  指令属性
language：声明当前JSP页面使用的脚本语言的种类，因页面是JSP页面，该属性通常都是Java，默认值也是Java，所以通常无须设置。
```java
<%@ page language="Java" %>
```
extends：指定JSP页面编译所产生的Java类所继承的父类，或实现的接口。
```java
<%@ page extends="package.class" %>
```
import：用来导入包。默认自动导入，不需要显示导入的包有``java.lang.*、javax.servlet.*、javax.servlet.jsp.*、javax.servlet.http.*``。
```java
<%@ page import="package.class | package.*,..." %>
```
session：设定该JSP页面是否需要HTTP Session。
```java
<%@ page session="true | false" %>
```
buffer：指定输出缓冲区的大小。输出缓冲区的JSP内部对象``out``用于缓存JSP页面对客户浏览器的输出，默认值为8KB，可以设置为none，也可以设置为其他值，单位为KB
```java
<%@ page buffer="none | 8KB | size KB" %>
```
autoFlush：当输出缓冲区即将溢出时，是否需要强制输出缓冲区的内容，设置为``true``时为正常输出，设置为``false``时，会在buffer溢出时产生一个异常。
```java
<%@ page autoFlush="true | false" %>
```
info：设置该JSP程序的信息，也可以看做是其说明，可以通过``Servlet.getServletInfo()``方法获取该值。
```java
<%@ page info="text" %>
```
errorPage：指定错误处理页面。如果本页面产生了异常或错误，而该JSP页面没有对应的处理代码，则会自动调用该属性指定的JSP页面。
因为JSP内建了异常机制支持，所以JSP可以不处理异常，即使是checked异常。
```java
<%@ page errorPage="relativeURL" %>
```
isErrorPage：设置本JSP页面是否为错误处理程序。如果该页面本身已是错误处理页面，则通常不需要指定errorPage。
```java
<%@ page isErrorPage="true | false" %>
```
contentType：**从服务器到浏览器**，用于设定生成网页的文件格式和编码字符集，即MIME类型和页面字符集类型，默认的MIME类型是``text/html``，默认的字符集类型是``ISO-8859-1``，一般字符集类型设置为``utf-8``。
```java
<%@ page contentType="text/html;charSet=ISO-8859-1" %>
```
pageEncoding：**将jsp编译成Servlet文件**，生成网页的编码字符集。
```java
<%@ page pageEncoding="ISO-8859-1" %>
```

##  include指令
使用include指令，可以将一个外部文件嵌入到当前JSP文件中，同时解析这个页面中的JSP语句（如果有的话）。这是个静态的include语句，它会把目标页面的其他编译指令一起包含进来，但动态include则不会。

include既可以包含静态的文本，也可以包含动态的JSP页面。静态的include编译指令会将被包含的页面加入本页面，融合成一个页面，因此被包含的页面甚至不需要是一个完整的页面。

include编译指令的语法如下：
```
<@ include file="relativeURLSpec" %>

如果被嵌入的文件经常需要改变，则建议使用动态的include语句：``<jsp:include>``。

需要指出的是，静态包含会将被包含页面的编译指令也包含进来，而如果两个页面的编译指令冲突，那么页面就很出错。
