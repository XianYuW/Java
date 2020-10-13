# JDBC

```
JDBC(java Data Base Connectivity)：是java访问数据库的标准规范，是一种用于执行SQL语句的Java API，可以为多种关系数据库提供统一访问，它由一组用Java语言编写的类和接口组成，是Java访问数据库的标准规范。
```

## JDBC原理

```
JDBC是接口，驱动是接口的实现，没有驱动就没法完成数据库的连接，从而不能操作数据库。

每个数据库厂商都需要提供自己的驱动，用来连接自己公司的数据库

也就是说驱动一般都由数据库生成厂商提供。


```

JDBC的用处：

```
希望使用统一的一套代码，就可以操作所有的关系型数据库
```

JDBAC的好处：

```
1.面向接口编程
2.代码不依赖于任何的数据库
3.只要稍作修改，就可以切换到其他数据库
```

```
总结:

 JDBC就是由sun公司定义的一套操作所有关系型数据库的规则(接口)，而数据库厂商需要实现这套接口,提供数据库驱动jar包, 我们可以使用这套接口编程,真正执行的代码是对应驱动包中的实现类。
```

### JDBC开发

```
注册驱动：Class.forname("jdbc:mysql://localhost:3306/数据库名?char =UTF-8")

获取连接:Connection con =DriverManaager.getConnection(url,user,password);

获取语句执行平台:Statemnt statement = con.createStatement;

处理结果集:ResultSet resultset = statement.executeQuery(sql);	

释放资源:result.close();
		statement.close();
		con.close();
```

#### 注册驱动

```
//1.注册驱动（可以省略），forname方法将类进行初始化
Class.forName("com.mysql.jdbc.Driver");

com.mysql.jdbc.Driver源码(实现了Driver接口)：
public class Driver extends NonRegisteringDriver implements java.sql.Driver {
	//无参构造方法
    public Driver() throws SQLException {
    }
	//静态代码块，类加载到内存就会执行，既类被初始化就执行
    static {
        try {
        	/*
            DriverManager 驱动管理类 
            registerDriver(new Driver) 注册驱动的方法
        	注册数据库驱动
        	*/
            DriverManager.registerDriver(new Driver());
        } catch (SQLException var1) {
            throw new RuntimeException("Can't register driver!");
        }
    }
}
```

#### 获取连接:

***Connection con =DriverManaager.getConnection(url,user,password);***

```
Connection getConnection(String url, String user,String password)

mySql URL的格式
 jdbc:mysql://localhost:3306/数据库名?characterEncoding=UTF-8
 	jdbc: -- 协议
 	mysql:-- 子协议
 	//localhost:3306 -- 主机和端口
 	/数据库名 --  指定的数据库名
 	characterEncoding = UTF-8 -- 指定编码的字符集
```

#### 获取语句执行平台:

***Statemnt statement = con.createStatement;***

```
通过Connection 的 createStatement方法 获取sql语句执行对象：
	Statement createStatement()
	
​Statement -- 代表一条语句对象，用于发送 SQL 语句给服务器，
    		用于执行静态 SQL 语句并返回它所生成结果的对象。
    int executeUpdate(String sql1); --执行insert update delete语句.
    								 返回int类型,代表受影响的行数
    String sql_insert = "insert into 表名(字段名1...) values(字段值1...)";
    String sql_update = "update table 表名 set 列名 = 值 where 条件表达式 ";
    String sql_delete = "delete from tablename where 条件"
    								 
    ResultSet executeQuery(String sql2);--执行select语句, 返回ResultSet结果集对象
```

#### 处理结果集:

***ResultSet resultset = statement.executeQuery(sql);***

只有在进行查询操作的时候, 才会处理结果集

