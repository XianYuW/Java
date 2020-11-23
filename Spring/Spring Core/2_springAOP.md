# AOP

## 一 转账案例

**需求**

使用spring框架整合DBUtils技术，实现用户转账功能

### **1.1** 基础功能

**步骤分析**

```
1. 创建java项目，导入坐标 
2. 编写Account实体类 
3. 编写AccountDao接口和实现类 
4. 编写AccountService接口和实现类 
5. 编写spring核心配置文件 
6. 编写测试代码
```

#### 代码实现

1）创建java项目，导入坐标

```
	<!--指定编码及版本-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
        <java.version>1.11</java.version>
        <maven.compiler.source>1.11</maven.compiler.source>
        <maven.compiler.target>1.11</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>dom4j</groupId>
            <artifactId>dom4j</artifactId>
            <version>1.6.1</version>
        </dependency>
        <dependency>
            <groupId>jaxen</groupId>
            <artifactId>jaxen</artifactId>
            <version>1.1.6</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.9</version>
        </dependency>
        <dependency>
            <groupId>commons-dbutils</groupId>
            <artifactId>commons-dbutils</artifactId>
            <version>1.6</version>
        </dependency>
        <dependency>
            <groupId>javax.annotation</groupId>
            <artifactId>javax.annotation-api</artifactId>
            <version>1.3.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>

    </dependencies>
```

2） 编写Account实体类

```
public class Account {
    private Integer id;
    private String name;
    private Double money;
    //get set toString()
}
```

3）编写AccountDao接口和实现类 

```
public interface AccountDao {
    //转出操作
    public void out(String outName,Double money);
    //转入操作
    public void in(String inName,Double money);
}
```

```
@Repository("accountDao")
public class AccountDaoImpl implements AccountDao {

    @Autowired
    private QueryRunner queryRunner;

    @Override
    public void out(String outName, Double money) {
        String sql = "update account set money = money - ? where name = ?";
        try {
            queryRunner.update(sql, money, outName);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void in(String inName, Double money) {
        String sql = "update account set money = money + ? where name = ?";
        try {
            queryRunner.update(sql, money, inName);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

4）编写AccountService接口和实现类 

```
public interface AccountService {
    //转钱操作
    public void transfer(Account out,Account in,Double money);
}
```

```
@Service("accountService")
public class AccountServiceImpl implements AccountService {

    @Autowired
    private AccountDao accountDao;

    @Override
    public void transfer(Account out, Account in, Double money) {
        //转出
        accountDao.out(out.getName(),money);
        //转入
        accountDao.in(in.getName(),money);

        System.out.println("转账成功");    
    }
}
```

5）编写spring核心配置文件以及数据库连接配置类、jdbc.properties

```
jdbc.properties：
    jdbc.driverClassName=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql:///spring_db?characterEncoding=utf-8
    jdbc.username=root
    jdbc.password=123456
```

```
@PropertySource("classpath:jdbc.properties")
public class DataSourceConfig {
    @Value("${jdbc.driverClassName}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    @Bean("dataSource")
    public DataSource getDataSource(){
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }
}
```

```
@Configuration
@ComponentScan("com.lagou")
@Import(DataSourceConfig.class)
public class SpringConfig {

