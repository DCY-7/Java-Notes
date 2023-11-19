# JDBC （Java DataBase Connectivity）

## 一、 JDBC简介

JDBC （Java DataBase Connectivity）Java数据库连接，提供了Java语言和数据库连接交互的API。

JDBC是一种规范，提供了一套接口，允许以一种可移植的方式访问数据库底层。

只能操作关系型数据库。



## 二、 JDBC架构

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203191323.jpg)

JDBC 支持两层和三层处理模型进行数据库的访问，但是通常使用两层模型。

- JDBC API  ：是由JDK提供的用于JDBC管理数据库连接的
- JDBC Driver API ：支持JDBC管理的驱动器连接。



```Java
//1. java 提供jdbc api ---------interface 
//2. 不同的关系型数据库的诞生 
//3. 关系型数据库厂商-----提供一个jdbc driver -----如何去操作数据库必须implments interface 
//4. 可以将jdbc driver -----以第三方依赖的形式导入的java | web project 
//5. 厂商操作库所有的实现 可以使用多态来表示（对象可以通过反射生成对象） 
//6. 可以按照相同的标准，不同的实现去操作不同的数据库 

User user = new Use r();//一般创建对象的方式 
//不知道类，或者说类不是自己创建的，是别人创建的（jar) 
Class clazz = Class.forName("全限定类名"); 
Object o = clazz.newInstance();//就生成了对象
```



## 三、 常用组件

在JDBC API 中常用的组件基本都是接口，java.sql 包中。

### 3.1 DriverManager

管理数据库驱动的，可以在项目加载的驱动列表中读取最近的一条驱动程序（可以在项目中多次去使用Class.forName来驱动，如果Class.forName加载的是同一个驱动，DriverManager会使用最后一次加载的驱动）。

**分析得到**:Class.forName()只需要加载一次就可以了。

```Java
//静态代码块,不能抛出异常，捕获就可以，没有在项目中加入mysql-connector-j.jar 
static{ 
    Class.forName("com.mysql.cj.jdbc.Driver"); 
}
```

| 返回类型                   | 方法                                                         | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| static Connection          | **getConnection(String url)**                                | 用于获取connection对象的。将用户名和密码集成在url中；url="jdbc:mysql://127.0.0.1:3306/java1908z user=root&password=123456" |
| static Connection          | **getConnection(String url , Properties info)**              | 获取Connection对象，url连接字符串,Properties配置对象用于保存配置文件信息，可以将用户名，密码及相关参数保存在properties对象中 |
| static Connection          | **getConnection(String url , String user,  String password)** | 获取Conenction对象。url连接字符串,user为用户名,password是密码 |
| static Enumeration<Driver> | **getDrivers()**                                             | 获取已经加载的驱动，返回枚举类型                             |



### 3.2 Driver

是由第三方数据库厂商提供，一般直接由DriverManager管理，每个驱动程序类必须实现的接口。



### 3.3 Connection

用于获取java和数据库会话的连接信息。与特定数据库的连接（会话）。在连接上下文中执行 SQL 语句并返回结果。 `Connection` 对象的数据库能够提供描述其表、所支持的 SQL 语法、存储过程、此连接功能等等的信息。

