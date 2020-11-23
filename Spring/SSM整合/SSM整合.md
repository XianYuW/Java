# SSM整合

## 1.1需求和步骤分析

**需求**

使用ssm框架完成对 account 表的增删改查操作。

**步骤分析**

```
1. 准备数据库和表记录 
2. 创建web项目 
3. 编写mybatis在ssm环境中可以单独使用 
4. 编写spring在ssm环境中可以单独使用 
5. spring整合mybatis 
6. 编写springMVC在ssm环境中可以单独使用 
7. spring整合springMVC
```

## 1.2环境搭建

**1）准备数据库和表记录**

**2）创建web项目** 

## 1.3编写mybatis在ssm环境中可以单独使用

导入相关坐标

```
 	<!-- 设置为web工程 -->
    <packaging>war</packaging>

    <!--指定编码及版本-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
        <java.version>1.11</java.version>
        <maven.compiler.source>1.11</maven.compiler.source>
        <maven.compiler.target>1.11</maven.compiler.target>
    </properties>

    <dependencies>

        <!--mybatis坐标-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.15</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.1</version>
        </dependency>
    </dependencies>
```

account实体

```
public class Account {
    private Integer id;
    private String name;
    private Double money;
    
}
```

AccountDao接口

```
public interface AccountDao {
    public List<Account> findAll();
    
    public Account findById(@Param("id") Integer id);
}
```

AccountDao.xml映射

映射配置文件这里要注意：XxxDao.xml文件要与XxxDao.java同包同名，否则会报错

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.lagou.dao.AccountDao">
    <select id="findAll" resultType="account">
        select * from account
    </select>
    
    <select id="findById" parameterType="int" resultType="account">
        <!--select * from account where id = #{param1}-->
        select * from account where id = #{id}
    </select>
</mapper>
```

**mybatis核心配置文件**

jdbc.properties

```
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql:///spring_db?characterEncoding=utf-8&useSSL=false
jdbc.username=root
jdbc.password=123456
```

SqlMapConfig.xml

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
        <package name="com.lagou.domain"/>
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
        <package name="com.lagou.dao"/>
    </mappers>

</configuration>
```

测试代码

```
public class MybatisTest {
    private InputStream inputStream = null;
    private SqlSessionFactory sqlSessionFactory = null;
    private SqlSession sqlSession = null;
    @Before
    public void before() throws IOException {
        inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        sqlSession = sqlSessionFactory.openSession();

    }

    @After
    public void after(){
        sqlSession.close();
    }

    @Test
    public void test() throws IOException {
        AccountDao mapper = sqlSession.getMapper(AccountDao.class);
        List<Account> list = mapper.findAll();
        for (Account account : list) {
            System.out.println(account);
        }
    }
    
    @Test
    public void test2() throws IOException {
        AccountDao mapper = sqlSession.getMapper(AccountDao.class);
        Account account = mapper.findById(1);
        System.out.println(account);
    }
}
```



## 1.4编写spring在ssm环境中可以单独使用

相关坐标

```
		<!--spring坐标-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <dependency><!-- aspectj的织入（切点表达式需要用到该jar包） -->
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.13</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <!--spring整合junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
```

AccountService接口

```
public interface AccountService {
    public List<Account> findAll();

    public Account findById(@Param("id") Integer id);
}
```

AccountServiceImpl实现类

```
@Service("accountService")
public class AccountServiceImpl implements AccountService {

    @Autowired
    private AccountDao accountDao;
    @Override
    public List<Account> findAll() {
        System.out.println("findAll方法执行了。。。");
        return accountDao.findAll();
    }

    @Override
    public Account findById(Integer id) {
        System.out.println("findById方法执行了。。。");
        return accountDao.findById(id);
    }
}
```

Spring核心配置文件

applicationContext.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation=" http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">

    <context:component-scan base-package="com.lagou.service"></context:component-scan>
    <context:component-scan base-package="com.lagou.dao"></context:component-scan>

</beans>
```

测试代码

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration({"classpath:applicationContext.xml"})
public class SpringTest {

    @Autowired
    private AccountService accountService;

    @Test
    public void test(){
        List<Account> list = accountService.findAll();

    }
}
```

## 1.5 spring整合mybatis

### 为什么不能直接加注解？

```
此时，AccountSeerviceImpl实现类中需要用到AccountDao的代理对象，但是此时自动注入会失败；

因为对于代理对象是通过mybatisAPI获取到的
AccountDao mapper = sqlSession.getMapper(AccountDao.class);
这个代理对象mapper根本没有在IOC容器中。
既然没有在IOC容器中，那么怎么能自动注入呢？
因此现在就要将代理对象的创建交给Spring，让spring去负责创建代理对象，并且将代理对象加入到IOC容器中
```

