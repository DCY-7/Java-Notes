# MyBatis

## 一、 MyBatis的介绍

​    **MyBatis**是一个半自动ORM Java持久化框架，它通过XML描述符或注解把对象与存储过程或SQL语句关联起来。

> [维基百科-MyBatis](https://zh.wikipedia.org/wiki/MyBatis)
>
> [百度百科-MyBatis](https://baike.baidu.com/item/MyBatis)

## 二、 为什么要学习MyBatis

​    传统JDBC存在一些问题：

| 问题                                                         | 后续解决                   | mybatis的解决        |
| ------------------------------------------------------------ | -------------------------- | -------------------- |
| 数据库连接的硬编码问题                                       | 使用properties配置文件解决 | 使用全局配置文件解决 |
| 数据库频繁的获取连接和释放连接，网络IO次数和数据库资源的使用 | 使用第三方数据库连接池     | 使用全局配置文件     |
| 查询结果的封装                                               | 通过反射的方式封装查询结果 | 使用映射文件         |
| SQL语句存在硬编码的问题                                      | -                          | 使用映射文件         |

```java
public static void main(String[] args){
    Connection conn=null;
    PreparedStatement pstmt=null;
    ResultSet rs=null;
    //1.导入数据库连接驱动  
    try{
        //2.通过反射加载驱动
        class.forName("com.mysql.cj.jdbc.Driver");     
        //3.获取连接 
        conn=DriverManager.getConnection("java:mysql://localhost/java1908z","root","123456");
        String sql="select * from b_user";
        //4.编写SQL语句并发送到数据库执行
        pstmt=conn.prepareStatement(sql);
        rs=pstmt.excuteQuery();
        while(rs.next()){
        //5.处理返回结果
        }
    } catch (ClassNotFoundException e){
        e.printStackTrace();
    } catch (SQLException e){
        e.printStackTrace();
    } finally{
        //6.关闭资源
        if(ResultSet!=null) rs.close();
        if(PreparedStatement!=null) pstmt.close();
        if(conn!=null) conn.close();
    }
}
```

## 三、 MyBatis的运行原理

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203181233.jpg)

**总结：**

- mybatis配置文件：包含全局配置文件和映射文件。全局配置文件包含了数据库连接池，事务的定义，插件，反射工厂等相关信息。映射文件中包含了sql语句执行的相关信息(sql语句、输出参数、输出结果)
- mybatis使用SqlSessionFactoryBuild去build（）加载配置文件从而生成SqlSessionFactory工厂对象，会话工厂
- 可以通过SqlSeesionFactory工厂来创建SqlSession（会话）
- SqlSession:是提供给开发人员的操作，本质其本身并没有去操作数据库，是通过调用底层的
- Executor执行器来操作（简单执行器和缓存执行器【默认】）
- Executor执行MapperStatment对象（sql语句、输入参数、结果类型)



## 四、MyBatis的入门程序

### 4.1 环境搭建

- mysql的连接驱动：mysql-connector-java-8.0.19.jar

- mybatis的核心依赖：mybatis-3.5.5.jar

  ![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203181239.jpg)

### 4.2 创建配置文件

​    全局配置文件、映射文件都使用XML语言

​    XML中包含：

- 声明：声明版本信息、编码格式
- 约束：dtd、schema（mybatis使用的是dtd约束）
- 节点
- 属性

#### 4.2.1 全局配置文件：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <!--配置环境信息-->
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost/java1908z"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
      </dataSource>
    </environment>
  </environments>
  <!--加载映射文件-->
  <mappers>
    <mapper resource="UserMapper.xml"/>
  </mappers>
</configuration>
```



#### 4.2.2 映射文件：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- 
	主节点： namespace:命名空间，类似package，目的是为了包装sql id的唯一性 
	java package为了保证类名的唯一性 --> 
<mapper namespace="UserMapper"> 
    <!-- 
		用于执行select查询语句的 
		id:必须（定义MapperStatmentId的一部分，要求整个项目中id唯一 
		resultType:定义查询结果的映射类型（输出结果类型的) int是Integer的别名，代表输出结果的类型是整形的 
	--> 
    <select id="selectCount" resultType="int"> 
        select count(*) from s_user 
    </select>
  
</mapper>
```



#### 4.2.3 配置dtd约束：

​    **注意：**约束文件不要放在中文目录下

#### 4.2.4 编写代码：

```java
public static void main(String[] args){
    //创建构建者对象
    SqlSessionFactorBuilder factoryBuilder=new SqlSessionFactorBuilder();
    //读取的文件路径是基于类路径的：必须在bin目录的根目录或classes目录的根目录
    InputStream is=Resources.getResourceAsStream("mybatis-config.xml");
    //构建工厂对象
    SqlSessionFactory factory=factoryBuilder(is);
    //工厂对象获取SQLSession对象
    SqlSession sqlSession=factory.openSession();
    //参数：statementId MapperStatement(ID中包含有sqlID) 就是namespace+sqlId
    //该方法调用的sql语句只能返回一行数据（可以是多列）
    Integer count=sqlSession.selectOne("UserMapper.selectCount");
    System.out.println(count);
    //释放资源
    sqlSession.close();
}
```



### 4.3 SQLSession常用方法

| 返回值类型 | 方法                                          | 描述                                                         |
| ---------- | --------------------------------------------- | ------------------------------------------------------------ |
| <T> T      | selectOne(String statement)                   | 执行返回值为单行的select语句                                 |
| <T> T      | selectOne(String statement,Object parameter)  | 执行返回值为单行的select语句，parameter参数为SQL语句的输入参数 |
| <E> E      | selectList(String statement)                  | 执行返回值为null或多行的select语句                           |
| <E> E      | selectList(String statement,Object parameter) | 执行返回值为null或多行的select语句，parameter参数为SQL语句的输入参数 |
| int        | insert(String statement)                      | 执行insert语句，返回值为受影响行数                           |
| int        | insert(String statement,Object parameter)     | 执行带参数的insert语句，返回值为受影响行数                   |
| int        | update(String statement)                      | 执行update语句，返回值为受影响行数                           |
| int        | update(String statement,Object parameter)     | 执行带参数的update语句，返回值为受影响行数                   |
| int        | delete(String statement)                      | 执行delete语句，返回值为受影响行数                           |
| int        | delete(String statement,Object Parameter)     | 执行带参数的delete语句，返回值为受影响行数                   |
| void       | commit()                                      | 提交事务                                                     |
| void       | rollback()                                    | 事务回滚                                                     |
| void       | close()                                       | 关闭资源                                                     |
| <T> T      | getMapper(Class<T> type)                      | 获取接口的代理对象                                           |

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="UserMapper"> 
    <!-- 
		用于执行select查询语句的 
		id:必须（定义MapperStatmentId的一部分，要求整个项目中id唯一 
		resultType:定义查询结果的映射类型（输出结果类型的) int是Integer的别名，代表输出结果的类型是整形的 
		parameterType:定义输入参数的类型，可以写全限定类名也可以写别名 string java.lang.String的别名
		parameterMap:已经过时 
		#{}是占位符（?):当输入参数为简单数据类型(基本+包装+String)时,#{可以是任意字符} 
		${}是字符拼接:占位符会对类型进行处理,而字符拼接原样输出
	--> 
    <select id="selectCount" resultType="int"> 
        select count(*) from s_user 
    </select>
    <select id="selectCountBySex" resultType="int" parameterType="java.lang.String"> 			select id from s_user where sex=#{sex}
    </select>
  
</mapper>
```

**总结：**

- parameterType和resultType：

  parameterType是用于指定输入参数的类型的，可以填写别名也可以填写全限定类名

  resultType是定义输出映射的类型的，可以填写别名也可以填写全限定类名，

  注意：如果输出结果是单行记录，resultType定义的就是输出结果的类型。如果输出结果为多行结果，resultType定义的是list集合的泛型

- \#{}和${}：

  \#{}代表占位符，#{}中可以接受简单类型、hashMap、pojo，如果输入参数为简单类型#{任意值},如果输入参数为hashmap，#{key}，如果输入参数为pojo类型#{属性名}

  ${}代表字符拼接,${}可以接受简单类型、hashMap、pojo，如果输入参数为简单类型${value}，如果输入参数为hashMap，${key}，如果输入参数为pojo类型${属性名}

- selectOne和selectList的区别：

  selectOne()要求查询结果只能为0行或1行记录

  selectList() 要求查询结果可以为0-1-n记录

### 4.4 传统DAO开发模式（ibatis）

需求：查询所有用户的个数，使用DAO模式（属于ibatis的开发方式）

#### 4.4.1 DAO模式包含的内容：

- 实体类
- DAO接口
- DAO的实现
- ibatis工具类

#### 4.4.2 工具类的创建

```java
public class SqlSessionUtil {

