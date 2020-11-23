# Spring概述

## Spring是什么

```
Spring是分层的 Java SE/EE应用 full-stack(全栈式)轻量级开源框架。

提供了表现层 SpringMVC和持久层 Spring JDBC Template以及 业务层 事务管理等众多的企业级应用

技术，还能整合开源世界众多著名的第三方框架和类库，逐渐成为使用最多的Java EE 企业应用开源框

架。
```

**两大核心**：以 **IOC**（Inverse Of Control：控制反转）和 **AOP**（Aspect Oriented Programming：面向

切面编程）为内核。

```
IOC:控制反转：把对象的创建权交给Spring ，[new User()]
AOP:面向切面编程：在不修改源代码的情况下，对方法进行增强
	AOP底层就是动态代理，AOP就是对动态代理进行了封装
```



## Spring优势

```
1）方便解耦，简化开发 
	Spring就是一个容器，可以将所有对象创建和关系维护交给Spring管理 
	
	什么是耦合度？对象之间的关系，通常说当一个模块(对象)更改时也需要更改其他模块(对象)，这就是 耦合，
	耦合度过高会使代码的维护成本增加。要尽量解耦 
	例如：
		注册驱动：DriverManager.registerDriver(new com.mysql.jdbc.Driver());
		//new ,存在编译期依赖，可以通过反射解决
		
		但是仅仅使用反射会造成硬编码的问题：例如：Class.forName("com.mysql.jdbc.Driver")
		通常的解耦思路：
			配置文件+反射
	
2）AOP编程的支持 
	Spring提供面向切面编程，方便实现程序进行权限拦截，运行监控等功能。
	
3）声明式事务的支持 
	通过配置完成事务的管理，无需手动编程 
	
4）方便测试，降低JavaEE API的使用 
	Spring对Junit4支持，可以使用注解测试 
	
5）方便集成各种优秀框架 
	不排除各种优秀的开源框架，内部提供了对各种优秀框架的直接支持
```

## 初识IOC

**概述**

```
控制反转（Inverse Of Control）不是什么技术，而是一种设计思想。它的目的是指导我们设计出更加松耦合的程序。

控制：在java中指的是对象的控制权限（创建、销毁）

反转：指的是对象控制权由原来 由开发者在类中手动控制反转到由Spring容器控制

举个栗子：
	* 传统方式 
		之前我们需要一个userDao实例，需要开发者自己手动创建 new UserDao(); 
	* IOC方式 
		现在我们需要一个userDao实例，直接从spring的IOC容器获得，对象的创建权交给了spring控制
```

## 自定义IOC容器

```
需求
	实现service层与dao层代码解耦合
	步骤分析
		1. 创建java项目，导入自定义IOC相关坐标
		2. 编写Dao接口和实现类
		3. 编写Service接口和实现类
		4. 编写测试代码
```

**实现**

### 传统方式

创建java项目，导入自定义IOC相关坐标

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
    </dependencies>
```

**编写**Dao接口和实现类

```
public interface IUserDao {
    public void save();
}
```

```
public class IUserDaoImpl implements IUserDao {

    @Override
    public void save() {
        System.out.println("保存成功了...");
    }
}
```

**编写**Service接口和实现类

```
public interface IUserService {
public void save() throws ClassNotFoundException, 
						IllegalAccessException, 
						InstantiationException;
}
```

```
public class IUserServiceImpl implements IUserService {
    @Override
    public void save() throws ClassNotFoundException, 
    						IllegalAccessException, 
    						InstantiationException {
        //调用dao层方法，传统方式：存在编译期依赖：耦合重
        //IUserDao userDao = new IUserDaoImpl();

        //反射
        IUserDao userDao = (IUserDao) 			 													Class.forName("com.lagou.dao.impl.IUserDaoImpl").newInstance();


    }
}
```

**编写测试代码**

```
public class UserTest { 
	@Test 
	public void testSave() throws Exception { 
		IUserService userService = new IUserServiceImpl(); 
		userService.save(); 
	} 
}
```

**问题**

当前service对象和dao对象耦合度太高，而且每次new的都是一个新的对象，导致服务器压力过大。

### 自定义IOC容器方法

**编写**beans.xml

把所有需要创建对象的信息定义在配置文件中

```
<?xml version="1.0" encoding="UTF-8" ?> 
<beans> 
	<bean id="userDao" class="com.lagou.dao.impl.IUserDaoImpl"></bean> 
</beans>
```

**编写**BeanFactory工具类

```
public class BeanFactory {

