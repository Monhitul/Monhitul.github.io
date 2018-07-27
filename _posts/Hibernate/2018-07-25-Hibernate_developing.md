---
layout: blog
project: false
istop: true
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-8-1/24280498.jpg
category: Hibernate
title: Hibernate的开发过程
tags:
- Hibernate
redirect_from:
  - /2018/07/25/Hibernate_developing/
---

# 总述

Hibernate的开发流程为：

1. 创建配置文档hibernate.cfg.xml；

2. 创建持久化类；

3. 创建数据库文件；

4. 创建对象-关系映射文件；

5. 访问数据库

*Hibernate版本为5.3.2*

## 创建hibernate.cfg.xml

```
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!--数据库连接url配置-->
        <property name="connection.url">jdbc:mysql://127.0.0.1:3306/database_name?useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=GMT%2B8</property>
        <!--数据库驱动配置-->
        <property name="connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <!--数据库用户名配置-->
        <property name="connection.username">username</property>
        <!--数据库密码配置-->
        <property name="connection.password">password</property>
        <!--数据库方言-->
        <property name="dialect">org.hibernate.dialect.MySQL5Dialect</property>

        <property name="show_sql">true</property>
        <property name="format_sql">true</property>
        <property name="hbm2ddl.auto">update</property>
        <!--使用getCurrentSession方式打开会话，若使用openSession方式则不需要配置该常量-->
        <property name="hibernate.current_session_context_class">thread</property>

        <!-- 查询翻译器 -->
        <!--<property name="hibernate.query.factory_class">org.hibernate.hql.ast.ASTQueryTranslatorFactory</property>-->

        <!--要用斜杆-->
        <!--将hbm文件包含进来-->
        <mapping resource="com/entity/Students.hbm.xml"/>
        <mapping class="com.entity.Students"/>
        <mapping resource="com/entity/Users.hbm.xml"/>
        <mapping class="com.entity.Users"/>
    </session-factory>
</hibernate-configuration>
```

## 创建持久化类

创建实体类，对应数据库，如：

```
package com.entity;

import java.util.Date;

//学生类
public class Students {
    private String sid;//学号
    private String sname;
    private String gender;//性别
    private Date birthday;
    private String address;

    public Students() {
    }

    public Students(String sid, String sname, String gender, Date birthday, String address) {
        this.sid = sid;
        this.sname = sname;
        this.gender = gender;
        this.birthday = birthday;
        this.address = address;
    }

    public String getSid() {
        return sid;
    }

    public void setSid(String sid) {
        this.sid = sid;
    }

    public String getSname() {
        return sname;
    }

    public void setSname(String sname) {
        this.sname = sname;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Students [sid="+sid+",sname="+sname+",gender="+gender+",birthday="+birthday+",address="+address+"]";
    }
}

```

该类按JavaBean遵循的基本原则：
1. 公有的类

2. 提供公用的不带参数的默认的构造方法

3. 属性私有

4. 属性setter/getter封装

## 创建数据库文件

数据库名称应与``hibernate.cfg.xml``中配置的保持一致。

一个实体类对应一张表单，属性相对应。

<img src="/style/images/passage/Hibernate_developing_database.png">

## 创建对象-关系映射文件

数据库创建完成后，需要创建映射文件，即实体类对应数据库。

```xml
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-mapping PUBLIC
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>

    <class name="com.entity.Students" table="students" schema="students_manager">
        <id name="sid" column="sid" type="java.lang.String">
            <generator class="assigned"/>
        </id>
        <property name="sname" column="sname" type="java.lang.String"/>
        <property name="gender" column="gender" type="java.lang.String"/>
        <property name="birthday" column="birthday" type="date"/>
        <property name="address" column="address" type="java.lang.String"/>
    </class>
</hibernate-mapping>
```

一个实体类对应一个hbm文件，而且必须在同一个目录下。

其中``name``对应实体类中的属性名称，``column``对应数据表中的属性名称，``type``为数据类型，可写成Java或数据库中的数据类型。

``id``对应主键，其中``<generator class="assigned"/>``，``assigned``表示手工赋值，也可以为``native``，表示自动增长，相当于``auto-increment``，不用手工赋值。

hbm文件创建完成后，需包含进去cfg文件中。

## 访问数据库

流程如下：

- 创建Configuration对象

- 创建sessionFactory对象

- 创建session对象

- 开启TransAction事务

- 开始访问

- 提交事务

```java
//创建Configuration对象
Configuration config=new Configuration().configure();
//创建sessionFactory对象
SessionFactory sessionFactory=config.buildSessionFactory();
//创建session对象，也可用openSession方式
Session session=sessionFactory.getCurrentSession();
//开启TransAction事务
Transaction tx=session.beginTransaction();

Students s=Students("10001","lisi","男",new Date(),"666宿舍楼");
session.save(s);

//提交事务
tx.commit();
```

创建Session对象，可用openSession方式，也可用getCurrentSession方式，差别在 ``openSession``每次创建都是创建新的Session对象，``getCurrentSession``每次创建的都是同一个对象。