```
ResultSet接口
	--作用：封装数据库查询的结果集，对结果集进行遍历，取出每一条记录。
	
ResultSet接口方法:
	boolean next()	1) 游标向下一行
					2) 返回 boolean 类型，如果还有下一条记录，返回 true，否则返回 false
					
	xxx getXxx( String or int)
			1) 通过列名，参数是 String 类型的字段名称。返回不同的类型
			2) 通过列号，参数是整数，从 1 开始。根据获取列的数据类型，返回不同的类型
			
	xxx getXxx( String or int)重载方法参数可以是String 或者int
	
		实例：
			//使用while循环，通过列名 
			while(resultSet.next()){ 
				//获取id 
				int id = resultSet.getInt("id"); 
				//获取姓名 
				String username = resultSet.getString("username"); 
				//获取生日 
				Date birthday = resultSet.getDate("birthday"); 					                   System.out.println(id + " = " +username + " : " + birthday);
             }
             
             //使用while循环 ，通过列号
			while(resultSet.next()){ 
				//获取id 
				int id = resultSet.getInt(1); 
				//获取姓名 
				String username = resultSet.getString(2); 
				//获取生日 
				Date birthday = resultSet.getDate(3); 					                   		   System.out.println(id + " = " +username + " : " + birthday);
             }
```

#### 释放资源





## SQL注入

### 什么是SQL注入?

```
我们让用户输入的密码和 SQL 语句进行字符串拼接。用户输入的内容作为了 SQL 语句语法的一部分，改变了 原有SQL 真正的意义，以上问题称为 SQL 注入 .
```

### 如何实现的注入

```
select * from jdbc_user where username = 'abc' and password = 'abc' or '1'='1' 
其中：
		name='abc' and password='abc' 为假 
		'1'='1' 真 

相当于 select * from user where true=true; 查询了所有记录
```

###  如何解决

```
要解决 SQL注入 就不能让用户输入的密码和我们的 SQL语句 进行简单的字符串拼接。
因此我们通过引入预处理对象来解决
```



## 预处理对象

### PreparedStatement接口介绍

```
PreparedStatement 是 Statement 接口的子接口，继承于父接口中所有的方法。它是一个预编译的 SQL 语句对象.

预编译: 是指SQL语句被预编译--指的是sql语句被缓存在MySQL服务器中，
	并存储在 PreparedStatement 对象中。然后可以使用此对象多次高效地执行该语句。

​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​
​PreparedStatement 特点​：
	因为有预先编译的功能，提高 SQL 的执行效率。
	可以有效的防止 SQL 注入的问题，安全性更高
```

### 获取PreparedStatement对象

***PreparedStatemnt ps = connection.prepareStatement(sql);***

```
通过Connection创建PreparedStatement对象，方法：
PreparedStatement prepareStatement(String sql)
	指定预编译的 SQL 语句，SQL 语句中使用占位符 ? 创建一个语句对象
```

### PreparedStatement接口常用方法

```
int executeUpdate();--执行insert update delete语句.
ResultSet executeQuery();--执行select语句. 返回结果集对象 Resulet
```

```
setXxx(int parameterIndex, Xxx x)重载方法：
	void setDouble(int parameterIndex, double x)
	void setInt(int parameterIndex, int x)
	void setString(int parameterIndex, String x)
	void setObject(int parameterIndex, Object x)
```



### 使用PreparedStatement的步骤

```
 编写 SQL 语句，未知内容使用?占位：
	String sql = "SELECT * FROM jdbc_user WHERE username=? AND password=?";
​ 获得 PreparedStatement 对象 
	PreparedStatemnt ps = connection.prepareStatement(sql);
​ 设置实际参数：setXxx( 占位符的位置, 真实的值)
	ps.setString(1,root);
	ps.setString(2,123456);
​ 执行参数化 SQL 语句
	ps.executeQurey();
​ 关闭资源
	ps.close();
```

### PreparedStatement的执行原理

```
Statement对象没执行一条语句，就会将sql发送给MySQL服务器，数据库要先编译再执行，
	如果有多条数据，就需要执行多次

而PreparedStatement会将sql语句发送给mysql服务器进行预编译，
	然后引用预编译后的结果，这样就只需要编译一次。当我们执行多条语句，只需要设置参数就可以了，
	即使有多条数据，数据库也只是进行了一次编译
​ 	通过减少编译次数，提高执行效率
```

