# JSP_note

## 一、 JSP基础

### 1.1 什么是动态网页

  动态网页是相对静态网页而言的。当浏览器请求服务器上的某个页面时，服务器根据**当前时间、环境参数、数据库操作等**动态的生成HTML页面，然后在发送给浏览器。“动态”是指服务器端页面的动态生成，相反静态是指页面时实实在在、独立的文件。

  动态网页是在静态网页的基础上，使用Java、.net、asp编程语言和数据库交互。

### 1.2 应用服务器

  应用服务器是指通过各种协议把商业逻辑暴露给客户端的程序。（端口号：80，默认可以省略，域名只能绑定 80端口）

- Tomcat应用服务器

  作为apache旗下的免费的开源的小型应用服务器。（500左右）。tomcat可以快速搭建tomcat集群

  推荐使用解压缩版本

  ![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203190440.jpg)



- JBoss应用服务器

  免费的大型应用服务器（不开源），像oracle。性能扩展是需要收费的

- Weblogic应用服务器

  大型应用服务器，收费

### 1.3 webapps目录下的web项目

web 项目必须满足的格式

- WEB-INF
  - lib目录（存放项目的第三方依赖）
  - class目录（所有java文件编译后的class文件）
  - web.xml文件（servlet3.0之前必须，之后可选）

### 1.4 项目访问

  URL的书写：请求协议://+服务器（域名或 IP）+:端口号+/路径

  路径：/项目名称（webapps下的文件夹名称）+/资源路径

http://127.0.0.1:8080/web1908-01/login.jsp

**常见问题：**

- ”404“问题
  - 首先查看Tomcat是否启动
  - 找到webapps目录下是否有指定的文件夹
  - 确定端口号是否正确
- 在生产环境中web项目需要绑定域名，通常端口号为 80，默认使用80端口时可以省略:80
- 如果想让项目为默认启动项目，需要在server.xml文件中配置默认启动项目，或者将项目名称直接修改为ROOT

### 1.5 在eclipse中创建并部署web project

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203190633.jpg)



![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203190658.jpg)



![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203190702.jpg)



![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203190705.jpg)



**注意：**如果 src（不允许有错）和 WebContent（允许有错）下均没有错误，而项目上有错，说明build path中出错。

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203190711.jpg)



## 二、 JSP组成

  JSP（Java server page）由HTML代码和Java的相关内容组合而成。

### 2.1 指令

  包含3种常见指令

```jsp
<%@page%> //page指令，用于声明页面使用语言、导入的第三方依赖、页面的编码格式等相关内容 
<%@taglib%> //用于导入第三方的标签库（jstl) 
<%@include%> //引入在编译其就可以引入jsp或其他页面
```

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!-- 
language：声明当前页面使用的编程语言
contentType：声明当前页面的类型
pageEncoding：声明页面的编码格式
isErrorPage：用于指定当前页面是否为一个异常页面，只有当isErrorPage=true时，exception内置对象才会生效
errorPage：用于指定当前页面发生异常时通过那一个页面进行异常处理
import：导入其他包的内容 <%@page import="com.softwin.service.impl.UserServiceImpl"%>
session：用于指定当前页面是否使用session，如果为false则无法使用
extends：用于制动当前页面的父类
-->
```

```jsp
<%@taglib prefix="" tagdir="" uri="" %>
<!--
prefix：前缀，当使用第三方的标签库时需要指定的前缀jstl c,fn,fmt
tagdir：用于加载本地的标签库（自定义标签库 velocity来做模板）
uri：用于加载云端的标签库（jstl）spring，strtus2都可以使用url加载
-->
```

```jsp
<%@include file="" %>
<!--
file：用于指定加载的页面，jsp会翻译成.java文件,在翻译以前就加载成功，编译后的.java文件中包含 include的内容
--> 
```

### 2.2 脚本

  可以使用java语言进行流程控制语句的编写的代码块。在JSP中，脚本必须写在<% %>中间，JSP脚本中不能出现**类**和**方法**的定义

```jsp
<%
	int a=0; 
	int b=1; 
	for(int i=0;i<100;i++){ 
        a=a+i; 
    }
	out.print(a); 
%>
```

### 2.3 声明

​	用于声明类和方法,声明使用 <%!  %>

```jsp
<%! 
    public int getNum(){ 
    	return 100; 
}
%>
```

声明可以被src中的java类和方法替代，因而在实际开发中使用场景不多

### 2.4 表达式

  将Java代码中变量的值直接输出到HTML的指定位置，声明使用<%= %>

```jsp
<%
	int a=getNum(); 
	int b=1; 
	for(int i=0;i<100;i++){ 
        a=a+i; 
    }
%>

