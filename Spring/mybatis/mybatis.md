# MyBatis常用配置：

###  environments标签

-----数据库环境的配置，支持多环境配置

```
<evironments default="develpoment">--------------指定默认的环境名称

	<evironment id="development">----------------指定当前环境的名称
		
		<transactionManager type="JDBC"/>--------指定事务管理类型时JDBC
		
		<!--数据源信息
                POOLED:使用mybatis的连接池
                UNPOOLED：不适用连接池，每一次都是用新的连接
            -->
		<dataSource type="POOLED">
			//配置参数
			<property name="driver" value="com.mysql.jdbc.Driver"/>
			<property name="url" value="jdbc:mysql:///mybatis_db?characterEncoding=utf-8"/>
            <property name="username" value="root"/>
            <property name="password" value="123456"/>
		</dataSource>
    </environment>
</environments>

```

```
1.事务管理器（transactionManager）类型有两种：
	- JDBC： 这个配置就是直接使用了JDBC 的提交和回滚设置，
			它依赖于从数据源得到的连接来管理事务作用域。
			
    - MANAGED： 这个配置几乎没做什么。它从来不提交或回滚一个连接，
    		而是让容器来管理事务的整个生命周期。 
    		例如：mybatis与spring整合后，事务交给spring容器管理。
    		
2. 数据源（dataSource）常用类型有三种：
	- UNPOOLED：这个数据源的实现只是每次被请求时打开和关闭连接。
	
	- POOLED： 这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来。
    
	- JNDI : 这个数据源实现是为了能在如 EJB 或应用服务器这类容器中使用，
			容器可以集中或在外部配置数据 源，然后放置一个 JNDI 上下文的数据源引用
```

### properties标签

实际开发中，习惯将数据源的配置信息单独抽取成一个properties文件，

该标签可以加载额外配置的properties： 

```
jdbc.driver=com.mysql.jdbc.Driver 
jdbc.url=jdbc:mysql:///mybatis_db 
jdbc.username=root 
jdbc.password=root
```

此时，配置文件要改为：

```
<evironments default="develpoment">--------------指定默认的环境名称

	<!--加载properties文件-->
    <properties resource="jdbc.properties"></properties>

	<evironment id="development">----------------指定当前环境的名称
		
		<transactionManager type="JDBC"/>
		<dataSource type="POOLED">
			//配置参数
			<property name="driver" value="${jdbc.driver}"/>
			<property name="url" value="${jdbc.url}"/>
            <property name="username" value="${jdbc.username}"/>
            <property name="password" value="${jdbc.password}"/>
		</dataSource>
    </environment>
</environments>

```

### typeAliases标签

类型别名是为 Java 类型设置一个短的名字。

为了简化映射文件 Java 类型设置，mybatis框架为我们设置好的一些常用的类型的别名：

```
<evironments default="develpoment">

	<!--加载properties文件-->
    <properties resource="jdbc.properties"></properties>
    
    <!--设置别名-->
    <typeAliases>
        <!--方式一：给单个实体起别名-->
       <!-- <typeAlias type="com.lagou.domain.User" alias="user"></typeAlias>-->
        <!--方式二：批量起别名 别名就是类名，且不区分大小写-->
        <package name="com.lagou.domain"/>

    </typeAliases>

	<evironment id="development">
		<transactionManager type="JDBC"/>
		<dataSource type="POOLED">
			//配置参数
			<property name="driver" value="${jdbc.driver}"/>
			<property name="url" value="${jdbc.url}"/>
            <property name="username" value="${jdbc.username}"/>
            <property name="password" value="${jdbc.password}"/>
		</dataSource>
    </environment>
</environments>
```

### mappers标签

该标签的作用是加载映射的，加载方式有如下几种：

```
1. 使用相对于类路径的资源引用，例如：
	<mapper resource="org/mybatis/builder/userMapper.xml"/> 
	
2. 使用完全限定资源定位符（URL），例如：
	<mapper url="file:///var/mappers/userMapper.xml"/> 
	
《下面两种mapper代理开发中使用:暂时了解》 
3. 使用映射器接口实现类的完全限定类名，例如： <mapper class="org.mybatis.builder.userMapper"/> 4. 将包内的映射器接口实现全部注册为映射器，例如： <package name="org.mybatis.builder"/>

<mappers>
        <!--<mapper resource="com/lagou/mapper/UserMapper.xml"></mapper>-->
        <!--使用class方式加载映射：使用该方式，接口和映射文件需要同包同名-->
        <!--<mapper class="com.lagou.mapper.UserMapper"></mapper>-->
        <!--批量加载映射-->
        <package name="com.lagou.mapper"/>
    </mappers>
```

## Mybatis的API概述

### SqlSession工厂构建器SqlSessionFactoryBuilder

常用API：SqlSessionFactory build(InputStream inputStream)

通过加载mybatis的核心文件的输入流的形式构建一个SqlSessionFactory对象

```
//加载核心配置文件
InputStream is = Resources.getResourceAsStream("SqlMapConfig.xml");
//获取SqlSessionFactor工厂对象
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(is);
```



### SqlSession工厂对象SqlSessionFactory

SqlSessionFactory 有多个个方法创建SqlSession 实例。常用的有如下两个：

```
//获取sqlSession会话对象
SqlSession sqlSession = sqlSessionFactory.openSession();

有两个方法：
openSession():	会默认开启一个事务，但是不会自动提交，需要手动提交事务
openSession(boolean autoCommit)		设置参数为自动提交，设置为true，则不需要手动提交事务
```



### SqlSession会话对象

SqlSession 实例在 MyBatis 中是非常强大的一个类。在这里你会看到所有执行语句、提交或回滚事务

和获取映射器实例的方法。

```
执行语句的方法主要有：
<T> T selectOne(String statement, Object parameter) 
<E> List<E> selectList(String statement, Object parameter) 
int insert(String statement, Object parameter) 
int update(String statement, Object parameter) 
int delete(String statement, Object parameter)

操作事务的方法主要有：
void commit() 
void rollback()
```





## Mybatis基本原理