### **整合思想**

```
将mybatis接口代理对象的创建权交给spring管理，我们就可以把dao的代理对象注入到service中，
此时也就完成了spring与mybatis的整合了。
```

### 导入整合包

```
<!--mybatis整合spring坐标--> 
<dependency> 
	<groupId>org.mybatis</groupId> 
	<artifactId>mybatis-spring</artifactId> 
	<version>1.3.1</version> 
</dependency>
```

### **编写applicationContext.xml文件，让spring配置文件管理mybatis**

注意：此时可以将mybatis主配置文件删除。

```
	<!--spring整合mybatis-->

    <!--3.引入jdbc配置-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--2.DataSource-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"></property>
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>

    <!--1.声明SqlSessionFactoryBean,将sqlSessionFactory的创建权交给spring，生成sqlSesson
        然后发现要设置DataSource-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--批量设置别名-->
        <property name="typeAliasesPackage" value="com.lagou.domain"/>
        <!--引入加载mybatis核心配置文件：sqlMapConfig.xml-->
        <property name="configLocation" value="classpath:SqlMapConfig.xml"/>
    </bean>

    <!--4.配置mapper映射扫描
        作用：对包下的所有接口进行扫描，生成该接口的代理对象，存到IOC容器中
    -->
    <bean  class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.lagou.dao"/>
    </bean>
```

整合之后service接口实现类就可以实现dao层的自动注入了，改动略

### 总结

```
SqlSessionFactory是贤臣不安全的，因此要配置成单例的
数据库的连接以及数据库连接池事务管理交给spring来完成
spring的配置文件中要借助MapperScannerConfigurer配置完成对dao接口的扫描，这样才能生成代理对象存入IOC容器中
```



## 1.6编写springMVC在ssm环境中可以单独使用

引入坐标

```
		<!--springMVC坐标-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
```

**2）导入页面资源**

js,css,img等图片资源，略

**3）前端控制器DispathcerServlet**

```
	<!--前端控制器-->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>2</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--中文乱码过滤器：解决post方式提交的乱码-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>
        	org.springframework.web.filter.CharacterEncodingFilter
        </filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

**load-on-startup的作用**

```
<load-on-startup>2</load-on-startup>的作用
1)load-on-startup元素标记容器是否在启动的时候就加载这个servlet(实例化并调用其init()方法)。
2)它的值必须是一个整数，表示servlet应该被载入的顺序
3)当值为0或者大于0时，表示容器在应用启动时就加载并初始化这个servlet；
4)当值小于0或者没有指定时，则表示容器在该servlet被选择时才会去加载。
5)正数的值越小，该servlet的优先级越高，应用启动时就越先加载。
6)当值相同时，容器就会自己选择顺序来加载。
```

/*和/的区别

```
/ 拦截所有请求 包括静态资源和动态请求 但是不拦截jsp
/*拦截所有请求 包括静态资源和动态请求 也拦截jsp
区别就在于/ 不拦截jsp /*拦截jsp

/**的意思是所有文件夹及里面的子文件夹
/*是所有文件夹，不含子文件夹

注意点案例
理解下面的案例有助于理解如何使用

场景：当我在客户端调用URL：/user/list然后返回user.jsp视图，

当配置的是/：DispathcherServlet拿到这个请求然后返回对应的controller，
然后依据Dispather Type为Forward类型转发到user.jsp视图，即就是请求user.jsp视图(/user/user.jsp)，此时Dispather没有拦截/user/user.jsp，
因为此时你配置的是默认的/，就顺利的交给ModleAndView去处理显示了。
当配置的是/*：DispathcherServlet拿到这个请求然后返回对应的controller，然后通过Dispather Type通过Forward转发到user.jsp视图，
即就是请求user.jsp视图(/user/user.jsp)，此时Dispather已经拦截/user/user.jsp，Dispatcher会把他当作Controller去匹配，没有匹配到就会报404错误。
结论：在配置视图的时候尽量用/这种方式。

< url-pattern>/</url-pattern>  
	会匹配到/login这样的路径型url，不会匹配到模式为*.jsp这样的后缀型url
	
< url-pattern>/*</url-pattern> 
	会匹配所有url：路径型的和后缀型的url(包括/login,*.jsp,*.js和*.html等)
```

控制器编写

AccountController

```
@Controller
@RequestMapping("/account")
public class AccountController {
    /**
     * 查询所有用户
     */
    @RequestMapping("/findAll")
    public String findAll(Model model){
        //实现查询所有账户
        ArrayList<Account> list = new ArrayList<>();
        Account account1 = new Account();
        account1.setId(1);
        account1.setName("迪丽热巴");
        account1.setMoney(1000d);
        list.add(account1);

        Account account2 = new Account();
        account2.setId(2);
        account2.setName("古力娜扎");
        account2.setMoney(900d);
        list.add(account2);

        //将封装好的list集合存到model中
        model.addAttribute("list",list);
        return "list";
    }
}
```

 **list.jsp**

```
<c:forEach items="${list}" var="account">

              <tr>
                  <td>
                      <input type="checkbox" name="ids" value="${account.id}">
                  </td>
                  <td>${account.id}</td>
                  <td>${account.name}</td>
                  <td>${account.money}</td>
                  <td>
                  	<a class="btn btn-default btn-sm"
          	 href="${pageContext.request.contextPath}/account/findById?id=${account.id}">				修改</a>&nbsp;
          	 		<a class="btn btn-default btn-sm" href="">删除</a></td>
              </tr>
              </c:forEach>