|      返回值       | 方法                                  | 描述                                                         |
| :---------------: | :------------------------------------ | ------------------------------------------------------------ |
|       void        | **close()**                           | 关闭，释放Collection资源                                     |
|       void        | **commit()**                          | 事务提交（只有为显示事务时，才需要手动执行commit()方法，执行完成后数据会持久化到数据库中。只有在getAutoCommit()返回发了false的时候才能使用） |
|     Statement     | **createStatement()**                 | 创建一个 Statement对象；来将SQL语句发送到数据库              |
|      boolean      | **getAutoCommit()**                   | 获取此  Connection对象是否为隐式事务，true代表隐式事务，false代表显式事务 |
|       void        | **setAutoCommit(boolean autoCommit)** | 将此连接的自动提交模式设置为给定状态。true为自动提交,false为手动提交 |
|      boolean      | **isClosed()**                        | 判断当前连接是否已经被释放                                   |
|      boolean      | **isReadOnly()**                      | 判断当前连接是否只读模式。isReadOnly可以提高查询效率         |
| CallableStatement | **prepareCall(String sql)**           | 用来获取CallableStatement对象                                |
| PreparedStatement | **prepareStatement(String sql) **     | 创建一个 PreparedStatement 对象来将参数化的 SQL 语句发送到数据库 |
|       void        | **rollback()**                        | 事务回滚，回滚所有insert、update、delete信息                 |
|       void        | **rollback(Savepoint savepoint)**     | 事务回滚，按照回滚点进行回滚                                 |
|     Savepoint     | **setSavepoint**()                    | 设置回滚点                                                   |

```java
public static void main(String[] args) throws SQLException { 
    Connection conn = null; 
    Statement stmt = null; 
    PreparedStatement pstmt = null; 
    PreparedStatement pstmt2 = null; 
    Savepoint s1=null; 
    //如果insert语句能够插入到数据库中，说明有事务 
    String sql="insert into b_user(sex,name) values(?,?)"; 
    try {
        conn = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/java1908z", "root", "123456"); 
        //改为显示事务 
        conn.setAutoCommit(false); 
        System.out.println(conn.getAutoCommit());//false
        pstmt = conn.prepareStatement(sql); 
        pstmt2 = conn.prepareStatement(sql); 
        pstmt.setString(1, "男"); 
        pstmt.setString(2, "ceshi"); 
        pstmt.executeUpdate(); 
        s1 = conn.setSavepoint();
        pstmt2.setString(1, "女"); 
        pstmt2.setString(2, "ceshi女"); 
        pstmt2.executeUpdate(); 
        conn.rollback(s1); 
        //手动提交 
        conn.commit(); 
    } catch (SQLException e) { 
        e.printStackTrace(); 
    }finally { 
        try {
            if(pstmt!=null)pstmt.close();
            if(conn!=null)conn.close();
        } catch (SQLException e) { 
            // TODO Auto-generated catch block 
            e.printStackTrace(); 
        } 
    }
}
```



### 3.4 Statement

用于执行静态 SQL 语句并返回它所生成结果的对象。 

#### 3.4.1 Statement

**注意**：SQL语句只能为静态SQL语句，包含java中字符串拼接都属于静态SQL

|  返回值   | 方法                      | 描述                                                         |
| :-------: | ------------------------- | :----------------------------------------------------------- |
|   void    | addBatch(String sql)      | 向statment中增加批量sql语句，一般用于dml语句                 |
|   void    | clearBatch()              | 清空命令列表                                                 |
|  boolean  | execute(String sql)       | 如果sql语句是查询语句，返回true，否则返回false(select * from b_user;delete from b_user;) |
| ResultSet | executeQuery(String sql)  | 执行给定的  DQL语句，该语句返回单个 ResultSet 对象(查询结果) |
|    int    | executeUpdate(String sql) | 执行DML、DDL语句，返回结果为影响行数                         |
|   int[]   | executeBatch()            | 在使用了addBatch以后，statement对象中包含了多条sql语句，同时执行多条SQL语句，返回影响行数 |

> 存在SQL注入漏洞，利用了静态SQL的字符拼接

- 用户登录

  ```MySQL
  CREATE TABLE t_user(
  	id INT PRIMARY KEY AUTO_INCREMENT,
      usernaem varchar(20),
      pwd varchar(20)
  );
  ```