```
mybatis配置文件：
	SqlMapConfig： 核心配置文件：数据源，事务 ，引入映射配置文件
	Mapper1.xml/Maper2.xml/Mappern.xml:映射配置文件，
									主要配置了要执行的sql语句，传入参数，传出参数等
|									
↓									
SqlSessionFactory(工厂对象)
	new SqlSessionFactoryBuilder().build(in);
	1)进行了初始化配置：使用了dom4j解析了配置文件，将映射配置文件中的配置封装了一个一个
	的MappedStatement对象(MappedStatement:类)
	将这些MappedStatement对象封装成map集合
	|	key				|		value		|
	|userMapper.findAll |	MappedStatement	|
	|userMapper.save	|	MappedStatement	|
	
	2)创建了sqlSessionFactory对象
|
↓
sqlSession(会话对象)
	本身并不会直接操作数据库，委派给执行器进行操作
	sqlSession。selectList("UserMapper.findAll");
|
↓
Executor(执行器)
	执行器的底层代码就是JDBC
	根据传递的key，去map集合中查找，查找到了对应的value，也就是mappedStatement对象，
	就开始执行操作
|
↓
MappedStatement(包含输入映射，输出映射)
	输入映射：{map}、{String、Integer等基础数据类型}、{pojo}
	输出映射：{map}、{String、Integer等基础数据类型}、{pojo}、{list}
|
↓
数据库
```

## Mybatis代理开发方式

采用 Mybatis 的基于接口代理方式实现 持久层 的开发，这种方式是我们后面进入企业的主流。

基于接口代理方式的开发只需要程序员编写 Mapper 接口，Mybatis 框架会为我们动态生成实现类的对

象。

```
要求我们遵循一定的规范

​	Mapper.xml映射文件中的namespace与mapper接口的全限定名相同

​	Mapper接口方法名和Mapper.xml映射文件中定义的每个statement的id相同

​	Mapper接口方法的输入参数类型和mapper.xml映射文件中定义的每个sql的parameterType的类型相同

​	Mapper接口方法的输出参数类型和mapper.xml映射文件中定义的每个sql的resultType的类型相同
```

### Mybatis基于接口代理方式的内部执行原理

```
1.通过追踪源码我们会发现，我们使用的mapper实际上是一个代理对象,是由MapperProxy代理产生的。


        public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
        final MapperProxyFactory<T> mapperProxyFactory = (MapperProxyFactory<T>) 									knownMappers.get(type);
        if (mapperProxyFactory == null) {
          throw new BindingException("Type " + type + " is not known to the 							MapperRegistry.");
        }
        try {
          return mapperProxyFactory.newInstance(sqlSession);//返回一个代理对象的实例
        } catch (Exception e) {
          throw new BindingException("Error getting mapper instance. Cause: " + e, e);
        }
      }
      

2.【mapperInterface.getClassLoader()】 类加载器
  【new Class[] { mapperInterface }】 class数组
  【mapperProxy】 	实现了invocationHandle接口的实现类
	protected T newInstance(MapperProxy<T> mapperProxy) {
    return (T) Proxy.newProxyInstance(mapperInterface.getClassLoader(), new Class[] { mapperInterface }, mapperProxy);
  }

  public T newInstance(SqlSession sqlSession) {
    final MapperProxy<T> mapperProxy = new MapperProxy<>(sqlSession, mapperInterface, methodCache);
    return newInstance(mapperProxy);
  }
  
3.追踪MapperProxy的invoke方法会发现，其最终调用了mapperMethod.execute(sqlSession, args)
  
4.进入execute方法会发现，最终工作的还是sqlSession

```

因此，代理对象时怎么产生的？

```
UserMapper mapper = sqlSession.getMapper(UserMapper.class);

//当前返回的	其实是基于UserMapper所产生的代理对象：底层：JDK动态代理	实际类型：proxy
```

底层就是基于JDK动态代理产生的代理对象，【代理对象调用接口中的任何方法时，底层的invoke方法都会执行】

```
User user = mapper.findUserById(3);
```

这里调用方法findUserById，实际执行的方法时底层的invoker方法



### invoker方法是怎么执行的



```
public Object invoke(Object proxy, Method method, Object[] args, SqlSession sqlSession) throws Throwable {  return mapperMethod.execute(sqlSession, args);}
```

```
public Object execute(SqlSession sqlSession, Object[] args) {
    Object result;
    switch (command.getType()) {
      case INSERT: {
        Object param = method.convertArgsToSqlCommandParam(args);
        result = rowCountResult(sqlSession.insert(command.getName(), param));
        break;
      }
      case UPDATE: {
        Object param = method.convertArgsToSqlCommandParam(args);
        result = rowCountResult(sqlSession.update(command.getName(), param));
        break;
      }
      case DELETE: {
        Object param = method.convertArgsToSqlCommandParam(args);
        result = rowCountResult(sqlSession.delete(command.getName(), param));
        break;
      }
      case SELECT:
      	if (method.returnsVoid() && method.hasResultHandler()) {
          executeWithResultHandler(sqlSession, args);
          result = null;
        } else if (method.returnsMany()) {
          result = executeForMany(sqlSession, args);
        } else if (method.returnsMap()) {
          result = executeForMap(sqlSession, args);
        } else if (method.returnsCursor()) {
          result = executeForCursor(sqlSession, args);
        } else {
          Object param = method.convertArgsToSqlCommandParam(args);
          result = sqlSession.selectOne(command.getName(), param);
          if (method.returnsOptional()
              && (result == null || !method.getReturnType().equals(result.getClass()))) {
            result = Optional.ofNullable(result);
          }
        }
        break;
```

invoker方法的内部还是去调用sqlSession的API方法完成增删改查

```
result = sqlSession.selectOne(command.getName(), param);
```

# Mybatis复杂映射&配置文件深入

## Mybatis高级查询

### ResutlMap属性：建立对象关系映射

* resultType 如果实体的属性名与表中字段名一致，将查询结果自动封装到实体类中 