	private static SqlSessionFactory sqlSessionFactory; 
		
	static { 
		//构建者模式（构建的是工厂对象） 
		try {
			SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder(); 
			InputStream is = Resources.getResourceAsStream("mybatis- config.xml");
			//工厂对象需要唯一（缓存，【sqlSession必须出自同一个sqlSessionFactory) 
			sqlSessionFactory = builder.build(is); 
			//和jdbc connection很想，方法级别 
			} catch (IOException e) { 
				e.printStackTrace(); 
			}
		}
	
	public static SqlSession getSqlSession() throws IOException { 
		return sqlSessionFactory.openSession(); 		
	}
		
	
}

```

#### 4.4.3 DAO接口的创建

```java
public interface UserDao { 
    /**
     * 查询所有用户的总数 
     * @return 
     */ 
    int selectUserCount(); 
}
```



#### 4.4.4 DAO接口的实现

```java
public class UserDaoImpl implements UserDao { 
    
    @Override public int selectUserCount() throws IOException { 
        SqlSession session = SqlSessionUtil.getSqlSession(); 
        int count = session.selectOne("UserMapper.selectCount"); 
        session.close(); 
        return count; 
    } 
}
```



#### 4.4.5 测试

```java
public class Test4 { 
    
    public static void main(String[] args) throws IOException { 
        UserDao dao = new UserDaoImpl(); 
        int count = dao.selectUserCount(); 
        System.out.println(count); 
    } 
}
```



### 4.5 传统DAO模式存在的问题

- 存在一定量的模板代码：工具类中获取sqlSessionFactoryBuild,sqlSessionFactory,SqlSession，包含操作数据库，包含释放资源
-  StatementId调用存在硬编码问题：selectOne("UserMapper.selectUserCount")

### 4.6 代理开发模式（推荐使用）

​    在mybatis的代理模式中，我们不再需要手动的去创建接口的实现类，而是使用了jdk的动态代理（代理接口）

**实现方式：创建接口（Mapper接口）**

**注意：**

- 接口中的方法的方法名必须和statmentid一致（sqlID)
- 方法的参数只能有一个(@Param注解)，并且输入参数的类型和parameterType类型一致，返回值类型或返回值类型中的泛型必须和ResultType（ResultMap)一致
- 映射文件中的namespace必须是接口的全限定类名
- pojo类中必须包含有无参的构造方法（使用反射封装查询结果）

```java
public class Test5 { 
    public static void main(String[] args) throws IOException { 
        SqlSession session = SqlSessionUtil.getSqlSession(); 
        //获取接口的代理对象 
        UserMapper mapper = session.getMapper(UserMapper.class); 
        int count = mapper.selectCount();
        System.out.println(count); 
    } 
}
```

## 五、 MyBatis配置文件详解

### 5.1 全局配置文件详解

​    全局配置文件公有11项配置，全局配置文件的名称可以随意、但是XML中的各项配置顺序受到.dtd文件的约束。

#### properties（属性）

```xml
<!--
	用于配置mybatis的配置文件
	resource属性：用于加载本地classpath下的外部配置文件的，可以使用${}来获取配置信息
	url属性：用于加载云端的配置文件的

	优先级：property的配置信息先加载，在加载外部的properties配置文件中的配置信息，最后读取作为方法参数传递的属性，并覆盖之前读取过的同名属性
-->
<properties resource="jdbc.properties">
  <property name="username" value="root"/>
  <property name="password" value="123"/>
</properties>
```



#### setting（设置）

配置mybatis的全局变量及开关，一个配置完整的 settings 元素的示例如下：

```xml
<settings>
  <!--打开全局的缓存开关（可以使用二级缓存）-->
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
  <setting name="multipleResultSetsEnabled" value="true"/>
  <setting name="useColumnLabel" value="true"/>
  <setting name="useGeneratedKeys" value="false"/>
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  <setting name="defaultExecutorType" value="SIMPLE"/>
  <setting name="defaultStatementTimeout" value="25"/>
  <setting name="defaultFetchSize" value="100"/>
  <setting name="safeRowBoundsEnabled" value="false"/>
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  <setting name="localCacheScope" value="SESSION"/>
  <setting name="jdbcTypeForNull" value="OTHER"/>
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```



#### typeAliases（类型别名）

​    类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写。

- 系统自带别名：

  | 别名       | 映射的类型 |
  | :--------- | :--------- |
  | _byte      | byte       |
  | _long      | long       |
  | _short     | short      |
  | _int       | int        |
  | _integer   | int        |
  | _double    | double     |
  | _float     | float      |
  | _boolean   | boolean    |
  | string     | String     |
  | byte       | Byte       |
  | long       | Long       |
  | short      | Short      |
  | int        | Integer    |
  | integer    | Integer    |
  | double     | Double     |
  | float      | Float      |
  | boolean    | Boolean    |
  | date       | Date       |
  | decimal    | BigDecimal |
  | bigdecimal | BigDecimal |
  | object     | Object     |
  | map        | Map        |
  | hashmap    | HashMap    |
  | list       | List       |
  | arraylist  | ArrayList  |
  | collection | Collection |
  | iterator   | Iterator   |

- 用户自定义别名：

  ```xml
  <typeAliases>
      <!--单个别名设置-->
      <!--<typeAlias type="com.sofwin.pojo.User" alias="user"/>-->
  	<!--批量别名设置（推荐使用）-->
      <package name="com.sofwin.pojo"/>
  </typeAliases>
  ```

  

#### typeHandlers（类型处理器）

#### objectFactory（对象工厂）

​    不推荐重写

#### objectWarpperFactory（包装对象工厂）

#### reflectorFactory（反射工厂）

#### plugins（插件）

```xml
<plugins>
	<!--
	单个插件配置
	Interceptor：拦截器
	后期使用拦截器实现mybatis的分页
-->
   <pulgin  interceptor="" /> 
</plugins>
```



#### environments（环境配置）

```xml
<!--
默认使用的环境 ID（比如：default="development"）。
每个 environment 元素定义的环境 ID（比如：id="development"）。
事务管理器的配置（比如：type="JDBC"）。
数据源的配置（比如：type="POOLED"）。
-->
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC">
      <property name="..." value="..."/>
    </transactionManager>
    <dataSource type="POOLED">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```



#### databaseProvider（数据库厂商标识）

​    数据库厂商标识，在mybatis中可以配置多种数据库

#### mapper（映射器）

​    既然 MyBatis 的行为已经由上述元素配置完了，我们现在就要来定义 SQL 映射语句了。 但首先，我们需要告诉 MyBatis 到哪里去找到这些语句。 在自动查找资源方面，Java 并没有提供一个很好的解决方案，所以最好的办法是直接告诉 MyBatis 到哪里去找映射文件

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
<!-- 使用完全限定资源定位符（URL） -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
<!-- 
	使用映射器接口实现类的完全限定类名
	注意：mapper接口和映射文件必须保证同包 
-->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
<!-- 
	将包内的映射器接口实现全部注册为映射器（推荐使用） 
	注意：mapper接口和映射文件必须保证同包
-->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```



### 5.2 映射文件详解（核心）

​    MyBatis 的真正强大在于它的语句映射，这是它的魔力所在。由于它的异常强大，映射器的 XML 文件就显得相对简单。如果拿它跟具有相同功能的 JDBC 代码进行对比，你会立即发现省掉了将近 95% 的代码。MyBatis 致力于减少使用成本，让用户能更专注于 SQL 代码。

SQL 映射文件只有很少的几个顶级元素（按照应被定义的顺序列出）：

- `cache` – 该命名空间的缓存配置。
- `cache-ref` – 引用其它命名空间的缓存配置。
- `resultMap` – 描述如何从数据库结果集中加载对象，是最复杂也是最强大的元素。
- `sql` – 可被其它语句引用的可重用语句块。
- `insert` – 映射插入语句。
- `update` – 映射更新语句。
- `delete` – 映射删除语句。
- `select` – 映射查询语句。

```xml
<!--例如：-->
<mapper namespace="UserMapper"> 
    <select id="selectCount" resultType="int"> 
        select count(*) from s_user 
    </select>
</mapper>
```

#### select元素

​    只能填写DQL语句

