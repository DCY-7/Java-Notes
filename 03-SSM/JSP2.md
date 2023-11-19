# JSP_note

## 十二、 Servlet

### 12.1 Servlet介绍

  **Servlet**（Server Applet），全称**Java Servlet**。是用Java编写的服务端程序。其主要功能是交互式地浏览和修改数据，生成动态Web内容。狭义的Servlet是指Java语言实现的一个接口，广义的Servlet是指任何实现了Servlet接口的类，一般情况下，人们将Servlet理解为后者。

  Servlet运行于支持Java的应用服务器上。从实现上讲Servlet可以响应任何类型的请求，但绝大多数情况下Servlet只用来扩展基于HTTP协议的应用服务器

  Servlet 执行以下**主要任务：**

- 读取客户端（浏览器）发送的显式的数据。这包括网页上的 HTML 表单。
- 读取客户端（浏览器）发送的隐式的 HTTP 请求数据。这包括 cookies、媒体类型和浏览器能理解的压缩格式等等。
- 处理数据并生成结果。这个过程可能需要访问数据库，执行 RMI 或 CORBA 调用，调用 Web 服务，或者直接计算得出对应的响应。
- 发送显式的数据（即文档）到客户端（浏览器）。该文档的格式可以是多种多样的，包括文本文件（HTML 或 XML）、二进制文件（GIF 图像）、Excel 等。
- 发送隐式的 HTTP 响应到客户端（浏览器）。这包括告诉浏览器或其他客户端被返回的文档类型（例如 HTML），设置 cookies 和缓存参数，以及其他类似的任务。

  Servlet的**工作模式：**

- 客户端发送请求至服务器
- 服务器启动并调用Servlet，Servlet根据客户端请求生成响应内容并将其传给服务器
- 服务器将响应返回客户端
- 其他



### 12.2 Servlet版本信息

- Servlet 2.x
- Servlet 3.x    开始支持注解

**注意：**在Servlet使用注解时，要保证应用服务器也支持注解。Tomcat 7 以上版本开始支持注解。



### 12.3 Servlet使用方式

#### 12.3.1 定义方式

- implement Servlet
- extends HTTPServlet（推荐）

#### 12.3.2 创建方式

- 基于XML 方式

  - 创建类继承HTTPServlet，并重写doGet()、doPost()方法

    ```java
    package com.sofwin.controller;
    
    public class UserServlet extends HttpServlet {
    
    	@Override
    	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    		doPost(req,resp);
    	}
    
    	@Override
    	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    		String username = req.getParameter("username");
    		//处理业务逻辑 
            PrintWriter writer = resp.getWriter(); 
            writer.print(username);
    	}
    
    }
    ```

  - 在web.xml中配置Servlet

    ```xml
    <servlet>
        <servlet-name>userServlet</servlet-name> 
        <servlet-class>com.sofwin.controller.UserServlet</servlet-class> 
    </servlet> 
    <servlet-mapping> 
        <servlet-name>userServlet</servlet-name>
        <url-pattern>userServlet</url-pattern> 
    </servlet-mapping>
    ```

  

- 基于Annotation（注解）

  ```java
  package com.sofwin.controller;
  
  @WebServlet(name="userServlet",urlPatterns = "/system/user")
  public class UserServlet extends HttpServlet {
  
  	@Override
  	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
  		doPost(req,resp);
  	}
  
  	@Override
  	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
  		String status = req.getParameter("status");
  		if("query".equals(status)) {
  			query(req,resp);
  		}
  	}
  
  }
  ```

  

### 12.4 Servlet生命周期

Jsp运行原理（jsp翻译成servlet，.java文件，并编译，和执行_service),单例模式的。

Servlet也是单例模式，第一访问的时候，会根据url-patterns查找servlet-name，并找到对应的servlet-class，全限定类名，容器会根据servlet-class会反射生成对象（调用构造方法），会去调用init方法（无参的init和有参的init，当servlet有配置的初始化参数 时，调用init有参的方法，否则调用无参），会去调用services方法，根据请求类型的不同，来选择调用doGet或doPost.

第n次访问的时候，依旧根据url-patterns查找servlet-name,因为servlet-name唯一，可以在内存中查找是否已经有实例的对象，如果有，去调用service方法，从而去调用doGet()或doPost()。

当应用服务器停止时，调用destroy方法，来销毁servlet

- 创建无参构造方法 【只执行一次】
- 无参init方法【一次】
- service()【多次】
- doGet()或doPost()【多次】
- destroy()【一次】

**总结**