* ResutlMap 如果实体的属性名与表中字段名不一致，可以使用ResutlMap实现手动封装到实体类中

  

```
<!--id:标签的唯一标识    
	type：封装后的实体类型
-->
<resultMap id="userResultMap" type="com.lagou.domain.User">    
    <!--手动配置映射关系-->    
    <!--id： 用来配置主键-->    
    <id property="id" column="id"></id>    
        <!--result：表中普通字段的封装        
            property:对应类中的字段    column：对应数据库表中的字段    
        -->    
        <result property="username" column="username"></result>    
        <result property="birthday" column="birthday"></result>    
        <result property="sex" column="sex"></result>    
        <result property="address" column="address">
    </result>
</resultMap>
```

### 多条件查询（三种）

**方式一**

使用 #{arg0}-#{argn} 或者 #{param1}-#{paramn} 获取参数

UserMapper接口

```
public List<User> findByIdAndUsername1(Integer id,String username);
```

UserMapper.xml

```
	<select id="findByIdAndUsername1" resultMap="userResultMap">
        <!--select * from user where id=#{arg0} and username= #{arg1}-->
        select * from user where id=#{param1} and username= #{param2}
    </select>
```

**方式二**

使用注解，引入 @Param() 注解获取参数

```
UserMapper.xml:

	<select id="findByIdAndUsername2" resultMap="userResultMap">
        select * from user where id=#{id} and username= #{username}
    </select>
    
    其中，#{}里面的值不是随意放的，而是必须在对应的接口中通过@Param("")设置的值对应
UserMapper接口

     public List<User> findByIdAndUsername2(@Param("id") Integer id,
     							@Param("username") String username);

```

**方式三（推荐）**

使用pojo对象传递参数,其中#{}中的参数值与对应的传入参数类型中的属性值保持一致，

​	既#{id},#{username},要与对应的User类中的id，username等属性一致

UserMapper接口

```
public List<User> findByIdAndUsername3(User user);
```

UserMapper.xml

```
<select id="findByIdAndUsername3" parameterType="user" resultMap="userResultMap">
      select * from user where id=#{id} and username= #{username}
</select>
```

### 模糊查询(两种方式)

**方式一、 #{}**

UserMapper接口

```
public List<User> findByUsername1(String Username);
```

UserMapper.xml

```
	<select id="findByUsername1" parameterType="user" resultMap="userResultMap">
        select * from user where username like #{username}
    </select>
```

**方式二、${}**

UserMapper接口

```
public List<User> findByUsername2(String Username);
```

UserMapper.xml

```
	 <!--不推荐使用，因为会出现sql注入问题-->
	<select id="findByUsername2" parameterType="String" resultMap="userResultMap">
        select * from user where username like '${username}'
    </select>
```

**两种方式的区别**

```
#{} :表示一个占位符号

​	通过 #{} 可以实现preparedStatement向占位符中设置值，自动进行java类型和jdbc类型转换，
	#{}可以有效防止sql注入。
​	#{} 可以接收简单类型值或pojo属性值。
​	如果parameterType传输单个简单类型值， #{} 括号中名称随便写

${} :表示拼接sql串

​	通过 ${} 可以将parameterType 传入的内容拼接在sql中且不进行jdbc类型转换，
	会出现sql注入问题。
​	${} 可以接收简单类型值或pojo属性值。
​	如果parameterType传输单个简单类型值， ${} 括号中只能是value。
```



## Mybatis映射文件深入

### 返回主键

**应用场景**

我们很多时候有这种需求，向数据库插入一条记录后，希望能立即拿到这条记录在数据库中的主键值。

**方式一、useGeneratedKeys**

UserMapper接口

```
	/*
    添加用户，返回主键，方式一
     */
    public void saveUser(User user);
```

UserMapper.xml

```
	<!--添加用户：获取返回主键，方式一-->
    <!--
        useGeneratedKeys:声明返回主键
        keyProperty：把返回主键的值，封装到实体中的哪那个属性上
    -->
    <insert id="saveUser" parameterType="user" useGeneratedKeys="true" keyProperty="id">
        insert into user(username,birthday,sex,address)
        values(#{username},#{birthday},#{sex},#{address})
    </insert>
```

测试类

```
	@Test
    public void saveUser1() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        User user = new User();
        user.setUsername("roce");
        user.setBirthday(new Date());
        user.setSex("女");
        user.setAddress("上海");

        System.out.println(user);
        //User{id=null, username='roce', birthday=Thu Nov 05 19:00:29 CST 2020, sex='女', 			address='上海'}
        mapper.saveUser(user);
        System.out.println(user);
        //User{id=14, username='roce', birthday=Thu Nov 05 19:00:29 CST 2020, sex='女', 				address='上海'}
        sqlSession.close();
    }
```

**方式二、selectKey**

UserMapper接口

```
public void saveUser2(User user);
```

UserMapper.xml

```
	<insert id="saveUser2" parameterType="user">
        <!--
            selectKey:适用范围更广，支持所有类型的数据库
            order="AFTER"：设置sql语句执行前（后），执行此语句
            keyColumn="id"：指定主键对应的列名
            keyProperty="id"：把返回主键的值，封装到实体中的那个属性上
            resultType="int"：指定主键类型
        -->
        <selectKey order="AFTER" keyColumn="id" keyProperty="id" resultType="int">
            select LAST_INSERT_ID()
        </selectKey>
        insert into user(username,birthday,sex,address)
        values(#{username},#{birthday},#{sex},#{address})
    </insert>
```

测试类同上

### 动态SQL

**应用场景**

当我们要根据不同的条件，来执行不同的sql语句的时候，需要用到动态sql。

#### **1.** 动态SQL之if标签

UserMapper接口

```
public List<User> findUserByIdAndUsername1(User user);
```

UserMapper.xml

```
	<!--if标签-->
    <select id="findUserByIdAndUsername1" parameterType="user" resultMap="userResultMap">
        <include refid="userTable"/>
        <!-- 
        where标签相当于 where 1=1，但是如果没有条件，就不会拼接where关键字
        -->
        <where>
            <if test="id != null">
                and id=#{id}
            </if>
            <if test="username != null">
                and username=#{username}
            </if>
        </where>
    </select>
```