| 属性            | 描述                                                         |
| :-------------- | :----------------------------------------------------------- |
| `id`            | 在命名空间中唯一的标识符，可以被用来引用这条语句。           |
| `parameterType` | 将会传入这条语句的参数的类全限定名或别名                     |
| `resultType`    | 期望从这条语句中返回结果的类全限定名或别名。 注意，如果返回的是集合，那应该设置为集合的泛型。 resultType 和 resultMap 之间只能同时使用一个。 |
| `resultMap`     | 对外部 resultMap 的命名引用。结果映射是 MyBatis 最强大的特性，如果你对其理解透彻，许多复杂的映射问题都能迎刃而解。 resultType 和 resultMap 之间只能同时使用一个。 |

#### 输出结果映射

- resultType

  - 简单数据类型

    ​    可以使用内置别名，也可以使用包装类型的全限定类名。查询结果只能是显示一列信息，如果查询结果为一行一列，那就封装为一个简单数据类型的对象。如果查询结果为多行一列，那就封装为List集合，集合泛型为resultType中指定的类型。

    ```xml
    <select id="selectUserNameById_10" resultType="string"> 
        select username from s_user where id=10 
    </select> 
    <select id="selectUserName" resultType="string"> 
        select username from s_user 
    </select>
    ```

    

  - pojo+dto

    ​    查询结果为多列，查询结果的封装可以使用pojo/dto。可以使用自定义别名，也可以使用pojo的全限定类名。

    ​    将查询结果的列名和pojo中的属性名进行对比，如果列名和属性名完全一致（区分大小写），对象中的该属性使用查询结果的数据，如果列名和属性名无法对应，改对象的该属性为默认值。只要有一列可以和属性对应成功，那么返回值就不为空，当前对应的属性有值，其他属性使用默认值。如果所有列都无法和属性对应，返回null。

    ​    **如果查询结果的列名和封装结果的属性名可以对应一致，建议使用resultType进行结果映射**

    ```xml
    <select id="selectUserInfo" resultType="user"> 
        select id,username as usernames from s_user where id=10 
    </select> 
    <select id="selectUserInfos" resultType="com.sofwin.pojo.User"> 
        select * from s_user 
    </select>
    ```

    

- resultMap

  **查询结果和属性名不对应的情况**

  **处理高级查询的结果映射(第7章讲)**

  ```xml
  <!-- 
  	处理查询结果列名和属性名无法对应的情况 type:需要映射的类型(pojo类型或扩展类型）【可以写全限定类名也可以写自定义别名】 
  	也是接口中方法的返回值类型或泛型 
  	id:resultMap的唯一标识，不能重复 
  -->
  <resultMap type="user" id="map1">
      <!-- 
  		用于映射主键列的 column:查询结果的列名 
  		property:需要映射到的属性名 
  		javaType:用于指定属性的类型(一般省略） 
  		jdbcType:用于指定列的类型(一般省略） 
  	-->
  	<id column="ids" property="id"/>
      <!-- 
  		用于映射普通属性 
  		column: 
  		property: 
  		javaType： 
  		jdbcType: 
  	-->
      <result column="usernames" property="username"/>
  </resultMap>
  
  <!-- resultMap：使用定义的resultMap的id -->
  <select id="selectUserInfo1" resultMap="map1"> 
      select id as ids,username as usernames from s_user 
  </select>
  ```

  **extends继承**

  ```xml
  <resultMap type="user" id="map0">
  	<id column="ids" property="id"/>
      <result column="usernames" property="username"/>
  </resultMap>
  <resultMap type="user" id="map1" extends="map0"> 
      <result column="sexs" property="sex"/> 
  </resultMap>
  ```

**总结：**

​    映射文件中定义了返回结果类型，如果在接口中定义的返回结果是单个对象，那么mybatis会自动去调用selectOne()（如果返回查询返回的是多条记录，会报错Expected one result (or null) to be returnedby selectOne(), but found:)。如果在接口中定义了返回结果是集合类型，那么mybatis会自动调用selectList()（查询结果可以返回单条记录，也可以返回Null，也可以返回多条记录）

#### 输入映射

​    parameterType和parameterMap（过时)

​    parameterType中可以是内置别名或自定义别名或全限定类名

​    `#{}`和`${}`

- 简单数据类型

  ```xml
  <select id="selectUserNameById_10" resultType="string" parameterType="int"> 
      select username from s_user where id=#{id} 
  </select>
  ```

  

- HashMap

  ```xml
  <!-- 根据用户名和密码查询用户信息 --> 
  <select id="selectUserByUnameAndPwd" resultType="user" parameterType="map"> 
      select * from s_user where username=#{username} and pwd=#{pwd} 
  </select>
  ```

  ```java
  public static void main(String[] args) throws IOException { 
      SqlSession session = SqlSessionUtil.getSqlSession(); 
      //获取接口代理对象
      UserMapper mapper = session.getMapper(UserMapper.class); 
      Map map = new HashMap(); 
      //key只能是#{}中的字符 
      map.put("username", "admin1"); 
      map.put("pwd", "admin1");
      List<User> users = mapper.selectUserByUnameAndPwd(map); 
      System.out.println(users.get(0).getUsername());
  }
  ```

  

- pojo+dto（扩展数据类型）

  ```xml
  <!-- 根据用户名和密码查询用户信息 --> 
  <select id="selectUserByUnameAndPwd" resultType="user" parameterType="user"> 
      select * from s_user where username=#{username} and pwd=#{pwd} 
  </select>
  ```

  ```java
  public static void main(String[] args) throws IOException { 
      SqlSession session = SqlSessionUtil.getSqlSession(); 
      //获取接口代理对象
      UserMapper mapper = session.getMapper(UserMapper.class); 
      User user = new User(); 
      user.setUsername("admin1"); 
      user.setPwd("admin1");
      List<User> users = mapper.selectUserByUnameAndPwd(user); 
      System.out.println(users.get(0).getUsername());
  }
  ```

  

#### insert元素

​    用于处理insert语句

- id
- parameterType：输入映射
- **没有输出结果映射**，但是有int返回值，返回值代表影响行数

**插入失败的原因：sqlSession没有使用自动提交的事务**

```xml
<!-- 插入用户信息 -->
<insert id="insertUser" parameterType="user" >
    <selectKey keyColumn="ids" keyProperty="id" order="AFTER" resultType="int" > 
        select last_insert_id() as ids
    </selectKey> 
    insert into s_user(username,pwd) values(#{username},#{pwd}) 
</insert>
```

```xml
<!-- 
	插入用户信息 
	keyProperty:指定输入参数中作为主键的属性 
	useGeneratedKeys:指定使用自增主键来映射主键列 
--> 
<insert id="insertUser" parameterType="user" keyProperty="id" useGeneratedKeys="true"> 
    insert into s_user(username,pwd) values(#{username},#{pwd}) 
</insert>
```



#### update元素

​    用于执行update语句

- id
- parameterType
- **没有输出结果映射**但有整形的返回值 为影响行数

```xml
<!-- 用户更新 --> 
<update id="updateUser" parameterType="user"> 
    update s_user set username=#{username},pwd=#{pwd} where id=#{id}
</update>
```



#### delete元素

​    用于执行delete语句

- id
- parameterType
- **没有输出结果映射**但有整形的返回值 为影响行数

```xml
<delete id="deleteUser" parameterType="int"> 
    delete from s_user where id=#{id} 
</delete>
```



## 六、 动态SQL

### 6.1 搭建mybatis的调试环境

mybatis是基于日志进行调试，在配置了日志的相关信息后，可以在控制台生成sql语句。就是通过控制台打印的sql语句，及输入参数来确定sql语句是否正确。

**步骤：**

- 在mybatis中加入日志依赖(commons-logging.jar,log4j.jar|slf4j.jar|logback.jar)commons-logging.jar是所有日志框架的标准(interface)log4j|slf4j|logback日志的实现
- 在src根目录增加log4j.properties（log4j的日志配置文件[properties,xml])

```properties
### 设置###
log4j.rootLogger = debug,stdout,D,E

### 输出信息到控制抬 ###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d{yyyy-MM-ddHH:mm:ss,SSS} method:%l%n%m%n

### 输出DEBUG 级别以上的日志到=E://logs/error.log ###
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = c://logs/log.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = DEBUG
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss} [ %t:%r ]- [ %p ] %m%n

### 输出ERROR 级别以上的日志到=E://logs/error.log ###
log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File =c://logs/error.log
log4j.appender.E.Append = true
log4j.appender.E.Threshold = ERROR
log4j.appender.E.layout = org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss} [ %t:%r ]- [ %p ] %m%n
```