<a href="#"><%=a+100000 %></a>
```

**注意：**表达式中只能写变量或表达式，所以不允许有`;`或不允许以`;`结尾

### 2.5 动作

  `<jsp:` 开头的，叫动作

```jsp
<!-- 是在运行时才引入 --> 
<jsp:include page=""></jsp:include> 
<!-- 请求转发 --> 
<jsp:forward page=""></jsp:forward>
```

  使用场景不多

### 2.6 注释

- 单行注释		`//`
- 多行注释        `/*  */`
- 文档注释        `/**  */`
- 可见性注释        `<!-- -->`
- 不可见性注释        `<%-- --%>`



## 三、 9大内置对象

  内置对象时JSP给我们提供的对象，不需要我们去创建，可以直接使用。

  在JSP中进行数据交互时有两种方式：一种是请求参数、另一种是作用域

### 3.1 四大作用域

  用于存储数据，供JSP页面之间数据交互

| 返回值类型 | 方法                                  | 描述                                               |
| ---------- | ------------------------------------- | -------------------------------------------------- |
| void       | setAttribute(String key,Object value) | 向作用域中存放值                                   |
| Object     | getAttribute(String key)              | 根据key从作用域中找到对应的value，如果没有返回null |
| void       | removeAttribute(String key)           | 根据key移除作用域中对应的值，包含key也被移除       |



#### 3.1.1 pageContent(PageContent)

  作用域只在当前页面有效

| 返回值类型     | 方法                | 描述                   |
| -------------- | ------------------- | ---------------------- |
| Exception      | getException()      | 获取exception对象      |
| Object         | getPage()           | 获取page对象           |
| ServletRequest | getRequest()        | 获取out对象            |
| HttpSession    | getSession()        | 获取session对象        |
| ServletConfig  | getServletConfig()  | 获取config对象         |
| ServletContext | getServletContext() | 获取servletContext对象 |



#### 3.1.2 request(HttpServletRequest)

  作用域在当前页面、一次请求有效

  客户端发起一次请求，在响应以前不管经历了多少个页面，只要没有重新让客户端发请求，都称为一次请求

| 返回值类型        | 方法                                                         | 描述                                                         |
| ----------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| HttpSession       | getSession()                                                 | 获取session对象                                              |
| RequestDispatcher | getRequestDispatcher(String path).forward(request,response)* | 将请求转发到path中（转发无论多少次都属于一次请求，request作用域有效） |
| String            | getContextPath()                                             | 获取请求的根uri（用于加载CSS、js、image，并且在重定向时推荐使用getContextPath()来查找资源） |
| Cookie[]          | getCookies()                                                 | 读取客户端的cookie                                           |
| Enumeration       | getHeader(String name)                                       | 获取请求头                                                   |
| String            | getMethod()                                                  | 获取请求的请求方式（GET、PUT、POST）                         |
| String            | getLocalAddr()                                               | 获取客户端的请求IP地址                                       |
| String            | getCharacterEncoding()                                       | 获取请求的编码格式                                           |
| void              | setCharacterEncoding(String env)***                          | 设置请求的编码格式（解决post请求的中文乱码问题），该语句必须放在最前面 |
| String            | getParameter(String name)*                                   | 获取请求参数的值（如果是form表单提交请求,参数name应该是form表单中组件的name属性对应的值。如果是get请求方式，参数name就应该是请求地址后?后的key=value中的key 。如果没有对应的请求参数，返回null） |
| String[]          | getParameterValues(String name)*                             | 获取请求参数的值,说明请求参数中有多个同名的请求参数。（在请求中如果有多个同名的请求参数，并且获取时使用了request.getParameter()，只能返回第一个请求参数的值。如果想返回所有同名参数的值，getParameterValues()，返回string[]数组，如果没有找到请求参数，返回null。更多使用场景获取checkbox多选对应的值，getParamterValues()只能获取到被选中的checkbox中的value值，并且返回的数组长度和前端checkbox选中的个数一致） |



#### 3.1.3 session(HttpSession)

  作用域在当前页面、一次请求、一次会话有效

  session的默认有效时间是30分钟。在同一个浏览器中，没有关闭并且session有效期未到期时，我们发送的多次请求称为一次会话。

  提供一种方式，跨多个页面请求或对 Web 站点的多次访问标识用户并存储有关该用户的信息。 session时存储在服务端的，必然会占用内存需要谨慎使用。

| 返回值类型 | 方法                                 | 描述                                                         |
| ---------- | ------------------------------------ | ------------------------------------------------------------ |
| String     | getId()                              | 获取sessionId                                                |
| void       | invalidate()                         | 让session强制失效                                            |
| void       | setMaxInactiveInterval(int interval) | 设置session的有效期，参数为秒（有效期是从最后一次和浏览器进行交互开始计时） |