测试

```
@Test
    public void tsetIf() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        User user = new User();
        user.setUsername("roce");
        user.setId(14);
        
        List<User> list = mapper.findUserByIdAndUsername1(user);
        for (User user1 : list) {
            System.out.println(user1);
        }
        sqlSession.close();
    }
```



#### 2.动态 **SQL** 之set标签

UserMapper接口

```
public void updateUserSet(User user);
```

UserMapper.xml

```
	<!-- set标签在更新的时候，自动加上set关键字，然后去掉最后一个条件的逗号 -->
    <update id="updateUserSet" parameterType="user">
        update user
        <set>
            <if test="username != null">
                username = #{username},
            </if>
            <if test="birthday != null">
                birthday = #{birthday},
            </if>
            <if test="sex != null">
                sex = #{sex},
            </if>
            <if test="address != null">
                address = #{address},
            </if>
        </set>
        where id = #{id}
    </update>
```

测试

```
	@Test
    public void tsetSet() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        User user = new User();
        user.setUsername("roce");
        user.setId(12);

        mapper.updateUserSet(user);
        sqlSession.close();
    }
```



#### 3.**动态** **SQL** **之**foreach标签

**foreach****主要是用来做数据的循环遍历**

UserMapper接口

```
public List<User> findByList(List<Integer> ids);
```

UserMapper.xml

```
 	<!-- 如果查询条件为普通类型 List集合，collection属性值为：collection 或者 list
         sql语句为：select * from user where id in (1,2,3)
     -->
    <select id="findByList" parameterType="collection" resultMap="userResultMap">
        <include refid="userTable"/>
        <where>
            <!--
                List集合，collection属性值为：collection 或者 list
                open:代表语句开始部分
                close：代表语句的结束部分
                separator：分隔符
            -->
            <foreach collection="collection" open="id in (" close=")" item="id" separator=",">
                #{id}
            </foreach>
        </where>
    </select>
```

测试类

```
	@Test
    public void tsetForeach() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);

        List<User> users = mapper.findByList(list);
        for (User user : users) {
            System.out.println(user);
        }
        sqlSession.close();
    }
```



### SQL片段

**应用场景**

映射文件中可将重复的 sql 提取出来，使用时用 include 引用即可，最终达到 sql 重用的目的

UserMapper.xml

```
	<sql id="userTable">
        select * from user
    </sql>
    
    
    <!--引入sql片段--> 
    <include refid="selectUser"></include>
```



## Mybatis核心配置文件深入

### plugins标签

MyBatis可以使用第三方的插件来对功能进行扩展，分页助手PageHelper是将分页的复杂操作进行封

装，使用简单的方式即可获得分页的相关数据

开发步骤：

①导入通用PageHelper的坐标

```
<!-- 分页助手 --> 
<dependency> 
	<groupId>com.github.pagehelper</groupId> 
	<artifactId>pagehelper</artifactId> 
	<version>3.7.5</version> 
</dependency> 
<dependency> 
	<groupId>com.github.jsqlparser</groupId> 
	<artifactId>jsqlparser</artifactId> 
	<version>0.9.1</version> 
</dependency>
```

②在mybatis核心配置文件中配置PageHelper插件

```
	<plugins>
        <!-- 分页助手的插件 -->
        <plugin interceptor="com.github.pagehelper.PageHelper">
            <!-- 指定方言 -->
            <property name="dialect" value="mysql"/>
        </plugin>
    </plugins>
    
    <plugins>标签只能在<environments>标签之前
```

③测试分页数据获取

```
	/**
     * 测试plugins标签
     * @throws IOException
     */
    @Test
    public void tsetPlugins() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        //设置分页参数
        //参数一：当前页
        //参数二：每页显示的条数
        for (int i = 1;i <= 6 ;i++) {
            System.out.println("当前第----------------------"+i+"页");
            PageHelper.startPage(i,2);
            List<User> users = mapper.findAll();
            for (User user : users) {
                System.out.println(user);
            }
        }
        sqlSession.close();
    }
```

**获得分页相关的其他参数**

```
//其他分页的数据 
PageInfo<User> pageInfo = new PageInfo<User>(select);
System.out.println("总条数："+pageInfo.getTotal()); 
System.out.println("总页数："+pageInfo.getPages()); 
System.out.println("当前页："+pageInfo.getPageNum()); 
System.out.println("每页显示长度："+pageInfo.getPageSize()); 
System.out.println("是否第一页："+pageInfo.isIsFirstPage()); 
System.out.println("是否最后一页："+pageInfo.isIsLastPage());
```

### 知识小结

MyBatis核心配置文件常用标签：

1、properties标签：该标签可以加载外部的properties文件

2、typeAliases标签：设置类型别名

3、environments标签：数据源环境配置标签

4、plugins标签：配置MyBatis的插件



# Mybatis多表查询

## 数据库表关系介绍

关系型数据库表关系分为

```
* 一对一 
* 一对多 
* 多对多
```

## 一对一（多对一）

**一对一查询模型**

用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户

一对一查询的需求：查询所有订单，与此同时查询出每个订单所属的用户

**一对一查询语句**

```
SELECT * FROM orders o LEFT JOIN USER u ON o.`uid`=u.`id`;
```

**代码实现**

Order实体

```
ublic class Orders {
    private Integer id;
    private Date ordertime;
    private double total;
    private  Integer uid;
    // 表示当前订单属于哪个用户
    private User user;
}
```

OrderMapper接口

```
public interface OrderMapper {
    /**
     * 一对一关联查询，查询所有订单，同时还要查询每个订单所属用户信息
     * @return
     */
    public List<Orders> findAllwithUser();
}
```