### 6.2 流程控制语句

#### if

单条件查询

```xml
<!-- 
  	根据用户相关查询条件查询用户信息
  		1：当输入的用户名不为空时按用户名模糊查询
  		2：当输入的密码不为空时按密码精确查询
  		3：当输入的性别不为空时按性别查询
  		
  	<if test="逻辑表达式">SQL语句</if>
  	逻辑表达式，如果输入参数为hashmap或pojo类型时，直接在test中使用属性名进行判断，and or 进行与或运算
  	
   -->
   <select id="selectUsers01" parameterType="user" resultType="user">
       select * from s_user where 1=1
       <if test="username!=null and username!=''">
       and username like '%${username}%'
       </if>
   </select>
   
   <!-- 
     简单类型输入参数的判断
    -->
    <select id="selectUsers02" parameterType="string" resultType="user">
		select *  from s_user where 1=1
   		<if test="_parameter!=null and _parameter!=''">
       	and username like '%${value}%'
   		</if>
	</select>
```



#### choose

类似switch，多条件查询

```xml
<!-- 
	  choose：多条件选择
	  when：test：和<if>中的test效果一样，当返回为true时，执行<when>标签中的语句
	 -->
	<select id="selectUsers03" parameterType="user" resultType="user">
		select * from s_user where 1=1
		<choose>
			<when test="username!=null and username!=''">
			and username like '%${username}%'
			</when>
			<when test="pwd!=null and pwd!=''">
			and pwd=#{pwd}
			</when>
			<otherwise>
			and 2=2
			</otherwise>
		</choose>
	</select>
```



#### foreach

对比JSTL中的`<c:foreach>`

```xml
<!--
    foreach标签的作用进行循环遍历 collection:jstl中的Items，需要遍历的集合(当输入参数为list类型时，collection中的值只
    能为list
    当输入参数为array数组时，collection中只能为array item:jstl中的var，每次遍历到的对象(迭代的别名)
    index:jstl中varstatus.index 循环遍历的索引 open:循环已open中的字符开始 close:循环已close中的字符结尾 separator:循环中每次循环已separator中的字符进行分割
-->
	<foreach collection="" item="" index="" close="" open="" separator="">
	</foreach>
```

```xml
<!-- 
  	循环结构
  	<foreach collection="" item="" index="" open="" separator="">	
   -->
   <select id="selectUsers04" resultType="user" parameterType="int">
    select *  from s_user where id in
    <foreach collection="list" item="id" open="(" close=")" separator=",">
    #{id}
    </foreach>
    </select>
```



### 6.3 where

`where 1=1`会导致索引失效，查询效率降低

`where`标签和选择结构结合使用，作用：判断有无查询条件，如果有会自动增加where关键字，去掉第一个and，如果没有则不会增加where关键字

```xml
<select id="selectUsers05" parameterType="string" resultType="user">
    	select * from s_user 
    	<where>
    		<if test="_parameter!=null and _parameter!=''">
    		and username like '%${value}%'
    		</if>
    	</where>
    </select>
```



### 6.4 set

用于`update`语句，增加set关键字，并去除最后一个逗号

```xml
<update id="updateUser01" parameterType="user">
    update s_user
    	<set>
    		<if test="username!=null and username!=''">
    		username=#{username},
    		</if>
    		<if test="pwd!=null and pwd!=''">
			pwd=#{pwd},
    		</if>
   		</set>
   		where id=#{id}
    </update>
```



### 6.5 trim

可以构建`set``where`

```xml
<!--
简单类的输入参数的判断
--> 
<!--
prefix:增加前缀 suffix:增加后缀 prefixOverrides:覆盖前缀 suffixOverrides:覆盖后缀
-->
<select id="selectUsers06" parameterType="string" resultType="user">
    select * from s_user
    	<trim prefix="where" prefixOverrides="and">
    	<if test="_parameter!=null and _parameter!=''">
    	and username like '%${value}%'
    	</if>
    	</trim>
    </select>
```



### 6.6 sql

定义SQL片段

为了实现sql代码的复用，我们可以将经常出现的sql语句， 查询字段列表(*)让索引失效，在企业开发过程中不允许出现select * ,select 字段名列表 以及查询条件定义为sql片段,可以在需要的地方使用<include refid="sql片段的id">

```xml
<sql id="baseColumn">id,username</sql>
	<select id="selectUsers07" parameterType="user" resultType="user">
	select 
	<include refid="baseColumn"></include>
	from s_user where 1=1
	<if test="username!=null and username!=''">
	and username like '%${username}%'
	</if>
	</select>
```



### 实例：实现单表的CRUD

选择用户表: 使用动态sql来完善

- 查询(多条件的组合查询) 
- 更新(输入参数的字段不为空才更新，为空的不更新) 
- 删除(使用in子句批量删除) 
- 插入(使用trim来判断insert语句的，要求不为空的列才出现在插入字段列表中)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.sofwin.mapper.UserMapper">
	<sql id="columnList">
	id,username,pwd,sex
	</sql>
	<sql id="tableName">
	s_user
	</sql>
	
	<select id="selectUsers" parameterType="user" resultType="user">
	select
	<include refid="columnList"></include>
	from
	<include refid="tableName"></include>
	<where>
		<if test="username!=null and username!=''">
		and username like '%${username}%'
		</if>
		<if test="pwd!=null and pwd!=''">
		and pwd like '%${pwd}%'
		</if>
		<if test="sex!=null and sex!=''">
		and sex like '%${sex}%'
		</if>
	</where>
	</select>
	
	<insert id="saveUser" parameterType="user">
	insert into
	<include refid="tableName"></include>
	<trim prefix="(" suffix=")" suffixOverrides=",">
		<if test="username!=null and username!=''">
		username,
		</if>
		<if test="pwd!=null and pwd!=''">
		pwd,
		</if>
		<if test="sex!=null and sex!=''">
		sex,
		</if>
	</trim>
	<trim prefix="values(" suffix=")" suffixOverrides=",">
		<if test="username!=null and username!=''">
		#{username},
		</if>
		<if test="pwd!=null and pwd!=''">
		#{pwd},
		</if>
		<if test="sex!=null and sex!=''">
		#{sex},
		</if>
	</trim>
	</insert>
	
	<update id="updateUser" parameterType="user">
	update
	<include refid="tableName"></include>
	<set>
		<if test="username!=null and username!=''">
		#{username},
		</if>
		<if test="pwd!=null and pwd!=''">
		#{pwd},
		</if>
		<if test="sex!=null and sex!=''">
		#{sex},
		</if>
	</set>
	where id=#{id}
	</update>
	
	<delete id="deleteUser" parameterType="user">
	delete from
	<include refid="tableName"></include>
	where id=#{id}
	</delete>
</mapper>
```



## 七、 高级查询结果映射

在开发中，需要查询、显示多张表的数据

### 7.1 基础环境

- 用户表 b_user
- 商品表 b_goods
- 订单主表 b_order
- 订单明细表 b_order_detail

用户表和订单主表属于一对多的关系

订单主表和订单明细表属于一对多的关系

订单明细表和商品表属于一对一的关系

用户表和商品表属于多对多的关系

### 7.2 一对一的结果映射

#### 7.2.1 需求

查询订单详情并关联显示商品信息

**注意：** 查询结果不允许出现列名相同的列，将字段多的列使用*，字段少的列，依次列出

```sql
select
from b_order_detail a 
left join b_goods b on a.goods_id=b.goods_id;
```

#### 7.2.2 结果映射

> 多表查询的结果，可以使用自定义的`pojo`，用属性去映射列名

```xml
<select id="selectOrderDetail" resultType="orderdetailgoods">
  select a.*,b.name,b.price from b_order_detail aleft join b_goods b on a.goods_id=b.goods_id