    private static Map<String,Object> iocmap = new HashMap<String,Object>();
    //程序启动时，初始化对象实例
    static{
        //1.读取配置文件
        InputStream stream = BeanFactory.class.getClassLoader().getResourceAsStream("beans.xml");
        //2.解析xml（dom4j）
        SAXReader saxReader = new SAXReader();
        try {
            Document document = saxReader.read(stream);
            //3.编写xpath表达式
            String xpath = "//bean";
            //4.获取到所有的bean标签
            List<Element> list = document.selectNodes(xpath);

            //5.遍历并使用反射创建对象实例，存储到map(ioc容器)中
            for (Element element : list) {
                String id = element.attributeValue("id");
                //className:com.lagou.dao.impl.IUserDaoImpl
                String className = element.attributeValue("class");

                //使用反射生成实例对象
                Object o = Class.forName(className).newInstance();
                //存储到ma集合中
                iocmap.put(id,o);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    public static Object getBean(String beanid){
        Object o = iocmap.get(beanid);
        return o;
    }
}
```

**修改**UserServiceImpl实现类

```
public class IUserServiceImpl implements IUserService {
    @Override
    public void save() throws ClassNotFoundException, 
    						IllegalAccessException, 
    						InstantiationException {
        //调用dao层方法，传统方式：存在编译期依赖：耦合重
        //IUserDao userDao = new IUserDaoImpl();

        //反射
        //IUserDao userDao = (IUserDao) Class.forName("com.lagou.dao.impl.IUserDaoImpl").newInstance();

        IUserDao userDao = (IUserDao)BeanFactory.getBean("userDao");
        userDao.save();

    }
}
```

#### 知识点

##### XPath的基本语法介绍

| 语法               | 说明                                                     |
| ------------------ | -------------------------------------------------------- |
| /AAA/DDD/BBB       | 表示一层一层的，AAA下面 的 DDD 下面的 BBB                |
| //BBB              | 表示和这个名字相同，只要名称是BBB的元素节点都得到        |
| /*                 | 所有元素                                                 |
| BBB[1],BBB[last()] | 第一种表示第一个BBB元素，第二种表示最后一个BBB元素       |
| //BBB[@id]         | 表示只要BBB元素上面有id属性，都得到                      |
| //BBB[@id='b1']    | 表示元素名称是BBB，在BBB上面有id属性，并且id的属性值是b1 |

##### XPath常用API介绍

```
selectSingleNode(query):查找和XPath查询匹配的一个节点。
	参数是XPath查询串
selectNode(query) :得到的是xml根节点下所有满足xpath的节点
	参数是XPath查询串
```

##### XPath获取节点信息示例：

```
public void test(){
	//1.创建XML解析对象
	SAXReader reader = new SAXReader();
	//2.解析XML		获取文档对象
	Document document = reader.read("文档路径or文件流");
	//3.编写xpath表达式
    String xpath = "//bean";
    //4.获取到所有的bean标签
    List<Element> list = document.selectNodes(xpath);
}
```



#### 小结

```
* 其实升级后的BeanFactory就是一个简单的Spring的IOC容器所具备的功能。

* 之前我们需要一个userDao实例，需要开发者自己手动创建 new UserDao(); 

* 现在我们需要一个userdao实例，直接从spring的IOC容器获得，对象的创建权交给了spring控制
* 最终目标：代码解耦合
```

## **Spring**快速入门

**介绍**

**需求**：借助spring的IOC实现service层与dao层代码解耦合

**步骤分析**

```
1. 创建java项目，导入spring开发基本坐标 
2. 编写Dao接口和实现类 
3. 创建spring核心配置文件 
4. 在spring配置文件中配置 UserDaoImpl 
5. 使用spring相关API获得Bean实例
```

 **实现**

**创建**java项目，导入spring开发基本坐标

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
    </dependencies>
```

**编写**Dao接口和实现类

```
public interface IUserDao {
    public void save();
}
```

```
public class UserDaoImpl implements IUserDao {
    @Override
    public void save() {
        System.out.println("保存成功了...");
    }
}
```

**创建**spring核心配置文件

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

**在**spring配置文件中配置UserDaoImpl

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
       
    <!--在spring配置文件中配置 UserDaoImpl
        id:唯一标识
        class：类全路径
    -->
    <bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl"></bean>


</beans>
```

**使用**spring相关API获得Bean实例

```
public class SpringTest {
    @Test
    public void test1(){
        //获取到了spring上下文对象，记住上写文对象可以获取到ioc容器中的bean对象
        ApplicationContext classPathXmlApplicationContext = 
        	new ClassPathXmlApplicationContext("applicationContext.xml");

        //使用上下文对象从ioc容器中获取到了bean对象
        IUserDao userDao = (IUserDao)classPathXmlApplicationContext.getBean("userDao");
        //这里除了可以用字符串类型，也可以用UserDao.class
        IUserDao userDao = 
        				(IUserDao)classPathXmlApplicationContext.getBean(UserDao.class);

        //调用方法
        userDao.save();
    }
}
```

**小结**

```
Spring的开发步骤
1. 导入坐标 
2. 创建Bean 
3. 创建applicationContext.xml 
4. 在配置文件中进行Bean配置 
5. 创建ApplicationContext对象，执行getBean
```

## Spring相关API

### API继承体系介绍

Spring的API体系异常庞大，我们现在只关注两个BeanFactory和ApplicationContext

### BeanFactory

```
BeanFactory是 IOC 容器的核心接口，它定义了IOC的基本功能。

​	特点：在第一次调用getBean()方法时，创建指定对象的实例
```

```
	@Test
    public void test2(){

        //核心接口，不会创建bean对象存到容器中
        BeanFactory xmlBeanFactory = new XmlBeanFactory(
        	new ClassPathResource("applicationContext.xml"));

        //getBean的时候，才真正创建bean对象
        IUserDao userDao = (IUserDao)xmlBeanFactory.getBean("userDao");

        userDao.save();
    }
```

### ApplicationContext

```
代表应用上下文对象，可以获得spring中IOC容器的Bean对象。

​	特点：在spring容器启动时，加载并创建所有对象的实例
```

**常用实现类**

```
1. ClassPathXmlApplicationContext 它是从类的根路径下加载配置文件 推荐使用这种。 
2. FileSystemXmlApplicationContext 它是从磁盘路径上加载配置文件，配置文件可以在磁盘的任意位置。 
3. AnnotationConfigApplicationContext 当使用注解配置容器对象时，
	需要使用此类来创建 spring 容器。它用来读取注解。
```

```
	@Test
    public void test1(){
        //获取到了spring上下文对象，记住上写文对象可以获取到ioc容器中的bean对象
        //加载的同时就创建了bean对象存到容器中
        ApplicationContext classPathXmlApplicationContext = 
        	new ClassPathXmlApplicationContext("applicationContext.xml");

        //使用上下文对象从ioc容器中获取到了bean对象
        IUserDao userDao = (IUserDao)classPathXmlApplicationContext.getBean("userDao");

        //调用方法
        userDao.save();
    }
```

**常用方法**

```
1. Object getBean(String name); 
	根据Bean的id从容器中获得Bean实例，返回是Object，需要强转。 
	例：
		IUserDao userDao = (IUserDao)classPathXmlApplicationContext.getBean("userDao");
	
2. <T> T getBean(Class<T> requiredType); 
	根据类型从容器中匹配Bean实例，当容器中相同类型的Bean有多个时，则此方法会报错。
	例：
		IUserDao userDao = classPathXmlApplicationContext.getBean(IUserDao.class);
		但是如果一个接口不止有一个实现类，那么该方法会报错，此时可以使用下一个方法
    
3. <T> T getBean(String name,Class<T> requiredType); 
	根据Bean的id和类型获得Bean实例，解决容器中相同类型Bean有多个情况。
	例：
		IUserDao userDao = 																			classPathXmlApplicationContext.getBean("userDao",IUserDao.class);
	
```

## Spring配置文件

### Bean标签基本配置

```
<bean id="" class=""></bean>
* 用于配置对象交由Spring来创建。 
* 基本属性： 
	id：Bean实例在Spring容器中的唯一标识 
	class：Bean的全限定名 

* 默认情况下它调用的是类中的 无参构造函数，如果没有无参构造函数则不能创建成功。
	既class绑定的类中如果没有提供无参构造，则会报错，没有无参构造函数则不能创建成功
```

### Bean标签范围配置

```
<bean id="" class="" scope=""></bean>
```

scope属性指对象的作用范围，取值如下：

| 取值范围       | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| singleton      | 默认值，单例                                                 |
| prototype      | 多例的                                                       |
| request        | WEB项目中，Spring创建一个Bean的对象，将对象存入到request域中 |
| session        | WEB项目中，Spring创建一个Bean的对象，将对象存入到session域中 |
| global session | WEB项目中，应用在Portlet环境，如果没有Portlet环境那么globalSession 相当于 session |

```
注：线程不安全的bean配置成singleton会引发问题，可以考虑配置为prototype


核心配置文件：
	<bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl" scope="singleton"></bean>
测试类：	
	@Test
    public void test3(){

        ApplicationContext classPathXmlApplicationContext = 
        	new ClassPathXmlApplicationContext("applicationContext.xml");
        
        IUserDao userDao = (IUserDao)classPathXmlApplicationContext.getBean("userDao");
        IUserDao userDao2 = (IUserDao)classPathXmlApplicationContext.getBean("userDao");

        System.out.println(userDao);//com.lagou.dao.impl.UserDaoImpl@6379eb
        System.out.println(userDao2);//com.lagou.dao.impl.UserDaoImpl@6379eb
    }
    
可以看到，两个实例化对象的地址相同，创建的是同一个实例化对象
```

```
核心配置文件：
	<bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl" scope="prototype"></bean>
测试类：
	@Test
    public void test4(){

        ApplicationContext classPathXmlApplicationContext = 
        	new ClassPathXmlApplicationContext("applicationContext.xml");

        IUserDao userDao = (IUserDao)classPathXmlApplicationContext.getBean("userDao");
        IUserDao userDao2 = (IUserDao)classPathXmlApplicationContext.getBean("userDao");

        System.out.println(userDao);//com.lagou.dao.impl.UserDaoImpl@a514af7
        System.out.println(userDao2);//com.lagou.dao.impl.UserDaoImpl@6b927fb
    }
 
可以看到，两个实例化对象的地址不同，创建了两个不同的实例化对象
```



```
1. 当scope的取值为singleton时 
   Bean的实例化个数：1个 
   Bean的实例化时机：当Spring核心文件被加载时，实例化配置的Bean实例 
   Bean的生命周期： 
   	对象创建：当应用加载，创建容器时，对象就被创建了 
   	对象运行：只要容器在，对象一直活着 
   	对象销毁：当应用卸载，销毁容器时，对象就被销毁了
2. 当scope的取值为prototype时 
   Bean的实例化个数：多个 
   Bean的实例化时机：当调用getBean()方法时实例化Bean 
   Bean的生命周期： 
   	对象创建：当使用对象时，创建新的对象实例 
   	对象运行：只要对象在使用中，就一直活着 
   	对象销毁：当对象长时间不用时，被 Java 的垃圾回收器回收了
```



### Bean生命周期配置

```
<bean id="" class="" scope="" init-method="" destroy-method=""></bean> 

* init-method：指定类中的初始化方法名称 
* destroy-method：指定类中销毁方法名称
```



## Bean实例化三种方式

### 无参构造方法实例化

它会根据默认无参构造方法来创建类对象，如果bean中没有默认无参构造函数，将会创建失败

```
    <!--方式一：无参构造方法进行实例化-->
    <bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl" scope="prototype"></bean>
```

### 工厂静态方法实例化

**应用场景**

```
依赖的jar包中有个A类，A类中有个静态方法m1，m1方法的返回值是一个B对象。如果我们频繁使用B对象，此时我们可以将B对象的创建权交给spring的IOC容器，以后我们在使用B对象时，无需调用A类中的m1方法，直接从IOC容器获得。
```

```
public class StaticFactoryBean {
    public static IUserDao createUserDao(){
        return new UserDaoImpl();
    }
}
```

```
<!--方式二：工厂静态方法进行实例化-->
    <bean id="userDao" class="com.lagou.factory.StaticFactoryBean" 
    factory-method="createUserDao" ></bean>
```



### 工厂普通方法实例化

**应用场景**

```
依赖的jar包中有个A类，A类中有个普通方法m1，m1方法的返回值是一个B对象。如果我们频繁使用B对象，

此时我们可以将B对象的创建权交给spring的IOC容器，以后我们在使用B对象时，无需调用A类中的m1方法，直接从IOC容器获得。
```

```
public class DynamicFactoryBean {
    public IUserDao createUserDao(){
        return new UserDaoImpl();
    }
}
```

```
	<!--方式三：工厂普通方法实例化-->
    <!--这个bean标签将工厂类存到容器中-->
    <bean id="dynamicFactoryBean" class="com.lagou.factory.DynamicFactoryBean"></bean>
    <!--同时还要将工厂类的createUserDao方法返回值存到容器中
        factory-bean="dynamicFactoryBean"   
        		指定工厂对象是哪一个，当前指定的是dynamicFactoryBean
        factory-method="createUserDao"   
        		指定要调用工厂对象的哪一个方法,当前调用的是createUserDao方法
       -->
    <bean id="userDao" factory-bean="dynamicFactoryBean" factory-method="createUserDao"></bean>
```



## Bean依赖注入概述(重点)

**依赖注入** **DI**（Dependency Injection）：它是 Spring 框架核心 IOC 的具体实现。

```
在编写程序时，通过控制反转，把对象的创建交给了 Spring，但是代码中不可能出现没有依赖的情况。IOC 解耦只是降低他们的依赖关系，但不会消除。例如：业务层仍会调用持久层的方法。
```

**代码示例**：

dao层以及实现类：

```
public interface IUserDao {
    public void save();
}
```

```
public class UserDaoImpl implements IUserDao {
    @Override
    public void save() {
        System.out.println("保存成功了...");
    }
}
```

service层以及实现类

```
public interface IUserService {
    public void save();
}
```

```
public class UserServiceImpl implements IUserService {
    @Override
    public void save() {
        ClassPathXmlApplicationContext context = 
        	new ClassPathXmlApplicationContext("applicationContext.xml");
        IUserDao userDao = (IUserDao) context.getBean("userDao");
        userDao.save();
    }
}
```

applicationContext.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
       
    <bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl"></bean>
    <!--配置UserService-->
    <bean id="userService" class="com.lagou.service.impl.UserServiceImpl"></bean>
    
</beans>
```

测试类(以往的方式)

```
 	@Test
    public void test5(){
        IUserService userService = new UserServiceImpl();
        userService.save();
    }
```

测试类（现在）

```
	@Test
    public void test5(){
        /*IUserService userService = new UserServiceImpl();
        userService.save();*/

        ClassPathXmlApplicationContext context = 
        	new ClassPathXmlApplicationContext("applicationContext.xml");
        IUserService userService = (IUserService) context.getBean("userService");
        userService.save();
    }
```

```
那这种业务层和持久层的依赖关系，在使用 Spring 之后，就让 Spring 来维护了。简单的说，就是通过框架把持久层对象传入业务层，而不用我们自己去获取。
```



### Bean依赖注入方式

依赖注入的方式本质上就两种：一种是构造方法注入，一种是set方法注入

#### **1** 构造方法

UserServiceImpl实现类

```
public class UserServiceImpl implements IUserService {

    //通过配置文件完成了userDao的注入
    private IUserDao userDao;

    public UserServiceImpl(IUserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void save() {
        userDao.save();
    }
}
```



思考：之前如何通过构造方法创建userService对象？

```
1）UserDao UserDao = new UserDao();
	相当于<bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl"></bean>
	
2）IUserService userService = new UserServiceImpl(userDao);
	相当于
	<bean id="userService" class="com.lagou.service.impl.UserServiceImpl">
		<!--<constructor-arg index="0" type="com.lagou.dao.IUserDao" ref="userDao"/>-->
		<constructor-arg name="userDao" ref="userDao"/>
	</bean>
```

现在呢？

核心配置文件applicationContext.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
       
    <bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl"></bean>
    <!--配置UserService-->
    <bean id="userService" class="com.lagou.service.impl.UserServiceImpl">
        <!--constructor-arg 通过该标签进行有参构造方法实例化
            index="0" 给构造方法中的第一个参数进行赋值
            type="" 指定当前参数的实例类型
            ref="userDao" 引用配置好的ioc容器中的对象，将其注入到第一个参数中
                        ref的值，和上方配置好的id值要相等
            value     普通类型的数据
        -->
        <!--<constructor-arg index="0" type="com.lagou.dao.IUserDao" ref="userDao"/>-->
        <!--简化写法
            name:有参构造方法中的参数名
            ref ：表示给相对应的参数名注入值
            value       普通类型的数据
        -->
        <constructor-arg name="userDao" ref="userDao"/>
    </bean>
    
</beans>
```

测试方法

```
	@Test
    public void test5(){
        ClassPathXmlApplicationContext context = 
        	new ClassPathXmlApplicationContext("applicationContext.xml");
        IUserService userService = (IUserService) context.getBean("userService");
        userService.save();
    }
```



#### 2 set方法

UserServiceImpl实现类

```
public class UserServiceImpl implements IUserService {

    //通过配置文件完成了userDao的注入
    private IUserDao userDao;

    public void setUserDao(IUserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void save() {
        userDao.save();
    }
}
```

思考：之前如何通过set方法完成userDao值的设置？

```
1）UserDao UserDao = new UserDao();
	相当于<bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl"></bean>
	
2）IUserService userService = new UserServiceImpl();
  userService.setUserDao(userDao);
```

现在通过配置文件呢

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
       
    <bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl"></bean>
    <!--配置UserService-->
    <bean id="userService" class="com.lagou.service.impl.UserServiceImpl">
    	<!--set方法完成依赖注入,通过property标签完成
            name="userDao" 要填写的是set方法后面的名称，首字母小写
            ref 引用数据类型
            value   普通类型的数据
        -->
        <property name="userDao" ref="userDao"/>
    </bean>
</beans>
```

测试类

```
	@Test
    public void test5(){
       
        ClassPathXmlApplicationContext context = 
        	new ClassPathXmlApplicationContext("applicationContext.xml");
        IUserService userService = (IUserService) context.getBean("userService");
        userService.save();
    }
```







#### 3 P命名空间注入

**P命名空间注入本质也是set方法注入**，但比起上述的set方法注入更加方便，主要体现在配置文件中

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl"></bean>
    <bean id="userService" class="com.lagou.service.impl.UserServiceImpl" 
    	p:userDao-ref="userDao">
    </bean>
</beans>
```

```
xmlns:p="http://www.springframework.org/schema/p"
这个约束头就是将第一个约束头改变一下就行 
```



###  Bean依赖注入的数据类型

上面操作，都是注入Bean对象，除了对象的引用可以注入，普通数据类型和集合都可以在容器中进行注入。

```
注入数据的三种数据类型

*1.普通数据类型		<property name = ""value=""/>
*2.引用数据类型		<property name = ""ref=""/>
*3.集合数据类型		<property name = ""value=""/>
```

其中引用数据类型，此处就不再赘述了，之前的操作都是对UserDao对象的引用进行注入的。下面将以set方法注入为例，演示普通数据类型和集合数据类型的注入。

#### **1** 注入普通数据类型

```
public class UserDaoImpl implements IUserDao {
    private String username;
    private int age;

    public void setUsername(String username) {
        this.username = username;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public void save() {
        System.out.println(username);
        System.out.println(age);
        System.out.println("保存成功了...");
    }
}
```

```
<bean id="user" class="com.lagou.domain.User"> 
	<property name="username" value="jack"/> 
	<property name="age" value="18"/> 
</bean>

注：使用<property>标签，表示使用的setXxx()方法完成注入，。但是类中并不一定有getUsername()方法
```

#### **2** 注入集合数据类型

##### 1）List集合注入

user.java

```
public class User {
    private String username;
    private Integer age;

    public void setUsername(String username) {
        this.username = username;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
    @Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                ", age=" + age +
                '}';
    }
}
```

UserDaoImpl

```
public class UserDaoImpl implements IUserDao {
    private String username;
    private int age;

    //注入集合数据类型
    private List<Object> list;

    public void setList(List<Object> list) {
        this.list = list;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public void save() {
        /*System.out.println(username);
        System.out.println(age);*/
        System.out.println(list);
        System.out.println("保存成功了...");
    }
}
```

```
	<bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl">
        <property name="username" value="猪头"/>
        <property name="age" value="18"/>

        <!--进行集合数据类型的注入-->
        <property name="list">
            <list>
                <value>aaa</value>
                <ref bean="user"></ref>
                <value>你好</value>
                <value>hello,world!</value>
            </list>
        </property>
    </bean>
```

测试类

```
	@Test
    public void test5(){
        ClassPathXmlApplicationContext context = 
        	new ClassPathXmlApplicationContext("applicationContext.xml");
        IUserService userService = (IUserService) context.getBean("userService");
        userService.save();
        //[aaa, User{username='马尔扎哈', age=18}, 你好, hello,world!]
        //保存成功了...
    }
```



##### 2）Set集合注入

```
public class UserDaoImpl implements IUserDao {
    private String username;
    private int age;

    //注入集合数据类型
    private List<Object> list;
    private Set<Object> set;

    public void setSet(Set<Object> set) {
        this.set = set;
    }

    public void setList(List<Object> list) {
        this.list = list;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public void save() {
        /*System.out.println(username);
        System.out.println(age);*/
        System.out.println(set);
        System.out.println("保存成功了...");
    }
}
```

```
	<bean id="user" class="com.lagou.domain.User">
        <property name="username" value="马尔扎哈"/>
        <property name="age" value="18"/>
    </bean>
	<bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl">
        <property name="username" value="猪头"/>
        <property name="age" value="18"/>

        <!--进行集合数据类型的注入-->
        <property name="list">
            <list>
                <value>aaa</value>
                <ref bean="user"></ref>
                <value>你好</value>
                <value>hello,world!</value>
            </list>
        </property>
        <property name="set">
            <set>
                <value>set集合</value>
                <ref bean="user"></ref>
            </set>
        </property>
    </bean>
```

##### 3）Array数组注入

```
public class UserDaoImpl implements IUserDao {

    //注入集合数据类型
    private Object[] array;

    public void setArray(Object[] array) {
        this.array = array;
    }

    @Override
    public void save() {
        System.out.println(Arrays.toString(array));

        System.out.println("保存成功了...");
    }
}
```

```
<bean id="user" class="com.lagou.domain.User"> 
	<property name="username" value="jack"/> 
	<property name="age" value="18"/> 
</bean> 
<bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl"> 
		<property name="array">
            <array>
                <value>array数组</value>
                <ref bean="user"></ref>
            </array>
        </property> 
</bean>
```

##### 4）Map集合注入

```
public class UserDaoImpl implements IUserDao {

    private Map<String,Object> map ;

    public void setMap(Map<String, Object> map) {
        this.map = map;
    }

    @Override
    public void save() {
        System.out.println(map);

        System.out.println("保存成功了...");
    }
}
```

```
<bean id="user" class="com.lagou.domain.User"> 
	<property name="username" value="jack"/> 
	<property name="age" value="18"/> 
</bean> 
<bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl"> 
	<property name="map">
    	<map>
    		<entry key="k1" value="map集合"/>
    		<entry key="k2" value-ref="user"/>
    	</map>
    </property>
</bean>
```

##### 5）Properties配置注入

```
public class UserDaoImpl implements IUserDao {

    private Properties properties;

    public void setProperties(Properties properties) {
        this.properties = properties;
    }

    @Override
    public void save() {

        System.out.println(properties);

        System.out.println("保存成功了...");
    }
}
```

```
<bean id="userDao" class="com.lagou.dao.impl.UserDaoImpl"> 
	<property name="properties">
    	<props>
    		<prop key="k1">value1</prop>
    		<prop key="k2">value2</prop>
    		<prop key="k3">value3</prop>
    	</props>
    </property>
</bean>
```

### 配置文件模块化

实际开发中，Spring的配置内容非常多，这就导致Spring配置很繁杂且体积很大，所以，可以将部分

配置拆解到其他配置文件中，也就是所谓的**配置文件模块化**。 

#### 1）并列的多个配置文件

```
ApplicationContext act = 
					new ClassPathXmlApplicationContext("beans1.xml","beans2.xml","...");
```

#### 2）主从配置文件

```
<import resource="applicationContext-xxx.xml"/>
```

```
注意：

​	同一个xml中不能出现相同名称的bean,如果出现会报错
​	多个xml如果出现相同名称的bean，不会报错，但是后加载的会覆盖前加载的bean
```

### 小结

```
Spring的重点配置

​<bean>标签：创建对象并放到spring的IOC容器 
	id属性:在容器中Bean实例的唯一标识，不允许重复 
	class属性:要实例化的Bean的全限定名 
	scope属性:Bean的作用范围，常用是Singleton(默认)和prototype 

​<constructor-arg>标签：(有参构造)属性注入 
	name属性：属性名称 
	value属性：注入的普通属性值 
	ref属性：注入的对象引用值 
	
​<property>标签：属性注入 
	name属性：属性名称 
	value属性：注入的普通属性值 
	ref属性：注入的对象引用值 
	<list> 
	<set> 
	<array> 
	<map> 
	<props> 
	
​<import>标签:导入其他的Spring的分文件
```

## DbUtils（IOC实战）

### DbUtils是什么？

DbUtils是Apache的一款用于简化Dao代码的工具类，它底层封装了JDBC技术。

**核心对象**

```
QueryRunner queryRunner = new QueryRunner(DataSource dataSource);
```

**核心方法**

```
int update(); 执行增、删、改语句 
T query(); 执行查询语句 
	ResultSetHandler<T> 这是一个接口，主要作用是将数据库返回的记录封装到实体对象
```

### Spring的xml整合DbUtils

**步骤分析**

```
1. 准备数据库环境 
2. 创建java项目，导入坐标 
3. 编写Account实体类
4. 编写AccountDao接口和实现类 
5. 编写AccountService接口和实现类 
6. 编写spring核心配置文件 
7. 编写测试代码
```

**实现**

2.导入坐标

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
        
    </dependencies>
```

**3.编写**Account实体类

```
public class Account {
    private Integer id;
    private String name;
    private Double money;
    //get,set,toString方法省略
}
```

4.编写AccountDao接口和实现类

```
public interface AccountDao {
    public List<Account> findAll();

    public Account findById(Integer id);

    public void save(Account account);

    public void update(Account account);

    public void delete(Integer id);
}
```

```
public class AccountDaoImpl  implements AccountDao {

    private QueryRunner qr;

    public void setQr(QueryRunner qr) {
        this.qr = qr;
    }

    @Override
    public List<Account> findAll() {

        List<Account> list = null;
        String sql = "select * from account";
        try {
            list = qr.query(sql, new BeanListHandler<Account>(Account.class));
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return list;
    }

    @Override
    public Account findById(Integer id) {

        Account query= null;
        String  sql = "select * from account where id = ?";
        try {
            query = qr.query(sql, new BeanHandler<Account>(Account.class),id);
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return query;
    }

    @Override
    public void save(Account account) {

        String sql = "insert into account values(null,?,?) ";
        try {
            qr.update(sql,account.getName(),account.getMoney());
        } catch (SQLException e) {
            e.printStackTrace();
        }

    }

    @Override
    public void update(Account account) {

        String sql = "update account set name = ? ,money = ? where id = ?";
        try {
            qr.update(sql,account.getName(),account.getMoney(),account.getId());
        } catch (SQLException e) {
            e.printStackTrace();
        }

    }

    @Override
    public void delete(Integer id) {

        String sql = "delete from account where id = ?";
        try {
            qr.update(sql,id);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

5.编写AccountService接口和实现类

```
public interface AccountService {
    public List<Account> findAll();

    public Account findById(Integer id);

    public void save(Account account);

    public void update(Account account);

    public void delete(Integer id);
}
```

```
public class AccountServiceImpl implements AccountService {
    private AccountDao accountDao;

    public void setAccountDao(AccountDao accountDao) {
        this.accountDao = accountDao;
    }

    @Override
    public List<Account> findAll() {
        return accountDao.findAll();
    }

    @Override
    public Account findById(Integer id) {
        return accountDao.findById(id);
    }

    @Override
    public void save(Account account) {
        accountDao.save(account);
    }

    @Override
    public void update(Account account) {
        accountDao.update(account);
    }

    @Override
    public void delete(Integer id) {
        accountDao.delete(id);
    }
}
```

6.编写spring核心配置文件 

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
       
    <!--1.account-->
    <bean id="account" class="com.lagou.domain.Account"></bean>
    <!--4.dataSource-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql:///spring_db??characterEncoding=utf-8"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </bean>

    <!--3.queryRunner
        因为queryRunner是有参构造，可以查看源码，因此我们要配置dataSource
        通过调用queryRunner的有参构造函数，
           name="ds"     参数名为ds
    -->
    <bean id="queryRunner" class="org.apache.commons.dbutils.QueryRunner">
        <constructor-arg name="ds" ref="dataSource"/>
    </bean>

    <!--2.AccountDao
        因为我们是通过set方法对queryRunner进行注入，因此需要配置一个queryRunner
    -->
    <bean id="accountDao" class="com.lagou.dao.impl.AccountDaoImpl" >
        <property name="qr" ref="queryRunner"/>

    </bean>

    <!--5.AccountService-->
    <bean id="accountService" class="com.lagou.service.impl.AccountServiceImpl">
        <property name="accountDao" ref="accountDao"/>
    </bean>

</beans>
```

7.编写测试代码

```
public class AccountServiceTest {
    private ClassPathXmlApplicationContext context;
    private AccountService accountService;

    @Before
    public void before(){
        context = new ClassPathXmlApplicationContext("applicationContext.xml");
        accountService = (AccountService) context.getBean("accountService");
    }

    @After
    public void after(){
		context.close();
    }
    //测试添加
    @Test
    public void save(){
        Account account = new Account();
        account.setName("古力娜扎");
        account.setMoney(1600d);

        accountService.save(account);
    }

    //测试查询
    @Test
    public void findById(){
        Account account = accountService.findById(4);
        System.out.println(account);
    }
    //测试查询所有
    @Test
    public void findAll(){
        。。。
    }
    //测试删除
    @Test
    public void delete(){
        accountService.delete(6);
    }
    //测试更新
    @Test
    public void update(){
		。。。
    }
}
```

**jdbc**配置文件

```
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql:///spring_db?characterEncoding=utf-8
jdbc.username=root
jdbc.password=123456
```

**抽取**jdbc配置文件

```
applicationContext.xml加载jdbc.properties配置文件获得连接信息。
首先，需要引入context命名空间和约束路径：

* 命名空间： 
	xmlns:context="http://www.springframework.org/schema/context" 
* 约束路径： 
	http://www.springframework.org/schema/context 	
    http://www.springframework.org/schema/context/spring-context.xsd
    
    其实也就是将xmlns="http://www.springframework.org/schema/beans"复制，
    然后将beans替换成context
    约束路径：也是将beans替换成context
    	http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        
* 然后通过context:property-placeholder标签引入配置文件
	<!--
        location="classpath:文件名":当前找文件的时候，会定义到当前项目编译过的classes目录下进行查找
    -->
	<context:property-placeholder location="classpath:文件名">
	</context:property-placeholder>
```

applicationContext.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
         http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd
">
	<context:property-placeholder location="classpath:jdbc.properties">
    </context:property-placeholder>
    <!--1.account-->
    <bean id="account" class="com.lagou.domain.Account"></bean>
    <!--4.dataSource-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driverClassName}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    ...
</beans>
```



### 整合DbUtils小结

```
* DataSource的创建权交由Spring容器去完成 

* QueryRunner的创建权交由Spring容器去完成，使用构造方法传递DataSource 

* Spring容器加载properties文件 
	<context:property-placeholder location="classpath:xx.properties"/> 
	<property name="" value="${key}"/>
```





## Spring注解开发

Spring是轻代码而重配置的框架，配置比较繁重，影响开发效率，所以**注解开发是一种趋势**，注解代替xml配置文件**可以简化配置，提高开发效率**。

### Spring常用注解

**介绍**

Spring常用注解主要是替代 <bean> 的配置

| 注解                       | 说明                                                       |
| -------------------------- | ---------------------------------------------------------- |
| @Component                 | 使用在类上用于实例化Bean                                   |
| @Controller                | 使用在web层类上用于实例化Bean                              |
| @Service                   | 使用在service层类上用于实例化Bean                          |
| @Repository                | 使用在dao层类上用于实例化Bean                              |
| 前面4层相当于配置了[Bean]  | 生成类的实例对象存到IOC容器中                              |
| @Autowried                 | 使用在字段上用于根据类型依赖注入                           |
| @Qualifier                 | 结合@Autowried一起使用，根据名称进行依赖注入，不能单独使用 |
| @Resource                  | 相当于@Autowried+@Qualifier，按照名称进行注入              |
| @Value                     | 注入普通属性，前三个是注入引用类型的，相当于【ref】        |
| 这四个相当于配置[property] | 要进行依赖注入                                             |
| @Scope                     | 标注Bean的作用范围                                         |
| @PostConstruct             | 使用在方法上标注该方法是Bean的初始化方法                   |
| @PreDestroy                | 使用在方法上标注该方法是Bean的销毁方法                     |

使用注解进行开发时，需要在applicationContext.xml中配置组件扫描，作用是指定哪个包及其子包下的Bean需要进行扫描以便识别使用注解配置的类、字段和方法。

```
<!--注解的组件扫描--> 
<context:component-scan base-package="com.lagou"></context:component-scan>
```



### 实现

#### 1）Bean实例化（IOC）

初步通过注解配置AccountServiceImpl

```
	<bean id="accountService" class="com.lagou.service.impl.AccountServiceImpl">
        <property name="accountDao" ref="accountDao"/>
    </bean>
```

```
/**
 * @Service(value = "accountService")
 *              //相当于配置了bean标签
 *             // id属性  (value = "accountService"),
 *						如果没有写value属性值，Bean的id为：类名首字母小写
 *                      当属性值只有一个，且为value的时候，value = 可以省略
 *             // class属性(AccountServiceImpl相当于直接声明了class属性)
 */
@Service("accountService")
public class AccountServiceImpl implements AccountService {
    /**
     * 采用注解方式，底层采用的是反射方式，就不用set方法注入了
     * @Autowried   使用在字段上用于根据类型依赖注入
     *  @Qualifier("accountDao")结合@Autowried一起使用，根据名称进行依赖注入，不能单独使用
     */
    @Autowired
    @Qualifier("accountDao")
    private AccountDao accountDao;

    @Override
    public List<Account> findAll() {
        return accountDao.findAll();
    }

    @Override
    public Account findById(Integer id) {
        return accountDao.findById(id);
    }

    @Override
    public void save(Account account) {
        accountDao.save(account);
    }

    @Override
    public void update(Account account) {
        accountDao.update(account);
    }

    @Override
    public void delete(Integer id) {
        accountDao.delete(id);
    }
}
```

#### 2）属性依赖注入（DI）

使用@Autowired或者@Autowired+@Qulifier或者@Resource进行userDao的注入

**@Value**

使用@Value进行字符串的注入，结合SPEL表达式获得配置参数

```
	/**
     * 采用注解方式，底层采用的是反射方式，就不用set方法注入了
     * @Autowried   使用在字段上用于根据类型依赖注入
     *  @Qualifier("accountDao")结合@Autowried一起使用，根据名称进行依赖注入，不能单独使用
     */
    @Autowired
    @Qualifier("accountDao")
    private AccountDao accountDao;

    @Value("普通属性注入")
    private String str;
	
	/**
	 *因为配置了jdbc.properties文件，所以可以通过el表达式来获取指定值
	 */
    @Value("${jdbc.driverClassName}")
    private String driver;
```

JDK11以后完全移除了javax扩展导致不能使用@resource，@PostConstruct以及@PreDestroy注解

```
需要maven引入依赖 
<dependency> 
	<groupId>javax.annotation</groupId> 
	<artifactId>javax.annotation-api</artifactId> 
	<version>1.3.2</version> 
</dependency>
```

#### 3）@Scope

使用@Scope标注Bean的范围

```
<bean scope=""/> 
```

```
@Service 
@Scope("singleton") 
public class UserServiceImpl implements UserService {.。。}
```

#### 4）Bean生命周期

```
<bean init-method="init" destroy-method="destory" />
```

使用@PostConstruct标注初始化方法，使用@PreDestroy标注销毁方法

```
    @PostConstruct
    public void init(){
        System.out.println("初始化方法....");
    }

    @PreDestroy
    public void destory(){
        System.out.println("销毁方法....");
    }
```

### Spring常用注解整合DbUtils

**步骤分析**

```
1. 拷贝xml配置项目，改为注解配置项目 
2. 修改AccountDaoImpl实现类 
3. 修改AccountServiceImpl实现类 
4. 修改spring核心配置文件 
5. 编写测试代码
```

1）拷贝xml配置项目，改为常用注解配置项目

过程略....

2）修改AccountDaoImpl实现类

```
@Repository("accountDao")
public class AccountDaoImpl  implements AccountDao {

    @Autowired
    private QueryRunner queryRunner;
    
    。。。
}
```

3）修改AccountServiceImpl实现类

```
@Service("accountService")
public class AccountServiceImpl implements AccountService {
   
    @Autowired
    @Qualifier("accountDao")
    private AccountDao accountDao;
    。。。
}
```

4）修改spring核心配置文件

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
         http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd
">

    <!--注解的组件扫描-->
    <context:component-scan base-package="com.lagou"></context:component-scan>

    <!--
        location="classpath:":当前找文件的时候，会定义到当前项目编译过的classes目录下进行查找
    -->
    <context:property-placeholder location="classpath:jdbc.properties">
    </context:property-placeholder>
    
    
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driverClassName}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    
    <bean id="queryRunner" class="org.apache.commons.dbutils.QueryRunner">
        <constructor-arg name="ds" ref="dataSource"/>
    </bean>
</beans>
```

5）编写测试代码

略



### Spring新注解

使用上面的注解还不能全部替代xml配置文件，还需要使用注解替代的配置如下：

```
* 非自定义的Bean的配置：<bean> 
* 加载properties文件的配置：<context:property-placeholder> 
* 组件扫描的配置：<context:component-scan> 
* 引入其他文件：<import>
```

| 注解            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| @Configuration  | 用于指定当前类是一个Sprin配置类，当创建容器时会从该类上加载注解 |
| @Bean           | 使用在方法上，标注将该方法的返回值存储到spring容器中         |
| @PropertySource | 用于加载properties文件中的配置                               |
| @ComponentScan  | 用于指定spring在初始化容器时要扫描的包                       |
| @Import         | 用于导入其他配置类                                           |

1）编写Spring核心配置类

```
@Configuration
@ComponentScan("com.lagou")
@PropertySource("classpath:jdbc.properties")
public class SpringConfig {

	@Value("${jdbc.driverClassName}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    @Bean("daraSource")
    public DataSource getDataSource(){
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName(driver);
        druidDataSource.setUrl(url);
        druidDataSource.setUsername(username);
        druidDataSource.setPassword(password);
        return druidDataSource;
    }
    
    @Bean("queryRunner")
    public QueryRunner getQueryRunner(@Autowired DataSource dataSource){
        QueryRunner queryRunner = new QueryRunner(dataSource);
        return queryRunner;
    }
}

```

2）编写测试代码

```
public class AccountServiceTest {
    //private ClassPathXmlApplicationContext context;
    private AnnotationConfigApplicationContext context；
    private AccountService accountService;
	
	//测试纯注解整合DBUtils
    @Before
    public void before(){
        /*context = new ClassPathXmlApplicationContext("applicationContext.xml");
        accountService = (AccountService) context.getBean("accountService");*/
        
        //当前改成了纯注解形式
        context = new AnnotationConfigApplicationContext(SpringConfig.class);
        accountService = (AccountService) context.getBean("accountService");
    }

    @After
    public void after(){
        context.close();
    }
    //测试查询所有
    @Test
    public void findAll(){
        List<Account> list = accountService.findAll();
        for (Account account : list) {
            System.out.println(account);
        }
    }
    。。。
}
```

**优化**

**编写数据库配置信息类，将核心配置类文件拆分**

就是将数据库相关的语句抽取到DataSourceConfig类中

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

    @Bean("daraSource")
    public DataSource getDataSource(){
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName(driver);
        druidDataSource.setUrl(url);
        druidDataSource.setUsername(username);
        druidDataSource.setPassword(password);
        return druidDataSource;
    }
}
```

然后将该类import到核心配置类

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

##  Spring整合Junit

### 普通Junit测试问题

在普通的测试类中，需要开发者手动加载配置文件并创建Spring容器，然后通过Spring相关API获得

Bean实例；如果不这么做，那么无法从容器中获得对象。

```
ApplicationContext applicationContext = 
	new ClassPathXmlApplicationContext("applicationContext.xml");
    
AccountService accountService = applicationContext.getBean(AccountService.class);
```

我们可以让SpringJunit负责创建Spring容器来简化这个操作，开发者可以直接在测试类注入Bean实

例；但是需要将配置文件的名称告诉它。

### Spring整合Junit

**步骤分析**

```
1. 导入spring集成Junit的坐标 
2. 使用@Runwith注解替换原来的运行器 
3. 使用@ContextConfiguration指定配置文件或配置类 
4. 使用@Autowired注入需要测试的对象 
5. 创建测试方法进行测试
```

1）导入spring集成Junit的坐标

```
<!--此处需要注意的是，spring5 及以上版本要求 junit 的版本必须是 4.12 及以上--> 
<dependency> 
	<groupId>org.springframework</groupId> 
	<artifactId>spring-test</artifactId> 
	<version>5.1.5.RELEASE</version> 
</dependency> 
<dependency> 
	<groupId>junit</groupId> 
	<artifactId>junit</artifactId> 
	<version>4.12</version> 
	<scope>test</scope> 
</dependency>
```

2）使用@Runwith注解替换原来的运行器

```
/**
 * @RunWith指定Junit的运行环境
 * SpringJUnit4ClassRunner是spring提供的作为junit运行环境的一个类
 */
@RunWith(SpringJUnit4ClassRunner.class) 
public class SpringJunitTest { }
```

3）使用@ContextConfiguration指定配置文件或配置类

```
@RunWith(SpringJUnit4ClassRunner.class) 
/**@ContextConfiguration(value = {"classpath:applicationContext.xml"}) 
  *加载spring 核心配置文件
  */
@ContextConfiguration(classes = {SpringConfig.class}) // 加载spring核心配置类 public class SpringJunitTest { }
```

4）使用@Autowired注入需要测试的对象

```
	//不需要ClassPathXmlApplicationContext加载了
	//private ClassPathXmlApplicationContext context;
	
    @Autowired
    private AccountService accountService;
	
    @Before
    public void before(){}

    @After
    public void after(){}
```

测试  略