SqlMapConfig.xml映射

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!--加载properties文件-->
    <properties resource="jdbc.properties"></properties>

    <!--设置别名-->
    <typeAliases>
        <typeAlias type="com.lagou.domain.User" alias="user"></typeAlias>
        <typeAlias type="com.lagou.domain.Orders" alias="orders"></typeAlias>
    </typeAliases>
    
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>

    </environments>

    <!--加载映射配置-->
    <mappers>
        <!--<mapper resource="com/lagou/mapper/OrderMapper.xml" ></mapper>-->
        <package name="com.lagou.mapper"/>
    </mappers>
    
</configuration>
```

OrderMapper.xml映射

```
	<select id="findAllwithUser" /*resultType="com.lagou.domain.Orders"*/ >
        select * from orders o left join user u on o.uid = u.id
    </select>
    
    问题：此时mybatis不能通过自动封装来将数据封装到Orders类，因此要使用映射封装，将获取的数据封装到Orders类。
    因此，上述配置应该改为：
    
    <resultMap id="orderMap" type="com.lagou.domain.Orders">
        <id property="id" column="id"/>
        <result property="ordertime" column="ordertime"/>
        <result property="total" column="total"/>
        <result property="uid" column="uid"/>

        <!--
            association:在进行一对一关联查询配置时，使用association标签进行关联
            property="user" 要封装实体类的属性名
            javaType="com.lagou.domain.User"    要封装的实体类的属性类型
        -->
        <association property="user" javaType="com.lagou.domain.User">
            <id property="id" column="uid"/>
            <result property="username" column="username"/>
            <result property="birthday" column="birthday"/>
            <result property="sex" column="sex"/>
            <result property="address" column="address"/>
        </association>
    </resultMap>
    
    <select id="findAllwithUser" resultMap="orderMap">
        select * from orders o left join user u on o.uid = u.id
    </select>
```

测试代码

```
@Test
    public void test1() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        OrderMapper mapper = sqlSession.getMapper(OrderMapper.class);

        List<Orders> list = mapper.findAllwithUser();
        for (Orders orders : list) {
            System.out.println(orders);
        }
        sqlSession.close();
    }
```

##  一对多

**介绍**

**一对多查询模型**

用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户

一对多查询的需求：查询所有用户，与此同时查询出该用户具有的订单

**一对多查询语句**

```
SELECT *,o.id oid FROM USER u LEFT JOIN orders o ON u.`id` = o.`uid`;
```

**代码实现**

User实体

```
public class User {
    private Integer id;
    private String username;
    private Date birthday;
    private String sex;
    private String address;

    //表示多方关系：集合 ：代表了当前用户所具有的订单列表
    private List<Orders> orderList;
}
```

UserMapper接口

```
public interface UserMapper {
    /**
     * 一对多查询，查询所有用户，同时还要查询出每个用户所关联的订单
     */
    public List<User> findAllwithOrder();
}
```

UserMapper.xml映射

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.lagou.mapper.UserMapper">

    <resultMap id="userMap" type="com.lagou.domain.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="birthday" column="birthday"/>
        <result property="sex" column="sex"/>
        <result property="address" column="address"/>



        <!--
            collection:在进行一对多关联查询配置时，使用collection标签进行关联
            property="user" 要封装实体类的属性名
            ofType="com.lagou.domain.Orders"    要封装的实体类的属性类型
        -->
        <collection property="orderList" ofType="com.lagou.domain.Orders">
            <id property="id" column="oid"/>
            <!--
            	property:对应类中的字段
            	column：对应表中的字段
            -->
            <result property="ordertime" column="ordertime"/>
            <result property="total" column="total"/>
            <result property="uid" column="uid"/>
        </collection>
    </resultMap>

    <select id="findAllwithOrder" resultMap="userMap">
        SELECT u.*,o.id oid,o.ordertime,o.total,o.uid FROM USER u LEFT JOIN orders o ON u.`id` = o.`uid`;
    </select>
</mapper>
```

测试代码

```
@Test
    public void test2() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        List<User> list = mapper.findAllwithOrder();
        for (User user : list) {
            System.out.println(user);
        }
        sqlSession.close();
    }
```

## 多对多

**介绍**

**多对多查询的模型**

用户表和角色表的关系为，一个用户有多个角色，一个角色被多个用户使用

多对多查询的需求：查询所有用户同时查询出该用户的所有角色

**多对多查询语句**

```
SELECT u.*,r.id rid,r.rolename,r.roleDesc
FROM
	USER u -- 用户表 
	LEFT JOIN sys_user_role ur -- 左外连接中间表 
		ON u.`id` = ur.`userid` 
	LEFT JOIN sys_role r -- 左外连接中间表 
		ON ur.`roleid` = r.`id` ;
```

**User**和Role **实体**

```
public class User {
    private Integer id;
    private String username;
    private Date birthday;
    private String sex;
    private String address;

    //表示多方关系：集合 ：代表了当前用户所具有的订单列表
    private List<Orders> orderList;

    //表示多方关系：集合 ：代表了当前用户所具有的订单列表
    private List<Role> roleList;
}
public class Role {
    private Integer id;
    private String rolename;
    private String roleDesc;
}
```

**UserMapper**接口

```
	/**
     * 多对多查询，查询所有用户，同时还要查询出每个用户所关联的角色信息
     */
    public List<User> findAllwithRole();
```

**UserMapper.xml**映射

```
	<resultMap id="userRoleMap" type="com.lagou.domain.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="birthday" column="birthday"/>
        <result property="sex" column="sex"/>
        <result property="address" column="address"/>

        <collection property="roleList" ofType="com.lagou.domain.Role">
            <id property="id" column="rid"/>
            <result property="rolename" column="rolename"/>
            <result property="roleDesc" column="roleDesc"/>
        </collection>
    </resultMap>

    <select id="findAllwithRole" resultMap="userRoleMap">
        SELECT u.*,r.id rid,r.rolename,r.roleDesc
        FROM
            USER u
            LEFT JOIN sys_user_role ur
                ON u.`id` = ur.`userid`
            LEFT JOIN sys_role r 
                ON ur.`roleid` = r.`id` ;
    </select>
```

测试类

```
	@Test
    public void test3() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        List<User> list = mapper.findAllwithRole();
        for (User user : list) {
            System.out.println(user);
        }
        sqlSession.close();
    }
```