> 当servlet被部署在应用服务器中（应用服务器中用于管理Java组件的部分被抽象成为[容器](https://zh.wikipedia.org/wiki/容器_(计算机科学))）以后，由容器控制servlet的生命周期。除非特殊指定，否则在容器启动的时候，servlet是不会被加载的，servlet只会在第一次请求的时候被加载和实例化。servlet一旦被加载，一般不会从容器中删除，直至应用服务器关闭或重新启动。但当容器做存储器回收动作时，servlet有可能被删除。也正是因为这个原因，第一次访问servlet所用的时间要大大多于以后访问所用的时间。
>
> servlet在服务器的运行生命周期为，在第一次请求（或其实体被内存垃圾回收后再被访问）时被加载并执行一次初始化方法，跟着执行正式运行方法，之后会被常驻并每次被请求时直接执行正式运行方法，直到服务器关闭或被清理时执行一次销毁方法后实体销毁。

第一次请求的响应时间和后面请求的响应时间不一样

可以使用 <load-on-startup> ，来修改servlet的实例化和初始化的时机（应用启动时执行）



### 12.5 使用Servlet实现用户表的CRUD

对比jsp，我们每个请求是一个Jsp。每一类型的请求用一个servlet来处理。使用1个servlet（UserServlet)来处理CRUD,如何去辨别每个请求。答： 增加请求参数，标识不同的请求

- 创建webProject
- 搭建项目（MVC分层，搭建jdbc使用druid）
  - 导入mysql-connector-j.jar,druid.jar,commons-fileupload.jar,commons-io.jar
  - 通过包分层
- 测试项目
  - 测试service层
- 编写新增的请求和页面C
- 编写查询的请求和页面R
- 编写更新的请求和页面U
- 编写删除的请求和页面D

**CRUD一天的时间可以从创建数据库到功能完成（js验证）**



## 十三、 过滤器

### 13.1 过滤器介绍

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203190819.jpg)

  **过滤器是Servlet的高级特性之一，是实现了Filter接口的Java类**

  过滤器可以动态地拦截请求和响应，以变换或使用包含在请求或响应中的信息。可以将一个或多个过滤器附加到一个 Servlet 或一组 Servlet。过滤器也可以附加到 JavaServer Pages(JSP) 文件和 HTML 页面。

### 13.2 过滤器原理

  过滤器是基于函数回调的

 老师安排任务，在任务完成后将执行结果反馈我

接口：

```java
public interface Fk { 
    void fkInfo(); 
}
```

实现：

```java
public class Teacher implements Fk { 
    
    @Override 
    public void fkInfo() { 
        System.out.println("向刘老师反馈信息....."); 
    } 
}
```

做任务：

```java
public class Student { 
    public void doHomeWork(Fk fk) { 
        System.out.println("写作业"); 
        fk.fkInfo(); 
    }
    public static void main(String[] args) { 
        Student st = new Student(); 
        st.doHomeWork(new Teacher()); 
    } 
}
```



### 13.3 过滤器使用方式

- 基于XML方式（推荐）

  - 定义过滤器（创建类实现Filter接口）

    ```java
    package com.sofwin.filter;
    
    public class CheckLoginFilter implements Filter {
    
        /**
          * 销毁方法 
          */
    	@Override
    	public void destroy() {
    
    	}
    
        /**
          * 过滤的方法 
          * 参数1：request 
          * 参数2:response 
          * 参数3：过滤器链 
          */
    	@Override
    	public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain)
    			throws IOException, ServletException {
    		System.out.println("pre_checkLogin.........."); 
            //放行代码
            chain.doFilter(request, response); 
            System.out.println("after_checkLogin........");
    	}
        
        /**
          * 初始化方法 
          */
    	@Override
    	public void init(FilterConfig filterConfig) throws ServletException {
    
    	}
    
    }
    ```

  - 在web.xml中配置过滤器

    ```java
    <filter>
      	<filter-name>checkLogin</filter-name>
      	<filter-class>com.sofwin.filter.CheckLoginFilter</filter-class>
    </filter>
    <filter-mapping>
      	<filter-name>checkLogin</filter-name>
      	<url-pattern>/*</url-pattern>
    </filter-mapping>
    ```

    

- 基于Annotation（注解）

  - 定义过滤器（创建类实现Filter接口）

    ```java
    package com.sofwin.filter;
    
    /**
      * @author Mac 
      *@WbFilter 类级别的注解，表示该类是一个过滤器 
      *filterName:指定过滤器的名称 
      *urlPatterns:指定过滤器过滤的请求 
      */
    @WebFilter(filterName = "checkLogin",urlPatterns = "/*")
    public class CheckLoginFilter implements Filter {
    
        /**
          * 销毁方法 
          */
    	@Override
    	public void destroy() {
    
    	}
    
        /**
          * 过滤的方法 
          * 参数1：request 
          * 参数2:response 
          * 参数3：过滤器链 
          */
    	@Override
    	public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain)
    			throws IOException, ServletException {
    		System.out.println("pre_checkLogin.........."); 
            //放行代码
            chain.doFilter(request, response); 
            System.out.println("after_checkLogin........");
    	}
        
        /**
          * 初始化方法 
          */
    	@Override
    	public void init(FilterConfig filterConfig) throws ServletException {
    
    	}
    
    }
    ```

  - 使用注解配置filter

### 13.4 过滤器生命周期

1. 创建过滤器的实例（应用启动时创建，**只执行一次**，过滤器是**单例模式**）
2. init(初始化方法在实例创建后调用，**只执行一次**)
3. doFilter(访问被过滤的请求是调用，**多次调用**)
4. destroy（在应用服务器停止时调用，只调用一次，用于销毁实例，释放资源）