#### 3.1.4 application(Application)

  作用域在整个项目运行期间有效.

  当重启启动应用以后，application作用域失效。application以前用于做网站浏览量计算的。



### 3.2 五个常用对象

#### 3.2.1 reponse(HttpServletResponse)

  是向客户端发送响应的

| 返回值      | 方法                                 | 描述                                         |
| ----------- | ------------------------------------ | -------------------------------------------- |
| void        | addCookie(Cookie cookie)             | 将cookie写入到客户端浏览器中                 |
| void        | flushBuffer()                        | 刷新缓冲流                                   |
| String      | getCharacterEncoding()               | 获取编码格式                                 |
| void        | setCharacterEncoding(String charset) | 设置响应的编码格式                           |
| PrintWriter | getWriter()                          | 和out对象差不多，用 于向客户端浏览器输出内容 |
| void        | sendRedirect(String location)*       | 请求重定向到location（2次请求）              |



**注意：**

- `request.setCharactorEncoding()`处理**jsp或servlet通过请求参数获取时**的中文乱码
- `response.setCharactorEncoding()`处理**响应的jsp显示时的**中文乱码

#### 3.2.2 page(Page)

  java类中的this关键字。引用成员变量和构造方法。

#### 3.2.3 out(JspWriter)

  向客户端输出。在同步请求中使用很少，在异步请求中用于对异步请求响应数据。

  JSP 页面中的操作和模板数据是使用 JspWriter 对象编写的，该对象由隐式变量 out 引用，而此变量是使用 PageContext  对象中的方法自动初始化的。

#### 3.2.4 config(ServletConfig)

  servlet 容器使用的 servlet 配置对象，该对象在初始化期间将信息传递给 servlet，用于获取servlet的相关配置信息.

| 返回值类型     | 方法                         | 描述                                   |
| -------------- | ---------------------------- | -------------------------------------- |
| String         | getInitParamter(String name) | 通过初始化变量名获取值                 |
| Enumeration    | getInitparamterNames()       | 获取所有初始化变量的名称，返回枚举类型 |
| ServletContext | getServletContext()          | 获取servletContext对象                 |
| String         | getServletName()             | 获取servlet名称                        |



#### 3.2.5 exception(Throwable)

  用于获取当前的异常信息，只有在页面指定isErrorPage="true"，exception对象才能生效。主要用于项目中对异常的处理。

