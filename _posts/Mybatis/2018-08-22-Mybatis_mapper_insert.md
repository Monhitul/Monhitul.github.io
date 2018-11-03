---
layout: blog
project: false
istop: false
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
category: Mybatis
title: Mybatis | 映射器之insert，update和delete
tags:
- Mybatis
- insert
- update
- delete
redirect_from:
  - /2018/08/22/Mybatis_mapper_insert_update_delete/
---

# insert元素

在Mybatis中insert语句可以配置以下属性：

<table>
  <tr>
    <td>属性</td>
    <td>说明</td>
    <td>备注</td>
  </tr>
  <tr>
    <td>id</td>
    <td>SQL编号，用于标示这条SQL</td>
    <td>命名空间,id和databaseId唯一，否则Mybatis将抛出异常</td>
  </tr>
  <tr>
    <td>parameterType</td>
    <td>可以给出类的全命名，也可以给出别名，但是别名必须是Mybatis内部定义或者自定义的</td>
    <td>可以选择Java Bean、Map等简单的参数类型传递给SQL</td>
  </tr>
  <tr>
    <td>flushCache</td>
    <td>是否刷新缓存，取值为true时，插入时会刷新一级和二级缓存，否则不刷新</td>
    <td>取值为布尔值，默认值为true</td>
  </tr>
  <tr>
    <td>timeout</td>
    <td>设置超时参数，超时将抛出异常，单位为秒</td>
    <td>默认值是数据库厂商提供的JDBC驱动所设置的秒数</td>
  </tr>
  <tr>
    <td>statementType</td>
    <td>告诉Mybatis使用哪个JDBC的Statement工作</td>
    <td>取值为STSTEMENT(Statement)、PREPARED(PreparedStatement)、CALLABLE(CallableStatement)，默认为PREPARED</td>
  </tr>
  <tr>
    <td>useGeneratedKeys</td>
    <td>是否开启数据库内部生成的自增主键</td>
    <td>默认值为false</td>
  </tr>
  <tr>
    <td>keyProperty</td>
    <td>仅对insert和update有用，唯一标记一个属性，如果是复合主键，则每个名称用逗号隔开</td>
    <td>默认值为unset，不能和keyColumn连用</td>
  </tr>
  <tr>
    <td>keyColumn</td>
    <td>仅对insert和update有用，通过生成的键值设置表中的列名，如果是复合主键，则每个名称用逗号隔开</td>
    <td>不能和keyProperty连用</td>
  </tr>
  <tr>
    <td>databaseId</td>
    <td>与databaseIdProvider有关</td>
    <td>提供多种数据库的支持</td>
  </tr>
</table>

**Mybatis在执行完一条insert语句后，会返回一个整数表示其影响的记录数。**

## 一个简单的insert例子

```
<insert id="insertRole" parameterType="role">
    insert into t_role(role_name,note) values(#{roleName}, #{note})
</insert>
```
没有配置的属性将采取默认值。

## 主键回填

MySQL数据库中，我们表格采用了自增主键，在插入语句后，我们可以获得主键的值。

JDBC中的Statement对象在执行插入的SQL后，可以通过``getGeneratedKeys``方法获得数据库生成的主键（需要数据库驱动支持）。在insert语句中的属性``useGeneratedKeys``用来控制是否打开这个功能，默认值为false。当打开这个开关后，还要配置属性``keyProperty``或``keyColumn``，告诉系统把生成的主键放入哪个属性，如果存在多个主键，则要用逗号(,)隔开。代码如下：
```
<insert id="insertRole" parameterType="role" useGeneratedKeys="true" keyProperty="id">
    insert into t_role(role_name,note) values(#{roleName}, #{note})
</insert>
```
这里的``keyProperty``代表将用POJO的哪个属性去匹配这个主键，说明它会用数据库生成的主键去赋值给这个POJO的属性id。

## 自定义主键

Mybatis允许自定义键值的生成规则，主要依赖于``selectKey``元素进行支持。代码如下：
```
<insert id="insertRole" parameterType="role">
  <selectKey keyProperty="id" resultType="long" order-"BEFORE">
    select if(max(id)=null),1,max(id)+3) from t_role
  </selectKey>
    insert into t_role(id,role_name,note) values(#{id}, #{roleName}, #{note})
</insert>
```
这里``selectKey``元素的``keyProperty``属性指定了采用哪个属性作为POJO的主键；``order``设置为``BEFORE``说明它将于当前定义的SQL前执行，会在插入之前先执行生成主键的SQL，然后再插入数据。

# update元素和delete元素

update和delete元素的使用代码如下：
```
<update id="updateRole" parameterType="role">
    update t_role set role_name = #{roleName}, note =  #{note} where id = #{id}
</update>

<delete id="deleteRole" parameterType="long">
    delete from t_role where id = #{id}
</update>
```
**与insert元素一样，update和delete在执行完也会返回一个整数，用以标示该SQL语句影响了数据库的记录行数。**

# sql元素

sql元素的作用在于可以定义一条SQL的一部分，方便后面的SQL引用它，在Mybatis中，只需要用sql元素编写一次便能在其他元素中引用它。代码如下：
```
<mapper namespace="com.monhitul.mybatis_test.dao.RoleDao">

  <sql id="roleCols">
    id, role_name, note
  </sql>

  <select id="getRole" parameterType="long" resultType="com.monhitul.mybatis_test.entity.Role">
    select <include refid="roleCols"/> from t_role where id = #{id}
  </select>
</mapper>
```
可以看到通过``include``元素可以将定义的sql元素引入到其他SQL中。

sql元素还支持变量传递，代码如下：
```
<sql id="roleCols">
  ${alias}.id, ${alias}.role_name, ${alias}.note
</sql>

<select id="getRole" parameterType="long" resultType="com.monhitul.mybatis_test.entity.Role">
  select 
  <include refid="roleCols">
    <property name="alias" value="r"/>
  </indlude>
  from t_role where id = #{id}
</select>
```
在``include``元素中定义一个命名为``alias``的变量，其值为SQL中表t_role的别名r，然后sql元素就能使用这个变量名了。