### Statement与PreparedStatement的区别?

```
Statement用于执行静态sql语句，在执行时，必须指定一个事先准备好的sql语句
​PreparedStatement是预编译的sql语句对象，语句中可以包含动态参数“?”，执行时可以设置动态参数的值
​PreparedStatement可以减少编译次数提高数据库性能
```

## JDBC控制事务

### 事务相关API

```
我们使用 Connection中的方法实现事务管理
	void setAutoCommit(boolean autoCommit)
			参数是 true 或 false 如果设置为 false，表示关闭自动提交，相当于开启事务
	void commit()		提交事务
	void rollback()		回滚事务
```

### 开发步骤

```
1. 获取连接
2. 开启事务			con.setAutoCommit(false);
3. 获取到 PreparedStatement , 执行两次更新操作
4. 正常情况下提交事务	con.commit();
5. 出现异常回滚事务		con.rollback();
6. 最后关闭资源
```







# 数据库连接池

## 什么是连接池

```
实际开发中“获得连接”或“释放资源”是非常消耗系统资源的两个过程，为了解决此类性能问题，通常情况我们
采用连接池技术，来共享连接Connection。这样我们就不需要每次都创建连接、释放连接了，这些操作都交
给了连接池.
```

### 连接池的好处

```
用池来管理Connection，这样可以重复使用Connection。 当使用完Connection后，调用Connection的
close()方法也不会真的关闭Connection，而是把Connection“归还”给池。
```

### 如何使用数据库连接池

```
Java为数据库连接池提供了公共的接口：javax.sql.DataSource，各个厂商需要让自己的连接池实现这个接口。
这样应用程序可以方便的切换不同厂商的连接池！

常见的连接池有 DBCP连接池, C3P0连接池, Druid连接池
```



## DBCP

 DBCP也是一个开源的连接池，是Apache成员之一，在企业开发中也比较常见，tomcat内置的连接池。

```
Java中提供了一个连接池的规则接口 ： DataSource , 它是java中提供的连接池

在DBCP包中提供了DataSource接口的实现类，我们要用的具体的连接池 BasicDataSource 类
```

```
//创建连接池对象 (有DBCP提供的实现类) 
public static BasicDataSource dataSource = new BasicDataSource(); 
//使用静态代码块进行配置 
static{ 
	dataSource.setDriverClassName(DRIVERNAME); 
	dataSource.setUrl(URL); 
	dataSource.setUsername(USERNAME); 
	dataSource.setPassword(PASSWORD); 
}
//获取连接的方法 
public static Connection getConnection() throws SQLException { 
	//从连接池中获取连接 
	Connection connection = dataSource.getConnection(); 
	return connection; 
}
```

## c3p0连接池

```
1) 将jar包 复制到myJar文件夹即可,IDEA会自动导入
2) 导入配置文件 c3p0-confifig.xml
```

```
C3P0提供的核心工具类, ComboPooledDataSource , 如果想使用连接池,就必须创建该类的对象

new ComboPooledDataSource(); 使用 默认配置

new ComboPooledDataSource("mysql"); 使用命名配置
```

```
//1.创建连接池对象 C3P0对DataSource接口的实现类 ,使用的配置是 配置文件中的默认配置
//public static ComboPooledDataSource dataSource =new ComboPooledDataSource();
//使用指定的配置
public static ComboPooledDataSource ds = new ComboPooledDataSource("mysql");
//获取连接的方法
public static Connection getConnection() throws SQLException {    
	return ds.getConnection();
}
//释放资源
...
```

## Druid连接池

```
Druid（德鲁伊）是阿里巴巴开发的号称为监控而生的数据库连接池，Druid是目前最好的数据库连接池。在功
能、性能、扩展性方面，都超过其他数据库连接池，同时加入了日志监控，可以很好的监控DB池连接和SQL的执行情况。
```

