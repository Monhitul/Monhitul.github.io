---
layout: blog
project: false
istop: false
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
category: Mybatis
title: Mybatis | 映射器之select
tags:
- Mybatis
- select
redirect_from:
  - /2018/08/22/Mybatis_mapper_select/
---

# 总述

Mybatis中的映射器在实际开发中工作量占比很大，这篇记录的是映射器的配置元素中的``select``。

# 配置介绍

在SQL中，select语句是用的最多的语句，在Mybatis中，select元素也是用的最多的元素。

select的配置有：

<table>
  <tr>
    <td>元素</td>
    <td>说明</td>
    <td>备注</td>
  </tr>
  <tr>
    <td>id</td>
    <td>它和Mapper的命名空间组合起来是唯一的，供Mybatis调用</td>
    <td>如果命名空间和id结合起来不唯一，Mybatis将抛出异常</td>
  </tr>
  <tr>
    <td>parameterType</td>
    <td>可以给出类的全命名，也可以给出别名，但是别名必须是Mybatis内部定义或者自定义的</td>
    <td>可以选择Java Bean、Map等简单的参数类型传递给SQL</td>
  </tr>
  <tr>
    <td>resultType</td>
    <td>定义类的全路径，或定义为int、double、float、map等参数，也可以使用别名，但要符合别名规范，且不能和resultMap同时使用</td>
    <td>常用的参数之一</td>
  </tr>
  <tr>
    <td>resultMap</td>
    <td>它是映射集的引用，等提供自定义映射规则的机会</td>
    <td>Mybatis最复杂的元素，可以设置映射规则、级联、typeHandler等</td>
  </tr>
  <tr>
    <td>flushCache</td>
    <td>它的作用是在调用SQL后，是否要求Mybatis清空之前查询本地缓存和二级缓存</td>
    <td>取值为布尔值，默认值为false</td>
  </tr>
  <tr>
    <td>useCache</td>
    <td>启动二级缓存的开关，是否要求Mybatis将此次结果缓存</td>
    <td>取值为布尔值，默认值为true</td>
  </tr>
  <tr>
    <td>timeout</td>
    <td>设置超时参数，超时将抛出异常，单位为秒</td>
    <td>默认值是数据库厂商提供的JDBC驱动所设置的秒数</td>
  </tr>
  <tr>
    <td>fetchSize</td>
    <td>获取记录的总条数设定</td>
    <td>默认值是数据库厂商提供的JDBC驱动所设置的条数</td>
  </tr>
  <tr>
    <td>statementType</td>
    <td>告诉Mybatis使用哪个JDBC的Statement工作</td>
    <td>取值为STSTEMENT(Statement)、PREPARED(PreparedStatement)、CALLABLE(CallableStatement)，默认为PREPARED</td>
  </tr>
  <tr>
    <td>resultSetType</td>
    <td>对应JDBC的resultSet接口</td>
    <td>默认值是数据库厂商提供的JDBC驱动所设置的</td>
  </tr>
  <tr>
    <td>databaseId</td>
    <td>与databaseIdProvider有关</td>
    <td>提供多种数据库的支持</td>
  </tr>
  <tr>
    <td>resultOrdered</td>
    <td>适用于嵌套结果select语句</td>
    <td>取值为布尔值，默认值为false</td>
  </tr>
  <tr>
    <td>resultSets</td>
    <td>适合于多个结果集的情况</td>
    <td>很少使用</td>
  </tr>
</table>

实际中用的最多的是id、parameterType、resultType、resultMap，如果要设置缓存，还要用到flushCache、useCache，其他的都是不常用的。

# 一个简单的select例子

```
<select id="getRole" parameterType="long" resultType="com.monhitul.mybatis_test.entity.Role">
    select id,role_name as roleName, note from t_role where id = #{id}
</select>
```

这里``id``属性的``getRole``对应上一篇中的接口中的``getRole``方法。

# 自动映射和驼峰映射

在Mybatis的配置文件``mybatis-config.xml``中，``setting``元素有两个可以配置的选项：``autoMappingBehavior``和``mapUnderscoreToCamelCase``，它们是控制***自动映射***和***驼峰映射***的开关。

*在默认情况下自动映射功能是开启的。*一般自动映射使用得比较多。

