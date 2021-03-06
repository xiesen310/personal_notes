---
title: struts01
tags: struts2,框架,Java,ssh
grammar_cjkRuby: true
---


# 介绍

> Struts2是Struts1的下一代产品,是在Struts1和WebWork技术的基础上尽心合并的全新框架,(WebWork是由WebSymhony组织开发的),虽然说Struts2和Struts1名字相似,但是设计思想有很大的不同

# HelloWorld

- 下载 [https://struts.apache.org/][1]
- 目录结构

![struts2目录结构][2]

- 导包struts没有将包进行分类,所以我们可以在官方实例的 `struts-blank.war` 进行解压,在lib包中把jar包找到

![struts2ja包位置][3]

![enter description here][4]

- 创建一个普通java类HelloWorldAction,写一个返回值为String的方法叫hehe

``` java
public class HelloWorkAction {
    public String hehe(){
        System.out.println("hehehe");
        return "success";
    }
}
```

- 在 src 根目录下创建一个名字为 ==struts.xml== 的文件

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
        "http://struts.apache.org/dtds/struts-2.3.dtd">
<struts>
    <!--
        struts.i18n.encoding=UTF-8 设置post的response和request响应请求的编码格式
        struts.enable.DynamicMethodInvocation = true 设置动态加载
        struts.devMode = true 设置热部署，以开发者的模式运行程序，出错了，提示信息比较丰富
        struts.handle.exception=true 设置后缀名
    -->
    <constant name="struts.i18n.encoding" value="UTF-8"></constant>
    <constant name="struts.devMode" value="true"></constant>
    <constant name="struts.action.extension" value="action,do,"></constant>

    <package name="haha" extends="struts-default" namespace="/hello">
        <action name="he" class="top.xiesen.struts.web.action.demo1.HelloWorkAction" method="hehe">
            <result name="success">/hehe.jsp</result>
        </action>
    </package>
    <include file="top/xiesen/struts/web/action/demo2/struts.xml"></include>
    <include file="top/xiesen/struts/web/action/demo3/struts.xml"></include>
    <include file="top/xiesen/struts/web/action/demo4/struts.xml"></include>
</struts>
```

- 在webcontent目录下,创建一个名为hehe的jsp文件
- web.xml中配置struts的核心过滤

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <filter>
        <filter-name>strutsFilter</filter-name>
        <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>strutsFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

</web-app>
```

# structs.xml文件配置

## package标签

> <package> 配置web应用的不同模块,一般在一个功能模块下配置一个package,在当前的package下配置这个模块的多个action

- name属性 给不同的模块起不同的名字,随便写,不重复即可
- namespace属性 给不同的模块设置访问的根路径,可以配置成 /
- extends属性 表示继承, struts-default 是struts2给我们提供的一个 package



## action标签

> action 标签表示配置一个请求

- name 属性表示请求路径的后缀,一般表示功能模块中的具体请求,name的名字就代表访问路径的名称,==名字前面不能加斜杠==
- class 属性表示当有请求过来的时候调用的是哪个类中的方法,配置全类名
- method 表示 class 请求调用的是 class 中的哪个方法,指的是具体的方法名

## result标签

> result 结果配置,用于设置不同的方法返回值,可以配置不同的返回值对应不同的视图

- name 属性表示结果处理名称,与action中的返回值对应
- type 属性表示指定哪个 result 类来处理显示的页面,默认是内部转发,可以在 struts-default 的文件中进行查看
- 标签体表示相对路径,相对于 web应用开始

## include标签

> 如果有多个配置文件,可以在主配置文件中加载其他配置文件

# 常量配置

![默认常量位置][5]

> 对于常量的配置, 默认加载的是structs核心包中的default.properties,如果通过以下3中进行配置,就会按照 ==default.properties==-->==structs.xml==-->==structs.properties(我们应用中的)==-->==web.xml== 的顺序加载,后面设置的常量会覆盖之前设置的常量

1.在structs.xml文件中,在structs的根标签下,书写 constant 标签进行配置,在项目中
主要使用这种方式

![enter description here][6]

2.在src下创建structs.properties文件,将内容复制到此文件进行修改

![enter description here][7]

3.在web.xml文件中,配置 context-param

![enter description here][8]

## 常用常量设置

- struts.i18n.encoding=UTF-8 用于配置接收参数和向外输出中文的编码格式一般设置为 UTF-8
- struts.action.extension=action,, 指定访问action的路径的后缀名,使用 , 表示可以有两个后缀名,可以是action也可以是没有后缀名
- struts.devMode = false 指定structs是否是以开发模式运行,能够支持修改配置文件后进行热部署,所以我们可以将其设置为true

# 动态方法调用

> 如果一个业务模块有多个方法,我们可以使用动态方法调用省略action的配置,设置动态方法调用有两种方法

## 方法一

- 开启动态方法调用 `<constant name="struts.enable.DynamicMethodInvocation" value="true"></constant>`
- 配置action的时候不写method
- 在访问的时候输入网址 http://localhost:8080/Struts2Test/命名空间/action的name!方法名
- 注意action属性值后面要加 ==!== ,在写方法名

``` xml
<!--
        动态加载方式二：不推荐 使用此方法进行动态加载
        1. 设置常量struts.enable.DynamicMethodInvocation = true
        2. 不写method属性
        3. 访问路径：http://localhost:9090/struts/user/user!delete
    -->
<constant name="struts.enable.DynamicMethodInvocation" value="true"></constant>
<package name="demo3" extends="struts-default" namespace="/user">
	<action name="user" class="top.xiesen.struts.web.action.demo3.Demo3Action">
		<result name="success">/hehe.jsp</result>
	</action>
</package>
```
## 方法二 通配符方式

- 关闭动态方法调用
- 对于方法名可以使用一个 * 通配符,在后面的class和method可以使用 {索引} 来读取前面的内容

``` xml
 <!--
        动态加载方式一，推荐使用这种方式进行部署
        设置name属性值为通配符的形式，method方法设置统配的值
        访问路径：http://localhost:9090/struts/user/user_方法名
    -->
<package name="demo3" extends="struts-default" namespace="/user">
	<action name="user_*" class="top.xiesen.struts.web.action.demo3.Demo3Action" method="{1}">
		<result name="success">/hehe.jsp</result>
	</action>
</package>

```

# structs2中的默认配置

- ==method== 的默认值 ==execute==
- ==result 的name属性== 默认值是 ==success==
- ==result的type属性== 默认值是 ==dispatcher==,默认内部转发
- ==class== 的默认值是 ==ActionSupport== 其中有 execute 方法返回值是 success
- 配置package下的默认的action,当访问当前包下,如果找不到指定action,就会自动寻找默认的 action

![enter description here][9]

# Action类

> Action类建立的方式一共有3种类型

- 1.普通的pojo,不需要实现接口,不需要继承任何父类,这种类的好处就是书写自由,框架代码侵入性低,但是缺点就是对于很多Struts2提供的很多功能我们都需要自己去实现,实际开发中并不常用
- 2.实现Action接口,抽象方法 execute() 需要去实现,并且内置了一些字符串供我们使用,实际开发中并不常用,主要是起到一个规范的作用
- 3.继承ActionSupport父类,在ActionSupport中提供很多丰富的功能,并且实现了**Action, Validateable, ValidationAware, TextProvider,LocaleProvider** 这些接口为我们提供了更多的功能,并且ActionSupport内部也有很多丰富的功能,这些功能包括获取国际化信息,数据校验,默认处理用户请求等功能,实际开发过程中大多使用这种方法来创建Action类


- 内部方法的特征为
	- 修饰符 public
	- 返回值为String
	- 方法名随意
	- 不能有参数
	- 可以抛异常

# 结果跳转的方式

> 结果的跳转方式可以通过result的type属性进行设置,比较常用的大致为四种，级：转发到指定页面，转发到action，重定向到页面，重定向action

## 转发

### 转发到指定页面

> 对于type属性,默认是 dispatcher ,就是转发到响应界面,可以不用进行配置

### 转发到指定action

对于type属性需要设置为 chain ,并在其下方配置 **<param>** 标签

![enter description here][10]

``` xml
<action name="test01" class="top.xiesen.struts.web.action.demo5.Demo5Action" method="test01">
	<result type="chain">
		<param name="actionspace">/test</param>
		<param name="actionName">test02</param>
	</result>
</action>
```

## 重定向

### 重定向到指定界面

> 对于type属性,设置为 **redirect** ,就是重定向到界面,如果需要进行重定向就必须进行此处的设置

![enter description here][11]

``` xml
<action name="test01" class="top.xiesen.struts.web.action.demo5.Demo5Action" method="test01">
	<result type="redirect">/hehe.jsp</result>
</action>
```

### 重定向到指定action

![enter description here][12]

``` xml
<action name="test01" class="top.xiesen.struts.web.action.demo5.Demo5Action" method="test01">
	<result type="redirectAction">
		<param name="actionspace">/test</param>
		<param name="actionName">test02</param>
	</result>
</action>
```

# 获取servlet原生对象

> 在struts2中,给我们提供了一个ActionContext类,在这个类中存放了所有我们学习servlet时的对象,这个类的本质就是一个map集合

## 存放的内容

- request对象
- response对象
- servletContext对象
- request域,把request中存放数据的区域单独分离出来本质就是一个map
- session域,把session中存放数据的区域单独分离出来本质就是一个map
- application域,把application中存放数据的区域单独分离出来本质就是一个map
- param参数,把之前我们操作的request.getparamterMap中的map分离出来
- attr域,把request域,session域,application域合在一起
- valueStack 值栈

# 生命周期

> 每次请求的时候创建ActionContext对象,请求结束的时候销毁

# 内部原理
> 当请求发生的时候,struts2就会创建ActionContext对象,并将这个对象和当前本地线程进行绑定,所以就算有100个用户同时访问,那么在系统内存中就有100个ActionContext对象,但是它是和线程进行绑定的,所以对于一个请求而言,在在请求没有结束的时候获取的都是当前请求对应的那个ActionContext获

# 获取方式

## 通过ActionContext方式获取

获取ActionContext对象 ActionContext context = ActionContext.getContext();
获取session域 Map<String, Object> session = context.getSession();
获取application域 Map<String, Object> application = context.getApplication();
获取request域 Map<String, Object> req = (Map<String,Object>)context.get("request");
向ActionContext中放置数据 context.put(key, value);
> 从ActionContext中获取数据 context.get(key)对于request而言,ActionContext不建议获取request对象,因为ActionContext对象的生命周期和request的生命周期一致,并且在struts2的核心过滤器中,可以看到,struts2对原生的request对象进行了代理操作,在后续的request对象操作的过程中,其实是操作的代理对象,其代理对象已经将原生req的getAttribute方法修改,会既找原生request域,又会找ActionContex中的内容通

![enter description here][13]

## 通过ServletActionContext方式获取

> 它是一个工具类,通过这个工具类可以获取到原生的request,response,session,servletContext,但是通过查看源码发现其内部还是从ActionContext中获取的

- 获取原生request对象 ServletActionContext.getRequest()
- 获取原生response对象 ServletActionContext.getResponse()
- 获取原生servletContext对象 ServletActionContext.getServletContext()
- 获取原生session对象是通过获取的request对象进行获取的

![enter description here][14]

## 通过实现接口的方式获取(了解)

> 我们可以通过实现**ServletRequestAware, ServletContextAware,SessionAware,ServletResponseAware**这四个接口来获取对应的4个原生对象,我们可以定义4个成员变量,在这个方法中将参数的值赋值给我们的成员变量,这些方法会在execute方法调用之前进行调用,所以在execute方法中就可以使用这些原生servlet对象

![enter description here][15]




  [1]: https://struts.apache.org/
  [2]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504785374788.jpg
  [3]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504785513165.jpg
  [4]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504785586699.jpg
  [5]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504786245782.jpg
  [6]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504786619717.jpg
  [7]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504786653176.jpg
  [8]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504786697450.jpg
  [9]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504787511895.jpg
  [10]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504788591156.jpg
  [11]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504789591060.jpg
  [12]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504790270189.jpg
  [13]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504792660961.jpg
  [14]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504792938690.jpg
  [15]: https://www.github.com/xiesen310/notes_Images/raw/master/images/1504793064323.jpg