## 小结

**MyBatis**多表配置方式

```
* 多对一（一对一）配置：使用<resultMap>+<association>做配置 
* 一对多配置：使用<resultMap>+<collection>做配置 
* 多对多配置：使用<resultMap>+<collection>做配置 
* 多对多的配置跟一对多很相似，难度在于SQL语句的编写。
```



# MyBatis嵌套查询

**什么是嵌套查询**

嵌套查询就是将原来多表查询中的联合查询语句拆成单个表的查询，再使用mybatis的语法嵌套在一起。

##  一对一嵌套查询

**介绍**

需求：查询一个订单，与此同时查询出该订单所属的用户

**一对一查询语句**

```
-- 先查询订单 
SELECT * FROM orders; 

-- 再根据订单uid外键，查询用户 
SELECT * FROM `user` WHERE id = #{订单的uid};
```

```
		<!-- 问题，1.怎么去执行第二条sql语句
                2.如何执行第二条语句的时候，将uid作为参数进行传递
         -->
```

代码实现**

OrderMapper接口

```
public List<Orders> findAllwithUser2();
```

OrderMapper.xml

```
 <resultMap id="orderMap2" type="com.lagou.domain.Orders">
        <id property="id" column="id"/>
        <result property="ordertime" column="ordertime"/>
        <result property="total" column="total"/>
        <result property="uid" column="uid"/>
        <!-- 问题，1.怎么去执行第二条sql语句
                2.如何执行第二条语句的时候，将uid作为参数进行传递
         -->
        <!--
            通过select标签，绑定namespace.id,可以调用sql语句
            column="uid",将当前查询出来的uid字段作为参数传递
        -->
        <association property="user" javaType="com.lagou.domain.User" 	  							select="com.lagou.mapper.UserMapper.findById" column="uid">
        </association>
    </resultMap>
    <select id="findAllwithUser2" resultMap="orderMap2">
        select * from orders 
    </select>
```

UserMapper接口

```
	/**
     * 嵌套查询，根据id查询用户
     */
    public User findById(Integer id);
```

UserMapper.xml

```
	<!--根据id查询用户-->
    <select id="findById" resultType="com.lagou.domain.User" parameterType="int">
        select * from user where id =#{id}
    </select>
```

测试类

```
	@Test
    public void test4() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        OrderMapper mapper = sqlSession.getMapper(OrderMapper.class);

        List<Orders> list = mapper.findAllwithUser2();
        for (Orders order : list) {
            System.out.println(order);
        }
        sqlSession.close();
    }
```



##  一对多嵌套查询

**介绍**

需求：查询所有用户，与此同时查询出该用户具有的订单

**一对多查询语句**

```
-- 先查询用户 
SELECT * FROM `user`; 

-- 再根据用户id主键，查询订单列表 
SELECT * FROM orders where uid = #{用户id};
```

**代码实现**

UserMapper接口

```
	/**
     * 一对多嵌套查询，查询所有用户，同时还要查询出每个用户所关联的订单
     */
    public List<User> findAllwithOrder2();
```

UserMapper.xml

```
	<resultMap id="useOrderMap" type="com.lagou.domain.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="birthday" column="birthday"/>
        <result property="sex" column="sex"/>
        <result property="address" column="address"/>
        <collection property="orderList" ofType="com.lagou.domain.Orders"
                    column="id" select="com.lagou.mapper.OrderMapper.findByUid">

        </collection>

    </resultMap>
    <select id="findAllwithOrder2" resultMap="useOrderMap">
        select * from user
    </select>
```

OrderMapper接口

```
	/**
     * 根据uid查询对应订单
     */
    public List<Orders> findByUid(Integer uid);
```

OrderMapper.xml

```
	<select id="findByUid" parameterType="int" resultType="com.lagou.domain.Orders">
        select * from orders where uid = #{uid}
    </select>
```

测试类

```
	@Test
    public void test5() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        List<User> list = mapper.findAllwithOrder2();
        for (User user : list) {
            System.out.println(user);
        }
        sqlSession.close();
    }
```



## 多对多嵌套查询

**介绍**

需求：查询用户 同时查询出该用户的所有角色

**多对多查询语句**

```
-- 先查询用户 
SELECT * FROM `user`; 

-- 再根据用户id主键，查询角色列表 
SELECT * 
FROM sys_role r INNER JOIN sys_user_role ur 
	ON ur.roleid = r.id 
	WHERE ur.userid = #{uid}
```

**代码实现**

UserMapper接口

```
	/**
     * 多对多嵌套查询，查询用户，同时查询出该用户的所有角色
     */
    public List<User> findAllwithRole2();
```

UserMapper.xml

```
	<resultMap id="userRoleMap2" type="com.lagou.domain.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="birthday" column="birthday"/>
        <result property="sex" column="sex"/>
        <result property="address" column="address"/>
        <collection property="roleList" ofType="com.lagou.domain.Role"
                    column="id" select="com.lagou.mapper.RoleMapper.findByUid">

        </collection>
    </resultMap>
    <select id="findAllwithRole2" resultMap="userRoleMap2">
        select * from user
    </select>
```

RoleMapper接口

```
public interface RoleMapper {
    /**
     * 根据用户id查询对应角色
     */
    public List<Role> findByUid(Integer uid);
}
```

RoleMapper.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.lagou.mapper.RoleMapper">
    <select id="findByUid" resultType="com.lagou.domain.Role">
        SELECT * FROM sys_role r INNER JOIN sys_user_role ur ON ur.roleid = r.id
	        WHERE ur.userid = #{uid}
    </select>
</mapper>
```

测试类

```

@Test
    public void test6() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        List<User> list = mapper.findAllwithRole2();
        for (User user : list) {
            System.out.println(user);
        }
        sqlSession.close();
    }
```





# Mybatis任务三：加载策略及注解开发

## **一** MyBatis加载策略

举例

```
* 在一对多中，当我们有一个用户，它有个100个订单 
	在查询用户的时候，要不要把关联的订单查出来？ 
	在查询订单的时候，要不要把关联的用户查出来？ 
	