### 13.5 过滤器链

  多个过滤器去过滤同一个资源（Servlet），多个过滤器称为一个过滤器链。

**过滤器链中每个过滤器的执行顺序：**

- 使用注解时：

  按照filterName中定义的名称的字符顺序来确定过滤器的顺序的。

  ```java
  @WebFilter(filterName="a",urlpatterns="/*") 
  @WebFilter(filterName="b",urlpatterns="/*") 
  //以上2个过滤器组成的过滤器链的顺序是先a,后b
  ```

- 使用XML方式时：

  xml方式的过滤器链是按照filter标签在web.xml中出现的顺序定义的，先定义的Filter先执行

  

### 13.6 过滤器特性（与拦截器对比）

- 过滤器依赖于容器（tomcat）
- 过滤器可以过滤所有资源（包含请求、图片等）
- 过滤器的原理基于函数回调的
- 过滤器不能访问请求上下文、值栈中的对象
- 过滤器只能在容器初始化时被调用一次

### 13.7 过滤器的使用场景

- 身份验证（用户列表的请求，在没有登录的情况下不允许访问（跳转到登录页））
- 字符编码设置(request.setCharactorEncoding,response.setCharactorEncoding)
- 字符过滤（论坛，不允许评论敏感字(gcd))

### 13.8 实现字符编码过滤

  spring中的CharactorEncodingFilter来，解决post请求的中文乱码问题，并解决响应客户时中文乱码问题

```java
package com.sofwin.filter;

public class CharactorEncodingFilter implements Filter {
	
	private static String encoding;

	@Override
	public void destroy() {

	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		request.setCharacterEncoding(encoding); 
		response.setCharacterEncoding(encoding); 
		chain.doFilter(request, response);
	}

	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
		encoding = filterConfig.getInitParameter("encoding");
	}

}
```

```xml
<filter>
  	<filter-name>charactorEncoding</filter-name> 
  	<filter-class>com.sofwin.filter.CharactorEncodingFilter</filter-class> 
  	<init-param> 
  		<param-name>encoding</param-name> 
  		<param-value>UTF-8</param-value> 
  	</init-param>
</filter>
<filter-mapping>
  	<filter-name>charactorEncoding</filter-name> 
  	<url-pattern>/*</url-pattern>
</filter-mapping>
```



## 十四、 监听器

### 14.1 监听器的定义

- 监听器是Servlet规范中定义的一种特殊类
- 用于监听ServletRequest、ServletSession、ServletContext等作用域对象的创建与销毁事件
- 用于监听作用域对象的属性发生修改的事件
- 可以在事件发生前、发生后做一些必要的处理

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203190828.jpg)

### 14.2 监听器的分类

1. 作用域对象的生命周期监听：作用域对象的创建和销毁
2. 作用域对象的属性监听：作用域中属性的添加、删除
3. 感知监听器：监听session对象中数据的状态和绑定状态

### 14.3 用于监听作用域的创建和销毁

request、session、servletContext

#### ServletRequestListener

- 基于XML方式

  1. 创建监听器

     ```java
     //创建一个类，实现ServletRequestListener,实现requestInitalized(),requestDestroyed()
     ```

  2. 注册监听器

     ```xml
     <listener> 
         <listener-class>com.sofwin.listener.RequestListener</listener-class> 
     </listener>
     ```

- 基于Annotation注解方式

  ```java
  @WebListener
  public class RequestListener implements ServletRequestListener {
  
  	/**
  	 * 监听request作用域销毁的
  	 */
  	@Override
  	public void requestDestroyed(ServletRequestEvent sre) {
  		System.out.println("request作用域销毁！");
  
  	}
  
  	/**
  	 * 监听request作用域创建的
  	 */
  	@Override
  	public void requestInitialized(ServletRequestEvent sre) {
  		System.out.println("request作用域创建！");
  	}
  
  }
  ```

- request作用域何时创建、销毁？

  访问.jsp文件（request对象自动创建，jsp响应后自动销毁）



#### HttpSessionListener

​    用于监听session作用域的创建和销毁，session作用域在调用getSession()方法时被创建。session作用域的销毁：1、session手动失效2、session过期3、session意外关闭

- 基于XML方式

  1. 创建监听器

     ```java
     //创建一个类，实现HttpSessionListener,实现sessionCreated(),sessionDestroyed()
     ```

  2. 注册监听器

     ```xml
     <listener> 
         <listener-class>com.sofwin.listener.SessionListener</listener-class> 
     </listener>
     ```

- 基于Annotation注解方式

  ```java
  @WebListener
  public class SessionListener implements HttpSessionListener {
  
  	/**
  	 *  当session作用域创建时触发该方法
  	 */
  	@Override
  	public void sessionCreated(HttpSessionEvent arg0) {
  		System.out.println("session作用域创建！");
  	}
  
  	@Override
  	public void sessionDestroyed(HttpSessionEvent arg0) {
  		System.out.println("session作用域销毁！");
  	}
  
  }
  ```

  

#### ServletContextListener