- 2个变量分别存储用户名和密码，当通过用户名和密码查询t_user表中的数据，如果有，认为登陆成功，否则登录失败

  ```Java
  public static void main(String[] args) throws SQLException { 
  	    String username="a";
      	String pwd=="' or 'a'='a";
    		Connection conn = null; 
  	    Statement stmt = null; 
  	    ResultSet rs = null;
  	    String sql ="select * from t_user where username='"+username+"' and pwd='"+pwd+"'";
      	// select * from t_user where username='a' and pwd='' or 'a'='a'
  	    try {
  	        conn = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/java1908z", "root", "123456"); 
  	        //改为显示事务 
  	        conn.setAutoCommit(false); 
  	        while(rs.next()) { 
                  flag=true; 
              }if(flag) { 
                  System.out.println("登录成功"); 
              }else { 
                  System.out.println("登录失败"); 
              } 
  	    } catch (SQLException e) { 
  	        e.printStackTrace(); 
  	    }finally { 
  	        try {
                  if(rs!=null)rs.close();
  	            if(pstmt!=null)pstmt.close();
  	            if(conn!=null)conn.close();
  	        } catch (SQLException e) { 
  	            // TODO Auto-generated catch block 
  	            e.printStackTrace(); 
  	        } 
  	    }
  	}
  ```

  



#### 3.4.2 CallableStatement

用于执行存储过程，`{call 存储过程名称 (?，?)}`，参数必须使用占位符，因为对于有返回值的存储过程我们需要通过占位符来注册返回值

```java 
public class JdbcTest3 {

	static {		
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	public static void main(String[] args) throws SQLException { 
	    Connection conn = null; 
	    CallableStatement prepareCall=null;
	    conn=DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/java1908z", "root", "123456");
	    try {
			prepareCall=conn.prepareCall("{call test_login(?,?,?)}");
			//给占位符设置参数
			prepareCall.setString(1, "张三");
			prepareCall.setString(2, "zhangsan");
			//注册一个输出参数（jdbc不知道存储过程中哪个参数是out) 
			//参数1，占位符的索引，占位符索引以1开头 
			//参数2，指定输出参数的类型 
			prepareCall.registerOutParameter(3, java.sql.Types.VARCHAR); 
			//执行存储过程 
			prepareCall.execute();
			//通过callablestatment对象获取输出值 
			System.out.println(prepareCall.getObject(3));
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}
```



#### 3.4.3 PreparedStatement

表示预编译的 SQL 语句的对象。 **动态SQL**，在静态SQL的基础上增加`?`作为占位符，占位符的复制，包含了数据类型，如果是String类型的，会自动拼接单引号。SQL 语句被预编译并存储在 PreparedStatement 对象中。然后可以使用此对象多次高效地执行该语句。

| 返回值    | 方法                            | 描述                                                         |
| --------- | ------------------------------- | ------------------------------------------------------------ |
| ResultSet | executeQuery()                  | 用于执行SELECT语句，对比statement，不需要sql语句             |
| int       | executeUpdate()                 | 用于执行DDL、DML语句                                         |
| void      | set类型(int parameterIndex, 值) | 为preparedStatment对象中预编译SQL语句中的占位符赋值，参数1为占位符的索引，从1开始，参数2为当前占位符的值 |

```Java
public class JdbcTest4 {

	static {		
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	public static void main(String[] args) throws SQLException { 
	    Connection conn = null; 
	    PreparedStatement pstmt=null;
	    ResultSet rs=null;
	    String username="张三";
	    String pwd="zhangsan";
	    
	    try {
			conn=DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/java1908z", "root", "123456");
			String sql="select * from t_user where username=? and pwd=?";
			pstmt=conn.prepareStatement(sql);
			//给占位符赋值
			pstmt.setString(1, username);
			pstmt.setString(2,pwd);
			rs=pstmt.executeQuery();
			boolean flag=false;
			while(rs.next()) {
				flag=true;
			}
			if(flag) {
				System.out.println("登陆成功！");
			}else {
				System.out.println("登录失败！");
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally {
			if(rs!=null)rs.close();
			if(pstmt!=null)pstmt.close();
			if(conn!=null)conn.close();
		}
	    
	}
}
```



### 3.5 ResultSet

表示数据库查询结果集的数据表。（二维表）既保持了查询结果的各行的数据，同时还保持了查询结构的表结构（每列的列名和列的类型）