1. 定义异常页面

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" isErrorPage="true"%> 
   <!DOCTYPE html> 
   <html> 
       <head>
           <meta charset="UTF-8"> 
           <title>Insert title here</title> 
       </head> 
       <body> 
           <%
               String code=""; 
               String description =""; 
               //exception是指捕获到的异常信息 
               if(exception instanceof ArithmeticException ){ 
                   code="0001"; 
                   description="除数不能为0"; 
               }
           %>
       	<h1>对不起，出错了。</h1> 
       	<h2>错误代码：<%=code %></h2> 
       	<hr> 
       	<%=description %> 
       </body> 
   </html>
   ```

   

2. 在可能出现异常的页面去指定异常页面

   ```jsp
   <%@page import="com.sofwin.pojo.User"%>
   <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" errorPage="exception.jsp"%> 
   <!DOCTYPE html> 
   <html> 
       <head> 
           <meta charset="UTF-8"> 
           <title>Insert title here</title> 
       </head> 
       <body> 
           <h1>Hello Jsp</h1> 
           <%
           	User user=null; 
           	out.print(user.getId()); 
           	int i=1/0; 
           %>
       </body> 
   </html>
   ```

   

## 四、 Cookies

  Cookie，有时也用其复数形式 Cookies。类型为“**小型文本文件**”，是某些网站为了辨别用户身份，进行Session跟踪而储存在用户本地终端上的数据（通常经过加密），由用户客户端计算机暂时或永久保存的信息 。

**用途：**

> 因为HTTP协议是无状态的，即服务器不知道用户上一次做了什么，这严重阻碍了交互式Web应用程序的实现。在典型的网上购物场景中，用户浏览了几个页面，买了一盒饼干和两瓶饮料。最后结帐时，由于HTTP的无状态性，不通过额外的手段，服务器并不知道用户到底买了什么，所以Cookie就是用来绕开HTTP的无状态性的"额外手段"之一。服务器可以设置或读取Cookies中包含信息，借此维护用户跟服务器**会话**中的状态。
>
> 在刚才的购物场景中，当用户选购了第一项商品，服务器在向用户发送网页的同时，还发送了一段Cookie，记录着那项商品的信息。当用户访问另一个页面，浏览器会把Cookie发送给服务器，于是服务器知道他之前选购了什么。用户继续选购饮料，服务器就在原来那段Cookie里追加新的商品信息。结帐时，服务器读取发送来的Cookie就行了。
>
> Cookie另一个典型的应用是当登录一个网站时，网站往往会请求用户输入用户名和密码，并且用户可以勾选“下次自动登录”。如果勾选了，那么下次访问同一网站时，用户会发现没输入用户名和密码就已经登录了。这正是因为前一次登录时，服务器发送了包含登录凭据（用户名加密码的某种加密形式）的Cookie到用户的硬盘上。第二次登录时，如果该Cookie尚未到期，浏览器会发送该Cookie，服务器验证凭据，于是不必输入用户名和密码就让用户登录了。

**Cookie的缺点：**

- Cookie会被附加在每个HTTP请求中，所以无形中增加了流量。
- 由于在HTTP请求中的Cookie是明文传递的，所以安全性成问题，除非用HTTPS。
- Cookie的大小限制在4KB左右，对于复杂的存储需求来说是不够用的。

**Cookie的使用：**

1. 创建Cookie

   ```java
   Cookie cookie=new Cookie(String name,String value)
   //创建cookie对象,name是 cookie的名称，value是cookie对应的值
   ```

2. 设置Cookie的路径

   ```java 
   cookie.setPath("/");
   //让cookie在本项目中的任意位置可见
   ```

3. 将Cookie写入到客户端中

   ```jsp
   response.addCookie(cookie);
   ```

4. 常用方法：

   | 返回值类型 | 方法        | 描述                     |
   | ---------- | ----------- | ------------------------ |
   | String     | getName()   | 获取cookie的名称         |
   | String     | getValue()  | 获取cookie对应的值       |
   | void       | setMaxAge() | 设置cookie的最大有效时间 |
   | void       | setPath()   | 设置cookie在任意目录可见 |

**记住密码的案例：**

1. 登录界面

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8"
   	pageEncoding="UTF-8"%>
   <!DOCTYPE html>
   <html>
   <head>
   <meta charset="UTF-8">
   <title>Insert title here</title>
   </head>
   <body>
   	<%
   		Cookie[] cookies = request.getCookies();
   	String username = "";
   	String pwd = "";
   	String remember = "";
   	if (cookies != null) {
   		for (Cookie c : cookies) {
   			System.out.println(c.getName());
   			if ("username".equals(c.getName())) {
   		username = c.getValue();
   			}
   			if ("pwd".equals(c.getName())) {
   		pwd = c.getValue();
   			}
   			if ("rememberme".equals(c.getName())) {
   		remember = c.getValue();
   			}
   		}
   	}
   	%>
   	<h1>用户登录</h1>
   	<form action="controller/checkLogin.jsp">
   		用户名：<input type="text" name="username" value="<%=username%>" /> <br>
   		密码：<input type="password" name="pwd" value="<%=pwd%>"> <br>
   		<%
   			if ("".equals(remember)) {
   		%>
   		<input type="checkbox" name="rememberme" value="1">
   		<%
   			} else {
   		%>
   		<input type="checkbox" name="rememberme" checked="checked" value="1">
   		<%
   			}
   		%>
   		记住密码 <input type="submit" value="登录">
   	</form>
   </body>
   </html>
   ```

2. 处理请求界面

   ```jsp
   <%@page import="com.softwin.service.impl.UserServiceImpl"%>
   <%@page import="com.softwin.service.UserService"%>
   <%@page import="com.softwin.pojo.User"%>
   <%@ page language="java" contentType="text/html; charset=UTF-8"
   	pageEncoding="UTF-8"%>
   <%
   	//获取form表单中的信息，利用请求参数
   String username = request.getParameter("username");
   String pwd = request.getParameter("pwd");
   String remember = request.getParameter("rememberme");
   
   //判断用户名和密码是否正确
   User user = new User();
   user.setUsername(username);
   user.setPwd(pwd);
   UserService service = new UserServiceImpl();
   boolean flag = service.checkLogin(user);
   if (flag) {
   	//判断客户是否选择记住密码,如果是
   	if (remember != null) {
   		Cookie cname = new Cookie("username", username);
   		cname.setPath("/");
   		Cookie cpwd = new Cookie("pwd", pwd);
   		cpwd.setPath("/");
   		Cookie cremember = new Cookie("rememberme", remember);
   		cremember.setPath("/");
   		response.addCookie(cname);
   		response.addCookie(cpwd);
   		response.addCookie(cremember);
   	}
   	request.getRequestDispatcher("/user_data.jsp").forward(request, response);
   	//response.sendRedirect(request.getContextPath()+"/user_data.jsp");
   } else {
   	request.getRequestDispatcher("/login.jsp").forward(request, response);
   }
   %>
   ```

   