```
获取数据库连接池对象：
	通过工厂来来获取 DruidDataSourceFactory类的createDataSource方法
	createDataSource(Properties p) 方法参数可以是一个属性集对象
	
```

```
public class DruidUtils {    
	/**     
	* 定义数据库连接池接口，使用它来接收Druid连接池对象    
    */    
    public static DataSource dataSource;    
    //静态代码块    
    static{        
    	try {            
    		//创建属性集对象            
    		Properties properties = new Properties();            
    		//Druid连接池不能够主动加载配置文件，因此需要指定文件            
    		InputStream inputStream= DruidUtils.class.getClassLoader().getResourceAsStream("druid.properties");            				//Properties对象的方法load   从字节流中读取配置信息            						 		properties.load(inputStream);            
    		//通过工厂类获取连接池对象            
    		dataSource = DruidDataSourceFactory.createDataSource(properties);        
    	} catch (Exception e) {            
    		e.printStackTrace();        
    	}    
    }//static    
    //获取连接    
    public static Connection getConnection() throws SQLException {        
    	return dataSource.getConnection();    
    }    
    //关闭连接    
    ...
}
```

## DBUtils工具类

```
使用JDBC我们发现冗余的代码太多了,为了简化开发 我们选择使用 DbUtils

 Commons DbUtils是Apache组织提供的一个对JDBC进行简单封装的开源工具类库，使用它能够简化JDBC应用程
序的开发，同时也不会影响程序的性能。

使用方式:
	DBUtils就是JDBC的简化开发工具包。需要项目导入commons-dbutils-1.6.jar。
	
DButils核心功能介绍：
1. QueryRunner 中提供对sql语句操作的API.
2. ResultSetHandler接口，用于定义select操作后，怎样封装结果集. 
3. DBUtils类,他就是一个工具类,定义了关闭资源与事务处理相关方法.
```

### 1.DBUtils完成CRUD

#### 1.1QueryRunner核心类

```
构造方法：
	QueryRunner() 

	QueryRunner(DataSource ds) ,
		提供数据源（连接池），DBUtils底层自动维护连接connection

常用方法：
	update(Connection conn, String sql, Object... params) 
		用来完成表数据的增加、删除、更新操作
	query(Connection conn, String sql, ResultSetHandler<T> rsh, Object... params) 
		用来完成表数据的查询操作
```

#### 1.2QueryRunner的创建

```
//方式一 手动模式
QueryRunner qr = new QueryRunner();
//方式二 自动模式 提供数据库连接池对象 DButils会自动维护连接
QueryRunner qr2 = new QueryRunner(DruidUtils.getDataSource());

//自动模式需要传入连接池对象
//获取连接池对象 
public static DataSource getDataSource(){ 
	return dataSource; 
}
```

#### 1.3QueryRunner实现增、删、改操作

```
public void testInsert() throws SQLException {    
	//1.创建 QueryRunner 手动模式创建    
	QueryRunner qr = new QueryRunner();    
	//2.编写 占位符方式 SQL    
	String sql = "insert into employee values(?,?,?,?,?,?)";    
	//3.设置占位符的参数    
	Object[] parm = {null,"马尔扎哈",18,"女",10000,"1997-06-15"};    
	//4.执行 update方法    
	Connection con = DBCPUtils.getConnection();    
	int i = qr.update(con, sql, parm);    
	//5.释放资源    
	DBCPUtils.close(con);
}
```

#### 1.4QueryRunner实现查询操作