* 回答
	在查询用户时，用户下的订单应该是，什么时候用，什么时候查询。 
	在查询订单时，订单所属的用户信息应该是随着订单一起查询出来。
```

**延迟加载**

就是在需要用到数据时才进行加载，不需要用到数据时就不加载数据。延迟加载也称懒加载。

```
* 优点：
	先从单表查询，需要时再从关联表去关联查询，大大提高数据库性能，
	因为查询单表要比关联查询多张表速度要快。 
	
* 缺点：
	因为只有当需要用到数据时，才会进行数据库查询，这样在大批量数据查询时，因为查询工作也要消耗时间，
	所以可能造成用户等待时间变长，造成用户体验下降。 
	
* 在多表中： 
	一对多，多对多：通常情况下采用延迟加载 
	一对一（多对一）：通常情况下采用立即加载 
	
* 注意：延迟加载是基于嵌套查询来实现的
```

**实现**

### **局部延迟加载**

在association和collection标签中都有一个fetchType属性，通过修改它的值，可以修改局部的加载策略。

UserMapper.xml

```
	<resultMap id="useOrderMap" type="com.lagou.domain.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="birthday" column="birthday"/>
        <result property="sex" column="sex"/>
        <result property="address" column="address"/>
        <!--
            fetchType="lazy" :延迟加载策略
            fetchType="eager" ： 立即加载策略
        -->
        <collection property="orderList" ofType="com.lagou.domain.Orders"
                    column="id" select="com.lagou.mapper.OrderMapper.findByUid"
          fetchType="lazy">
        </collection>
    </resultMap>
    <select id="findAllwithOrder2" resultMap="useOrderMap">
        select * from user
    </select>
```

测试类

```
	@Test
    public void test5() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        List<User> list = mapper.findAllwithOrder2();
        for (User user : list) {
            System.out.println(user);

            //要用到该用户的订单信息
            System.out.println(user.getOrderList());
            //因为这里需要用到OrderList，所以会进行加载，查询相关联的订单信息
        }
        sqlSession.close();
    }
```

**设置触发延迟加载的方法**

大家在配置了延迟加载策略后，发现即使没有调用关联对象的任何方法，但是在你调用当前对象的

equals、clone、hashCode、toString方法时也会触发关联对象的查询。

我们可以在SqlMapConfig.xml配置文件中使用lazyLoadTriggerMethods配置项覆盖掉上面四个方法。

```
<!-- settings标签 放在properties标签下面-->
<settings> 
	<!--所有方法都会延迟加载--> 
	<setting name="lazyLoadTriggerMethods" value="toString()"/> 
</settings>
```



### **全局延迟加载**

在Mybatis的核心配置文件中可以使用setting标签修改全局的加载策略。

```
<settings> 
	<!--开启全局延迟加载功能--> 
	<setting name="lazyLoadingEnabled" value="true"/> 
</settings>
```

**注意**

局部的加载策略优先级高于全局的加载策略。

```
<!-- 关闭一对一 延迟加载 -->
注意：使用全局延迟加载时，要将一对一局部延迟加载关闭
```



## 二 MyBatis缓存

**为什么使用缓存？**

```
当用户频繁查询某些固定的数据时,第一次将这些数据从数据库中查询出来,保存在缓存中。当用户再次查询这些数据时,不用再通过数据库查询,而是去缓存里面查询。减少网络连接和数据库查询带来的损耗,从而提高我们的查询效率,减少高并发访问带来的系统性能问题。

**一句话概括：**经常查询一些不经常发生变化的数据，使用缓存来提高查询效率。

像大多数的持久化框架一样，Mybatis也提供了缓存策略，通过缓存策略来减少数据库的查询次数，从而提高性能。 Mybatis中缓存分为一级缓存，二级缓存。
```

###  一级缓存

**介绍**

```
一级缓存是SqlSession级别的缓存，是默认开启的

所以在参数和SQL完全一样的情况下，我们使用同一个SqlSession对象调用一个Mapper方法，往往只执行一次SQL，因为使用SelSession第一次查询后，MyBatis会将其放在缓存中，以后再查询的时候，如果没有声明需要刷新，并且缓存没有超时的情况下，SqlSession都会取出当前缓存的数据，而不会再次发送SQL到数据库。
	
	查询了两次，但最后只执行了一次数据库操作，这就是Mybatis提供给我们的一级缓存在起作用了。因为一级缓存的存在，导致第二次查询id为1的记录时，并没有发出sql语句从数据库中查询数据，而是从一级缓存中查询。
```

**分析**

```
一级缓存是SqlSession范围的缓存，执行SqlSession的C（增加）U（更新）D（删除）操作，或者调
用clearCache()、commit()、close()方法，都会清空缓存。
```

```
1. 第一次发起查询用户id为1的用户信息，先去找缓存中是否有id为1的用户信息，如果没有，从数据库 查询用户信息。 2. 得到用户信息，将用户信息存储到一级缓存中。 
3. 如果sqlSession去执行commit操作（执行插入、更新、删除），清空SqlSession中的一级缓存，这 样做的目的为了让缓存中存储的是最新的信息，避免脏读。 
4. 第二次发起查询用户id为1的用户信息，先去找缓存中是否有id为1的用户信息，缓存中有，直接从缓存中获取用户信息。
```

**清除**

```
@Test 
public void testClearOneCache() throws Exception { 
	SqlSession sqlSession = MybatisUtils.openSession(); 
	UserMapper userMapper = sqlSession.getMapper(UserMapper.class); 
	User user1 = userMapper.findById(41); 
	System.out.println("第一次查询的用户：" + user1); //调用sqlSession清除缓存的方法 				sqlSession.clearCache(); 
	User user2 = userMapper.findById(41); 
	System.out.println("第二次查询的用户：" + user2); 
}
```

```
也可以在映射文件中设置 select标签中的flushCache属性


<!-- 每次查询时，都会清除缓存 --> 
< select flushCache="true"></select>
```

### **二级缓存**

**介绍**

```
二级缓存是namspace级别（跨sqlSession）的缓存，是默认不开启的
二级缓存的开启需要进行配置，实现二级缓存的时候，MyBatis要求返回的POJO必须是可序列化的。