```

springMVC核心配置文件

```
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    <!--1.组件扫描-->
    <!--mvc注解增强：处理器映射器以及处理器适配器-->
    <!--3.视图解析器-->
    <!--4.放行静态资源-->
</beans>
```

```
	<!--1.组件扫描-->
    <context:component-scan base-package="com.lagou.controller"></context:component-scan>

    <!--mvc注解增强：处理器映射器以及处理器适配器-->
    <mvc:annotation-driven></mvc:annotation-driven>

    <!--3.视图解析器,配置前缀和后缀-->
    <bean id="resourceViewResolver"
    class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/"></property>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--4.放行静态资源-->
    <mvc:default-servlet-handler></mvc:default-servlet-handler>
```





## 1.7 spring整合springMVC

### 1）整合思想

spring和springMVC其实根本就不用整合，本来就是一家。

但是我们需要做到spring和web容器整合，让web容器启动的时候自动加载spring配置文件，web容

器销毁的时候spring的ioc容器也销毁。

### 2）spring和web容器整合

ContextLoaderListener加载【掌握】

可以使用spring-web包中的ContextLoaderListener监听器，可以监听servletContext容器的创建和

销毁，来同时创建或销毁IOC容器。

```
	<!--配置spring的监听器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener
        </listener-class>
    </listener>
    <!--配置全局参数,借助监听器启动tomcat的时候将applicationContext.xml加载，构建IOC容器-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
```



### 3）修改AccountController

```
@Controller
@RequestMapping("/account")
public class AccountController {

    @Autowired
    private AccountService accountService;

    /**
     * 查询所有用户
     */
    @RequestMapping("/findAll")
    public String findAll(Model model){
        //实现查询所有账户
        List<Account> list = accountService.findAll();

        //将封装好的list集合存到model中
        model.addAttribute("list",list);
        return "list";
    }
}
```

### 总结

```
spring和springMVC可以进行无缝整合
springMVC和spring存在父子容器关系
web.xml中配置contextLoaderListener是为了时监听到servletContext对象的创建，从而读取spring的配置文件
```



## 1.8 spring配置声明式事务

### 1）spring配置文件加入声明式事务

```
	<!--spring的声明式事务管理-->
    <!--1.事务管理器-->
    <bean id="transactionManager"
    class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
```

实现类加上@Transactional注解

```
@Service("accountService")
@Transactional
public class AccountServiceImpl implements AccountService {}
```



### 2）add.jsp

```
<form action="${pageContext.request.contextPath}/account/save" method="post">
                <div class="form-group">
                    <label for="name">姓名：</label>
                    <input type="text" class="form-control" 
                    id="name" name="name" placeholder="请输入姓名">
                </div>
                      <div class="form-group">
                    <label for="money">余额：</label>
                    <input type="text" class="form-control" 
                    id="money" name="money" placeholder="请输入余额">
                </div>

                <div class="form-group" style="text-align: center">
                    <input class="btn btn-primary" type="submit" value="提交" />
                    <input class="btn btn-default" type="reset" value="重置" />
                    <input class="btn btn-default" type="button" 
                    onclick="history.go(-1)" value="返回" />
                </div>
            </form>
```



### 3）AccountController

```
	 /**
     * 增加用户
     */
    @RequestMapping("/save")
    public String save(Account account){
        accountService.save(account);

        return "redirect:/account/findAll";
    }
```



### 4）AccountService接口和实现类

```
public interface AccountService {
    public List<Account> findAll();

    public Account findById(@Param("id") Integer id);

    public void save(Account account);
}
```

```
	@Override
    public void save(Account account) {
        accountDao.save(account);
    }