    @Bean("queryRunner")
    public QueryRunner getQueryRunner(@Autowired DataSource dataSource){
        QueryRunner queryRunner = new QueryRunner(dataSource);
        return queryRunner;
    }
}
```

编写测试代码

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {SpringConfig.class})
public class SpringTest {
    @Autowired
    private AccountService accountService;

    @Test
    public void test(){
        Account out = new Account();
        out.setName("tom");

        Account in = new Account();
        in.setName("jerry");

        accountService.transfer(out,in,200d);
    }
}
```

#### 问题分析

上面的代码事务在dao层，转出转入操作都是一个独立的事务，但实际开发，应该把业务逻辑控制在

一个事务中，所以应该将事务挪到service层。

### 1.2 传统事务

**步骤分析**

```
1. 编写线程绑定工具类 
2. 编写事务管理器 
3. 修改service层代码 
4. 修改dao层代码
```

1）编写线程绑定工具类 

```
/**
 * 连接工具类：从数据源中获取一个连接，并将获取到的连接与数据源进行绑定
 *  ThreadLocal:线程内部的存储类，可以在指定的线程中存储数据
 *          内部的数据为  key：threadLocal(当前线程)  value：任意类型的值(Connection)
 */
@Component
public class ConnectionUtils {

    @Autowired
    private DataSource dataSource;

    private ThreadLocal<Connection> threadLocal = new InheritableThreadLocal<>();

    /**
     * 获取当前线程上绑定的连接：
     *      如果获取到的连接为空，那么就要从数据源中获取连接，并且放到ThreadLocal中(绑定到当前线程中)
     * @return
     */
    public Connection getThreadConnection(){
        //1.从ThreadLocal获取连接
        Connection connection = threadLocal.get();

        //2.判断当前线程中是否有Connection
        if(connection == null){
            //3.数据源中获取连接，并且放到ThreadLocal中
            try {
                //不为null
                connection = dataSource.getConnection();
                //存放到ThreadLocal中
                threadLocal.set(connection);

            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        return connection;
    }

    /**
     * 解除当前线程的连接绑定
     */
    public void removeTThreadConnection(){
        threadLocal.remove();
    }
}
```

```
如何确保dao层上两个方法使用的连接时同一个连接？
public void out(String outName, Double money){}
 public void in(String inName, Double money){}
 
 在service层上，先调用的时out方法，第一次从连接工具类ConnectionUtils中获取连接，由于当前获取的连接
 connection 为 null，因此转而调用dataSource.getConnection()获取连接；
 之后in方法调用连接时，connection获取的值为前面out获取的dataSource.getConnection()，因此确保了使用的是同一连接。
 当使用完毕之后，会释放资源，解除当前线程的绑定
 
 而在out和in()方法内不，queryRunner.update()方法中，有两个参数的重载方法和三个参数的重载方法，
 public int update(String sql, Object... params){}
 private int update(Connection conn, boolean closeConn, String sql, Object... params){}
 在调用的时候如果没有给定第一个connection参数，则会调用Datasource.getConnection()自动获取一个连接来进行sql语句的执行
 因为我们要让他们处于同一connection连接下执行，因此我们要手动传递connecton对象
 所以dao层的代码也需要修改
 
```

2）编写事务管理器 

```
/**
 * 事务管理工具类：包含：开启事务，提交事务，回滚事务，释放资源
 */
@Component
public class TransactionManager {

    @Autowired
    private ConnectionUtils connectionUtils;

    /**
     * 开启事务
     */
    public void beginTransaction(){
        //1.获取connection对象
        Connection connection = connectionUtils.getThreadConnection();
        try {
            //2.关闭自动提交事务，开启手动提交事务
            connection.setAutoCommit(false);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    /**
     * 提交事务
     */
    public void commit(){
        //1.获取connection对象
        Connection connection = connectionUtils.getThreadConnection();
        try {
            connection.commit();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    /**
     * 回滚事务
     */
    public void rollback(){
        //1.获取connection对象
        Connection connection = connectionUtils.getThreadConnection();
        try {
            connection.rollback();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    /**
     * 释放资源
     *      1.将手动提交事务改为自动提交事务
     *      2.将连接归还到连接池
     *      3.解除线程绑定
     */
    public void release(){
        //将手动提交事务改为自动提交事务
        Connection connection = connectionUtils.getThreadConnection();
        try {
            connection.setAutoCommit(true);
            //将连接归还到连接池
            connectionUtils.getThreadConnection().close();
            //解除线程绑定
            connectionUtils.removeTThreadConnection();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

3）修改service层代码 

```
@Service("accountService")
public class AccountServiceImpl implements AccountService {

    @Autowired
    private AccountDao accountDao;

    @Autowired
    private TransactionManager transactionManager;

    @Override
    public void transfer(Account out, Account in, Double money) {
        //手动开启事务，调用事务管理器类中的开启事务方法
        transactionManager.beginTransaction();
        try {
            //转出
            accountDao.out(out.getName(),money);
            //转入
            accountDao.in(in.getName(),money);

            System.out.println("转账成功");

            //手动提交事务
            transactionManager.commit();
        } catch (Exception e) {
            e.printStackTrace();
            //手动回滚事务
            transactionManager.rollback();
        }
        //手动释放资源
        transactionManager.release();
    }
}
```

4）修改dao层代码

```
@Repository("accountDao")
public class AccountDaoImpl implements AccountDao {