</select>
```

```java
package com.sofwin.pojo; 
public class OrderDetailGoods {}
```

**分析：** 

- 新建的实体类中的属性和OrderDetail和Goods实体类中的属性是重复的
- 查询结果并没有遵循ORM（对象关系映射）

**不推荐使用这种写法！**

> 使用`resultMap`标签来进行映射

使用步骤：

1. 创建扩展数据类型（dto），用于描述实体类之间的关系

   ```java
   package com.sofwin.dto;
   
   public class OrderDetailDto extends OrderDetail implements Serializable{
   private static final long serialVersionUID = 1L;
   	
   	private Goods goods;
   
   	public Goods getGoods() {
   		return goods;
   	}
   
   	public void setGoods(Goods goods) {
   		this.goods = goods;
   	}
   }
   ```

   

2. 使用`resultMap`标签来映射结果，使用`association`来进行一对一的结果映射

   ```xml
   <resultMap type="com.sofwin.dto.OrderDetailDto" id="map1">
     	<id column="order_detail_id" property="order_detail_id"/> 
     	<result column="order_id" property="order_id"/> 
     	<result column="goods_id" property="goods_id"/> 
     	<result column="num" property="num"/>
       <association property="goods" javaType="goods">
         <id column="goods_id" property="goods_id"/>
     	  <result column="name" property="name"/>
     	  <result column="price" property="price"/>
       </association>
   </resultMap>
   
   <select id="selectOrderDetail1" resultMap="map1">
   	select a.*,b.name,b.price from b_order_detail a left join b_goods b on a.goods_id=b.goods_id
   </select>
   ```

   

原理：

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203181257.jpg)

### 7.3 一对多的结果映射

#### 7.3.1 需求

查询订单信息，并关联订单详情表

```sql
select a.*,b.order_detail_id,b.goods_id,b.num 
from b_order a
left join b_order_detail b
on a.order_id=b.order_id;
```

#### 7.3.2 结果映射

使用`resultMap`来进行结果的映射

1. 创建扩展数据类型（dto），用于描述实体类之间的关系

   ```java
   package com.sofwin.dto;
   
   public class OrderDto extends Order implements Serializable {
   
   	private static final long serialVersionUID = 1L;
   
       private List<OrderDetail> detail
   
   	public List<OrderDetail> getDetail() {
   		return detail;
   	}
   
   	public void setDetail(List<OrderDetail> detail) {
   		this.detail = detail;
   	}
   
   }
   
   ```
   

   
2. 使用`resultMap`标签来映射结果，使用`connection`来进行一对一的结果映射

   ```xml
   <resultMap type="com.sofwin.dto.OrderDto" id="map2">
     	<id column="order_id" property="order_id"/> 
     	<result column="order_no" property="order_no"/> 
     	<result column="account" property="account"/> 
     	<result column="record_date" property="record_date"/> 
     	<result column="user_id" property="user_id"/>
       <collection property="detail" ofType="OrderDetail">
         <id column="order_detail_id" property="order_detail_id"/> 
         <result column="goods_id" property="goods_id"/> 
         <result column="order_id" property="order_id"/> 
         <result column="num" property="num"/> 
       </collection>
   </result<id column="ID" jdbcType="INTEGER" property="id" />
       <result column="mainId" jdbcType="INTEGER" property="mainid" />
       <result column="goodsId" jdbcType="INTEGER" property="goodsid" />
       <result column="price" jdbcType="VARCHAR" property="price" />
       <result column="num" jdbcType="VARCHAR" property="num" />
       <result column="totalPrice" jdbcType="VARCHAR" property="totalprice" />
    <result column="tastedId" jdbcType="INTEGER" property="tastedid" />
       <result column="packageId" jdbcType="INTEGER" property="packageid" />Map>
   
   <select id="selectOrderDetails" resultMap="map2">
   	select a.*,b.order_detail_id,b.goods_id,b.num from b_order a left join b_order_detail b on a.order_id=b.order_id
   </select>
   ```
   
   

### 7.4 多对多的结果映射

mybatis并没有提供多对多的标签，而是使用一对多和一对一的标签混合使用

#### 7.4.1 需求

查看用户购买的商品信息

```sql
select a.*,d.* 
from b_user a
left join b_order b on a.id=b.user_id 
left join b_order_detail c on b.order_id=c.order_id 
left join b_goods d on c.goods_id=d.goods_id;
```



#### 7.4.2 结果映射

1. 创建扩展数据类型（dto），用于描述实体类之间的关系

   ```java
   package com.sofwin.dto;
   
   import java.util.Date;
   import java.util.List;
   
   public class UserDto {
   
   	private Integer user_id;
   	private String name;
   	public Integer getUser_id() {
   		return user_id;
   	}
   
   	//set(),get()略
   }
   
   ```

   

2. 使用`resultMap`标签来映射结果，混合使用`associate`和`connection`来进行一对一的结果映射

   ```xml
   <resultMap type="com.sofwin.dto.UserDto" id="map3"> 
       <id column="user_id" property="user_id"/> 
       <result column="name" property="name"/> 
       <result column="sex" property="sex"/> 
       <result column="birthday" property="birthday"/> 
       <collection property="orders" ofType="com.sofwin.dto.OrderDto"> 
         <id column="order_id" property="order_id"/> 
         <collection property="details" ofType="com.sofwin.dto.OrderDetailDto"> 
           <id column="order_detail_id" property="order_detail_id"/> 
           <association property="goods" javaType="goods"> 
             <id column="goods_id" property="goods_id"/> 
             <result column="name" property="name"/> 
             <result column="price" property="price"/> 
           </association> 
         </collection> 
       </collection> 
   </resultMap>
   
   <select id="selectUserGoods" resultMap="map3">
     	select a.*,d.* 
     	from b_user a 
     	left join b_order b on a.user_id = b.user_id 
     	left join b_order_detail c on b.order_id=c.order_id 
     	left join b_goods d on c.goods_id = d.goods_id
   </select>
   ```

**注意：** 多对多的结果映射，就是`associate`和`connection`的混合使用。要特别注意扩展实体类的定义（dto），并在扩展类中描述清楚实体类和实体类之间的关系，最后使用`resultMap`标签去翻译实体关系



## 八、 延迟加载

​    所谓延迟加载，也叫按需加载。在连接查询时，可以利用延迟加载的机制，先加载主要信息，当需要关联其他信息时，再去按需求去加载关联信息。

​    例如：在电商项目中，显示用户的订单，一般不去关注订单的详细信息，只有想要查看详细信息时才会点击查看详细信息。（查询订单主表并关联显示订单明细）

​    延迟加载可以大大提高服务器的性能，单表的查询效率要高于多表查询的效率。

### 8.1 开启延迟加载的开关

在全局配置文件中可以配置开启延迟加载的开关：

- lazyLoadingEnabled    true
- aggressiveLazyLoading    false

```xml
<setting name="lazyLoadingEnabled" value="true"/>
<setting name="agressiveLazyLoading" value="false"/>
```



### 8.2 使用associate实现延迟加载

#### 8.2.1 需求

查询订单明细并关联显示商品详情

#### 8.2.2 编写映射文件

将关联查询拆分为多个SQL语句或mapperStatement对象，按照需求去调用不同的对象

**步骤：** 

- 首先查询订单明细表
- 根据订单明细表中的`goods_id`字段去查询商品信息

```xml
<resultMap type="com.sofwin.dto.OrderDetailDto" id="map10"> 
    <id column="order_detail_id" property="order_detail_id"/>
    <result column="order_id" property="order_id"/>
    <result column="goods_id" property="goods_id"/> 
    <result column="num" property="num"/>
    <!--
      要显示延迟加载，不能使用<Id>和<result>去映射结果
      select:指定需要调用statmentId，注意:如果不在同一个映射文件中，应该 namespace.statmentId
      column:用于指定被延迟加载调用的select语句的输入参数的值，将当前查询结果的某列作为select 的输入
      参数，列名为column的值
      如果外键有多个:{key=column,key2=column2} key为被调用的select语句的输入参数的key或属性名,cloumn为当前查询的列名
      注意:被延迟加载调用的select中的标签可以在接口中不去定义方法 
    -->
    <association property="goods" javaType="goods" select="selectGoodsByGoodsId" column="goods_id"></association>
</resultMap>

<select id="selectOrderDetail3" resultMap="map10">
  select a.* from b_order_detail a
</select>

<!-- 根据订单明细中的商品id查询商品信息 goods类型-->
<select id="selectGoodsByGoodsId" resultType="goods" parameterType="int">
  select *  from b_goods where goods_id=#{id}
</select>
```



### 8.3 使用connection实现延迟加载

#### 8.3.1 需求

查新订单信息表并关联显示订单详情表

#### 8.3.2 编写映射文件

```xml
<resultMap type="com.sofwin.dto.OrderDto" id="map20">
  	<id column="order_id" property="order_id"/>
    <result column="order_no" property="order_no"/>
    <result column="account" property="account"/>
    <result column="record_date" property="record_date"/>
    <result column="user_id" property="user_id"/>
	<!--
		延迟加载也不再使用id和result select:被调用的查询多个数据的statmentid column:和association一样
	-->
    <collection property="detail" ofType="OrderDetail" select="selectDetailByOrderId" column="order_id"></collection>