```
ResultSetHandler接口简介
	ResultSetHandler可以对查询出来的ResultSet结果集进行处理，达到一些业务上的需求。

ResultSetHandler结果集处理类
	本例展示的是使用ResultSetHandler接口的几个常见实现类实现数据库的增删改查，可以大大减少代码量，优化
程序。

每一种实现类都代表了对查询结果集的一种处理方式

ResultSetHandler实现类 								说明
ArrayHandler 						将结果集中的第一条记录封装到一个Object[]数组中，数组中的每											一个元素就是这条记录中的每一个字段的值
ArrayListHandler 					将结果集中的每一条记录都封装到一个Object[]数组中，将这些数组在										封装到List集合中。

BeanHandler 						将结果集中第一条记录封装到一个指定的javaBean中.
BeanListHandler 					将结果集中每一条记录封装到指定的javaBean中，再将这些javaBean										在封装到List集合中

ColumnListHandler 					将结果集中指定的列的字段值，封装到一个List集合中
KeyedHandler 						将结果集中每一条记录封装到Map<String,Object>,在将这个									map集合做为另一个Map的value,另一个Map集合的key是指定的字段的值。

MapHandler 							将结果集中第一条记录封装到了Map<String,Object>集合中，
									key就是字段名称，value就是字段值
MapListHandler 						将结果集中每一条记录封装到了Map<String,Object>集合中，
								key就是字段名称，value就是字段值，在将这些Map封装到List集合中。
								
ScalarHandler 					它是用于封装单个数据。例如 select count(*) from 表操作。
```

```
ArrayHandler/ArrayListHandler:

    Object[] query = qr.query(sql, new ArrayHandler(), param);
    List<Object[]> querys = qr.query(sql, new ArrayListHandler(),param);
```

```
BeanHandler/BeanListHandler:
	BeanHandler(Class<T> type),创建BeanHandler需要传递一个参数，就是JavaBean类的class文件对象
		例如：Employee.class 通过反射去创建Employee对象，
			 将结果集封装到对象中并返回
	
	Employee query = qr.query(sql1, new BeanHandler<Employee>(Employee.class), param);
	List<Employee> list = qr.query(sql2, new BeanListHandler<Employee>(Employee.class), 3000);
```

# 数据库批处理

## 什么是批处理

```
批处理(batch) 操作数据库
	批处理指的是一次操作中执行多条SQL语句，批处理相比于一次一次执行效率会提高很多。
	当向数据库中添加大量的数据时，需要用到批处理。
举例: 送货员的工作：
	未使用批处理的时候，送货员每次只能运送 一件货物给商家；
	使用批处理，则是送货员将所有要运送的货物, 都用车带到发放处派给客户。
```

## 实现批处理

Statement和PreparedStatement都支持批处理操作，这里我们介绍一下PreparedStatement的批处理方式：

### 1) 要用到的方法

```
void addBatch() 				将给定的 SQL 命令添加到此 Statement 对象的当前命令列表中。通过调用								方法 executeBatch 可以批量执行此列表中的命令。

int[] executeBatch() 			每次提交一批命令到数据库中执行，如果所有的命令都成功执行了，
								那么返回一个数组，这个数组是说明每条命令所影响的行数
```

### 2) mysql 批处理是默认关闭的，所以需要加一个参数才打开mysql 数据库批处理，在url中添加

```
rewriteBatchedStatements=true 
例如: url=jdbc:mysql://127.0.0.1:3306/db5?
characterEncoding=UTF-8&rewriteBatchedStatements=true
```

### 3）测试

```
@Test
public void testBatch() throws SQLException {    
	Connection con = DruidUtils.getConnection();    
	String sql = "insert into testBatch values(null,?)";    
	PreparedStatement ps = con.prepareStatement(sql);    
	for(int i = 0;i<10000;i++){        
		ps.setString(1,"猪头"+i);  
        //将sql添加到批处理列表
		ps.addBatch();    
	}    
	long start = System.currentTimeMillis(); 
    //统一批量执行
	ps.executeBatch();    
	long end = System.currentTimeMillis();    
	System.out.println("花费的时间是："+(end-start)); 
    //关闭连接
	DruidUtils.close(con,ps);
}
```

# MySql元数据

## 什么是元数据