​    用于监听servletContext作用域的创建和销毁。当应用服务器启动时创建、在停止时（正常停止）时销毁

- 基于XML方式

  1. 创建监听器

     ```java
     //创建一个类，实现ServletContextListener,实现contextDestroyed(),contextInitialized()
     ```

  2. 注册监听器

     ```xml
     <listener> 
         <listener-class>com.sofwin.listener.ApplicationListener</listener-class> 
     </listener>
     ```

- 基于Annotation注解方式

  ```java
  @WebListener
  public class ApplicationListener implements ServletContextListener {
  
  	/**
  	 * 应用停止时被调用
  	 */
  	@Override
  	public void contextDestroyed(ServletContextEvent arg0) {
  		System.out.println("servletContextDestroyed......");
  	}
  
  	/**
  	 * servletContext或应用启动时被调用的
  	 */
  	@Override
  	public void contextInitialized(ServletContextEvent arg0) {
  		System.out.println("servletContextInitialized......");
  	}
  
  }
  ```



### 14.4 用于监听作用域中属性的创建、修改、删除

request、session、servletContext

#### ServletRequestAttributeListener

​    用于监听request作用域中属性的创建、修改、删除。第一次调用setAttribute()方法时会触发创建事件、同一个key在第二次调用此方法时会触发修改事件、调用removeAttribute()方法时触发删除事件

- 基于XML方式

  1. 创建监听器

     ```java
     //创建一个类，实现ServletRequestAttributeListener,实现attributeAdded(),attributeRemoved(),attributeReplaced()
     ```

  2. 注册监听器

     ```xml
     <listener> 
         <listener-class>com.sofwin.listener.RequestAttributeListener</listener-class> 
     </listener>
     ```

- 基于Annotation注解方式

  ```java
  @WebListener
  public class RequestAttributeListener implements ServletRequestAttributeListener {
  
  	@Override
  	public void attributeAdded(ServletRequestAttributeEvent arg0) {
  		System.out.println("request...add");
  	}
  
  	@Override
  	public void attributeRemoved(ServletRequestAttributeEvent arg0) {
  		System.out.println("request...remove");
  	}
  
  	@Override
  	public void attributeReplaced(ServletRequestAttributeEvent arg0) {
  		System.out.println("request...replaced");
  	}
  
  }
  ```

#### HttpSessionAttributeListener

​    监听session作用域中属性的创建、修改、删除

- 基于XML方式

  1. 创建监听器

     ```java
     //创建一个类，实现HttpSessionAttributeListener,实现attributeAdded(),attributeRemoved(),attributeReplaced()
     ```

  2. 注册监听器

     ```xml
     <listener> 
         <listener-class>com.sofwin.listener.SessionAttributeListener</listener-class> 
     </listener>
     ```

- 基于Annotation注解方式

  ```java
  @WebListener
  public class SessionAttributeListener implements HttpSessionAttributeListener {
  
  	@Override
  	public void attributeAdded(HttpSessionBindingEvent arg0) {
  		System.out.println("session...add");
  	}
  
  	@Override
  	public void attributeRemoved(HttpSessionBindingEvent arg0) {
  		System.out.println("session...remove");
  	}
  
  	@Override
  	public void attributeReplaced(HttpSessionBindingEvent arg0) {
  		System.out.println("session...replaced");
  	}
  
  }
  ```

#### ServletContextAttributeListener

​    监听ServletContext作用域中的值的创建、修改、删除的监听器    

- 基于XML方式

  1. 创建监听器

     ```java
     //创建一个类，实现ServletContextAttributeListener,实现attributeAdded(),attributeRemoved(),attributeReplaced()
     ```

  2. 注册监听器

     ```xml
     <listener> 
         <listener-class>com.sofwin.listener.ContextAttributeListener</listener-class> 
     </listener>
     ```

- 基于Annotation注解方式

  ```java
  @WebListener
  public class ContextAttributeListener implements ServletContextAttributeListener {
  
  	@Override
  	public void attributeAdded(ServletContextAttributeEvent arg0) {
  		System.out.println("context...added");
  	}
  
  	@Override
  	public void attributeRemoved(ServletContextAttributeEvent arg0) {
  		System.out.println("context...removed");
  	}
  
  	@Override
  	public void attributeReplaced(ServletContextAttributeEvent arg0) {
  		System.out.println("context...replaced");
  	}
  }
  ```



### 14.5 监听session对象中数据的状态和绑定状态

​    保存在Session域中的对象可以有多种状态：绑定(session.setAttribute(“bean”,Object))到Session中,随Session对象持久化到一个存储设备中；从Session域中解除(session.removeAttribute(“bean”))绑定,随Session对象从一个存储设备中恢复。
   Servlet 规范中定义了两个特殊的监听器接口”HttpSessionBindingListener和HttpSessionActivationListener”来帮助JavaBean 对象了解自己在Session域中的这些状态，实现这两个接口的类不需要 web.xml 文件中进行注册。

#### HttpSessionBindingListener

​    实现了HttpSessionBindingListener接口的JavaBean对象可以感知自己被绑定到Session中和 Session中删除的事件。