配置``autoMappingBehavior``选项的取值有：
* NONE，不进行自动映射
* PARTIAL，默认值，只对没有嵌套结果集进行自动映射
* FULL，对所有的结果集进行自动映射，包括嵌套结果。

默认情况下，使用PARTIAL级别就可以了。

为了实现自动映射，先要有一个POJO，如下：
```
public class Role{
  private Long id;
  private String roleName;
  private String note;
  /*setter and getter*/
}
```
则自动映射通过id获取角色信息的代码如下：
```
<select id="getRole" parameterType="long" resultType="com.monhitul.mybatis_test.entity.Role">
    select id,role_name as roleName, note from t_role where id = #{id}
</select>
```

如果系统都严格按照驼峰命名法，比如，数据库字段为role_name，则POJO属性名为roleName；又如数据库字段为user_name，则POJO属性名为userName，那么只要在配置项中把``mapUnderscoreToCamelCase``设置为``true``即可。

驼峰映射通过id获取角色信息的代码如下：
```
<select id="getRole" parameterType="long" resultType="com.monhitul.mybatis_test.entity.Role">
    select id,role_name,note from t_role where id = #{id}
</select>
```

# 传递多个参数

前面的例子中都是一个参数传递，而实际中，不可能只有一个参数。在这里呢，假设通过角色名称(role_name)和备注(note)对角色进行模糊查询。

## 使用map接口传递参数

在Mybatis中允许map接口通过键值对传递多个参数，把接口方法定义为：
```
public List<Role> findRoleByMap(Map<String,Object> parameterMap);
```
xml文件中为：
```
<select id="findRolesByMap" parameterType="map" resultType="role">
    select id, role_name as roleName, note from t_role
    where role_name like concat('%', #{roleName}, '%')
    and note like concat('%', #{note}, '%')
</select>
```
需要注意的是，参数``roleName``、``note``要求的是map的键，也就是说，定义map如下：
```
RoleDao roleDao = sqlSession.getMapper(RoleDao.class);
Map<String,Object> parameterMap = new HashMap<String,Object>();
parameterMap.put("roleName","1");
parameterMap.put("note","2");
List<Role> roles = roleDao.findRoleByMap(parameterMap);
```
使用map接口传递参数有个弊端，那就是可读性差，因此不建议这样使用。

## 使用注解传递参数

Mybatis提供了一个注解``@Param(org.apache.ibatis.annotations.Param)``，可以通过它定义映射器的参数名称。

把接口方法定义为：
```
public List<Role> findRoleByMap(@Param("roleName") String rolename,@Param("note") String note);
```
修改映射文件的代码如下：
```
<select id="findRolesByMap" resultType="role">
    select id, role_name as roleName, note from t_role
    where role_name like concat('%', #{roleName}, '%')
    and note like concat('%', #{note}, '%')
</select>
```
这里并不用给出``parameterType``属性，让Mybatis自动探索即可。

## 使用Java Bean传递参数

先定义一个参数的POJO，如下：
```
public class RoleParams{
  private Long id;
  private String roleName;
  private String note;
  /*setter and getter*/
}
```
把接口方法定义为：
```
public List<Role> findRoleByBean(RoleParams roleParam);
```
映射文件为：
```
<select id="findRolesByBean" parameterType="com.monhitul.mybatis_test.entity.RoleParams" resultType="role">
    select id, role_name as roleName, note from t_role
    where role_name like concat('%', #{roleName}, '%')
    and note like concat('%', #{note}, '%')
</select>
```
查询代码如下：
```
RoleDao roleDao = sqlSession.getMapper(RoleDao.class);
RoleParams roleParams = new RoleParams();
roleParams.setRoleName("1");
roleParams.serNote("2");
List<Role> roles = roleDao.findRoleByBean(roleParams);
```

## 混合使用

上述的几种方法也可以混合使用，比如说，再定义一个POJO如下：
```
public class PageParams{
  private int start;
  private int limit;
  /*setter and getter*/
}
```
接口设计如下：
```
public List<Role> findByMix(@Param("params") RoleParams roleParam,@Param("page") PageParam pageParam);
```
映射文件修改如下：
```
<select id="findByMix" resultType="role">
    select id, role_name as roleName, note from t_role
    where role_name like 
    concat('%', #{params.roleName}, '%')
    and note like concat('%', #{params.note}, '%')
    limit #{page.start}, #{page.limit}
</select>
```