</resultMap>

<select id="selectOrderDetails2" resultMap="map20">
   select * from b_order
</select>

<select id="selectDetailByOrderId" resultType="orderDetail" parameterType="int">
   select *  from b_order_detail where order_id=#{orderId}
</select>
```





## 九、 一级缓存和二级缓存

### 9.1 一级缓存

​    Mybatis的一级缓存是sqlSession级别的缓存，同一个sqlSession，在操作同一个stementId时，第一次 查询会从数据库中查询，后面的查询触发缓存，不在从数据库查询。因为执行器默认使用缓存执行器， 默认开启了一级缓存。

### 9.2 二级缓存

​    Mybatis的二级缓存是mapper级别的缓存，不同的sqlSession在调用相同mapper中的同一个sql语句时会触发缓存机制。默认二级缓存没有开启。

**注意：** 映射的实体类必须可序列化

### 9.3 实例：

```xml
<!-- 开启全局缓存开关 -->
<settings>
	<setting name="cacheEnabled" value="true"></setting>
</settings>
```

```xml
<!-- 开启局部二级缓存开关 -->
<cache/>
<!--  当前的映射文件中就 开启了二级缓存  -->
```

```java
//测试一级缓存
public static void main(String[] args) throws IOException { 
  SqlSession sqlSession = SqlSessionUtil.getSqlSession(); 
  OrderMapper mapper = sqlSession.getMapper(OrderMapper.class); 
  List<OrderDto> orders2 = mapper.selectOrderDetails2(); 
  List<OrderDto> orders =mapper.selectOrderDetails2(); 
  sqlSession.close();
}
```

```java
//测试二级缓存
public static void main(String[] args) throws IOException { 
  SqlSession sqlSession = SqlSessionUtil.getSqlSession(); 
  OrderMapper mapper = sqlSession.getMapper(OrderMapper.class); 
  List<OrderDto> orders2 = mapper.selectOrderDetails2();
  sqlSession.close();
  SqlSession sqlSession2 = SqlSessionUtil.getSqlSession(); 
  OrderMapper mapper2 = session2.getMapper(OrderMapper.class); 
  mapper2.selectOrderDetails2();
	sqlSession2.close();
}
```

**注意：**

- 实际开发中mybatis的二级缓存一般不用， <cache-ref> 可以指定第三方的缓存框架，比如:ehcache。 电商项目中缓存都使用nosql(not only sql)非关系型数据库
- 二级缓存在验证保证sqlSession出自于同一个sqlSessionFactory





## 十、 mybatis的分页插件-PageHelper

使用第三方插件，在GitHub上托管    [官方文档](https://github.com/pagehelper/Mybatis-PageHelper/blob/master/wikis/zh/HowToUse.md)

### 10.1 添加依赖

- pagehelper-5.1.11.jar
- jsqlparser-2.0.jar

**注意：** 需要和PageHelper 依赖的版本一致

### 10.2 配置PageHelper插件

```xml
<plugins>
  <plugin interceptor="com.github.pagehelper.PageInterceptor">
      <property name="helperDialect" value="mysql"/>
  </plugin>
</plugins>
```

### 10.3 在代码中使用

```java
PageHelper.startPage(pageNumber,pageSize);//会对他后面第一条查询语句进行分页 
List users = mapper.selectUsers();//查询所有用户
List users1 = mapper.selectUsers();//查询所有用户不会分页
PageInfo pageinfo = new PageInfo(users);//根据分页后的List可以构造分页对象
```

```java
package com.github.pagehelper;

/**
 * 对Page<E>结果进行包装
 * <p/>
 * 新增分页的多项属性，主要参考:http://bbs.csdn.net/topics/360010907
 */
@SuppressWarnings({"rawtypes", "unchecked"})
public class PageInfo<T> extends PageSerializable<T> {
    public static final int DEFAULT_NAVIGATE_PAGES = 8;
    //当前页
    private int pageNum;
    //每页的数量
    private int pageSize;
    //当前页的数量
    private int size;

    //由于startRow和endRow不常用，这里说个具体的用法
    //可以在页面中"显示startRow到endRow 共size条数据"

    //当前页面第一个元素在数据库中的行号
    private long startRow;
    //当前页面最后一个元素在数据库中的行号
    private long endRow;
    //总页数
    private int pages;

    //前一页
    private int prePage;
    //下一页
    private int nextPage;

    //是否为第一页
    private boolean isFirstPage = false;
    //是否为最后一页
    private boolean isLastPage = false;
    //是否有前一页
    private boolean hasPreviousPage = false;
    //是否有下一页
    private boolean hasNextPage = false;
    //导航页码数
    private int navigatePages;
    //所有导航页号
    private int[] navigatepageNums;
    //导航条上的第一页
    private int navigateFirstPage;
    //导航条上的最后一页
    private int navigateLastPage;

    public PageInfo() {
    }

    /**
     * 包装Page对象
     *
     * @param list
     */
    public PageInfo(List<T> list) {
        this(list, DEFAULT_NAVIGATE_PAGES);
    }

    /**
     * 包装Page对象
     *
     * @param list          page结果
     * @param navigatePages 页码数量
     */
    public PageInfo(List<T> list, int navigatePages) {
        super(list);
        if (list instanceof Page) {
            Page page = (Page) list;
            this.pageNum = page.getPageNum();
            this.pageSize = page.getPageSize();

            this.pages = page.getPages();
            this.size = page.size();
            //由于结果是>startRow的，所以实际的需要+1
            if (this.size == 0) {
                this.startRow = 0;
                this.endRow = 0;
            } else {
                this.startRow = page.getStartRow() + 1;
                //计算实际的endRow（最后一页的时候特殊）
                this.endRow = this.startRow - 1 + this.size;
            }
        } else if (list instanceof Collection) {
            this.pageNum = 1;
            this.pageSize = list.size();

            this.pages = this.pageSize > 0 ? 1 : 0;
            this.size = list.size();
            this.startRow = 0;
            this.endRow = list.size() > 0 ? list.size() - 1 : 0;
        }
        if (list instanceof Collection) {
            calcByNavigatePages(navigatePages);
        }
    }

    public static <T> PageInfo<T> of(List<T> list) {
        return new PageInfo<T>(list);
    }

    public static <T> PageInfo<T> of(List<T> list, int navigatePages) {
        return new PageInfo<T>(list, navigatePages);
    }

    public void calcByNavigatePages(int navigatePages) {
        setNavigatePages(navigatePages);
        //计算导航页
        calcNavigatepageNums();
        //计算前后页，第一页，最后一页
        calcPage();
        //判断页面边界
        judgePageBoudary();
    }

    /**
     * 计算导航页
     */
    private void calcNavigatepageNums() {
        //当总页数小于或等于导航页码数时
        if (pages <= navigatePages) {
            navigatepageNums = new int[pages];
            for (int i = 0; i < pages; i++) {
                navigatepageNums[i] = i + 1;
            }
        } else { //当总页数大于导航页码数时
            navigatepageNums = new int[navigatePages];
            int startNum = pageNum - navigatePages / 2;
            int endNum = pageNum + navigatePages / 2;

            if (startNum < 1) {
                startNum = 1;
                //(最前navigatePages页
                for (int i = 0; i < navigatePages; i++) {
                    navigatepageNums[i] = startNum++;
                }
            } else if (endNum > pages) {
                endNum = pages;
                //最后navigatePages页
                for (int i = navigatePages - 1; i >= 0; i--) {
                    navigatepageNums[i] = endNum--;
                }
            } else {
                //所有中间页
                for (int i = 0; i < navigatePages; i++) {
                    navigatepageNums[i] = startNum++;
                }
            }
        }
    }

    /**
     * 计算前后页，第一页，最后一页
     */
    private void calcPage() {
        if (navigatepageNums != null && navigatepageNums.length > 0) {
            navigateFirstPage = navigatepageNums[0];
            navigateLastPage = navigatepageNums[navigatepageNums.length - 1];
            if (pageNum > 1) {
                prePage = pageNum - 1;
            }
            if (pageNum < pages) {
                nextPage = pageNum + 1;
            }
        }
    }

    /**
     * 判定页面边界
     */
    private void judgePageBoudary() {
        isFirstPage = pageNum == 1;
        isLastPage = pageNum == pages || pages == 0;
        hasPreviousPage = pageNum > 1;
        hasNextPage = pageNum < pages;
    }

    public int getPageNum() {
        return pageNum;
    }

    public void setPageNum(int pageNum) {
        this.pageNum = pageNum;
    }