​    当对象被绑定到HttpSession对象中时，web服务器调用该对象的void valueBound(HttpSessionBindingEvent event)方法。
   当对象从HttpSession对象中解除绑定时，web服务器调用该对象的void valueUnbound(HttpSessionBindingEvent event)方法。

​    接口的实现必须是pojo上 

```java
public class JavaBeanDemo1 implements HttpSessionBindingListener {      
	private String name;          
	@Override     
	public void valueBound(HttpSessionBindingEvent event) {         
		System.out.println(name+"被加到session中了");     }      
	@Override     
	public void valueUnbound(HttpSessionBindingEvent event) {         
		System.out.println(name+"被session踢出来了");     
	}      
	public String getName() {         
		return name;     
	}      
	public void setName(String name) {         
		this.name = name;     
	}      
	public JavaBeanDemo1(String name) {         
		this.name = name;     
	} 
}
//上述的JavaBeanDemo1这个javabean实现了HttpSessionBindingListener接口，那么这个JavaBean对象可以感知自己被绑定到Session中和从Session中删除的这两个操作
```

#### HttpSessionActivationListener

​    实现了HttpSessionActivationListener接口的JavaBean对象可以感知自己被活化(反序列化)和钝化(序列化)的事件

​    当绑定到HttpSession对象中的javabean对象将要随HttpSession对象被钝化(序列化)之前，web服务器调用该javabean对象的sessionWillPassivate() 方法。这样javabean对象就可以知道自己将要和HttpSession对象一起被序列化(钝化)到硬盘中

​    当绑定到HttpSession对象中的javabean对象将要随HttpSession对象被活化(反序列化)之后，web服务器调用该javabean对象的sessionDidActive()方法。这样javabean对象就可以知道自己将要和 HttpSession对象一起被反序列化(活化)回到内存中

​    javabean随着HttpSession对象一起被活化的前提是该javabean对象除了实现该接口外还应该实现Serialize接口

```java
public class JavaBeanDemo2 implements HttpSessionActivationListener, Serializable {            
    private static final long serialVersionUID = 7589841135210272124L;    
    private String name; 
         
    @Override     
    public void sessionWillPassivate(HttpSessionEvent se) {                  
        System.out.println(name+"和session一起被序列化(钝化)到硬盘了，session的id是："+se.getSession().getId());     
    }      
    @Override     
    public void sessionDidActivate(HttpSessionEvent se) {         
        System.out.println(name+"和session一起从硬盘反序列化(活化)回到内存了，session的id是："+se.getSession().getId());     
    }     
    public String getName() {         
        return name;     
    }     
    public void setName(String name) {         
        this.name = name;     
    }      
    public JavaBeanDemo2(String name) {        
        this.name = name;     
    } 
}
```



## 十五、 EL表达式语言

​    EL（Express Language） 表达式语言是一种简洁的数据访问语言。通过它可以在JSP文件中方便地访问应用程序数据，从而代替传统的基于“<%= %>”形式的java表达式。

```jsp
java表达式：<%=request.getParameter("username")%>
EL表达式：${param.username}
```

### 15.1 基本语法

​    EL表达式的基本形式为：`${var}`,EL表达式和Java表达式一样，既可以插入到JSP文件的模板文本中，也可以作为JSP标签的属性的值。

### 15.2 访问对象的属性及数组的元素

- 使用`.`访问对象的属性

  ```
  ${customer.name}
  ```

- 使用`[]`访问对象的属性或数组

  ```
  ${customer["name"]}
  ${customer.name}
  ${customers[0]}
  ```

### 15.3 EL运算符

- 算术运算符

- 关系运算符

- 逻辑运算符

- 条件运算符

- empty运算符

  ```
  ${empty var}
  //检查是否为空值
  //如果变量var为null或var变量不存在，就返回null
  ```

### 15.4 隐含对象

​    EL语言定义了11个隐含对象，其中10个都是java.util.Map 类型。

| 隐含对象的固定变量名 | 类型          | 说明                                                         |
| -------------------- | ------------- | ------------------------------------------------------------ |
| cookie               | java.util.Map | 把客户请求中的Cookie名称和Cookie对象进行映射                 |
| header               | java.util.Map | 把HTTP请求头部的项目名称和项目值进行映射，例如${header.host}等价于<%request.getHeader("host")%> |
| headerValues         | java.util.Map | 把HTTP请求头部的项目名称和所有匹配的项目值的数组进行映射，例如${headerValues["accept-language"]}等价于<%=request.getHeaders("accpet-language")%> |
| initParam            | java.util.Map | 把Web应用的初始化参数名和参数值进行映射                      |
| pageScope            | java.util.Map | 把页面范围内的属性名和属性值进行映射                         |
| param                | java.util.Map | 把客户请求中的请求参数名和参数值进行映射                     |
| paramValues          | java.util.Map | 把客户请求中的请求参数名和所有匹配的参数值数组进行映射。例如${paraValues.username}等价于<%=request.getParmaeterValues("username")%> |
| requestScope         | java.util.Map | 把请求范围内的属性名和属性值进行映射                         |
| sessionScope         | java.util.Map | 把会话范围内的属性名和属性值进行映射                         |
| applicationScope     | java.util.Map | 把Web应用范围内的属性名和属性值进行映射                      |
| pageContext          | PageContext   | 表示把javax.servlet.jsp.PageContext对象                      |