```
除了表之外的数据都是元数据,可以分为三类：

​    查询结果信息： UPDATE 或 DELETE语句 受影响的记录数。
​    数据库和数据表的信息：包含了数据库及数据表的结构信息。
​    MySQL服务器信息： 包含了数据库服务器的当前状态，版本号等。
```

## 元数据相关的命令介绍 

```

-- 1.查看服务器当前状态 
		show status; 查看服务器的状态信息
		
-- 2.查看MySQl的版本信息 
		select version(); 获取mysql服务器的版本信息
		
-- 3.查询表中的详细信息 
		show columns from table_name; 显示表的字段信息等，和desc table_name一样
		
-- 4.显示数据表的详细索引信息 
		show index from table_name; 显示数据表的详细索引信息，包括PRIMARY KEY（主键）
		
-- 5.列出所有数据库 
		show databases:列出所有数据库
-- 6.显示当前数据库的所有表 
		show tables : 显示当前数据库的所有表
-- 7.获取当前的数据库名
		select database(): 获取当前的数据库名
```

## 使用JDBC获取元数据

### 常用类介绍

```
JDBC中描述元数据的类：
    DatabaseMetaData 		描述数据库的元数据对象
    ResultSetMetaData 		描述结果集的元数据对象
```

```
获取元数据对象的方法 : getMetaData ()
		connection 连接对象, 调用 getMetaData () 方法,
					获取的是DatabaseMetaData 数据库元数据对象
					
		PrepareStatement 预处理对象调用 getMetaData () , 
						获取的是ResultSetMetaData , 结果集元数据对象
```

```
DatabaseMetaData的常用方法：

	getURL() : 获取数据库的URL
	getUserName(): 获取当前数据库的用户名
	getDatabaseProductName(): 获取数据库的产品名称
	getDatabaseProductVersion(): 获取数据的版本号
	getDriverName(): 返回驱动程序的名称
	isReadOnly(): 判断数据库是否只允许只读 true 代表只读
```

```
ResultSetMetaData的常用方法：

	getColumnCount() : 当前结果集共有多少列
    getColumnName(int i) : 获取指定列号的列名, 参数是整数 从1开始
    getColumnTypeName(int i): 获取指定列号列的类型, 参数是整数 从1开始
```





# XML

## XML基本介绍

### 概述

```
XML即可扩展标记语言（Extensible Markup Language）
    W3C在1998年2月发布1.0版本，2004年2月又发布1.1版本，但因为1.1版本不能向下兼容1.0版
    本，所以1.1没有人用。同时，在2004年2月W3C又发布了1.0版本的第三版。
特点：
	可扩展的, 标签都是自定义的
	语法十分严格
```

### XML的作用

```
存储数据 				通常，我们在数据库中存储数据。不过，如果希望数据的可移植性更强，
						我们可以把数据存储 XML 文件中
配置文件 				作为各种技术框架的配置文件使用 (最多)

在网络中传输 				客户端可以使用XML格式向服务器端发送数据,
                        服务器接收到xml格式数据,进行解析
```

## XML文档声明格式

```
文档声明必须为结束；
文档声明必写在第一行；
```

### >语法格式:

```
<?xml version="1.0" encoding="UTF-8"?>
```

### >属性说明:

```
versioin：指定XML文档版本。必须属性，因为我们不会选择1.1，只会选择1.0；
encoding：指定当前文档的编码。可选属性，默认值是utf-8；
```

### >元素

```
Element 元素: 是XML文档中最重要的组成部分
元素的命名规则
    
    1. 不能使用空格，不能使用冒号
    2. xml 标签名称区分大小写
    3. XML 必须有且只有一个根元素
    
语法格式：
	<users><users>
	1.XML必须有且只有一个根元素，他是其他所有元素的父元素
	2.普通元素的结构开始标签、元素体、结束标签组成
	3.元素体：元素体可以是元素，也可以是文本
		例如：<hello> 大家好 </hello>
			或者是：<hello> <a>你好</a> </hello>
	4.空元素：只有开始标签，而没有结束标签，但元素必须自己闭合，如：<close/>
```