    public int getPageSize() {
        return pageSize;
    }

    public void setPageSize(int pageSize) {
        this.pageSize = pageSize;
    }

    public int getSize() {
        return size;
    }

    public void setSize(int size) {
        this.size = size;
    }

    public long getStartRow() {
        return startRow;
    }

    public void setStartRow(long startRow) {
        this.startRow = startRow;
    }

    public long getEndRow() {
        return endRow;
    }

    public void setEndRow(long endRow) {
        this.endRow = endRow;
    }

    public int getPages() {
        return pages;
    }

    public void setPages(int pages) {
        this.pages = pages;
    }

    public int getPrePage() {
        return prePage;
    }

    public void setPrePage(int prePage) {
        this.prePage = prePage;
    }

    public int getNextPage() {
        return nextPage;
    }

    public void setNextPage(int nextPage) {
        this.nextPage = nextPage;
    }

    public boolean isIsFirstPage() {
        return isFirstPage;
    }

    public void setIsFirstPage(boolean isFirstPage) {
        this.isFirstPage = isFirstPage;
    }

    public boolean isIsLastPage() {
        return isLastPage;
    }

    public void setIsLastPage(boolean isLastPage) {
        this.isLastPage = isLastPage;
    }

    public boolean isHasPreviousPage() {
        return hasPreviousPage;
    }

    public void setHasPreviousPage(boolean hasPreviousPage) {
        this.hasPreviousPage = hasPreviousPage;
    }

    public boolean isHasNextPage() {
        return hasNextPage;
    }

    public void setHasNextPage(boolean hasNextPage) {
        this.hasNextPage = hasNextPage;
    }

    public int getNavigatePages() {
        return navigatePages;
    }

    public void setNavigatePages(int navigatePages) {
        this.navigatePages = navigatePages;
    }

    public int[] getNavigatepageNums() {
        return navigatepageNums;
    }

    public void setNavigatepageNums(int[] navigatepageNums) {
        this.navigatepageNums = navigatepageNums;
    }

    public int getNavigateFirstPage() {
        return navigateFirstPage;
    }

    public int getNavigateLastPage() {
        return navigateLastPage;
    }

    public void setNavigateFirstPage(int navigateFirstPage) {
        this.navigateFirstPage = navigateFirstPage;
    }

    public void setNavigateLastPage(int navigateLastPage) {
        this.navigateLastPage = navigateLastPage;
    }

    @Override
    public String toString() {
        final StringBuilder sb = new StringBuilder("PageInfo{");
        sb.append("pageNum=").append(pageNum);
        sb.append(", pageSize=").append(pageSize);
        sb.append(", size=").append(size);
        sb.append(", startRow=").append(startRow);
        sb.append(", endRow=").append(endRow);
        sb.append(", total=").append(total);
        sb.append(", pages=").append(pages);
        sb.append(", list=").append(list);
        sb.append(", prePage=").append(prePage);
        sb.append(", nextPage=").append(nextPage);
        sb.append(", isFirstPage=").append(isFirstPage);
        sb.append(", isLastPage=").append(isLastPage);
        sb.append(", hasPreviousPage=").append(hasPreviousPage);
        sb.append(", hasNextPage=").append(hasNextPage);
        sb.append(", navigatePages=").append(navigatePages);
        sb.append(", navigateFirstPage=").append(navigateFirstPage);
        sb.append(", navigateLastPage=").append(navigateLastPage);
        sb.append(", navigatepageNums=");
        if (navigatepageNums == null) {
            sb.append("null");
        } else {
            sb.append('[');
            for (int i = 0; i < navigatepageNums.length; ++i) {
                sb.append(i == 0 ? "" : ", ").append(navigatepageNums[i]);
            }
            sb.append(']');
        }
        sb.append('}');
        return sb.toString();
    }
}
```





## 十一、 逆向工程（mybatis-Generator）

​    正向工程是先设计实体类和实体类之间的关系，再去创建数据库并设计表和表间关系。逆向工程是先设计表和表间关系，再去生成实体类和实体类之间的关系、映射文件和接口

​    mybatis官网提供了mybatis-generator项目，可以针对单表生成pojo,mapper接口以及mapper映射文件，提供了一套单表的CRUD操作。

### 11.1 搭建逆向工程

#### 11.1.1 Java代码

**注意：** 参考官网，实际开发过程中，逆向工程不直接搭建在正式的项目中，而是单独创建项目。

```java
package com.sofwin.util;

public class Generator {
	public static void main(String[] args) throws Exception{ 
		List<String> warnings = new ArrayList<String>(); 
		boolean overwrite = true; 
		//file中的url需要绝对路径
		File configFile = new File("/Users/dengchenyang/eclipse-workspace/mybatis-05/src/config.xml"); 
		ConfigurationParser cp = new ConfigurationParser(warnings); 
		Configuration config = cp.parseConfiguration(configFile); 
		DefaultShellCallback callback = new DefaultShellCallback(overwrite); 
		MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings); 
		myBatisGenerator.generate(null); 
	}
}
```



#### 11.1.2 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
	<!-- 定义逆向工程的内容
 				id：默认id
				targetRuntime:目标运行的mybatis
	-->
  <context id="DB2Tables" targetRuntime="MyBatis3">
    
    <!-- 配置数据库连接信息 -->
    <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
        connectionURL="jdbc:mysql://localhost/java1908z?nullCatalogMeansCurrent=true"
        userId="root"
        password="12345678">
    </jdbcConnection>
		
    <!-- 配置Java类型的解析器，数据库的什么类型应该转为Java什么类型 -->
    <javaTypeResolver >
      <property name="forceBigDecimals" value="false" />
    </javaTypeResolver>

    <!-- 用于配置pojo的生成位置
 					targetPackage：生成的pojo所在的包
					targetProject：生成的默认位置
		-->
    <javaModelGenerator targetPackage="com.sofwin.pojo" targetProject="./src">
      <property name="enableSubPackages" value="true" />
      <property name="trimStrings" value="true" />
    </javaModelGenerator>

    <!-- 用于配置映射文件的生成位置
 					targetPackage：生成的xml所在的包
					targetProject：生成的默认位置
		-->
    <sqlMapGenerator targetPackage="com.sofwin.mapper"  targetProject="./src">
      <property name="enableSubPackages" value="true" />
    </sqlMapGenerator>
    
		<!-- 用于接口文件文件的生成位置
 					targetPackage：生成的接口所在的包
					targetProject：生成的默认位置
		-->
    <javaClientGenerator type="XMLMAPPER" targetPackage="com.sofwin.mapper"  targetProject="./src">
      <property name="enableSubPackages" value="true" />
    </javaClientGenerator>
		
    <!-- 用于配置当前数据库需要逆向工程的表
					schema：指定数据库名
		-->
    <table tableName="b_order"></table>
    <table tableName="b_order_detail"></table>

  </context>
</generatorConfiguration>
```

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/img/20201203181317.jpg)



### 11.2 逆向工程的使用 

#### 11.2.1 不包含blog字段的

- insert

  | 方法                         | 描述                                                         |
  | ---------------------------- | ------------------------------------------------------------ |
  | insert(Object pojo)          | insert后的字段列表为所有列，一般在开发时需要改（自增主键存在问题） |
  | insertSelective(Object pojo) | insert时只会插入不为空的字段                                 |

  ```java
  public static void main(String[] args) throws IOException {
    SqlSession session = SqlSessionUtil.getSqlSession(); 
    SUserMapper mapper = session.getMapper(SUserMapper.class); 
    //将username='username'并且pwd='pwd'的数据更新为s_user表中的数据 
    SUser user = new SUser();
  	user.setUsername("username100"); 
    user.setPwd("pwd100");
  	int flag = mapper.insert(user); 
    System.out.println(flag);
    session.close();
  }
  ```

  ```java
  public static void main(String[] args) throws IOException {
    SqlSession session = SqlSessionUtil.getSqlSession(); 
    SUserMapper mapper = session.getMapper(SUserMapper.class); 
    //将username='username'并且pwd='pwd'的数据更新为s_user表中的数据 
    SUser user = new SUser();
  	user.setUsername("username100"); 
    user.setPwd("pwd100");
  	int flag = mapper.insertSelective(user); 
    System.out.println(flag);
    session.close();
  }
  ```

  