ResultSet 对象具有指向其当前数据行的光标。最初，光标被置于第一行之前。 next 方法将光标移动到下一行；因为该方法在 ResultSet 对象没有下一行时返回 false ，所以可以在 while 循环中使用它来迭代结果集。

在while循环中的rs为当前行的数据。

| 返回值类型        | 方法                        | 描述                                                         |
| ----------------- | --------------------------- | ------------------------------------------------------------ |
| 类型              | get类型(Int columnIndex)    | 在当前行中，根据列的索引获取对应列的值，索引以1开始          |
| 类型              | get类型(String columnLable) | 在当前行中，根据查询结果的列名获取对应的值                   |
| boolean           | next()                      | 结果集的指针向下移动一行，如果移动后为数据行，返回true，否则返回false |
| ResultSetMetaData | getMetaData()               | 获取此 `ResultSet` 对象列的编号、类型和属性（查询的数据的表结构信息） |



### 3.6 ResultSetMetaData

可用于获取关于 `ResultSet` 对象中列的类型和属性信息的对象

| 返回值类型 | 方法                       | 描述                                                     |
| ---------- | -------------------------- | -------------------------------------------------------- |
| int        | getColumnCount()           | 返回查询结果集的总列数                                   |
| String     | getColumnLabel(int column) | 通过列索引获取当前列的名称（可以获取列名也可以获取别名） |
| String     | getColumnName(int column)  | 通过索引获取当前列的列名                                 |



### 3.7 SQLException

提供关于数据库访问错误或其他错误信息的异常.



## 四、 JDBC开发步骤

### 4.1 导入数据库连接驱动

是由数据库供应商提供，每个关系型数据库都有其对应的连接驱动

### 4.2 使用反射加载驱动

Class.forName('')

### 4.3 获取连接（Connection）

通过DriverManager来生成会话

### 4.4 编写SQL语句并发送到数据库执行



### 4.5 处理返回结果



### 4.6 关闭资源



## 五、 JDBC入门

> 需求：查询用户表，将用户表中的数据显示在控制台
>

1. 创建Java Project

2. 导入数据库连接驱动

   在baidu中搜索maven repository (maven 中央仓库)，去下载对应的mysql connector j驱动程序。将其copy到项目的根目录中。右键add build path，将其加载到构建路径中

3. 创建Java类

   使用反射加载驱动，Class.forName("com.mysql.cj.jdbc.Driver")
   
4. 获取连接

   ```Java
   Connection conn=DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/java1908z","root","123456");
   ```

5. 编写SQL并发送到数据库中执行

   ```Java
   //执行静态SQL语句
   String sql = "select * from b_user"; 
   Statement stmt = conn.createStatement(); 
   ResultSet rs = stmt.executeQuery(sql);
   
   //执行存储过程
   prepareCall=conn.prepareCall("{call test_login(?,?,?)}");
   prepareCall.setString(1, "张三");
   prepareCall.setString(2, "zhangsan");
   prepareCall.registerOutParameter(3, java.sql.Types.VARCHAR); 
   prepareCall.execute();
   System.out.println(prepareCall.getObject(3));
   
   //执行动态SQL语句
   String sql="select * from t_user where username=? and pwd=?";
   pstmt=conn.prepareStatement(sql);
   pstmt.setString(1, username);
   pstmt.setString(2,pwd);
   rs=pstmt.executeQuery();//DQL语句
   ```

6. 处理返回结果

   ```Java
   while(rs.next()){
       System.out.println(rs.getInt(1)+","+rs.getString(2));
   }
   ```

7. 关闭资源

   每个端口的连接总数是固定的，如果你在和数据库进行通信时，会占用3306端口（服务器），如果你一直保持长连接，端口无法进行访问，必须要对已经完成资源进行释放。

   **注意：先使用的资源后释放**

   ```Java
   finally {
   			if(rs!=null)rs.close();
   			if(pstmt!=null)pstmt.close();
   			if(conn!=null)conn.close();
   		}
   ```