###  >属性

```
<bean id="" class=""> </bean>

1.属性是元素的一部分，他必须出现在元素的开始标签中
2.属性的定义格式：属性名=属性值，其中属性值必须使用单引或双引
3.一个元素可以有0-N个属性，但一个元素中不能出现同名属性
4.属性名不能使用空格、冒号等特殊字符，且必须以字母开头
```

### >注释

```
XML的注释，以"<!--"开始，以"-->"结束。注释内容会被XML解析器忽略
```

### >使用XML描述数据表中的数据

```
<?xml version=1.0 encoding=UTF-8>
<employees>
	<employee eid="2">
		<ename></
</employees>
```

## XML约束

```
在XML技术里，可以编写一个文档来约束一个XML文档的书写规范，这称之为XML约束。
常见的xml约束：
            DTD
            Schema
```

### 	1.DTD约束

```
DTD（Document Type Definition），文档类型定义，用来约束XML文档。
	规定XML文档中元素的名称，子元素的名称及顺序，元素的属性等。
	
编写DTD.
通常情况我们都是通过框架提供的DTD约束文档，编写对应的XML文档。常见框架使用DTD约束有：
Struts2、hibernate等。

创建student.dtd约束文件
<!ELEMENT students (student+)>
    <!ELEMENT student (name,age,sex)>
    <!ELEMENT name (#PCDATA)>
    <!ELEMENT age (#PCDATA)>
    <!ELEMENT sex (#PCDATA)>
    <!ATTLIST student number ID #REQUIRED>

<!--
    ELEMENT: 用来定义元素
    students (student+) : 代表根元素 必须是 <students>
    student+ : 根标签中至少有一个 student子元素, + 代表至少一个
    student (name,age,sex): student 标签中包含的子元素,按顺序出现
    #PCDATA: 是普通文本内容
    ATTLIST: 用来定义属性
    student number ID #REQUIRED student子元素中 有一个ID属性叫做 number,是必须填写的
    ID: 唯一 值只能是字母或者下划线开头
-->
```

### 	2.引入DTD

```
引入dtd文档到xml文档中,两种方式：
        内部dtd：将约束规则定义在xml文档中
        外部dtd：将约束的规则定义在外部的dtd文件中
        	本地：<!DOCTYPE 根标签名 SYSTEM "dtd文件的位置">
        	网络：<!DOCTYPE 根标签名 PUBLIC "dtd文件名字" "dtd文件的位置URL">
student.xml

```

## 2.Schema约束

什么是Schema

```
Schema是新的XML文档约束, 比DTD强大很多，是DTD 替代者；
Schema本身也是XML文档，但Schema文档的扩展名为xsd，而不是xml。 
Schema 功能更强大，内置多种简单和复杂的数据类型
Schema 支持命名空间 (一个XML中可以引入多个约束文档)
```

### Schema约束示例









## XML解析

### 解析概述

```
当将数据存储在XML后，我们就希望通过程序获得XML的内容。如果我们使用Java基础所学习的IO知
识是可以完成的，不过你需要非常繁琐的操作才可以完成，且开发中会遇到不同问题（只读、读写）。
人们为不同问题提供不同的解析方式，并提交对应的解析器，方便开发人员操作XML。
```

### XML解析方式

```
DOM：要求解析器把整个XML文档装载到内存，并解析成一个Document对象。
    优点：元素与元素之间保留结构关系，故可以进行增删改查操作。
    缺点：XML文档过大，可能出现内存溢出显现。
SAX：是一种速度更快，更有效的方法。它逐行扫描文档，一边扫描一边解析。并以事件驱动的方
式进行具体解析，每执行一行，都将触发对应的事件。（了解）
    优点：占用内存少 处理速度快，可以处理大文件
    缺点：只能读，逐行后将释放资源。
```