- update

  | 方法                                                  | 描述                                          |
  | ----------------------------------------------------- | --------------------------------------------- |
  | updateByExample(Object pojo,Example example)          | 根据example定义的查询条件更新所有字段         |
  | updateByExampleSelective(Object pojo,Example example) | 根据example定义的查询条件更新所有不为空的字段 |
  | updateByPrimkey(Object pojo)                          | 根据主键更新所有字段                          |
  | updateByPrimaryKeySelective(Object pojo)              | 根据主键更新所有不为空符字段                  |

  ```java
  public static void main(String[] args) throws IOException { 
    SqlSession session = SqlSessionUtil.getSqlSession(); 
    SUserMapper mapper = session.getMapper(SUserMapper.class); 
    //将username='username'并且pwd='pwd'的数据更新为user中的数据 
    SUser user = new SUser();
  	user.setUsername("username1");
    user.setPwd("pwd1");
  	SUserExample example = new SUserExample();
    Criteria criteria = example.createCriteria(); 
    criteria.andUsernameEqualTo("username");
    criteria.andPwdEqualTo("pwd");
  	int flag = mapper.updateByExample(user, example );
    System.out.println(flag);
  	session.close();
  }
  ```

  ```java
  public static void main(String[] args) throws IOException { 
    SqlSession session = SqlSessionUtil.getSqlSession(); 
    SUserMapper mapper = session.getMapper(SUserMapper.class); 
    //将username='username'并且pwd='pwd'的数据更新为user中的数据 
    SUser user = new SUser();
  	user.setUsername("username1");
    user.setPwd("pwd1");
  	SUserExample example = new SUserExample();
    Criteria criteria = example.createCriteria(); 
    criteria.andUsernameEqualTo("username");
    criteria.andPwdEqualTo("pwd");
  	int flag = mapper.updateByExampleselective(user, example );
    System.out.println(flag);
  	session.close();
  }
  ```

  ```java
  public static void main(String[] args) throws IOException { 
    SqlSession session = SqlSessionUtil.getSqlSession(); 
    SUserMapper mapper = session.getMapper(SUserMapper.class); 
    SUser user = new SUser();
  	user.setId(12); 
    user.setUsername("username"); 
    user.setPwd("pwd");
  	int flag = mapper.updateByPrimaryKey(user); 
    System.out.println(flag);
  	session.close(); 
  }
  ```

  ```java
  public static void main(String[] args) throws IOException { 
    SqlSession session = SqlSessionUtil.getSqlSession(); 
    SUserMapper mapper = session.getMapper(SUserMapper.class); 
    SUser user = new SUser();
  	user.setId(12); 
    user.setUsername("username"); 
    user.setPwd("pwd");
  	int flag = mapper.updateByPrimaryKeySelective(user); 
    System.out.println(flag);
  	session.close(); 
  }
  ```

  

- delete

  | 方法                            | 描述             |
  | ------------------------------- | ---------------- |
  | deleteByExample(Object pojo)    | 根据查询条件删除 |
  | deleteByPrimaryKey(Object pojo) | 根据主键删除     |

  ```java
  public static void main(String[] args) throws IOException { 
    SqlSession session = SqlSessionUtil.getSqlSession();
    SUserMapper mapper = session.getMapper(SUserMapper.class);
    int num = mapper.deleteByPrimaryKey(9);
    System.out.println(num);
  	session.close();
  }
  ```

  ```java
  public static void main(String[] args) throws IOException { 
    SqlSession session = SqlSessionUtil.getSqlSession();
    SUserMapper mapper = session.getMapper(SUserMapper.class); 
    SUserExample example = new SUserExample();
  	Criteria criteria = example.createCriteria(); 
    criteria.andUsernameEqualTo("admin3");
  	int num = mapper.deleteByExample(example );
    System.out.println(num);
  	session.close(); 
  }
  ```

  

- select

  | 方法                            | 描述                                     |
  | ------------------------------- | ---------------------------------------- |
  | selectByExample(Object pojo)    | 根据自定义查询条件查询数据，返回List类型 |
  | selectByPrimaryKey(Object pojo) | 根据主键查询数据（pojo类型）             |
  | countByExample(Object pojo)     | 根据查询条件返回总行数，返回long类型     |

  ```java
  public static void main(String[] args) throws IOException { 
    SqlSession session = SqlSessionUtil.getSqlSession(); 
    SUserMapper mapper = session.getMapper(SUserMapper.class); 
    SUser user = mapper.selectByPrimaryKey(20); 
    System.out.println(user.getUsername());
  	session.close(); 
  }
  ```

  ```java
  public static void main(String[] args) throws IOException { 
    SqlSession session = SqlSessionUtil.getSqlSession(); 
    SUserMapper mapper = session.getMapper(SUserMapper.class); 
    //用于创建自定义查询条件的
  	SUserExample example = new SUserExample();
    //用于保存查询条件的【重要的对象】，WHERE后会自动增加()
    Criteria criteria = example.createCriteria(); 
    //区间查询:between value1 and value2
  	// criteria.andIdBetween(20, 30); 
    //等值查询，相等:= id=value
  	// criteria.andIdEqualTo(20); 
    // 大于 id>value
  	// criteria.andIdGreaterThan(20); 
    //大于等于 id>=value
  	// criteria.andIdGreaterThanOrEqualTo(value) 
    //生成in子句
  	List<Integer> ids = new ArrayList<Integer>(); 
    ids.add(20);
  	ids.add(30);
  	// criteria.andIdIn(ids); 
    //is not null 判断非空
  	// criteria.andIdIsNotNull();
    // is null
  	// criteria.andIdIsNull();
    //< id<value
  	criteria.andIdLessThan(100);
    //<= id <=value
  	// criteria.andIdLessThanOrEqualTo(value);
    //not between value1 and value2	
  	// criteria.andIdNotBetween(value1, value2);
  	//<>注意:在映射文件中不能写<>,!=
  	// criteria.andIdNotEqualTo(value);
  	//not in
  	// criteria.andIdNotIn(values)
  	//like 使用的是占位符，加%% 
    criteria.andUsernameLike("%a%"); 
    //or连接，需要创建多个criteria对象
  	//查询id<=100 并且username包含a或者id>100的用户 
    Criteria criteria2 = example.createCriteria(); 
    criteria2.andIdGreaterThan(100);
    example.or(criteria2); 
    //true，在select语句后增加distinct关键字(去重)
    example.setDistinct(true);
  	//设置排序子句
  	example.setOrderByClause("id desc,username asc"); 
    //根据自定义查询条件来查询集合
  	List<SUser> users = mapper.selectByExample(example); 
    for (SUser sUser : users) {
  		System.out.println(sUser.getId()); 
    }
  	session.close(); 
  }
  ```



#### 11.2.2 包含blog字段的

- 修改逆向工程配置文件（config.xml）

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE generatorConfiguration
    PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
    "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
  
  <generatorConfiguration>
    <context id="DB2Tables" targetRuntime="MyBatis3" defaultModelType="hierarchical">
      
      <!--定义逆向工程内容
  				id:默认id
  				targetRuntime:目标运行的mybatis 
  				defaultModelType:hierarchical用于处理blob类型的字段
  		-->
      <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
          connectionURL="jdbc:mysql://localhost/java1908z?nullCatalogMeansCurrent=true"
          userId="root"
          password="12345678">
      </jdbcConnection>
  		
      <javaTypeResolver >
        <property name="forceBigDecimals" value="false" />
      </javaTypeResolver>
  
      <javaModelGenerator targetPackage="com.sofwin.pojo" targetProject="./src">
        <property name="enableSubPackages" value="true" />
        <property name="trimStrings" value="true" />
      </javaModelGenerator>
  
      <sqlMapGenerator targetPackage="com.sofwin.mapper"  targetProject="./src">
        <property name="enableSubPackages" value="true" />
      </sqlMapGenerator>
      
      <javaClientGenerator type="XMLMAPPER" targetPackage="com.sofwin.mapper"  targetProject="./src">
        <property name="enableSubPackages" value="true" />
      </javaClientGenerator>
  		
      <table tableName="b_order"></table>
      <table tableName="b_order_detail"></table>
  
    </context>
  </generatorConfiguration>
  ```

  

- update

  | 方法                                 | 描述                                 |
  | ------------------------------------ | ------------------------------------ |
  | updateByExampleWithBlob(Object pojo) | 根据查询条件来更新普通属性和blob属性 |
  | updateByPrimaryKey(Object pojo)      | 根据主键来更新普通属性和blob属性     |

  

- Select

  | 方法                                 | 描述                                 |
  | ------------------------------------ | ------------------------------------ |
  | selectByExampleWithBlob(Object pojo) | 根据查询条件来查询普通字段和blob字段 |

  