    @Autowired
    private QueryRunner queryRunner;

    @Autowired
    private ConnectionUtils connectionUtils;

    @Override
    public void out(String outName, Double money) {
        String sql = "update account set money = money - ? where name = ?";
        try {
            queryRunner.update(connectionUtils.getThreadConnection(),sql, money, outName);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void in(String inName, Double money) {
        String sql = "update account set money = money + ? where name = ?";
        try {
            queryRunner.update(connectionUtils.getThreadConnection(),sql, money, inName);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

#### 问题分析

```
上面代码，通过对业务层改造，已经可以实现事务控制了，但是由于我们添加了事务控制，也产生了一个新的问题：
	
	业务层方法变得臃肿了，里面充斥着很多重复代码。并且业务层方法和事务控制方法耦合了，违背了面向对象的开发思想。
```

## 二、Proxy优化转账案例

我们可以将业务代码和事务代码进行拆分，通过动态代理的方式，对业务方法进行事务的增强。

这样就不会对业务层产生影响，解决了耦合性的问题

### 具体思想

```
就是通过反射获取原方法，通过对原方法进行拦截，拦截前和拦截后对原方法进行增强
```



### 常用的动态代理技术

```
JDK 代理 : 

​	基于接口的动态代理技术·：利用拦截器（必须实现invocationHandler）加上反射机制生成一个代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理，从而实现方法增强


JDK动态代理		|-------------【目标接口】-----------|
|---------------|								【目标对象】
|【代理对象】     |
|内存(Java运行时) |
|---------------|
```

```
CGLIB代理：

​	基于父类的动态代理技术：动态生成一个要代理的子类，子类重写要代理的类的所有不是final的方法。
	在子类中采用方法拦截技术拦截所有的父类方法的调用，顺势织入横切逻辑，对方法进行增强
	
	
cglib动态代理		【目标对象】
						|
						|
                |---------------|
                |【代理对象】     |
                |内存(Java运行时) |
                |---------------|
```

### 2.1 JDK动态代理方式

Jdk工厂类

```测试代码
/**
 * JDK动态代理工厂类
 */
@Component
public class JDKProxyFactory {

    @Autowired
    private AccountService accountService;

    /**
     * 采用动态代理技术来生成目标类的代理对象
     * newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h) {}
     * ClassLoader loader 类加载器，借助被代理对象获取到类加载器
     * Class<?>[] interfaces 被代理类所需要实现的全部接口
     * InvocationHandler h 当代理对象调用接口中的任意方法时，那么都会执行InvocationHandler中的invoke方法
     */
    public AccountService createAccountServiceJDKProxy(){

        AccountService accountServiceProxy = (AccountService) Proxy.newProxyInstance(accountService.getClass().getClassLoader(), accountService.getClass().getInterfaces(), new InvocationHandler() {
            /**
             * Object proxy, Method method, Object[] args
             * proxy:当前的代理对象引用
             * method：被调用的目标方法的引用
             *args :被调用的目标方法所用到的参数
             */
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

                //让被代理对象的原方法执行
                Object invoke = method.invoke(accountService, args);

                return invoke;
            }
        });
        return accountServiceProxy;
    }
}
```

测试代码

```
	@Autowired
    private JDKProxyFactory JDKproxyFactory;
    
	@Test
    public void testTransferProxyJDK(){

        Account out = new Account();
        out.setName("tom");

        Account in = new Account();
        in.setName("jerry");

        //当前返回的实际上是AccountService的代理对象proxy
        AccountService accountServiceJDKProxy = JDKproxyFactory.createAccountServiceJDKProxy();
        accountServiceJDKProxy.transfer(out,in,-100d);

    }
```

优化，可以将事务操作抽取到动态代理类中

AccountServiceImpl

```
@Service("accountService")
public class AccountServiceImpl implements AccountService {

    @Autowired
    private AccountDao accountDao;
    
    @Override
    public void transfer(Account out, Account in, Double money) {
            //转出
            accountDao.out(out.getName(),money);
            //转入
            accountDao.in(in.getName(),money);
            System.out.println("转账成功");
    }
}
```

JDKProxyFactory

```
/**
 * JDK东塔代理工厂类
 */
@Component
public class JDKProxyFactory {

    @Autowired
    private AccountService accountService;
    @Autowired
    private TransactionManager transactionManager;

    public AccountService createAccountServiceJDKProxy(){

        AccountService accountServiceProxy = (AccountService) Proxy.newProxyInstance(accountService.getClass().getClassLoader(), accountService.getClass().getInterfaces(), new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) 
            throws Throwable {

                Object result = null;
                //手动开启事务，调用事务管理器类中的开启事务方法
                transactionManager.beginTransaction();
                try {
                    //让被代理对象的原方法执行
                    result = method.invoke(accountService, args);
                    //手动提交事务
                    transactionManager.commit();
                } catch (Exception e) {
                    e.printStackTrace();
                    //手动回滚事务
                    transactionManager.rollback();
                }
                //手动释放资源
                transactionManager.release();

                return result;
            }
        });
        return accountServiceProxy;
    }
}
```



### 2.2CGLIB动态代理方式

Cglib工厂类

```
/**
 * 该类就是采用cglib动态代理对目标类(AccountServiceImpl)进行方法(transfer)的动态增强(添加上事务控制)
 */
@Component
public class CGlibProxyFactory {

    @Autowired
    private AccountService accountService;
    @Autowired
    private TransactionManager transactionManager;

    public AccountService createAccountServiceCGlibProxy(){
        /**
         * 编写cglib对应的API来生成代理对象进行返回
         * 参数一：目标类的字节码对象
         * 参数二：动作类，当代理对象调用目标对象的原方法时，会执行intercept方法
         */


        AccountService accountServiceProxy = (AccountService) Enhancer.create(accountService.getClass(), new MethodInterceptor() {
            /**
             *o 代表生成的代理对象
             * method   调用目标方法的引用
             * objects  方法入参
             * methodProxy  代理方法
             */
            @Override
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {

                Object result = null;
                //手动开启事务，调用事务管理器类中的开启事务方法
                transactionManager.beginTransaction();
                try {
                    //让被代理对象的原方法执行
                    result = method.invoke(accountService, objects);
                    //手动提交事务
                    transactionManager.commit();
                } catch (Exception e) {
                    e.printStackTrace();
                    //手动回滚事务
                    transactionManager.rollback();
                }
                //手动释放资源
                transactionManager.release();
                return result;
            }
        });
        return accountServiceProxy;
    }
}
```

测试代码

```
	@Autowired
    private CGlibProxyFactory CGlibproxyFactory;
    
	@Test
    public void testTransferProxyCGlib(){

        Account out = new Account();
        out.setName("tom");

        Account in = new Account();
        in.setName("jerry");

        //当前返回的实际上是AccountService的代理对象proxy
        AccountService accountServiceJDKProxy = CGlibproxyFactory.createAccountServiceCGlibProxy();
        accountServiceJDKProxy.transfer(out,in,-100d);
    }
```



## 三 初识AOP

### 3.1什么是AOP

AOP 为 Aspect Oriented Programming 的缩写，意思为**面向切面编程**

AOP 是 OOP（面向对象编程） 的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内

容，利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高

程序的可重用性，同时提高了开发的效率。

**这样做的好处是**：

```
1. 在程序运行期间，在不修改源码的情况下对方法进行功能增强
2. 逻辑清晰，开发核心业务的时候，不必关注增强业务的代码
3. 减少重复代码，提高开发效率，便于后期维护
```

### 3.2 AOP底层实现

实际上，AOP 的底层是通过 Spring 提供的的**动态代理技术**实现的。在运行期间，Spring通过动态代

理技术动态的生成代理对象，代理对象方法执行时进行增强功能的介入，在去调用目标对象的方法，从

而完成功能的增强。

### 3.3 AOP相关术语

Spring 的 AOP 实现底层就是对上面的动态代理的代码进行了封装，封装后我们只需要对需要关注的

部分进行代码编写，并通过配置的方式完成指定目标的方法增强。

在正式讲解 AOP 的操作之前，我们必须理解 AOP 的相关术语，常用的术语如下：

```
* Target（目标对象）：代理的目标对象 

* Proxy （代理）：一个类被 AOP 织入增强后，就产生一个结果代理类 

* Joinpoint（连接点）：所谓连接点是指那些可以被拦截到的点。
		在spring中,这些点指的是方法，因为 spring只支持方法类型的连接点 

* Pointcut（切入点）：所谓切入点是指我们要对哪些 Joinpoint 进行拦截的定义 

* Advice（通知/ 增强）：所谓通知是指拦截到 Joinpoint 之后所要做的事情就是通知 
		分类：前置通知、后置通知、异常通知、最终通知、环绕通知 

* Aspect（切面）：是切入点和通知（引介）的结合 

* Weaving（织入）：是指把增强应用到目标对象来创建新的代理对象的过程。spring采用动态代理织 入，而AspectJ采用编译期织入和类装载期织入
```

### 3.4 AOP开发明确事项

#### 3.4.1 开发阶段（我们做的）

1)编写核心业务代码（目标类的目标方法） 切入点

2)把公用代码抽取出来，制作成通知（增强功能方法） 通知

3)在配置文件中，声明切入点与通知间的关系，即切面

#### 3.4.2 运行阶段（Spring框架完成的）

Spring 框架监控切入点方法的执行。一旦监控到切入点方法被运行，使用代理机制，动态创建目标对象的代理对象，根据通知类别，在代理对象的对应位置，将通知对应的功能织入，完成完整的代码逻辑运行。

#### 3.4.3 底层代理实现

在 Spring 中，框架会根据目标类是否实现了接口来决定采用哪种动态代理的方式。

当bean实现接口时，会用JDK代理模式

当bean没有实现接口，用cglib实现

（ 可以强制使用cglib（在spring配置中加入<aop:aspectj-autoproxy proxyt-target-class=”true”/>）



### 小结

```
* aop：面向切面编程 

* aop底层实现：基于JDK的动态代理 和 基于Cglib的动态代理 

* aop的重点概念： Pointcut（切入点）：真正被增强的方法 
				Advice（通知/ 增强）：封装增强业务逻辑的方法 
				Aspect（切面）：切点+通知 
				Weaving（织入）：将切点与通知结合，产生代理对象的过程
```





## 四 基于XML的AOP开发

### 4.1快速入门

**步骤分析**

```
1. 创建java项目，导入AOP相关坐标 
2. 创建目标接口和目标实现类（定义切入点） 
3. 创建通知类及方法（定义通知） 
4. 将目标类和通知类对象创建权交给spring 
5. 在核心配置文件中配置织入关系，及切面 
6. 编写测试代码
```

#### 4.1.1创建java项目，导入AOP相关坐标

1）创建java项目，导入AOP相关坐标 

```
<!--指定编码及版本-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
        <java.version>1.11</java.version>
        <maven.compiler.source>1.11</maven.compiler.source>
        <maven.compiler.target>1.11</maven.compiler.target>
    </properties>

    <dependencies>
        <!--导入spring的context坐标，context依赖aop-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>dom4j</groupId>
            <artifactId>dom4j</artifactId>
            <version>1.6.1</version>
        </dependency>
        <dependency>
            <groupId>jaxen</groupId>
            <artifactId>jaxen</artifactId>
            <version>1.1.6</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.9</version>
        </dependency>
        <dependency>
            <groupId>commons-dbutils</groupId>
            <artifactId>commons-dbutils</artifactId>
            <version>1.6</version>
        </dependency>
        <dependency>
            <groupId>javax.annotation</groupId>
            <artifactId>javax.annotation-api</artifactId>
            <version>1.3.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <!-- aspectj的织入（切点表达式需要用到该jar包） -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.13</version>
        </dependency>
        <!--spring整合junit-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>

    </dependencies>
```

2）创建目标接口和目标实现类（定义切入点） 

```
public interface AccountService {
    /**
     * 目标方法：(切入点：哟进行拦截增强的方法)
     */
    public void transfer();
}
```

```
public class AccountServiceImpl implements AccountService {
    /**
     * 目标方法：(切入点：哟进行拦截增强的方法)
     */
    @Override
    public void transfer() {
        System.out.println("转账方法执行了");
    }
}
```

3）创建通知类及方法（定义通知） 

```
/**
 * 通知类
 */
public class MyAdvice {

    public void before(){
        System.out.println("前置通知执行了");
    }
}
```

4）导入命名空间以及约束文档

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">

</beans>
```

将目标类和通知类对象创建权交给spring 

```
	<!--目标类交给IOC容器-->
    <bean id="accountService" class="com.lagou.service.impl.AccountServiceImpl"></bean>

    <!--通知类交给IOC容器-->
    <bean id="myAdvice" class="com.lagou.advice.MyAdvice"></bean>
```

5）在核心配置文件中配置织入关系，及切面 

```
	<!--AOP配置-->
、   <aop:config>
        <!--aop:aspect配置切面：切入点+通知
            先配置通知，  这里引用前面设置的通知类
            然后再通知后面设置切入点 通过execution()表达式
        -->
        <aop:aspect ref="myAdvice">
            <aop:before method="before" pointcut="execution(public void
            	com.lagou.service.impl.AccountServiceImpl.transfer())"/>
        </aop:aspect>
	</aop:config>
```

核心文件

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--目标类交给IOC容器-->
    <bean id="accountService" class="com.lagou.service.impl.AccountServiceImpl"></bean>

    <!--通知类交给IOC容器-->
    <bean id="myAdvice" class="com.lagou.advice.MyAdvice"></bean>

    <!--AOP配置-->
    <aop:config>
        <aop:aspect ref="myAdvice">
            <aop:before method="before" pointcut="execution(public void
            	com.lagou.service.impl.AccountServiceImpl.transfer())"/>
        </aop:aspect>
    </aop:config>
</beans>
```

6）编写测试代码

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class AccountServiceImplTest {

    @Autowired
    private AccountService accountService;

    @Test
    public void testTransfer(){
        accountService.transfer();
    }
}
```

### 4.2 XML配置AOP详解

#### 4.2.1切点表达式

表达式语法：

```
execution([修饰符] 返回值类型 包名.类名.方法名(参数))
execution(public void com.lagou.service.impl.AccountServiceImpl.transfer())
```

```
访问修饰符可以省略
	execution(void com.lagou.service.impl.AccountServiceImpl.*(..))
返回值类型、包名、类名、方法名可以使用星号 * 代替，代表任意
	execution(* com.lagou.service.impl.*.*(..))
	或execution(* *.*.*.*.*.*(..))
包名与类名之间一个点 . 代表当前包下的类，两个点 .. 表示当前包及其子包下的类
	execution(* com.lagou.service..*.*(..))
参数列表可以使用两个点 .. 表示任意个数，任意类型的参数列表
```

**切点表达式抽取**

当多个增强的切点表达式相同时，可以将切点表达式进行抽取，在增强中使用 pointcut-ref 属性代替

pointcut 属性来引用抽取后的切点表达式。

```
<aop:config> 
	<!--抽取的切点表达式--> 
	<aop:pointcut id="myPointcut" expression="execution(* com.lagou.service..*.* (..))">
    </aop:pointcut> 
    <aop:aspect ref="myAdvice"> 
    	<aop:before method="before" pointcut-ref="myPointcut"></aop:before> 
    </aop:aspect> 
</aop:config>
```

#### 4.2.2通知类型

通知的配置语法：

```
<aop:通知类型 method=“通知类中方法名” pointcut=“切点表达式"></aop:通知类型>
```

| 名称     | 标签                   | 说明                                                       |
| -------- | ---------------------- | ---------------------------------------------------------- |
| 前置通知 | 【aop:before】         | 用于配置前置通知。指定增强的方法在切入点方法之前执行       |
| 后置通知 | 【aop:afterReturning】 | 用于配置后置通知。指定增强的方法在切入点方法之后执行       |
| 异常通知 | 【aop:afterThrowing】  | 用于配置异常通知。指定增强的方法出现异常后执行             |
| 最终通知 | 【aop:after】          | 用于配置最终通知。无论切入点方法执行时是否有异常，都会执行 |
| 环绕通知 | 【aop:around】         | 用于配置环绕通知。开发者可以手动控制增强代码在什么时候执行 |

**注意：通常情况下，环绕通知都是独立使用的**

通知类

```
/**
 * 通知类
 */
public class MyAdvice {