## 五、 JSP运行原理

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203190722.jpg)

  jsp翻译后的.java文件是单例的，使用多线程的方式去调用_jspService方法，所以在.java文件中Volatile修饰成员变量，目的就是为了保证在多线程时成员变量的同步。



## 六、 GET请求和POST请求的比较

- Get请求和Post请求的差别
  - 请求参数的传递方式不同，get请求参数是在uri?key=value&key=value的形式传递，post请求将请求参数封装在请求体中传递
  - GET请求属于明码请求（不安全），post请求安全
  - GET请求的参数大小受浏览器的影响，Post请求参数的大小没有限制
  - GET请求中文乱码和POST请求中文乱码的解决方式不同（GET请求修改server.xml中的URIEncoding,Post使用request.setCharactorEncoding
  - GET请求不能提交type="file"，而post请求可以提交type="file"

- 常见的GET请求和post请求
  - post请求使用form表单提交请求，并且method='post'
  - form表单，method="GET"
  - <a href=""> 是get请求，传递请求参数只能通过?的形式
  - js document.location.href=请求地址 也是get请求



## 七、 转发和重定向的比较

  无论是转发和重定向，都可以实现页面的跳转。

- 转发

  > `request.getRequestDispatcher("url").forward(request,response);`
  >
  > - 是由服务端发起的请求，可以访问WEB-INF下的资源
  >
  > - 转发是1次请求
  >
  > - request作用域有效
  >
  > - 转发浏览器地址不变

- 重定向

  > `response.sendRedirect(url);`
  >
  > - 是由客户端发起的请求，不能访问WEB-INF下的资源
  > - 重定向是2次请求
  > - request作用域失效
  > - 重定向浏览器地址会发生改变



## 八、 MVC分层

**M(Model)**

​	模型层包含业务层和dao层

**V(View):**

​	显示层包含显示给用户的jsp(vue,thymealf),webContent目录下的根目录下的jsp

**C(Controller):**

​	控制层，接受和处理请求，并响应客户端

**注意**

在实际的开发过程中，不允许view层直接跳转到view层



## 九、 分页

### 9.1 为什么分页？

- 传统的查询将所有数据展示在一页中，查看数据不方便
- 海量数据查询速率成指数倍增长的，循环显示的效率也很低
- 海量数据在显示时浏览器会出现卡顿现象

### 9.2 概念

  将原来一页显示的数据，拆分成多个页来显示，每次给用户指定显示固定的数据。

### 9.3 分类

- 异步分页

  使用Ajax技术，在不刷新整个页面的情况下，实现数据分页

  例子：百度、微商城、淘宝APP(基于H5的APP开发，其中分页数据使用异步分页)

  异步分页通过监听滚动条位置的变化，触发数据的展示。

- 同步分页

  使用同步请求，来刷新整个页面从而实现数据分页

  同步分页的工具栏：首页、上一页、下一页、尾页、当前4/10,跳转到 X 页，共xx条数据

### 9.4 工具栏

```html
<div id="pageTool" style="float:right"> 
		&nbsp;&nbsp;<a href="javascript:void(0)" onclick="trunToPage('1');">首页 </a>
		&nbsp;&nbsp;<a href="javascript:void(0)" onclick="trunPage('-1');">上一页</a>
		&nbsp;&nbsp;<a href="javascript:void(0)" onclick="trunPage('1');">下一页
		&nbsp;&nbsp;<a href="javascript:void(0)" onclick="trunToPage('<%=users.getPages()%>');">尾页</a> 
		当前<%=users.getPageNumber() %>/<%=users.getPages() %>，跳转到
		<a href="javascript:void(0)" onmouseleave="skip();"> 
			<input type="text" id="skip" name="skip" value="<%=users.getPageNumber() %>" style="width:30px; text-align:center">
		</a>
		页， 共<%=users.getTotalCount() %>条数据 
</div>
```

### 9.5 抽象出分页对象

```java
package com.softwin.util;

import java.util.List;
import java.util.Map;

public class PageInfo {
	
	//当前页码 
	private Integer pageNumber; 
	//每页大小（页大小） 5 
	private Integer pageSize; 
	//总页数 
	private Integer pages; 
	//总记录数 
	private Integer totalCount; 
	//每页显示的数据 
	private List<Map> result;
	
	public Integer getPageNumber() {
		return pageNumber;
	}
	public void setPageNumber(Integer pageNumber) {
		this.pageNumber = pageNumber;
	}
	public Integer getPageSize() {
		return pageSize;
	}
	public void setPageSize(Integer pageSize) {
		this.pageSize = pageSize;
	}
	public Integer getPages() {
		if(totalCount/pageSize==0) {
			return totalCount/pageSize;
		}else {
			return totalCount/pageSize+1;
		}
	}
	public void setPages(Integer pages) {
		this.pages = pages;
	}
	public Integer getTotalCount() {
		return totalCount;
	}
	public void setTotalCount(Integer totalCount) {
		this.totalCount = totalCount;
	}
	public List<Map> getResult() {
		return result;
	}
	public void setResult(List<Map> result) {
		this.result = result;
	}
	
	
}
```