```Java
public static void main(String[] args) throws SQLException { 
    	//每个数据库都有不同的连接字符串，（由厂商提供）可以使用?来添加参数 
    	//如果无法连接数据库并提示时区不正确时可以用？serverTimezone=UTC
	    String url="jdbc:mysql://127.0.0.1:3306/java1908z";
    	//连接的目标数据库的用户名
    	String username="root";
    	//密码
    	String pwd="123456";
    	Connection conn = null; 
	    Statement stmt=null;
	    ResultSet rs=null;    
	    try {
            //使用反射加载驱动，参数是全限定类名
            Class.forName("com.mysql.cj.jdbc.Driver");
            //获取连接
			conn=DriverManager.getConnection(url,username,pwd);
			String sql="select * from b_user";
            //使用statement对象来发送并执行sql语句
			stmt=conn.createStatement(sql);
			rs=pstmt.executeQuery(sql);
			//处理返回结果 
            while(rs.next()) { 			
                System.out.println(rs.getInt(1)+","+rs.getString(2)); 
            }
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally {
			if(rs!=null)rs.close();
			if(pstmt!=null)pstmt.close();
			if(conn!=null)conn.close();
		}
	    
	}
```



## 六、 工厂模式提供Connection

可以创建一个类，用于提供Conenection会话对象。静态工厂，实例工厂。静态工厂是指生成对象的方法是静态方法。实例工厂是指生成对象的方法是普通方法。

```Java

public class JdbcUtil {
	private static Properties pro;
	static {		
		try {
			pro = new Properties();
			InputStream is= JdbcUtil.class.getClassLoader().getResourceAsStream("jdbc.properties");
			try {
				pro.load(is);
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			Class.forName(pro.getProperty("jdbc.driver"));
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	/**
	 * 获取连接
	 * @return
	 * @throws SQLException
	 */
	public static Connection getConnection() throws SQLException {
		return DriverManager.getConnection(pro.getProperty("jdbc.url"),pro.getProperty("jdbc.username"),pro.getProperty("jdbc.pwd"));
	}
	
	/**
	 * 关闭资源
	 * @param connection
	 * @param statement
	 * @param resultset
	 */
	public static void closeAll(Connection connection,Statement statement,ResultSet resultset) {
		try {
			if(resultset!=null) resultset.close();
			if(statement!=null) statement.close();
			if(connection!=null) connection.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
	
	/*
	 * 用于执行DML语句
	 */
	public static int executeUpdate(String sql,Object...objects) {
		Connection conn=null;
		PreparedStatement pstmt=null;
		try {
			conn=getConnection();
			pstmt=conn.prepareStatement(sql);
			if(objects!=null) {
				for (int i = 1; i <= objects.length; i++) {
					pstmt.setObject(i, objects[i-1]);
				}
			}
            return pstmt.executeUpdate();
		} catch (SQLException e) {
			e.printStackTrace();
		}finally {
			closeAll(conn, pstmt, null);
		}
		return 0;
	}
	
	/**
	 * 用于执行DQL语句
	 */
	public static List<Map> executeQuery(String sql,Object...objects){
		List<Map> result = new ArrayList<Map>();
		Connection conn=null;
		PreparedStatement pstmt=null;
		ResultSet rs=null;
		try {
			conn=getConnection();
			pstmt=conn.prepareStatement(sql);
			if(objects!=null) {
				for (int i = 1; i <= objects.length; i++) {
					pstmt.setObject(i, objects[i-1]);
				}
			}
			rs = pstmt.executeQuery();
			//获取resultSetMetaData表结构 
			ResultSetMetaData metaData = rs.getMetaData();
			//获取总列数
			int columnCount = metaData.getColumnCount();
			while(rs.next()) {
				Map<String, Object> map = new HashMap();
				for (int i = 0; i < columnCount; i++) {
					String columnLabel = metaData.getColumnLabel(i+1);
					map.put(columnLabel,rs.getObject(columnLabel));
				}
				result.add(map);
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally {
			JdbcUtil.closeAll(conn, pstmt, rs);
		}
		return result;
	}
}
```