    public void before(){
        System.out.println("前置通知执行了");
    }
    public void afterReturning(){
        System.out.println("后置通知执行了");
    }
    public void throwMethod(){
        System.out.println("错误通知执行了");
    }
    public void after(){
        System.out.println("最终通知执行了");
    }
}
```

前置，后置，异常，最终通知

```
<!--AOP配置-->
    <aop:config>
    	<aop:pointcut id="pointCut" 
    		expression="execution(public void *..AccountServiceImpl.transfer())"/>
        <!--aop:aspect配置切面：切入点+通知
            先配置通知，  这里引用前面设置的通知类
            然后再通知后面设置切入点 通过execution()表达式
        -->
        <aop:aspect ref="myAdvice">
            <aop:before method="before" pointcut-ref="pointCut"/>
            <aop:after-returning method="afterReturning" pointcut-ref="pointCut"/>
            <aop:after-throwing method="throwMethod" pointcut-ref="pointCut"/>
            <aop:after method="after" pointcut-ref="pointCut"/>
        </aop:aspect>
    </aop:config>
```

环绕通知以及通知类

```
public class MyAdvice {

    public void before(){
        System.out.println("前置通知执行了");
    }
    public void afterReturning(){
        System.out.println("后置通知执行了");
    }
    public void throwMethod(){
        System.out.println("错误通知执行了");
    }
    public void after(){
        System.out.println("最终通知执行了");
    }