```



### 5）AccountDao

```
public void save(Account account);
```



### 6）AccountDao.xml映射

```
	<!--添加账户-->
    <select id="save" parameterType="account">
        insert into account values(null,#{name},#{money})
    </select>	
```



## 1.9修改操作

### 1.9.1数据回显

**①AccountController**

```
	/**
     * 根据id查询账户信息，完成账户回显
     */
    @RequestMapping("/findById")
    public String findById(Integer id,Model model){
        Account account = accountService.findById(id);

        //存到model中
        model.addAttribute("account",account);

        //视图跳转
        return "update";
    }
```

**②AccountService接口和实现类**

```
public interface AccountService {
    public List<Account> findAll();

    public Account findById(@Param("id") Integer id);

    public void save(Account account);
}
```

​	实现类的方法

```
	@Override
    public Account findById(Integer id) {
        System.out.println("findById方法执行了。。。");
        return accountDao.findById(id);
    }
```

**③AccountDao接口和映射文件**

```

public interface AccountDao {
    public List<Account> findAll();

    public Account findById(@Param("id") Integer id);

    public void save(Account account);
}
```

AccountDao.xml

```
    <select id="findById" parameterType="int" resultType="account">
        <!--select * from account where id = #{param1}-->
        select * from account where id = #{id}
    </select>
```

**④update.jsp**

```
<form action="${pageContext.request.contextPath}/account/update" method="post">
                <input type="hidden" name="id" value="${account.id}">
                <div class="form-group">
                    <label for="name">姓名：</label>
                    <input type="text" class="form-control" id="name" 
                    name="name" value="${account.name}" placeholder="请输入姓名">
                </div>
                <div class="form-group">
                    <label for="money">余额：</label>
                    <input type="text" class="form-control" id="money" 
                    name="money"  value="${account.money}" placeholder="请输入余额">
                </div>

                <div class="form-group" style="text-align: center">
                    <input class="btn btn-primary" type="submit" value="提交" />
                    <input class="btn btn-default" type="reset" value="重置" />
                    <input class="btn btn-default" type="button" 
                    onclick="history.go(-1)" value="返回" />
                </div>
            </form>
```



### 1.9.2账户更新

**①AccountController**

```
 	/**
     * 更新账户
     */
    @RequestMapping("/update")
    public String update(Account account){
       accountService.update(account);
        //视图跳转
        return "redirect:/account/findAll";
    }
```

**②AccountService接口和实现类**

```
添加方法：
public void update(Account account);
```

实现类

```
添加方法：
	@Override
    public void update(Account account) {
        accountDao.update(account);
    }
```

**③AccountDao接口和映射文件**

```
添加方法：
	public void update(Account account);
```

AccountDao.xml

```
	<!--更新账户-->
    <select id="update" parameterType="account">
        update account set name = #{name},money = #{money} where id = #{id}
    </select>	
```



## 1.10批量删除

**1）list.jsp**

实现全选/全不选的效果

```
			<input type="checkbox" id="checkAll">
            <%--实现全选/全不选效果--%>
            <script>
            $('#checkAll').click(function () {
            	$('input[name="ids"]').
            	prop('checked',$(this).prop('checked'));
            })
            </script>
            
            <tr>
            	<td>
            		<input type="checkbox" name="ids">
            	</td>
            	...
            </tr>
```

实现删除选中的数据

```
<input class="btn btn-primary" type="button" value="删除选中" id="deleteBatchBtn">
 
<script>
    /*给删除选中按钮绑定点击事件*/
    $('#deleteBatchBtn').click(function () {
            if(confirm('您确定要删除吗')){
                if($('input[name=ids]:checked').length > 0){
                    /*提交表单*/
                    $('#deleteBatchForm').submit();
                }
            }else {
                alert('想啥呢，没事瞎操作啥')
            }
    })

</script>
```



**2）AccountController**

增加delete的controller方法

```
	/**
     * 批量删除
     */
    @RequestMapping("/deleteBatch")
    public String deleteBatch(Integer[] ids){
        accountService.deleteBatch(ids);

        return "redirect:/account/findAll";
    }
```

**3）AccountService接口和实现类**

```
public void deleteBatch(Integer[] ids);
```

```
	@Override
    public void deleteBatch(Integer[] ids) {
        accountDao.deleteBatch(ids);
    }
```



**4）AccountDao接口和映射文件**

```
public void deleteBatch(Integer[] ids);
```

```
	<!--批量删除-->
    <select id="deleteBatch" parameterType="int">
        delete from account
        <where>
            <foreach collection="array" open="id in (" close=")" separator="," item="id">
                #{id}
            </foreach>
        </where>
    </select>
```