按照类型可以分为以下四种类型：

- 表示HTTP请求中的特定数据：header、headerValues、param、paramValues和cookie
- 表示特定范围：pageScope、requestScope、sessionScope和applicationScope
- 表示PageContext对象：pageContext
- 表示Web应用的初始化参数集合



## 十六、 JSTL

​    自定义JSP标签是用来代替JSP中的Java程序片段的有效途径。大多数Web应用的JSP文件常常要实现一些通用的功能：比如重定向、对日期时间的格式化输出等，此外，这些JSP文件还要实现一些通用的流程控制逻辑：比如用if-else语句来进行条件判断，再比如用while语句或for语句来进行循环操作。

​    为了提高Web应用的开发效率，Oracle公司制定了一组标准标签库的最新规范，这组标准标签库简称为JSTL（JavaServer Pages Standard Tag Library）

### 16.1 JSTL标签库简介

​    JSTL标签库实际上包含五个不同的标签库。JSTL规范为这些标签库的URI和前缀做了约定，如表所示：

| 标签库名  | 前缀 | URI                                   | 描述                                                         |
| --------- | ---- | ------------------------------------- | ------------------------------------------------------------ |
| Core      | c    | http://java.sun.com/jsp/jstl/core     | 核心标签库，包括一般用途的标签、条件标签、迭代标签和URL相关的标签 |
| Functions | fn   | http://java.sun.com/jsp.jstl/function | 包含了一组通用的EL函数，在EL表达式中可以使用这些EL函数       |
| I18N      | fmt  | http://java.sun.com/jsp.jstl/fmt      | 包含编写国际化Web应用的标签，以及对日期、时间和数字格式化的标签 |
| Sql       | sql  | http://java.sun.com/jsp.jstl/sql      | 包含访问关系型数据库的标签                                   |
| Xml       | xml  | http://java.sun.com/jsp.jstl/xml      | 包含对XML文档进行操作的标签                                  |

### 16.2 JSTL Core标签库

​    在JSP文件中使用Core标签库，要先通过`taglib`指令引入该标签库：

```JSP
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

#### 一般用途标签

| 标签         | 描述                                           |
| ------------ | ---------------------------------------------- |
| `<c:out>`    | 把一个表达式的结果打印到网页上                 |
| `<c:set>`    | 定义变量并给变量赋值                           |
| `<c:remove>` | 删除指定范围或作用域中变量                     |
| `<c:catch>`  | 用于捕获异常，并把异常对象放在指定的命名变量中 |

#### 条件标签

| 标签                                    | 描述                            |
| --------------------------------------- | ------------------------------- |
| `<c:if>`                                | 用于实现Java语言中的if语句功能  |
| `<c:choose>`,`<c:when>`,`<c:otherwise>` | 用于实现java语言中的if-else功能 |

#### 迭代标签

| 标签          | 描述                                         |
| ------------- | -------------------------------------------- |
| `<c:forEach>` | 用于遍历集合中的对象，并且能重复执行标签主体 |

#### URL相关标签

| 标签           | 描述                                             |
| -------------- | ------------------------------------------------ |
| `<c:import>`   | 包含其他Web资源，与`<jsp:include>`指令的作用类似 |
| `<c:url>`      | 按照特定的重写规则重新构造URL                    |
| `<c:redirect>` | 负责重定向                                       |

```JSP 
<c:set var="username" value="admin" scope="request"></c:set>
${requestScope.userName }<br>
<c:out value="${userName }"></c:out>
<c:remove var="username" scope="request"/>
<%-- <c:redirect url="add.jsp"></c:redirect>
 --%>
<%-- <c:catch var="ex">
<%
int a=1;
int b=0;
int c=a/b;
%>
</c:catch> --%>

<%
	List users = new ArrayList(); 
	for(int i=0;i<20;i++){ 
		Test t = new Test(); 
		t.setUsername("username"+i); 
		users.add(t); 
	}
	request.setAttribute("users", users);
%>

<h1>循环结构</h1>
<c:forEach items="${users }" var="user" varStatus="t"> 
	${t.index }--${user.username }<br> 
</c:forEach>
<h1>---------------------------</h1>
<c:forEach items="${users }" var="user" varStatus="t" begin="4" end="9" step="2">
	${t.index }--${user.username }<br>
</c:forEach>
<h1>选择结构</h1>
<c:forEach items="${users }" var="user" varStatus="t" >
	<c:if test="${t.index%2==0 }">
		偶数：${t.index }--${user.username }<br>
	</c:if>
	<c:if test="${t.index%2==1 }">
		奇数：${t.index }--${user.username }<br>
	</c:if>