    //ProceedingJoinPoint:  正在执行的连接点：切点
    public Object around(ProceedingJoinPoint pjp){
        Object proceed = null;
        try {
            System.out.println("前置通知执行了");
            //切点方法执行
            proceed = pjp.proceed();
            System.out.println("后置通知执行了");
        } catch (Throwable throwable) {
            throwable.printStackTrace();
            System.out.println("错误通知执行了");
        }finally {
            System.out.println("最终通知执行了");
        }
        return null;
    }
}
```

```
<aop:config>
        <aop:pointcut id="pointCut" 
        	expression="execution(public void *..AccountServiceImpl.transfer())"/>
        <aop:aspect ref="myAdvice">
            <aop:around method="around" pointcut-ref="pointCut"/>
        </aop:aspect>
    </aop:config>
```



### 4.3知识小结

```
* aop织入的配置 
	<aop:config> 
		<aop:aspect ref=“通知类”> 
			<aop:before method=“通知方法名称” pointcut=“切点表达式"></aop:before> 
		</aop:aspect> 
	</aop:config> 
	
* 通知的类型 前置通知、后置通知、异常通知、最终通知 环绕通知 

* 切点表达式 execution([修饰符] 返回值类型 包名.类名.方法名(参数))
```

## 基于注解的AOP开发

### 5.1快速入门

**步骤分析**

```
1. 创建java项目，导入AOP相关坐标 
2. 创建目标接口和目标实现类（定义切入点） 
3. 创建通知类（定义通知） 
4. 将目标类和通知类对象创建权交给spring
5. 在通知类中使用注解配置织入关系，升级为切面类 
6. 在配置文件中开启组件扫描和 AOP 的自动代理 
7. 编写测试代码
```















# Spring JDBCTemplate & 声明式事务

## 一Spring的JdbcTemplate

### 1.1 JdbcTemplate**是什么？

JdbcTemplate是spring框架中提供的一个模板对象，是对原始繁琐的Jdbc API对象的简单封装。

**核心对象**

```
JdbcTemplate jdbcTemplate = new JdbcTemplate(DataSource dataSource);
```

**核心方法**

```
int update(); 
	执行增、删、改语句 
	
List<T> query(); 查询多个 
T queryForObject(); 查询一个 
	new BeanPropertyRowMapper<>(); 实现ORM映射封装
```

**举个栗子**

查询数据库所有账户信息到Account实体中

```
public class JdbcTemplateTest { 
	@Test 
	public void testFindAll() throws Exception { 
		// 创建核心对象 
		JdbcTemplate jdbcTemplate = new JdbcTemplate(JdbcUtils.getDataSource()); 
		// 编写sql 
		String sql = "select * from account"; 
		// 执行sql
		List<Account> list = jdbcTemplate.query(sql, 
						new BeanPropertyRowMapper<> (Account.class));
	}
}
```