**注意**：在jdbc阶段，我们的查询可以返回泛型为Map的集合，泛型为map的集合破坏了我们面向对象编程的思路，后期我们会使用反射来替换map。 List<User>



## 七、 消除工具类中的硬编码（解耦）

在JdbcUtil类中存在硬编码问题：开发环境和生产环境肯定是不一样。需要调整硬编码的参数：`url,user,passWord`，这些参数存在.java文件中，还需要使用相同的环境去编译.java文件，将字节码文件替换到生产环境中。

**使用配置文件**来解决硬编码问题：

- 数据库连接信息硬编码
- SQL语句的硬编码（暂时不解决SQL语句的硬编码，mybatis阶段可以解决）

### 7.1 常用的配置文件

- properties文件（数据库连接信息配置）
- XML文件（节点化的配置文件）
- yml文件（springboot阶段）

### 7.2 properties配置文件

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver 
jdbc.url=jdbc:mysql://127.0.0.1/java1908z 
jdbc.username=root 
jdbc.pwd=123456
```

Properties 类表示了一个持久的属性集。 Properties 可保存在流中或从流中加载。属性列表中每个键及其对应值都是一个字符串。

Properties类可以通过流加载.properties文件，在加载成功后可以通过getkey的方式获取key对应的value值

| 返回值类型 | 方法                       | 描述                                                         |
| ---------- | -------------------------- | ------------------------------------------------------------ |
| void       | load(InputStream inStream) | 从输入流中读取属性列表（键和元素对）。将key和value保存在properties对象中 |
| String     | getProperty(String key)    | 用指定的 KEY在此属性列表中搜索VALUES                         |

```Java
package com.softwin.util;

import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Properties;