</c:forEach>
<h1>---------------------------</h1>
<c:forEach items="${users }" var="user" varStatus="t" >
	<c:choose> 
		<c:when test="${t.index%2==0 }">偶数：${t.index }--${user.username }<br></c:when> 
		<c:when test="${t.index%5==0}">5的倍数-${t.index }--${user.username }<br></c:when> 
		<c:otherwise>奇数：${t.index }--${user.username }<br></c:otherwise> 
	</c:choose>
</c:forEach>
```



### 16.3 JSTL  Functions标签库

​    在JSP文件中使用Function标签库，要先通过`taglib`指令引入该标签库：

```jsp
<%@taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
```

| 标签                        | 描述                                             |
| --------------------------- | ------------------------------------------------ |
| `<fn:contains()>`           | 用于判断源字符串是否含有目标字符串               |
| `<fn:containsIgnoreCase()>` | 用于判断源字符串是否含有目标字符串，忽略大小写   |
| `<fn:startsWith()>`         | 判断字符串是否以指定的字符开头                   |
| `<fn:endWith()>`            | 判断字符串是否以指定的字符结尾                   |
| `<fn:indexOf()>`            | 获取子字符串第一次出现的位置，如果没有找见返回-1 |
| `<fn:length()>`             | 返回字符串的长度或集合中的元素的个数             |
| `<fn:replace()>`            | 使用新的字符串替换目标字符串中的数据             |

```jsp
<%
	List users = new ArrayList(); 
	for(int i=0;i<100;i++){ 
		Test t = new Test(); 
		t.setUsername("username"+i); 
		users.add(t); 
	}
	request.setAttribute("users", users); 
%>
<h1>函数</h1> 
${fn:contains("Hello World","E") } <br> 
${fn:containsIgnoreCase("Hello World","E") } <br> 
${fn:startsWith("Hello World","He") } <br> 
${fn:endsWith('Hello World','ld') } <br>
${fn:indexOf('Hello World','orld') } <br>
${fn:length(users)} <br>
```



### 16.4 JSTL I18N标签库

​    在JSP文件中使用I18N标签库，要先通过`taglib`指令引入该标签库：

```JSP
<%@taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
```

| 标签                 | 描述                                 |
| -------------------- | ------------------------------------ |
| `<fmt:setTimeZone>`  | 设置时区,把时区保存到特定范围内      |
| `<fmt:timeZone>`     | 设置标签主体使用的时区               |
| `<fmt:formatNumber>` | 格式化数字                           |
| `<fmt:parseNumber>`  | 解析被格式化的字符串类型的数字       |
| `<fmt:formatDate>`   | 格式化日期和时间                     |
| `<fmt:parseDate>`    | 解析被格式化的字符串类型的日期和时间 |

```jsp
<%
	List users = new ArrayList(); 
	for(int i=0;i<100;i++){ 
		Test t = new Test(); 
		t.setUsername("username"+i); 
		users.add(t); 
	}
	request.setAttribute("users", users); 
	request.setAttribute("date", new Date());
