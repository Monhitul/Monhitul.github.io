---
layout: blog
project: false
istop: true
background-image: http://monhitul.cn:8080/blogimages/暮光1.jpg
category: Linux
title: Linux的档案权限与目录配置
tags:
- Linux
redirect_from:
  - /2018/08/11/Linux_file_permissions_and_directory_configuration/
---

# 使用者和群组

Linux中，存在三种身份，``owner``，``group``，``others``。

其中，同一个用户可以隶属于多个群组，一个群组可以包含多个用户。


# Linux档案属性

使用``ls -al``可以查看所有文件目录的属性等信息。

``ls``是``list``的意思，重点在显示档案的名称和相关属性，``-al``表示列出所有档案详细的权限与属性（包含隐藏档案，就是以小数点``.``开头的）。

```
[root@VM_16_8_centos ~]# ls -al
total 60
dr-xr-x---.  7 root root 4096 Jul 27 21:51 .
dr-xr-xr-x. 19 root root 4096 Aug 11 16:50 ..
-rw-------   1 root root 1091 Aug 11 16:34 .bash_history
-rw-r--r--.  1 root root   18 Dec 29  2013 .bash_logout
-rw-r--r--.  1 root root  176 Dec 29  2013 .bash_profile
-rw-r--r--.  1 root root  176 Dec 29  2013 .bashrc
drwxr-xr-x   3 root root 4096 Apr 15 20:52 .cache
drwxr-xr-x   3 root root 4096 Apr 15 20:52 .config
-rw-r--r--.  1 root root  100 Dec 29  2013 .cshrc
-rw-------   1 root root   35 Jul 27 21:51 .lesshst
drwxr-xr-x   2 root root 4096 Jul 24 00:12 .pip
-rw-r--r--   1 root root   73 Jul 24 00:12 .pydistutils.cfg
drwx------   2 root root 4096 Apr 15 20:52 .ssh
-rw-r--r--.  1 root root  129 Dec 29  2013 .tcshrc
drwxr-xr-x   2 root root 4096 Jul 27 21:48 test
-rw-r--r--.  1 root root 2036 Aug 10 18:01 haha.log
-rw-------   1 root root    0 Jul 24 00:12 .viminfo
```

## 属性代表意思

* 第一栏代表这个档案的类型与权限(permission)，共有十个字元。

第一个字元代表这个档案是目录、档案或连结档等等：
当为``[d]``则是目录；
当为``[-]``则是档案；
若是``[l]``则表示为连结档(link file)；
若是``[b]``则表示为装置档里面的可供储存的周边设备(可随机存取装置)；
若是``[c]``则表示为装置档里面的序列埠设备，例如键盘、滑鼠(一次性读取装置)。

接下来的字元中，以三个为一组，且均为``[rwx]``的三个参数的组合。其中，``[r]``代表可读(read)、``[w]``代表可写(write)、``[x]``代表可执行(execute)。要注意的是，这三个权限的位置不会改变，如果没有权限，就会出现减号``[-]``而已。

第一组为档案拥有者可具备的权限；第二组为加入此群组之帐号的权限；第三组为非本人且没有加入本群组之其他帐号的权限。

* 第二栏表示有多少档名连结到此节点(i-node)。

每个档案都会将他的权限与属性记录到档案系统的i-node中，不过，我们使用的目录树却是使用档名来记录，因此每个档名就会连结到一个i-node啰！这个属性记录的，就是有多少不同的档名连结到相同的一个i-node号码去就是了。

* 第三栏表示这个档案(或目录)的拥有者帐号。

* 第四栏表示这个档案的所属群组。

* 第五栏为这个档案的容量大小，预设单位为bytes。

* 第六栏为这个档案的建档日期或者是最近的修改日期。

如果想要显示完整的时间格式，可以利用ls的选项，``ls -l --full-time``就能够显示出完整的时间格式了,包括年、月、日、时间。

* 第七栏为这个档案的档名

# 改变档案属性与权限

chgrp ：改变档案所属群组
chown ：改变档案拥有者
chmod ：改变档案的权限, SUID, SGID, SBIT等等的特性

## chgrp

```
[root@VM_16_8_centos ~]# chgrp [-R] dirname/filename ... 
选项与参数：
-R : 进行递回(recursive)的持续变更，亦即连同次目录下的所有档案、目录都更新成为这个群组之意。常常用在变更某一目录内所有的档案之情况。
[root@VM_16_8_centos ~]# chgrp users haha.log
```

要被改变的群组名称必须要在/etc/group档案内存在才行，否则就会显示错误！

## chown

```
[root@VM_16_8_centos ~]# chown [-R]帐号名称档案或目录 
[root@VM_16_8_centos ~]# chown [-R]帐号名称:群组名称档案或目录
选项与参数：
-R : 进行递回(recursive)的持续变更，亦即连同次目录下的所有档案都变更
[root@study ~]# chown moon haha.log
```

## chmod

可以使用数字来代表各个权限，各权限的分数为：r:4，w:2，x:1。
每种身份(owner/group/others)各自的三个权限(r/w/x)分数是需要累加的，例如当权限为``[-rwxrwx---]``时，分数是：

owner = rwx = 4+2+1 = 7 
group = rwx = 4+2+1 = 7 
others= --- = 0+0+0 = 0

所以设定权限的变更时，该档案的权限数字就是770，变更权限的指令chmod的语法是这样的：

```
[root@VM_16_8_centos ~]# chmod [-R] xyz档案或目录
选项与参数：
xyz : 就是刚刚提到的数字类型的权限属性，为rwx 属性数值的相加。
-R : 进行递回(recursive)的持续变更，亦即连同次目录下的所有档案都会变更

[root@VM_16_8_centos ~]# chmod 777 haha.log
```

### 符号类型改变档案权限

九个权限分别是``user``，``group``，``others``这三种身份，那么可以用``u, g, o``来代表三种身份的权限，另外，``a``代表all亦即全部的身份。

<table>
	<tbody>
		<tr style="text-align:center">
			<td>
				<font style="vertical-align: inherit;">
				<font style="vertical-align: inherit;">chmod</font></font></td><td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">u </font></font><br><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">g </font></font><br><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">o </font></font><br><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">a</font></font>
			</td>
			<td style="font: 11pt '細明體'"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">+(加入) </font></font><br><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">-(除去) </font></font><br><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">=(设定)</font></font>
			</td>
			<td>
				<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">r </font></font><br><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">w </font></font><br><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">x</font></font>
			</td>
			<td>
				<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">档案或目录</font></font>
			</td>
		</tr>
	</tbody>
</table>

假如要设置一个档案的权限成为``-rwxr-xr-x``时，基本上就是：

user (u)：具有可读、可写、可执行的权限；group 与others (g/o)：具有可读与执行的权限。

所以就是：

```
[root@VM_16_8_centos ~]# chmod u=rwx,go=rx haha.log
```

**注意那个``u=rwx,go=rx``是连在一起的，中间并没有任何空白字元！**

另外，如果不知道原先的档案属性，而只想要增加``haha.log``这个档案的每个人均可写入的权限，那么我就可以使用：

```
[root@VM_16_8_centos ~]# chmod a+w haha.log 
```

而如果是要将权限去掉而不更动其他已存在的权限,例如要拿掉全部人的可执行权限，则：


```
[root@VM_16_8_centos ~]# chmod a-x haha.log
```
