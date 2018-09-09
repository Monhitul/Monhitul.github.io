---
layout: blog
project: false
istop: false
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-8-1/24280498.jpg
category: SpringBoot
title: SpringBoot | 将War部署到Tomcat服务器
tags:
- SpringBoot
- Tomcat
- War
redirect_from:
  - /2018/09/09/SpringBootDeployTomcat/
---

# 总述

这次要分享的是，如何将Spring Boot打包成WAR部署到Tomcat服务器，很实用的一个东西。

# 简介

Spring Boot是一个关于配置框架的约定，它允许我们设置Spring项目的生产就绪设置，而Tomcat是最流行的Java Servlet容器之一。

默认情况下，Spring Boot构建一个独立的Java应用程序，可以作为桌面应用程序运行或配置为系统服务，但是在某些环境中我们无法安装新服务或手动运行应用程序。

与独立应用程序相反，Tomcat作为一项服务安装，可以在同一个应用程序进程中管理多个应用程序，从而无需为每个应用程序进行特定设置。

# 开始

这里就不贴实际操作图片了，跟着一步步就可以完成的，工具是IntelliJ IDEA、Maven。

首先呢，在``pom.xml``里面，将打包方式改为``war``，默认是``jar``。
```
<packaging>war</packaging>
```

然后添加Tomcat依赖。
```
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-tomcat</artifactId>
   <scope>provided</scope>
</dependency>
```

最后，要通过实现``SpringBootServletInitializer``接口来初始化Tomcat所需的Servlet上下文。
```
@SpringBootApplication
public class MonhitulExampleApplication extends SpringBootServletInitializer {
}
```

接下来就可以打包了。

<img src="/style/images/passage/springbootTomcat/Maven.PNG">

我们可以先``clean``一下，然后``package``，就会生成``war``文件在项目目录下``target``目录下，再把war文件丢到Tomcat的webapps里就行了，访问地址就是：域名+文件夹名+其他。

# 结尾

这两天就是由于这个问题踩了许多坑，前面的步骤缺一都会报404，记录下来，方便大家，方便以后。
