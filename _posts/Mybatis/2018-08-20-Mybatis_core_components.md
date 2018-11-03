---
layout: blog
project: false
istop: false
background-image: http://monhitul.cn:8080/blogimages/天台1.jpg
category: Mybatis
title: Mybatis的核心组件
tags:
- Mybatis
redirect_from:
  - /2018/08/20/Mybatis_core_components/
---

# 总述

Mybatis的核心组件分为四个部分，分别是：

``SqlSessionFactoryBuilder``：构造器，它会根据配置或代码来生成SqlSessionFactory，采用的是分布构建的Builder模式。

``SqlSessionFactory``：工厂接口，依靠它来生成SqlSession，使用的是工厂模式。

``SqlSession``：会话，一个既可以发送SQL执行返回结果，也可以获取Mapper的接口。在现有技术中，一般会让其在业务逻辑代码中“消失”，而使用的是Mybatis提供的SQL Mapper接口编程技术，它能提高代码的可读性和可维护性。

``SQL Mapper``：映射器，Mybatis新设计存在的组件，它由一个**Java接口和XML文件**（或注解）构成，需要给出对应的SQL和映射规则。它负责发送SQL去执行，并返回结果。

**注意，无论是映射器还是SqlSession，都可以发送SQL到数据库执行。**

# SqlSessionFactory（工厂接口）

在Mybatis中，既可以通过读取配置的XML文件的形式，也可以通过Java代码的形式去生成SqlSessionFactory。建议采用XML的形式，因为代码的方式在需要修改的时候会比较麻烦。

在配置XML或者提供代码后，Mybatis会读取配置文件，通过一个类对象``Configuration``构建整个Mybatis的上下文。**SqlSessionFactory是一个接口**，存在两个实现类：``SqlSessionManager``和``DefaultSqlSessionFactory``，一般而言，具体是由``DefaultSqlSessionFactory``实现的，而``SqlSessionManager``使用在多线程的环境中，它的具体实现依靠``DefaultSqlSessionFactory``，它们之间关系如下：
<img src="/style/images/passage/mybatis_core_components/sessionFactory.PNG">
SqlSessionFactory唯一的作用就是生产Mybatis的核心对象SqlSession，往往采用单例模式处理塔。

## 使用XML创建SqlSessionFactory

首先，在Mybatis中的XML分为两类，一类是***基础配置文件***，通常只有一个，主要是配置一些最基本的上下文参数和运行环境；另一类是***映射文件***，可以配置映射关系、SQL、参数等信息。

以下为一个简易的基础配置文件，命名为``mybatis-config.xml``。
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration   PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <typeAliases><!-- 别名 -->
      <typeAlias alias="role" type="com.monhitul.mybatis_test.entity.Role"/>
  </typeAliases>
  <!-- 数据库环境 -->
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis_test"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
      </dataSource>
    </environment>
  </environments>
  <!-- 映射文件 -->
  <mappers>
    <mapper resource="com/monhitul/mybatis_test/mapper/RoleMapper.xml"/>
    <!-- <mapper class="com.monhitul.mybatis_test.mapper.RoleMapper2"/> -->
  </mappers>
</configuration>
```
在这个基础配置文件中，``<typeAlias>``元素定义了一个别名role，它代表着com.monhitul.mybatis_test.entity.Role这个类，这样定义后,在上下文中就可以使用别名去代替全限定名。

有了基础配置文件，就可以通过一段简短的代码来生成SqlSessionFactory，如下所示：
```
SqlSessionFactory sqlSessionFactory = null;
String resource = "mybatis-config.xml";
InputStream inputStream;
try{
	inputStream = Resources.getResourceAsStream(resource);
	sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
}catch(IOException e){
	e.printStackTrace();
}
```
首先读取mybatis-config.xml，然后通过SqlSessionFactoryBuilder的Builder方法去创建SqlSessionFactory。

## 使用代码创建SqlSessionFactory

主要代码如下：
```
//数据库连接池信息
PooledDataSource dataSource = new PooledDataSource();
dataSource.setDriver("com.mysql.jdbc.Driver");
dataSource.setUsername("root");
dataSource.setPassword("123456");
dataSource.setUrl("jdbc:mysql://localhost:3306/mybatis_test");
dataSource.setDefaultAutoCommit(false);

//采用MyBatis的JDBC事务方式
TransactionFactory transactionFactory = new JdbcTransactionFactory();
Environment environment = new Environment("development", transactionFactory, dataSource);

//创建Configuration对象
Configuration configuration = new Configuration(environment);

//注册一个MyBatis上下文别名
configuration.getTypeAliasRegistry().registerAlias("role", Role.class);
			