%>
<h1>格式化标签库</h1>
${date }<br>
<fmt:formatDate value="${date }" pattern="yyyy-MM-dd"/><br> 
<fmt:formatDate value="${date }" pattern="yyyy年MM月dd日hh点mm分ss秒"/><br>
<fmt:formatNumber value="3.1415926" pattern="$0.00"></fmt:formatNumber> <br> 
<fmt:formatNumber value="3.1415926" type="percent"></fmt:formatNumber>
```



## 十七、 异步请求（特别重要）

### 17.1 AJAX

AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。

AJAX 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。

通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203190839.png)

### 17.2 异步请求、同步请求的比较

1. 在请求方式上：
   - 同步请求由用户直接向浏览器发送，而异步请求由异步引擎代理对象发送请求
2. 服务器响应内容上：
   - 同步请求响应整个页面，而异步请求响应数据
3. 客户端处理方式上：
   - 同步请求重新加载和渲染整个页面，而异步请求动态更新局部内容

### 17.3 原生态AJAX

1. 创建XMLHttpRequest代理对象

   ```js
   var httpRquest;
   //主流浏览器的获取方式
   httpRequest=new XMLHttpRequest();
   //ie5和ie6
   httpRequest = new ActiveXObject("Microsoft.XMLHTTP");
   ```

2. 使用代理对象发送请求

   ```js
   open(method,url,asy(true|false是否为异步))
   send(); 
   //需要通过onreadystatechange事件来监听
   ```

3. 使用JS操作CSS或HTML

   ```js
   <%@ page language="java" contentType="text/html; charset=UTF-8"
       pageEncoding="UTF-8"%>
   <!DOCTYPE html>
   <html>
   <head>
   <meta charset="UTF-8">
   <title>Insert title here</title>
   </head>
   <body>
   <h1>标题1</h1>
   用户名：<input type="text" name="username" id="username" onblur="checkName(this);">
   <span id="info"></span>
   </body>
   <script type="text/javascript">
   function checkName(obj){ 
   	var username = obj.value; 
   	var xmlRequest;
   	if(window.XMLHttpRequest){ 
   		xmlRequest = new XMLHttpRequest();
   	}
   	else{
   		xmlRequest = new ActiveXObject("Microsoft.XMLHTTP"); 
   	}
   	xmlRequest.open("get",'/checkUsername?username='+username,true); 
   	xmlRequest.send();
   	xmlRequest.onreadystatechange=function(){
   		if(xmlRequest.readyState==4 && xmlRequest.status==200){
   			var text = xmlRequest.responseText;
   			if(text=='ok'){ 
   				document.getElementById("info").innerHTML='该用户可用'; 
   			}else{
   				document.getElementById("info").innerHTML='该用户不存在'; 
   			}
   		}
   	}
   }
   function test(){
   	alert("My First JavaScript");
   }
   </script>
   </html>
   ```

### 17.4 XMLHttpRequest API详解

> 响应

- `ResponseText`：用于获取服务器端响应给代理对象的文本信息
- `ResponseXML`：服务器端需要响应给代理对象代理对象的XML格式的数据

> 状态

- `ReadyState`：0请求未初始化、1请求建立连接、2请求已接收、3请求处理中、4请求已完成，响应已就绪
- `State`：200正常响应、400内部错误

> 发送请求

```js
xmlRquest.open(请求方式get|post，请求地址url,是否异步执行true|false); 
xmlRquest.send();
//无参，请求方法是get,可以将请求参数用?形式封装,如果请求方式是post请求，需要传递参数 
xmlRequest.setRequestHeader("Content-type","application/x-www-form-urlencoded"); 
xmlRequest.send('userName='+userName);
```

### 17.5 基于JQuery的AJAX

1. 导入JQuery核心库`<script type="text/javascript" src="jquery-3.5.0.min.js"></script>`
2. JQuery使用工厂函数

#### 17.5.1 $.ajax（掌握）

```js
$.ajax({
	url:'异步请求地址',
	type:'post|get请求方式', 
    dataType:'text,json,xml,script响应的数据格式', 
    success:function(ret){
	//响应成功后触发的回调函数，ret为响应的数据
	},
	data:{
	//传递参数
	key1:value1,
	key2:value2
	},
	error:function(){ 
    //请求没有响应成功时触发的回调函数
	},
	async:是否异步请求true|false,
    timeout:设置请求超时时间
});
```

```js
function checkName(obj) {
	var userName = obj.value;
	$.ajax({
		url : 'checkUserName',
		type : 'post',
		dataType : 'text',
		async : false,
		data : {
			'userName' : userName
		},
		success : function(ret) {
			if (ret == 'ok') {
				$("#info").html('用户存在');
			} else {
				$("#info").html("用户不存在");
			}
		}
	});
	alert(123)
}
```

#### 17.5.1 $.get

```js
//$.get就是在$.ajax的基础上增加了type:'get' 
$.get(url,data,success回调函数,dataType); 
url:请求地址
data:请求参数。可以省略 
success:请求成功以后的回调，使用匿名函数 
dataType:text,xml,html,script,json 请求响应的数据类型
```

```js
	$.get('checkUserName?userName=' + userName, function(ret) {
		if (ret == 'ok') {
			$("#info").html("存在");
		} else {
			$("#info").html('不存在');
		}
	});
```

#### 17.5.1 $.post

```js
$.post(url,data,success,dataType);
//使用post方式发送异步请求 url:异步请求地址
data:请求参数
success:响应成功的回调
dataType:响应的数据格式
```

```js
	function checkName(obj) {
		var userName = obj.value;
		$.post('checkUserName', {
			'userName' : userName
		}, function(ret) {
			if (ret == 'ok') {
				$("#info").html('存在');
			} else {
				$("#info").html('不存在');
			}
		})
	}
```

#### 17.5.1 $.getJSON

```js
$.getJSON(url,data,success);
//规定了请求方式get请求，响应的数据格式为json格式
```

### 17.6 JSON

​    [JSON](https://baike.baidu.com/item/JSON)([JavaScript](https://baike.baidu.com/item/JavaScript) Object Notation, JS 对象简谱) 是一种轻量级的数据交换格式。是为了降低xml方式数据的传递，web Service SOA 以 xml的格式(数据量大、解析负责)，是一种轻量级的数据交互格式，它是基于ECMASCRIPT的子集， 采用了完全独立于编程语言的文本格式来存储和表示数据。数据层次结构简洁清晰。

#### 17.6.1 JSON格式

- JSON对象：`{‘属性名’:值,'属性名':值'......} 注意 最后一个属性对应的值后面没有,`
- JSON数组：`[{json对象},{json对象}] 注意 最后一个对象后没有,`

#### 17.6.2 常用JS的JSON操作函数

```js
JSON.parse(str);//将json格式的字符串转为json对象或json数组 
JSON.stringify(jsonObject);//将json对象或json数组转为json格式的字符串
```

#### 17.6.3 FastJSON

​    为了解决json字符串的拼接问题，市面上出现了很多的json工具项目(可以将java对象转为json格式的字符串，将java集合转为json数组格式的字符串)

- FastJSON
- Jackson
- json

fastjson用于将Java Bean序列化为JSON字符串，也可以从JSON字符串反序列化到JavaBean

> https://github.com/alibaba/fastjson/wiki/Quick-Start-CN
>
> 官方文档