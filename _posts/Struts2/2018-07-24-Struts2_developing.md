---
layout: blog
project: false
istop: true
background-image: http://monhitul.cn:8080/blogimages/超凡1.jpg
category: Struts2
title: Struts2的开发过程
tags:
- Struts2
redirect_from:
  - /2018/07/24/Struts2_developing/
---

# 总述

Struts2的开发流程为：

1. 在web.xml文件中定义核心Filter拦截用户请求；

2. 定义处理用户请求的Action类，并对其进行配置；

3. 配置处理结果和物理资源视图之间的对应关系；

4. 编写视图资源。

*Struts2版本为2.5.14.1*

## 定义Filter过滤器

```
<filter>
    <filter-name>struts2</filter-name>
    <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>struts2</filter-name>
    <!-- 拦截所有请求 -->
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

``filter-name``应保持一致，

## 定义Action类并进行配置

### 定义

在``com.action``包下定义，例子：

```
//学生Action类
public class StudentsAction extends ActionSupport {
    //默认的调用方法
    public String execute(){
        ...
        return SUCCESS;
    }

    //查询所有学生的动作
    public String query(){
        ...
        return "query_success";
    }

    //删除学生动作
    public String delete(){
        ...
        return "delete_success";
    }

    //添加学生动作
    public String add(){
        ...
        return "add_success";
    }

    //修改学生资料动作
    public String modify(){
        ...
        return "modify_success";
    }

    //修改学生资料保存动作
    public String save(){
        ...
        return "save_success";
    }
}
```

其中Action类必须继承ActionSupport父类，``query``、``delete``等为用户自定义的方法。

### 配置

具体配置过程见 <a style="font-style:italic;" href="../15/configure_action.html">Struts2 | 配置Action</a>。

``struts.xml``应位于src目录下，如下：

```
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
        "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>

    <constant name="struts.devMode" value="true" />
    <constant name="struts.enable.DynamicMethodInvocation" value="true" />
    
    <package name="Users" namespace="/users" extends="struts-default">
        <action name="*_*" class="com.action.{1}Action" method="{2}">
            <result name="login_success">/users/Users_login_success.jsp</result>
            <result name="login_failure">/users/Users_login.jsp</result>
            <result name="logout_success">/users/Users_login.jsp</result>
            <allowed-methods>login,logout</allowed-methods>
            <!--<allowed-methods>logout</allowed-methods>-->
        </action>
    </package>

    <package name="Students" namespace="/students" extends="struts-default">
        <action name="*_*" class="com.action.{1}Action" method="{2}">
            <result name="Students_query_success">/students/Students_query_success.jsp</result>
            <result name="delete_success" type="chain">Students_query</result>
            <result name="add_success">/students/Students_add_success.jsp</result>
            <result name="modify_success">/students/Students_modify.jsp</result>
            <result name="save_success">/students/Students_modify_success.jsp</result>
            <allowed-methods>query,delete,add,modify,save</allowed-methods>
        </action>
    </package>

</struts>
```

**所有``package``必须直接或间接继承``struts-deafault``**

**重要：自定义动态方法，必须加上``allowed-methods``属性！**

``action``中，class为定义的Action类；method为自定义的调用方法，不写则默认为``execute``。

## 配置对应关系

见上图中的``result``。

``result``中，``name``为自定义调用方法中所返回的String字符串，不写则默认为``SUCCESS``;``type``属性指定结果类型，默认为``dispatcher``类型，表示指定JSP作为视图的结果类型；当``type=chain``时，是Action链式处理的结果类型，表示调用Action类。还有``plainText``、``redirect``、``redirectAction``等类型，暂不考虑。

JSP页面中，如下：

```
<form name="addForm" action="/students/Students_add.action" method="post">
<table>
  ...
</table>
</form>
```

其中``action``对应``struts.xml``中``action``的``name``属性。

## 编写视图资源

嗯，如题。