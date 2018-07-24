---
layout: blog
project: false
istop: true
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-8-1/24280498.jpg
category: Struts2
title: 配置Action
tags:
- Struts2
- Action
redirect_from:
  - /2018/07/15/configure_action/
---

实现Action处理类后，需要在``struts.xml``文件中配置该Action。配置Action就是让Struts2知道哪个Action处理哪个请求，也就是完成用户请求和Action之间的对应关系。可以认为，Action是Struts2的基本“程序单位”。

#  包和命名空间
Struts2使用包来组织Action，因此，将Action定义放在包定义下完成，定义Action通过使用``<package.../>``下的``<action.../>``子元素来完成，而每个``package``元素配置一个包。

定义``<package.../>``元素时可以指定以下几个属性。

**name**：这是一个必需属性，该属性指定该包的名字，该名字是该包被其他包引用时的key。

**extends**：这是一个可选属性，该属性指定该包继承其他包，继承其他包可以继承其中的Action定义、拦截器定义等。

**namespace**：这是一个可选属性，该属性定义该包的命名空间。同一个命名空间里不能有同名的Action，不同的命名空间里可以有同名的Action。如果配置时没有指定该属性，则该包下的所有Action处于默认的包空间下。

**默认命名空间为``namespace=""``，可以处理任何命名空间下的Action请求。**

Action处理的URL应该是命名空间+Action名。``namespace="/"``为指定根命名空间。

**abstract**：这是一个可选属性，该属性指定该包是否是一个抽象包，抽象包中不能包含Action定义。

**注意：因为Struts2的配置文件是从上到下处理的，所以父包必须在子包前面定义。**

#  Action的基本配置

Action的属性有：

**name**：该属性既是该Action的名字，也指定了该Action所处理的请求的URL。不可省略，通常由字母和数字组成，如果需要使用斜线(/)，则需要指定Struts2允许出现Action name中出现斜线，通过设置struts.enable.SlashesInActionNames常量为“true”指定。不推荐在name中使用点(.)和中划线(-)，可能引发一些未知异常。

**class**：该属性指定了该Action的实现类。并不是必需的，如果不指定该属性，系统则默认使用系统的ActionSupport类。

**method**：**该属性指定使用哪个方法处理请求，如果省略该属性，则默认使用execute方法处理请求**。

#  使用Action的动态方法调用
如用户登录表单``<form>``中，存在两个按钮：“登录”，“注册”，分别提交给Action的不同方法处理。

表单如下：
```
<!-- action属性为 actionName!methodName 的形式，
其中ActionName指定提交到哪个Action，而methodName指定提交到指定方法-->
action="ActionName!methodName"
```
“注册”按钮触发regist函数，该函数代码如下：
```
function regist()
{
	//获取页面的第一个表单
	targetForm = document.forms[0];
	//动态修改表单的action属性，修改后实质就是将表单提交给loginAction中的regist方法处理
	targetForm.action = "loginAction!regist";
}
```
修改表单元素的action属性后，实质就是将表单提交给loginAction中的regist方法处理

对于使用动态方法调用的方法，例如regist方法，该方法的方法声明与系统默认的execute方法只有方法名不同，其他部分比如形参列表、返回值类型都应该完全相同。

**使用动态方法调用前必须设置Struts2运行动态方法调用，设置 struts.enable.DynamicMethodInvocation 常量为“true”。**

#  使用通配符
在配置``<action.../>``元素时，允许在直到name属性时用“*”代表一个或多个任意字符，接下来就可以在class、method属性及``<result.../>``子元素中使用``{N}``的形式来代表前面的第 N 个星号(``*``)所匹配的子串。		

在struts.xml中配置如下：
```
<package name="lee" extends="struts-default">
	<!--所有以Action结尾的请求，都可以用LoginRegistAction处理，method属性使用{1}，代表进行模式匹配时第一个*所代替的字符串-->
	<action name="*Action" class="org.crazyit.app.action.LoginRegistAction" method="{1}">
		<result name="success">/welcome.jsp</result>
	</action>
</package>
```
上面的``<action name="*Action".../>``元素定义了一系列的逻辑Action，只要用户请求的URL是*Action.action的模式，都可以使用该Action处理。例如，请求的URL为``loginAction.action``，则调用lee.LoginRegistAction类的login方法。

还可以在``<result.../>``子元素中使用``{N}``表达式。如：
```
<action name="*">
	<result>/{1}.jsp</result>
</action>
```
**如配置了``abcAction``,``*Action``,``*``,则除非请求的URL与 Action的name属性 绝对相同，否则将按先后顺序来决定由哪个Action执行用户请求。``*Action``并不会比``*``更优先匹配 abcAction 的请求，而是先找到哪个Action，就由哪个处理。**

#  配置默认Action
除了配置``name="*"``的Action之外，还可以配置默认Action，当用户请求找不到对应的Action时，系统默认的Action将处理用户请求。

配置默认Action通过``<default-action-ref.../>``元素完成，如下：
```
<package name="lee" extends="action-default">
	...
	<!--配置一个默认Action，为simpleViewResultAction-->
	<default-action-ref name="simpleViewResultAction"/>
	...
	<!--通过action元素配置默认的Action-->
	<action name="simpleViewResultAction" class="lee.SimpleViewResultAction"
		<result .../>
		...
	</action>
	...
</package>
```
**将默认Action配置在默认命名空间里就可以让该Action处理所有的用户请求。**

#  配置Action的默认处理类
Struts2允许定义开发者自己配置Action的默认处理类，使用``<default-class-ref .../>``元素，配置该元素只需指定一个class属性，该class属性指定的类就是Action的默认处理类。

在struts2-core-2.2.1.jar压缩包的struts-default.xml文件中有如下配置片段：
```
<package name="struts-default" abstract="true">
	...
	<!--配置Action的默认处理类-->
	<default-class-ref class="com.opensymphony.xwork2.ActionSupport"/>
</package>
```
一般情况下，我们配置的Action的默认处理类就是ActionSupport。