### 9.6 分页JSP

```jsp
<%@page import="com.softwin.util.PageInfo"%>
<%@page import="java.util.Map"%>
<%@page import="java.util.List"%>
<%@page import="com.softwin.dao.impl.UserDaoImpl"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<style>
div {
	border: 1px solid gray;
	padding:5px 10px;
	margin-top:10px;
}
th,td{
	text-align:center;
}
</style>
<%
	PageInfo users=(PageInfo)request.getAttribute("users");
	String path=request.getContextPath();
	String username=request.getAttribute("username").toString();
	String sex=request.getAttribute("sex").toString();
	String sPageSize=request.getAttribute("sPageSize").toString();
%>
<body>
	<form action="<%=path%>/controller/query.jsp" id="form1">
	<input type="hidden" id="pageNumber" name="pageNumber" value="<%=users.getPageNumber()%>"/>
	<input type="hidden" id="pages" name="pages" value="<%=users.getPages()%>"/>
	<h1>用户列表页</h1>
	<div>
		用户名：
		<input type="text" name="username" value="<%=username %>" > &nbsp;
		性别：
		<select name="sex">
			<option>全部</option>
			<%if(sex.equals("男")){ %>
			<option selected="selected">男</option>
			<%}else{ %>
			<option >男</option>
			<%} %>
			<%if(sex.equals("女")){ %>
			<option selected="selected">男</option>
			<%}else{ %>
			<option >女</option>
			<%} %>
		</select>
		&nbsp;
		每页数据条数：
		<select name="pageSize">
			<%if(sPageSize.equals("10")){ %>
			<option selected="selected">10</option>
			<%}else{ %>
			<option >10</option>
			<%} %>
			<%if(sPageSize.equals("20")){ %>
			<option selected="selected">20</option>
			<%}else{ %>
			<option >20</option>
			<%} %><%if(sPageSize.equals("30")){ %>
			<option selected="selected">30</option>
			<%}else{ %>
			<option >30</option>
			<%} %>
		</select>
		&nbsp;
		<input type="submit" value="查询">
		&nbsp;
		<input type="button" value="新增" onclick="add();">
		&nbsp;
		<input type="button" value="批量删除" onclick="deletes();">
	</div>
	<div>
		<table style="width:100%;" border="1" cellpadding="0" cellspacing="0">
			<thead>
    			<tr>
      				<th><input type="checkbox" ></th>
      				<th>用户名</th>
      				<th>密码</th>
      				<th>性别</th>
      				<th>操作</th>
    			</tr>
  			</thead>
			<tbody>
				<%for(Map m:users.getResult()){ %>
				<tr>
					<td><input type="checkbox" name="ids" value="<%=m.get("id")%>"></td>
					<td><%=m.get("username") %></td>
					<td><%=m.get("pwd") %></td>
					<td><%=m.get("sex") %></td>
					<td>
						<a href="<%=path%>/controller/toEdit.jsp?id=<%=m.get("id")%>">编辑</a>
						&nbsp;&nbsp;&nbsp;
						<a href="<%=path%>/controller/delete.jsp?id=<%=m.get("id")%>">删除</a>
					</td>
				</tr>
				<% } %>
			</tbody>
		</table>
	</div>
	<div id="pageTool" style="float:right"> 
		&nbsp;&nbsp;<a href="javascript:void(0)" onclick="trunToPage('1');">首页 </a>
		&nbsp;&nbsp;<a href="javascript:void(0)" onclick="trunPage('-1');">上一页</a>
		&nbsp;&nbsp;<a href="javascript:void(0)" onclick="trunPage('1');">下一页
		&nbsp;&nbsp;<a href="javascript:void(0)" onclick="trunToPage('<%=users.getPages()%>');">尾页</a> 
		当前<%=users.getPageNumber() %>/<%=users.getPages() %>，跳转到
		<a href="javascript:void(0)" onmouseleave="skip();"> 
			<input type="text" id="skip" name="skip" value="<%=users.getPageNumber() %>" style="width:30px; text-align:center">
		</a>
		页， 共<%=users.getTotalCount() %>条数据 
	</div>
	</form>
</body>
<script type="text/javascript">
	function add(){
		document.location.href="<%=path%>/controller/toEdit.jsp";
	}
	function deletes(){
		//将action临时改为deletes.jsp
		document.forms.form1.action="<%=path%>/controller/deletes.jsp";
		//from表单提交
		document.forms.form1.submit();
	}
	function trunPage(num){ 
		var pageNumber = document.getElementById("pageNumber").value; 
		var pages = document.getElementById("pages").value;	
		pageNumber=parseInt(pageNumber)+parseInt(num); 
		if(pageNumber<=0){
			pageNumber=1;
		}else if(pageNumber>pages){
			pageNumber=pages;
		}else{
			document.getElementById("pageNumber").value=pageNumber;			
		}
		document.forms.form1.submit(); 
	}
	function trunToPage(pageNumber){ 
		document.getElementById("pageNumber").value=pageNumber; 
		document.forms.form1.submit(); 
	}
	function skip(){
		var skipNumber = document.getElementById("skip").value;	
		var pages = document.getElementById("pages").value;
		/* if(skipNumber>=1 && skipNumber<=pages){ */
			document.getElementById("pageNumber").value=skipNumber;	
		/* 
		} */
		document.forms.form1.submit();
	}
</script>
</html>
```