//加入一个映射器
configuration.addMapper(RoleMapper.class);
configuration.addMapper(RoleMapper2.class);
			
//使用SqlSessionFactoryBuilder构建SqlSessionFactory
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(configuration);
return sqlSessionFactory; 
```

# SqlSession

在Mybatis中，SqlSession是其核心接口。存在两个实现类：``SqlSessionManager``和``DefaultSqlSessionFactory``，``DefaultSqlSessionFactory``是单线程使用的，而
``SqlSessionManager``是在多线程环境下使用的。SqlSession的作用类似于JDBC中的Connection对象，代表着一个连接资源的启用。

SqlSession主要作用有3个：
* 获取Mapper接口
* 发送SQL给数据库
* 控制数据库事物

创建SqlSession的方法如下：
```
SqlSession sqlSession = SqlSessionFactory.openSession();
```
SqlSession控制数据库事务的方法，可参考如下：
```
//定义SqlSession
SqlSession sqlSession = null;
try{
  //打开SqlSession会话
  sqlSession = SqlSessionFactory.openSession();
  //do something...
  sqlSession.commit();//提交事务
} catch(Exeception e){
  sqlSession.rollback();//回滚事务
} finally{
  //在finally中确保资源被顺利关闭
  if(sqlSession != null){
    sqlSession.close();
  }
}
```
这里使用``commit``方法提交事务，或者使用``rollback``方法回滚事务。因为数据库的连接资源，使用后要及时关闭，若不关闭，连接资源被耗尽，整个系统就瘫痪了。

# 映射器
映射器是Mybatis中最重要、最复杂的组件，有一个接口和对应的XML文件，（或注解）组成。可以配置以下内容：
* 描述映射规则；
* 提供SQL语句，并可以配置SQL参数类型、返回类型、缓存刷新等信息；
* 配置缓存；
* 提供动态SQL。

我们先定义一个实体类，其实就是一个POJO，简单Java对象。
```
package com.monhitul.mybatis_test.entity.Role;
public class Role{
  private Long id;
  private String roleName;
  private String note;
  /** setter and getter **/
}
```

**映射器的主要作用就是将SQL查询到的结果映射为一个POJO，或者将POJO的数据插入到数据库中，并定义一些关于缓存的重要内容。**

在这里需要注意的是，需要的只是一个接口，而不是一个实现类。作为一个初学者，我还不清楚这里面相关的处理机制，只知道与Java动态代理有关，实现了接口，或许以后会说说这个。

## 使用XML实现映射器
这里有两部分，接口和XML。接口对应前面的实体类Role，实现CRUD，放到dao包。
```
package com.monhitul.mybatis_test.dao;
public interface RoleDao{
  public Role getRole(Long id);
  //其他就省略啦
}
```

大家是否还记得前面使用XML创建SqlSessionFactory的时候，配置文件中有这样一段：

```
<mapper resource="com/monhitul/mybatis_test/mapper/RoleMapper.xml"/>
```

它的作用就是引入一个XML文件，用XML方式创建映射器。

RoleMapper.xml文件如下：
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.monhutil.mybatis_test.dao.RoleDao">
  <select id="getRole" parameterType="long" resultType="role">
    select id,role_name as roleName,note from t_role where id = #{id}
  </select>
</mapper>
```
这是个很重要的文件，对数据库的操作就在这里了。

``<mapper>``元素中的属性``namespace``所对应的是一个接口的全限定名，Mybatis可以通过这个找到对应的接口。

``<select>``元素表明这是一条查询语句，还有``insert``、``update``等，这个往后讲；``parameterType="long"``说明传递给SQL的是一个long型的参数，``resultType="role"``表示返回的是一个role类型的返回值，而这个role，就是我们前面定义过的别名，在mybatis-config.xml中。

``#{id}``表示传递进去的参数。

## 使用注解实现映射器
使用注解的方式只需要一个接口，代码参考如下：

```
import com.monhitul.mybatis_test.entity.Role;
public interface RoleMapper2{
  @Select("select id,role_name as roleName,note from t_role where id=#{id}")
  public Role getRole(Long id);
}
```

**若XML和注解同时定义，则XML方式会覆盖掉注解方法。**Mybatis官方推荐使用的是XML方式。

在前面的``mybatis-config.xml``文件中，``<mapper>``中被注释的部分，对应的就是注解的方式。

## 使用Mapper接口发送SQL

SqlSession可以获取Mapper接口，通过Mapper接口发送SQL，如下：
```
RoleDao roleDao = sqlSession.getMapper(RoleDao.class);
Role role = roleDao.getRole(1L);
```
