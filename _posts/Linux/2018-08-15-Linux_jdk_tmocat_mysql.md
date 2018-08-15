---
layout: blog
project: false
istop: true
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
category: Linux
title: Linux环境下JDK、Tomcat和MySQL的安装与配置
tags:
- Linux
- JDK
- Tomcat
- Mysql
redirect_from:
  - /2018/08/15/Linux_jdk_tmocat_mysql/
---

# 总述

Linux为CentOS 7.4 64位，JDK为jdk_10.0.2，Tomcat为9.0.10，MySQL为8.0.12。

# JDK

## 卸载OpenJDK

由于系统本身就自带OpenJDK，所以我们要卸载掉它。

使用``rpm -qa | grep java``命令可以查看系统自带的OpenJDK。

然后使用``rpm -e --nodeps java-xxx``命令卸载掉系统自带的OpenJDK。

再可以使用``rpm -qa | grep java``命令查看是否真的卸载了。

不过不知道为什么，我使用``rpm -qa | grep java``命令查看后，发现我的系统并没有自带OpenJDK，这就省却了一步。

## 创建JDK安装路径

接下来呢，创建JDK的安装路径，使用``cd /usr/local``命令切换到该目录下，使用``mkdir java``命令创建一个新的目录。

## 下载上传JDK

从官网下载JDK，选择Linux环境下的，我选的是最新的版本。

<img src="/style/images/passage/linux_jdk_tomcat_mysql/jdkDown.PNG">

然后使用FileZilla连接到你的Linux服务器，将安装包上传到root根目录下。

<img src="/style/images/passage/linux_jdk_tomcat_mysql/filezilla.PNG">

主机就是服务器的IP地址，用户名选择root，密码，端口默认是22端口。

上传后，我们可以借助PuTTY回到服务器上，可以在``/root``目录下使用``ll``命令查看，可以看到我们已经上传的安装包。

<img src="/style/images/passage/linux_jdk_tomcat_mysql/apk.PNG">

呃...Tomcat和MySQL的先忽略它们。

使用``tar –xvf jdk-10.0.2_linux-x64_bin.tar.gz –C /usr/local/java``命令将root目录下的``jdk-10.0.2_linux-x64_bin.tar.gz``文件解压到``/usr/local/java``目录下。 

这时我们进入``/usr/local/java``目录，就可以看到很熟悉的东西了。

<img src="/style/images/passage/linux_jdk_tomcat_mysql/jdk.PNG">

## 配置环境变量

接下来仍是熟悉到配置环境变量，不过跟Windows环境下的有些不同。

使用``vim /etc/profile``命令打开profile这个文件，它里面是Linux系统的一些环境变量。

然后在该文件到最后一行开始，加上

```
#set java environment 注释
JAVA_HOME=/usr/local/java/jdk-10.0.2
CLASSPATH=.:$JAVA_HOME/lib.tools.jar
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME CLASSPATH PATH
```

至于怎么加呢，这是vim的内容了，vim以后再说。在这里，打开文件后，直接输入字母``i``，就可以进入``insert``模式，再使用方向键将光标移到末行，加上上面到内容后，按``esc``退出``insert``模式，回到命令行模式，再输入``:wq``回车，表示保存并退出，这样就可以了。

<img src="/style/images/passage/linux_jdk_tomcat_mysql/vim.PNG">

OK，JDK就解决了。

# Tomcat

安装配置JDK后，自然就是Tomcat了。

跟JDK一样到步骤，先创建安装目录``/usr/local/tomcat``，然后从官网下载对应版本的Tomcat安装包，上传到服务器，然后解压到该目录下，这些跟JDK一样，不多说了。

启动跟关闭Tomcat，可以先使用``cd /usr/local/tomcat/bin``进入``bin``目录，启动的话，使用``./startup.sh``，关闭使用``shutdown.sh``。

配置Web管理账号的话，需要修改文件``conf/tomcat-users.xml``，在元素中添加帐号密码，需要指定角色。
```
vim /usr/local/tomcat/conf/tomcat-users.xml

<tomcat-users>
 <user name="admin" password="admin" roles="admin-gui,manager-gui" />
</tomcat-users>
```

至此，Tomcat就可以跑起来了，其他属性到修改，诸如端口这些，跟Windows环境下是一致的，不过是使用vim进行配置文件的修改。

# MySQL

在MySQL官网上，可以看到很多种安装方法，这里采用到是使用yum进行安装。

只要服务器联网，我们可以直接在服务器上下载MySQL安装包，毕竟MySQL的第一个版本就是Linux环境的。

## 查看发行版本

```
[root@VM_16_8_centos ~]# cat /etc/redhat-release
CentOS Linux release 7.4.1708 (Core)
```

## 下载Yum Repository

根据Linux的发行版本（CentOS、Fedora都属于红帽系），可以从mysql官方（http://dev.mysql.com/downloads/repo/yum/）获取Yum Repository。

我们先进入这个网址，MySQL官网。

<img src="/style/images/passage/linux_jdk_tomcat_mysql/mysqlyum.PNG">

选择适合我们发行版本的，并不需要下载，只是需要注意下划线处，接下来用到。

回到服务器，我们可以使用``wget -i http://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm``命令进行下载，这时划线处就是这么用的。

## 安装Yum Repository

接下来进行安装，使用``yum -y install mysql80-community-release-el7-1.noarch.rpm``命令即可。

## 安装MySQL

也是一个命令，``yum -y install mysql-community-server``，然后就耐心等等。

## 启动数据库

使用``systemctl start  mysqld.service``即可启动MySQL。

MySQL进程会自动在进程日志中打印root用户的初始密码，仔细找就OK了。

## 修改root用户密码

使用初始密码登录后，如果不修改密码的话，什么都做不了，那就修改密码吧。

使用``ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';``即可修改密码，然鹅。。。

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
```

导致这个问题呢，是因为不知道从哪个版本开始，MySQL要求密码强度不能太低，这样的话，换一个复杂点的密码，大小写字母加数字加标点符号，成功解决。

修改密码成功后呢，我们可以这样查看：
```
mysql> show variables like 'validate_password%';
+--------------------------------------+--------+
| Variable_name                        | Value  |
+--------------------------------------+--------+
| validate_password.check_user_name    | ON     |
| validate_password.dictionary_file    |        |
| validate_password.length             | 8      |
| validate_password.mixed_case_count   | 1      |
| validate_password.number_count       | 1      |
| validate_password.policy             | MEDIUM |
| validate_password.special_char_count | 1      |
+--------------------------------------+--------+
7 rows in set (0.00 sec)
```
可以看到对密码的要求。

至此，MySQL的安装配置也OK了。

JDK+Tomcat+MySQL三件套搭建成功，大家可以开心地去开发了。