### 9.7 分页DAO

```java
@Override
	public PageInfo selectUsersByPage(User user, Integer pageSize, Integer pageNumber) {
		PageInfo page = new PageInfo();
		//除了pages属性外的其他属性赋值
		page.setPageNumber(pageNumber); 
		page.setPageSize(pageSize); 
		String sql = "select * from s_user where 1=1 ";
		if(user!=null) {
			if(user.getUsername()!=null && !"".equals(user.getUsername())) {
				sql+=" and username like '%"+user.getUsername()+"%'";
			}
			if(user.getSex()!=null && !"全部".equals(user.getSex())) {
				sql+=" and sex like '%"+user.getSex()+"%'";
			}
		}
		//求总条数的 
		page.setTotalCount(getTotalCount(sql)); 
		sql+=" limit "+(pageNumber-1)*pageSize+","+pageSize;
		List<Map> users = JdbcUtil.executeQuery(sql, null);
		page.setResult(users);
		return page;	
	}

	public int getTotalCount(String sql) {
		int flag=0;
		sql="select count(*) count from ("+sql+") a";
		List<Map> result=JdbcUtil.executeQuery(sql,null);
		if(result.size()==1) {
			Map map=(Map)result.get(0);
			flag=Integer.parseInt(map.get("count").toString());
		}
		return flag;
	}
```



## 十、 JNDI

  JNDI（Java Naming and Directory Interface,Java命名与服务接口）是Java的一个目录服务应用程序接口（API），他提供一个目录系统，并将服务名称与对象关联起来，从而使得开发人员在开发过程中可以使用名称来访问对象。

  用于操作由应用服务器提供的服务。主要用于资源共享（资源解耦合）。共享资源和服务.

```java
Context context = new InitalContext();
context.lookup("资源或服务的名称");
```

1. 在context.xml中配置（比较常用）

   ```xml
   <!-- 配置资源-->
   <Environment name="tjndi" value="hello jndi" type="java.lang.String" />
   <!-- name:资源名称 value:资源的值 type:资源的类型 -->
   ```

   ```java
   //获取资源
   Context context = new InitalContext();
   String tjndi = (String)context.lookup("java:comp/env/tjndi");//java:comp/env/ 资源名称
   ```

   > - 资源能够在应用服务器中所有项目中共享
   > - 只有运行在应用服务器上的代码才可以获取JNDI

2. 在server.xml中配置

   ```xml
   <Context docBase="java1908z-jsp" path="/java1908z-jsp" reloadable="true" source="org.eclipse.jst.jee.server:java1908z-jsp"> 
       <Environment name="tjndi" value="hello jndi" type="java.lang.String" /> 
   </Context>
   ```

   > - 对单个资源提供资源或服务
   > - 因为Enviroment定义在java1908z-jsp项目中，所以资源只能够被java1908z-jsp项目获取