public class JdbcUtil {
	private static Properties pro;
	static {		
		try {
			pro = new Properties();
			InputStream is= JdbcUtil.class.getClassLoader().getResourceAsStream("jdbc.properties");
			try {
				pro.load(is);
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			Class.forName(pro.getProperty("jdbc.driver"));
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	/**
	 * 获取连接
	 * @return
	 * @throws SQLException
	 */
	public static Connection getConnection() throws SQLException {
		return DriverManager.getConnection(pro.getProperty("jdbc.url"),pro.getProperty("jdbc.username"),pro.getProperty("jdbc.pwd"));
	}
	
	/**
	 * 关闭资源
	 * @param connection
	 * @param statement
	 * @param resultset
	 */
	public static void closeAll(Connection connection,Statement statement,ResultSet resultset) {
		try {
			if(resultset!=null) resultset.close();
			if(statement!=null) statement.close();
			if(connection!=null) connection.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
	
	/*
	 * 用于执行DML语句
	 */
	public static int executeUpdate(String sql,Object...objects) {
		int flag=0;
		Connection conn=null;
		PreparedStatement pstmt=null;
		try {
			conn=getConnection();
			pstmt=conn.prepareStatement(sql);
			if(objects!=null) {
				for (int i = 1; i <= objects.length; i++) {
					pstmt.setObject(i, objects[i-1]);
				}
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}finally {
			closeAll(conn, pstmt, null);
		}
		return flag;
	}
	
	/**
	 * 用于执行DQL语句
	 */
	public static List<Map> executeQuery(String sql,Object...objects){
		List<Map> result = new ArrayList<Map>();
		Connection conn=null;
		PreparedStatement pstmt=null;
		ResultSet rs=null;
		try {
			conn=getConnection();
			pstmt=conn.prepareStatement(sql);
			if(objects!=null) {
				for (int i = 1; i <= objects.length; i++) {
					pstmt.setObject(i, objects[i-1]);
				}
			}
			rs = pstmt.executeQuery();
			//获取resultSetMetaData表结构 
			ResultSetMetaData metaData = rs.getMetaData();
			//获取总列数
			int columnCount = metaData.getColumnCount();
			while(rs.next()) {
				Map<String, Object> map = new HashMap();
				for (int i = 0; i < columnCount; i++) {
					String columnLabel = metaData.getColumnLabel(i+1);
					map.put(columnLabel,rs.getObject(columnLabel));
				}
				result.add(map);
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally {
			JdbcUtil.closeAll(conn, pstmt, rs);
		}
		return result;
	}
}
```

**注意**

1. 没有正确的key
2. user,pwd这些key可能存在于其他框架中，`jdbc.driver，jdbc.username,jdbc.pwd`来区分其他默认的key



## 八、 DAO模式

DAO模式（Data Access Object）数据访问对象 是一个面向对象的数据库访问接口，用于与数据库进行交互，处于业务逻辑层和数据库之间。

在J2EE中DAO模式的使用是为了建立一个健壮的J2EE应用，将对数据库的操作抽象在一个公共的API中。在程序设计中，API就是接口，在接口中定义该应用中可能会用到的操作数据库的方法，在应用中需要操作对应的数据时，使用该接口对应的实现。

### 8.1 DAO的组成

- **实体类**

  私有的属性（包装数据类型），公有的 `setter/getter`方法，需要实现 序列化

- **DAO接口**

  定义操作数据库的方法

- **DAO的实现**

  MySQL的实现，SQLserver的实现

- **JDBC工具类**

![2.jpg](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203191335.png)





## 九、 三层架构

**两层：**

![img](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203191339.jpg)

**三层：**

![img](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203191342.jpg)

**常用的企业分层架构图：**

- 表现层

  用于展示界面form或window,jsp,html,veloctity

- 业务层

  用于业务逻辑处理的

- 数据层

  用于和数据库交互的

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203191346.jpg)

### 9.1 分层原则

- 封装性原则

  每一层对外提供接口，隐藏内部实现细节，

- 顺序访问原则

  下层为上层提供服务，但不使用上层服务，不能跨层调用。

- 数据传递

  上层调用下层时，数据传递只能使用实体类（特殊情况下可以使用引用数据类型（包装数据类型））

### 9.2 项目中架构分层

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203191350.jpg)



## 十、 数据库连接池

传统JDBC的缺点：请求时间长（有网络I/O），Connection获取的性能较低。

数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个；释放空闲时间超过最大空闲时间的数据库连接来避免因为没有释放数据库连接而引起的数据库连接遗漏。这项技术能明显提高对数据库操作的性能。

**建议**：手写一个连接池（List)，保证线程同步

### 10.1 主流的数据库连接池

- DBCP
  - common-dbcp.jar和common-pool.jar
- C3P0
  - C3P0.jar
- DRUID（阿里提供的开源数据库连接池）
  - 下载DRUID连接池jar包
  - 加入到build path中
  - 创建连接池

```Java
static{
    	//创建连接池 
		DruidDataSource ds = new DruidDataSource(); 
		//设置连接池对应的驱动 
		ds.setDriverClassName(pro.getProperty("jdbc.driver")); 
		//设置连接池的url 
		ds.setUrl(pro.getProperty("jdbc.url")); 
		//设置用户名 
		ds.setUsername(pro.getProperty("jdbc.username")); 
		ds.setPassword(pro.getProperty("jdbc.pwd")); 
		//设置初始化连接个数 
		ds.setInitialSize(20); 
}
public static Connection getConnectionFromPool() throws SQLException {
		return ds.getConnection();
	}
```

|                   属性                   | 说明                                                         |          建议值           |
| :--------------------------------------: | :----------------------------------------------------------- | :-----------------------: |
|                   url                    | 数据库的jdbc连接地址。一般为连接oracle/mysql。示例如下：     |                           |
|                                          | mysql : jdbc:mysql://ip:port/dbname?option1&option2&…        |                           |
|                                          | oracle : jdbc:oracle:thin:@ip:port:oracle_sid                |                           |
|                                          |                                                              |                           |
|                 username                 | 登录数据库的用户名                                           |                           |
|                 password                 | 登录数据库的用户密码                                         |                           |
|               initialSize                | 启动程序时，在连接池中初始化多少个连接                       |        10-50已足够        |
|                maxActive                 | 连接池中最多支持多少个活动会话                               |                           |
|                 maxWait                  | 程序向连接池中请求连接时,超过maxWait的值后，认为本次请求失败，即连接池 |            100            |
|                                          | 没有可用连接，单位毫秒，设置-1时表示无限等待                 |                           |
|        minEvictableIdleTimeMillis        | 池中某个连接的空闲时长达到 N 毫秒后, 连接池在下次检查空闲连接时，将 |        见说明部分         |
|                                          | 回收该连接,要小于防火墙超时设置                              |                           |
|                                          | net.netfilter.nf_conntrack_tcp_timeout_established的设置     |                           |
|      timeBetweenEvictionRunsMillis       | 检查空闲连接的频率，单位毫秒, 非正整数时表示不进行检查       |                           |
|                keepAlive                 | 程序没有close连接且空闲时长超过 minEvictableIdleTimeMillis,则会执 |           true            |
|                                          | 行validationQuery指定的SQL,以保证该程序连接不会池kill掉,其范围不超 |                           |
|                                          | 过minIdle指定的连接个数。                                    |                           |
|                 minIdle                  | 回收空闲连接时，将保证至少有minIdle个连接.                   |     与initialSize相同     |
|             removeAbandoned              | 要求程序从池中get到连接后, N 秒后必须close,否则druid 会强制回收该 |   false,当发现程序有未    |
|                                          | 连接,不管该连接中是活动还是空闲, 以防止进程不会进行close而霸占连接。 | 正常close连接时设置为true |
|          removeAbandonedTimeout          | 设置druid 强制回收连接的时限，当程序从池中get到连接开始算起，超过此 |  应大于业务运行最长时间   |
|                                          | 值后，druid将强制回收该连接，单位秒。                        |                           |
|               logAbandoned               | 当druid强制回收连接后，是否将stack trace 记录到日志中        |           true            |
|              testWhileIdle               | 当程序请求连接，池在分配连接时，是否先检查该连接是否有效。(高效) |           true            |
|             validationQuery              | 检查池中的连接是否仍可用的 SQL 语句,drui会连接到数据库执行该SQL, 如果 |                           |
|                                          | 正常返回，则表示连接可用，否则表示连接不可用                 |                           |
|               testOnBorrow               | 程序 **申请** 连接时,进行连接有效性检查（低效，影响性能）    |           false           |
|               testOnReturn               | 程序 **返还** 连接时,进行连接有效性检查（低效，影响性能）    |           false           |
|          poolPreparedStatements          | 缓存通过以下两个方法发起的SQL:                               |           true            |
|                                          | public PreparedStatement prepareStatement(String sql)        |                           |
|                                          | public PreparedStatement prepareStatement(String sql,        |                           |
|                                          | int resultSetType, int resultSetConcurrency)                 |                           |
| maxPoolPrepareStatementPerConnectionSize | 每个连接最多缓存多少个SQL                                    |            20             |
|                 filters                  | 这里配置的是插件,常用的插件有:                               |      stat,wall,slf4j      |
|                                          | 监控统计: filter:stat                                        |                           |
|                                          | 日志监控: filter:log4j 或者 slf4j                            |                           |
|                                          | 防御SQL注入: filter:wall                                     |                           |
|            connectProperties             | 连接属性。比如设置一些连接池统计方面的配置。                 |                           |
|                                          | druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000       |                           |
|                                          | 比如设置一些数据库连接属性:                                  |                           |
|                                          |                                                              |                           |



## 十一、 课后作业

- 完善数据库连接池
- 使用DAO模式和三层架构模式，实现用户登录，在控制台打印输出所有的用户列表。
  - 提示：用户登录，请输入用户名，密码，成功后打印列表
  - 登录失败后重新登录