也就是要求实现Serializable接口，配置方法很简单，只需要在映射XML文件配置 <cache/> 就可以开启二级缓存了。
```

**验证**

**配置核心配置文件**

```
<settings> 
	<!--因为cacheEnabled的取值默认就为true，所以这一步可以省略不配置。 
		为true代表开启二级缓存；为false代表不开启二级缓存。 --> 
	<setting name="cacheEnabled" value="true"/> 
</settings>
```

配置UserMapper.xml （以这个映射配置文件为例）

```
<mapper namespace="com.lagou.dao.UserMapper"> 
	<!--当前映射文件开启二级缓存--> 
	<cache></cache> 
	
	<!--<select>标签中设置useCache=”true”代表当前这个statement要使用二级缓存。 
		如果不使用二级缓存可以设置为false 
		注意：针对每次查询都需要最新的数据sql，要设置成useCache="false"，禁用二级缓存。 
	--> 
	<select id="findById" parameterType="int" resultType="user" useCache="true" > 
		SELECT * FROM `user` where id = #{id} 
	</select> 
</mapper>
```

同时，实体类需要进行序列化

```
public class User implements Serializable {。。。}
```

测试类

```
	@Test
    public void testTwoCache() throws Exception {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = 
        	new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession1 = sqlSessionFactory.openSession();
        UserMapper userMapper1 = sqlSession1.getMapper(UserMapper.class);

        User user1 = userMapper1.findById(1);
        System.out.println("第一次查询的用户：" + user1);

        //只有执行了sqlSession.commit或者sqlSession1.close()那么一级缓存中的信息才会刷新到二级缓存
        sqlSession1.close();

        SqlSession sqlSession2 = sqlSessionFactory.openSession();
        UserMapper userMapper2 = sqlSession2.getMapper(UserMapper.class);

        User user2 = userMapper2.findById(41);
        System.out.println("第二次查询的用户："+user2);

        sqlSession2.close(); 
    }
```

**注意问题（脏读）**

mybatis的二级缓存因为是namespace级别，所以在进行多表查询时会产生脏读问题

# MyBatis注解

## MyBatis常用注解

这几年来注解开发越来越流行，Mybatis也可以使用注解开发方式，这样我们就可以减少编写

Mapper映射文件了。我们先围绕一些基本的CRUD来学习，再学习复杂映射多表操作。

```
* @Insert：实现新增，代替了<insert></insert>
* @Delete：实现删除，代替了<delete></delete> 

* @Update：实现更新，代替了<update></update> 
* @Select：实现查询，代替了<select></select> 

* @Result：实现结果集封装，代替了<result></result> 
* @Results：可以与@Result 一起使用，封装多个结果集，代替了<resultMap></resultMap> 
* @One：实现一对一结果集封装，代替了<association></association> 
* @Many：实现一对多结果集封装，代替了<collection></collection>
```

## MyBatis注解的增删改查【重点】

示例

```
public interface UserMapper {
    /**
     * 查询所有用户
     */
    @Select("select * from user")
    public List<User> findAll();

    /**
     * 添加用户
     * @param user
     */
    @Insert("insert into user(username,birthday,sex,address) values(#{username},#{birthday},#{sex},#{address}) ")
    public void save(User user);

    /**
     * 更新用户
     * @param user
     */
    @Update("update user set username = #{username} where id = #{id}")
    public void update(User user);

    /**
     * 删除用户
     * @param id
     */
    @Delete("delete from user where id = #{id}")
    public void delete(Integer id);
}
```

测试代码

```
public class MybatisTest {
    private SqlSessionFactory sqlSessionFactory;
    private SqlSession sqlSession;

    /**
     * @Before  在@Test方法标注的方法之前执行
     */
    @Before
    public void before() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        sqlSession = sqlSessionFactory.openSession(true);
    }

    @After
    public void after(){
        sqlSession.close();
    }
    @Test
    public void test1() throws IOException {

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> list = mapper.findAll();
        for (User user : list) {
            System.out.println(user);
        }
    }
    @Test
    public void test2() throws IOException {

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = new User();
        user.setUsername("hello");
        user.setBirthday(new Date());
        user.setSex("中");
        user.setAddress("helloworld");
        mapper.save(user);

    }
}
```

## 使用注解实现复杂映射开发

之前我们在映射文件中通过配置 <resultMap>、<association>、<collection> 来实现复杂关系映射。

使用注解开发后，我们可以使用 @Results、@Result，@One、@Many 注解组合完成复杂关系的配置。

| 注解             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| @Results         | 代替的是标签【resultMap】，该注解中可以使用单个@Result注解，也可以使用@Result集合。<br />使用格式：@Results({@Result(),@Result(),...})或者@Results(@Result()) |
| @Result          | 代替了【id】标签和【result】标签<br />@Result中属性介绍：<br />column：数据库的字段名<br />property：需要装配的属性名<br />one：需要使用的@One注解，代替了【assocation】标签<br />many：需要使用的@Many注解，代替了【collection】标签 |
| @one------一对一 | 代替了【assocation】标签，在注解中用来指定子查询返回单一对象。<br />@One注解属性介绍：<br />@Result(property = "",column="",one=@One(select="namespace.id"),fetchType="")<br />select：用来指定多表查询的sqlMapper<br />使用格式：<br />例子：@Result(property = "user",javaType = User.class,column = "uid",  <br />      one = @One(select = "com.lagou.mapper.UserMapper.findById",<br />      fetchType = FetchType.EAGER)) |
| @Many-----一对多 | 代替了【collection】标签，在注解中用来指定子查询返回对象集合<br />使用格式：<br />@Result(property = "",column="",many=@Many(select="namespace.id")) |

####  **注解延迟加载**

```
* fetchType = FetchType.LAZY 表示懒加载 
* fetchType = FetchType.EAGER 表示立即加载 
* fetchType = FetchType.DEFAULT 表示使用全局配置
```