3. 在META-INFO中配置

   ```xml
   <!--配置服务（数据库连接池）-->
   <Resource 
       <!--资源的提供者：容器--> 
   	auth="Container" 
   	<!--驱动--> 
   	driverClassName="com.mysql.cj.jdbc.Driver" 
   	<!--最大有效连接数--> 
   	maxActive="100" 
   	<!--最小连接数--> 
   	maxIdle="30" 
   	<!--最大连接等待时间--> 
   	maxWait="10000" 
   	<!--服务名称--> 
   	name="jdbc/news2" 
   	<!-- 数据库连接密码--> 
   	password="123456" 
   	<!--服务类型--> 
   	type="javax.sql.DataSource" 
   	<!--指定DataSource的类型，druid-->
   	factory="com.alibaba.druid.pool.DruidDataSourceFactory" 
   	<!--数据库连接字符串--> 
   	url="jdbc:mysql://localhost:3306/java1908z?serverTimezone=UTC" 
   	<!--数据库用户名--> 
   	username="root"/>
   ```
   ```xml
<!--在web.xml中引用服务-->
   <resource-ref> 
       <res-ref-name>java:comp/env/jdbc/news2</res-ref-name> 
       <res-type>javax.sql.DataSource</res-type> 
       <res-auth>Container</res-auth> 
   </resource-ref>
   ```
   
   ```java
   //JNDI查找数据库连接池
   Context context = new InitialContext(); 
   DataSource ds = (DataSource)context.lookup("java:comp/env/jdbc/news2"); 
   Connection conn = ds.getConnection();
   ```
   
   > - META-INFO目录是web项目中的配置文件目录。

## 十一、 文件上传（commons-fileupload）

- 普通流文件上传
- FastDFS分布式文件上传：按照每个文件系统的压力将文件存储在不同的磁盘位置，并提供访问的服务
- HDFS分布式文件上传：将文件分片上传

**使用步骤：**

- 创建DiskFileItemFactory对象
- 创建ServletFileUpload对象（根据磁盘文件工厂）
- 使用parseRequest(request)，将request请求解析为List<FileItem>
- 使用迭代处理解析后的结果（如果isFormField()返回true，说明为普通组件，否则为多媒体组件type="file"）

### 11.1 搭建环境

- commons-fileupload.jar
- commons-io.jar（间接依赖）

### 11.2 form表单发送多媒体请求

**注意：**

- 包含enctype="multipart/form-data"
- type="file"的组件
- 必须使用Post请求

```HTML
<body>
<h1>文件上传</h1>
<!--如果使用enctype="multipart/from-data"那么request对象将无法获取请求参数request.getParameter("name")  -->
<form action="controller/upload.jsp" enctype="multipart/form-data" method="POST">
	用户名：<input type="text" name="username"/><br> 
	密码：<input type="password" name="pwd"/><br> 
	用户头像:<input type="file" name="headerImge"/> 
<input type="submit" value="保存"/>
</form>
</body>
```



### 11.3 解析多媒体请求并实现上传

```java
		//1.创建磁盘文件工厂
	DiskFileItemFactory factory=new DiskFileItemFactory();
	//2.使用磁盘文件工厂创建ServletFileUpload对象
	ServletFileUpload upload = new ServletFileUpload(factory);
	//3.使用parseRequest()方法去解析request请求，返回List<FileItem>
	List<FileItem> items = upload.parseRequest(request);
	
	//4.使用迭代处理解析后返回的结果
	String path = request.getRealPath("/")+"/upload";//获取项目在Tomcat中的绝对路径
	File f = new File(path); //创建目录
	String fileNameTemp = UUID.randomUUID().toString();//获取唯一ID
	
	Iterator<FileItem> ite=items.iterator();
	while(ite.hasNext()){
		FileItem item = ite.next();
		if(item.isFormField()){
			//普通组件
			if(item.getFieldName().equals("username")){ 
				System.out.println("username"+item.getString()); 
			}
			if(item.getFieldName().equals("pwd")){ 
				System.out.println("pwd"+item.getString()); 
			}
		}else{
			//多媒体组件
			String clientFileName = item.getName();
			String fileName = path+ fileNameTemp+clientFileName.substring(clientFileName.lastIndexOf("."), clientFileName.length());
			item.write(new File(fileName));
		}
	}
```



### 11.4 ServletFileUpload

| 返回值类型     | 方法                                           | 描述                                                         |
| -------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| boolean        | isMultipartContent(HttpServletRequest request) | 用于判断request请求中是否含有多媒体请求，如果有返回true，否则返回false |
| List<FileItem> | parseRequest (HttpServletRequest request)      | 将request请求解析为List<FileItem>集合                        |



### 11.5 FileItem

| 返回值类型 | 方法                       | 描述                                                         |
| ---------- | -------------------------- | ------------------------------------------------------------ |
| String     | getContentType()           | 获取contentType                                              |
| String     | getFiledName()             | 获取form表单中组件的name属性值                               |
| String     | getName()                  | 获取type="file"的客户端选择的文件名称，在普通组件中使用是返回null |
| long       | getSize()                  | 获取文件的大小                                               |
| boolean    | isFormField()              | 判断当前组件是否为普通组件（true）                           |
| void       | write(File file)           | 将内存中的多媒体请求写入到磁盘上                             |
| String     | getString()                | 用于获取请求参数中的值                                       |
| String     | getString(String encoding) | 获取请求参数中的值（以某种编码格式获取）：为了解决中文乱码的问题 |

