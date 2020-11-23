# SpringMVC入门

## 一、SpringMVC简介

### 1.1 MVC模式

MVC是软件工程中的一种软件架构模式，它是一种分离业务逻辑与显示界面的开发思想。

```
* M（model）模型：处理业务逻辑，封装实体 
* V（view） 视图：展示内容 
* C（controller）控制器：负责调度分发（1.接收请求、2.调用模型、3.转发到视图）
```

### 1.2 SpringMVC概述

SpringMVC 是一种基于 Java 的实现 MVC 设计模式的轻量级 Web 框架，属于SpringFrameWork 的后续产品，已经融合在 Spring Web Flow 中。

SpringMVC 已经成为目前最主流的MVC框架之一，并且随着Spring3.0 的发布，全面超越 Struts2，成为最优秀的 MVC 框架。它通过一套注解，让一个简单的 Java 类成为处理请求的控制器，而无须实现任何接口。同时它还支持 RESTful 编程风格的请求。

### 总结

SpringMVC的框架就是封装了原来Servlet中的共有行为；例如：参数封装，视图转发等。

### 1.3 SpringMVC快速入门

**需求**

客户端发起请求，服务器接收请求，执行逻辑并进行视图跳转。

**步骤分析**

```
1. 创建web项目，导入SpringMVC相关坐标 
2. 配置SpringMVC前端控制器 DispathcerServlet 
3. 编写Controller类和视图页面 
4. 使用注解配置Controller类中业务方法的映射地址 
5. 配置SpringMVC核心文件 spring-mvc.xml
```

1）创建web项目，导入SpringMVC相关坐标

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
        <!--springMVC坐标-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <!--servlet坐标-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <!--jsp坐标-->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
```

2）配置SpringMVC前端控制器 DispathcerServlet 

web.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--springMVC的前端控制器：DispatcherServlet-->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <!-- /会匹配到所有的访问路径，但是不会匹配到象*.jsp这样的方法url  /user  /add  /update  /a.jsp不会匹配-->
        <!-- /和/*的区别

        -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
</web-app>
```

3）编写Controller类和视图页面 

UserController.java

```

public class UserController {

    public String quic(){
        //业务逻辑
        System.out.println("springMVC入门成功。。。。");
        //视图跳转
        return "/WEB-INF/pages/success.jsp";
    }
}
```

/WEB-INF/pages/ success.jsp

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>success</title>
</head>
<body>
<h3>success请求成功</h3>
</body>
</html>
```

4）使用注解配置Controller类中业务方法的映射地址 

UserController.java

```
@Controller
public class UserController {

    @RequestMapping("/quick")
    public String quic(){
        //业务逻辑
        System.out.println("springMVC入门成功。。。。");
        //视图跳转
        return "/WEB-INF/pages/success.jsp";
    }
}
```

5）配置SpringMVC核心文件 spring-mvc.xml

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
    <!--配置注解扫描-->
    <context:component-scan base-package="com.lagou.controller"/>
</beans>
```

这时，我们添加了注解扫描，但是我们要借助前面写的web.xml来加载spring-mvc.xml文件，因此添加

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--springMVC的前端控制器：DispatcherServlet-->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--加载spring-mvc.xml-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <!--再应用启动的时候，就完成servletd的实例化以及初始化操作-->
        <load-on-startup>2</load-on-startup>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <!-- /会匹配到所有的访问路径，但是不会匹配到象*.jsp这样的方法url  /user  /add  /update  /a.jsp不会匹配-->
        <!-- /和/*的区别

        -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```



### 1.4 web工程执行流程

```
1.输入请求地址
http://localhost/MVC_quickstart/quick

2.tomcat服务器、	
	2.1接受到请求后，解析请求资源地址
	2.2创建请求对象request
	2.3创建响应对象response
	2.4调用目标资源
		因为该地址会被DispatcherServlet所拦截，因此会将该请求交给【前端控制器】处理
		
		3.前端控制器
			3.1解析映射地址，找到对应的处理器(处理器指的是UserController以及里面的quick方法)
			3.2调用对应的组件进行解析，然后匹配到当前配置的@RequestMapping("/quick"),让该方法运行
			
	2.5获取response中的对象，组装成http相应返回客户端

```



### 1.5知识小结

```
* SpringMVC是对MVC设计模式的一种实现，属于轻量级的WEB框架。

* SpringMVC的开发步骤： 
	1.创建web项目，导入SpringMVC相关坐标 
	2.配置SpringMVC前端控制器 DispathcerServlet 
	3.编写Controller类和视图页面 
	4.使用注解配置Controller类中业务方法的映射地址 
	5.配置SpringMVC核心文件 spring-mvc.xml
```

## **二** SpringMVC组件概述

### 2.1 SpringMVC的执行流程

```
1. 用户发送请求至前端控制器DispatcherServlet。

2. DispatcherServlet收到请求调用HandlerMapping处理器映射器。

3. 处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器对象及处理器拦截器(如 果有则生成)一并返回给DispatcherServlet。 

4. DispatcherServlet调用HandlerAdapter处理器适配器。

5. HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)。 

6. Controller执行完成返回ModelAndView。 

7. HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。 

8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器。 

9. ViewReslover解析后返回具体View。 

10. DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。

11. DispatcherServlet将渲染后的视图响应响应用户。
```

### 2.2 SpringMVC组件解析

```
1. 前端控制器：DispatcherServlet 用户请求到达前端控制器，它就相当于 MVC 模式中的 C，DispatcherServlet 是整个流程控制的 中心，由它调用其它组件处理用户的请求，DispatcherServlet 的存在降低了组件之间的耦合性。 

2. 处理器映射器：HandlerMapping HandlerMapping 负责根据用户请求找到 Handler 即处理器，SpringMVC 提供了不同的映射器 实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。 

3. 处理器适配器：HandlerAdapter 通过 HandlerAdapter 对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型 的处理器进行执行。 

4. 处理器：Handler【**开发者编写**】 它就是我们开发中要编写的具体业务控制器。由 DispatcherServlet 把用户请求转发到 Handler。由Handler 对具体的用户请求进行处理
。 
5. 视图解析器：ViewResolver View Resolver 负责将处理结果生成 View 视图，View Resolver 首先根据逻辑视图名解析成物 理视图名，即具体的页面地址，再生成 View 视图对象，最后对 View 进行渲染将处理结果通过页面展示给 用户。 

6. 视图：View 【**开发者编写**】 SpringMVC 框架提供了很多的 View 视图类型的支持，包括：jstlView、freemarkerView、 pdfView等。最常用的视图就是 jsp。一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展 示给用户，需要由程序员根据业务需求开发具体的页面。

***笔试题：springmvc中的三大组件是什么？

处理器映射器，处理器适配器、视图解析器
```

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
    <!--配置注解扫描-->
    <context:component-scan base-package="com.lagou.controller"/>

    <!--处理器映射器-处理器适配器 进行了功能的增强：支持json的读写-->
    <mvc:annotation-driven/>

    <!--视图解析器:ViewResolver-->
    <bean id="viewResolver" 
    class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--当配置了前缀和后缀之后，视图跳转返回的就是逻辑视图名
                例如：success：解析逻辑视图名，解析成具体的物理文件地址，前缀+success+后缀
            name="prefix"  前缀
            name="suffix"   后缀
        -->
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

### 2.3 SpringMVC注解解析

#### @Controller

SpringMVC基于Spring容器，所以在进行SpringMVC操作时，需要将Controller存储到Spring容器

中，如果使用@Controller注解标注的话，就需要使用：

```
<!--配置注解扫描--> 
<context:component-scan base-package="com.lagou.controller"/>
```

#### @RequestMapping

```
* 作用：
	用于建立请求 URL 和处理请求方法之间的对应关系 
	
* 位置：
	1.类上：请求URL的第一级访问目录。此处不写的话，就相当于应用的根目录。写的话需要以/开头。 
		它出现的目的是为了使我们的URL可以按照模块化管理: 
			用户模块
				/user/add 
				/user/update 
				/user/delete 
				... 
			账户模块
				/account/add 
				/account/update 
				/account/delete 
	2.方法上：请求URL的第二级访问目录，和一级目录组成一个完整的 URL 路径。 

* 属性：
	1.value：用于指定请求的URL。它和path属性的作用是一样的 
	2.method：用来限定请求的方式 
	3.params：用来限定请求参数的条件 
		例如：params={"accountName"} 表示请求参数中必须有accountName 
			 pramss={"money!100"} 表示请求参数中money不能是100
```

```
@Controller
@RequestMapping("/user") //一级访问目录
public class UserController {

    /**
     *path:作用等同于value，同样是设置方法的映射地址
     * method：用来限定请求的方式，RequestMethod.POST：只能以post的请求方式访问该方法，其他请求方式会报错
     * params:用来限定请求参数的条件 params={"accountName"}    表示请求参数中必须有accountName
     */
    @RequestMapping(value = "/quick",method = RequestMethod.GET, params={"accountName"})   //二级访问目录
    public String quick1(){
        //业务逻辑
        System.out.println("springMVC入门成功。。。。");

        //视图跳转  逻辑视图名
        return "success";
    }
} 
```



### 2.4知识小结

```
* SpringMVC的三大组件 
	处理器映射器：HandlerMapping 
	处理器适配器：HandlerAdapter 
	视图解析器：View Resolver 
	
* 开发者编写 
	处理器：Handler 
	视图：View
```



## 三、SpringMVC的请求

### 3.1请求参数类型介绍

```
客户端请求参数的格式是： name=value&name=value……
服务器要获取请求的参数的时候要进行类型转换，有时还需要进行数据的封装

SpringMVC可以接收如下类型的参数：

    基本类型参数
    对象类型参数
    数组类型参数
    集合类型参数
```

### 3.2获取基本类型参数

Controller中的业务方法的参数名称要与请求参数的name一致，参数值会自动映射匹配。并且能自动做类型转换；自动的类型转换是指从String向其他类型的转换。

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--${pageContext.request.contextPath}动态的来获取当前项目的路径：MVC_quickstart
        a标签的请求方式：get请求
    --%>
    <a href="${pageContext.request.contextPath}/user/simpleParam?id=1&userName=马尔扎哈">
        基本参数类型
    </a>
    <hr>
</body>
</html>
```

```
/**
     * 获取基本类型参数
     */
    @RequestMapping("/simpleParam")   //二级访问目录
    public String simpleParam(Integer id,String userName){
        //业务逻辑
        System.out.println(id);
        System.out.println(userName);

        //视图跳转  逻辑视图名
        return "success";
    }
```

### 3.3获取对象类型参数

Controller中的业务方法参数的POJO属性名与请求参数的name一致，参数值会自动映射匹配。

```
<body>
    <%--${pageContext.request.contextPath}动态的来获取当前项目的路径：MVC_quickstart
        a标签的请求方式：get请求
    --%>
    <a href="${pageContext.request.contextPath}/user/simpleParam?id=1&userName=马尔扎哈">
        基本参数类型
    </a>
    <hr>
    <%--form表单  表单的提交的请求方式为post类型--%>
    <form action="${pageContext.request.contextPath}/user/pojoParam" method="post">
        编号:<input type="text" name="id"><br>
        用户名:<input type="text" name="userName"><br>
        <input type="submit" value="对象类型">
    </form>
</body>
```

```
package com.lagou.domain;

public class User {
    private Integer id;
    private String userName;
	...
}
```

```
	/**
     * 获取对象类型参数
     */
    @RequestMapping("/pojoParam")   //二级访问目录
    public String pojoParam(User user){
        //业务逻辑
        System.out.println(user);

        //视图跳转  逻辑视图名
        return "success";
    }
```

但是如果userName为中文，**post请求会出现乱码，get不会出现乱码问题**，因此要设置过滤器进行编码的过滤

### 3.4中文乱码过滤器

当post请求时，数据会出现乱码，我们可以设置一个过滤器来进行编码的过滤。

**web.xml**内加入过滤器配置

```
<!--中文乱码过滤器-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>
        	org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <!--设置初始化参数
            因为要通过编码方式设置编码来解决乱码问题，所以要指定编码方式
        -->
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    
    <filter-mapping>
        <!-- /* 拦截所有-->
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```



### 3.5获取数组类型参数

Controller中的业务方法数组名称与请求参数的name一致，参数值会自动映射匹配。

```
	<%--演示获取数组类型获取参数--%>
    <form action="${pageContext.request.contextPath}/user/arrayParam" method="post">
        编号:<input type="checkbox" name="ids" value="1">1<br>
        编号:<input type="checkbox" name="ids" value="2">2<br>
        编号:<input type="checkbox" name="ids" value="3">3<br>
        编号:<input type="checkbox" name="ids" value="4">4<br>

        <input type="submit" value="对象类型">
    </form>
```

```
	/**
     * 获取集合类型参数
     */
    @RequestMapping("/arrayParam")   //二级访问目录
    public String arrayParam(Integer[] ids){
        //业务逻辑
        System.out.println(Arrays.toString(ids));

        //视图跳转  逻辑视图名
        return "success";
    }
```

### 3.6获取集合（复杂）类型参数

获得集合参数时，要将集合参数包装到一个POJO中才可以。

```
	<%--演示获取集合（复杂）类型获取参数--%>
    <form action="${pageContext.request.contextPath}/user/queryParam" method="post">
        搜索关键字:<input type="text" name="keyword"><br>
        user对象:<input type="text" name="user.id" placeholder="编号"><br>
        <input type="text" name="user.userName" placeholder="姓名"><br>
        list集合: <br>
        第一个元素:<input type="text" name="userList[0].id" placeholder="编号"><br>
        <input type="text" name="userList[0].userName" placeholder="姓名"><br>
        第二个元素:<input type="text" name="userList[1].id" placeholder="编号"><br>
        <input type="text" name="userList[1].userName" placeholder="姓名"><br>
        map集合:<br>
        第一个元素:<input type="text" name="userMap['u1'].id" placeholder="编号"><br>
        <input type="text" name="userMap['u1'].userName" placeholder="姓名"><br>
        第二个元素:<input type="text" name="userMap['u2'].id" placeholder="编号"><br>
        <input type="text" name="userMap['u2'].userName" placeholder="姓名"><br>

        <input type="submit" value="集合类型">
    </form>
```

```
public class QueryVo {
    private String  keyword;
    private User user;
    private List<User> userList;
    private Map<String,User> userMap;
}
```

```
	/**
     * 获取集合类型参数
     */
    @RequestMapping("/queryParam")   //二级访问目录
    public String queryParam(QueryVo queryVo){
        //业务逻辑
        System.out.println(queryVo);

        //视图跳转  逻辑视图名
        return "success";
    }
```

### 3.7自定义类型转换器

SpringMVC 默认已经提供了一些常用的类型转换器；例如：客户端提交的字符串转换成int型进行参

数设置，日期格式类型要求为：yyyy/MM/dd 不然的话会报错，对于特有的行为，SpringMVC提供了自

定义类型转换器方便开发者自定义处理。

```
	<%--自定义类型转换器--%>
    <form action="${pageContext.request.contextPath}/user/converterParam" method="post">
        生日:<input type="text" name="birthday"><br>
        <input type="submit" value="自定义类型转换器">
    </form>
```

```
public class DateConverter implements Converter<String, Date> {
    @Override
    public Date convert(String s) {
        Date date = null;
        if(s.contains("/")){
            SimpleDateFormat format = new SimpleDateFormat("yyyy/MM/dd");
            try {
                date = format.parse(s);
            } catch (ParseException e) {
                e.printStackTrace();
            }
        }else if(s.contains("-")){
            SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd");
            try {
                date = format.parse(s);
            } catch (ParseException e) {
                e.printStackTrace();
            }
        }
        return date;
    }
}
```

然后进行转换器配置，这里**property标签的下一层标签set标签的作用：**

```
	<!--处理器映射器-处理器适配器 进行了功能的增强：支持json的读写
		此处要添加conversion-service="conversionService"
	-->
    <mvc:annotation-driven conversion-service="conversionService"/>

	<!--自定义转换器配置-->
    <bean id="conversionService"
    class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <bean class="com.lagou.converter.DateConverter"></bean>
            </set>
        </property>
    </bean>
```



```
	/**
     * 自定义类型转换器
     */
    @RequestMapping("/converterParam")   //二级访问目录
    public String converterParam(Date birthday){
        //业务逻辑
        System.out.println(birthday);

        //视图跳转  逻辑视图名
        return "success";
    }
```



### 3.8相关注解

**@RequestParam**

当请求的参数name名称与Controller的业务方法参数名称不一致时，就需要通过@RequestParam注解显示的绑定

```
	<%--演示@RequestParam--%>
    <a href="${pageContext.request.contextPath}/user/findByPage?pageNo=2">
        分页查询
    </a>
```

```
	/**
     * 演示@RequestParam
     * @RequestParam注解：
     *      name:匹配页面传递参数名称
     *      defaultValue：设置参数的默认值，如果页面没有传递该参数，那么该参数就是默认值
     *      required：设置是否必须传递该参数，默认值为true，如果设置了默认值，那么该值自动改为false
     */
    @RequestMapping("/findByPage")   //二级访问目录
    public String findByPage(
    		@RequestParam(name = "pageNo",defaultValue = "1") Integer pageNum
            ,@RequestParam(defaultValue = "5")Integer pageSize){
        //业务逻辑
        System.out.println(pageNum);
        System.out.println(pageSize);

        //视图跳转  逻辑视图名
        return "success";
    }
```

**@RequestHeader**

获取请求头的数据。

```
	/**
     * @RequestHeader的使用
     *获取请求头的数据。
     */
    @RequestMapping("/requestHead")
    public String requestHead(@RequestHeader("cookie") String cookie){
        System.out.println(cookie);
        return "success";
    }

```



**@CookieValue**

获取cookie中的数据。

```
    /**
     * @CookieValue的使用
     *获取cookie中的数据。
     */
    @RequestMapping("/cookieValue")
    public String cookieValue(@CookieValue("JSESSIONID") String jsessionId){
        System.out.println(jsessionId);
        return "success";
    }
```



### 3.9获取Servlet相关API

SpringMVC支持使用原始ServletAPI对象作为控制器方法的参数进行注入，常用的对象如下：

```
	/**
     * 原始servletAPI的获取
     */
    @RequestMapping("/servletAPI")
    public String servletAPI(HttpServletRequest request, HttpServletResponse response,
                             HttpSession session){
        System.out.println(request);
        System.out.println(response);
        System.out.println(session);
        return "success";
    }
```

## 四、SpringMVC的响应

### 4.1 SpringMVC响应方式介绍

**页面跳转**

1)返回字符串逻辑视图

2) void原始ServletAPI

3)ModelAndView

**返回数据**

1) 直接返回字符串数据

2)将对象或集合转为json返回（任务二演示）

### 4.2返回字符串逻辑视图

直接返回字符串：此种方式会将返回的字符串与视图解析器的前后缀拼接后跳转到指定页面

```
@RequestMapping("/returnString") 
public String returnString() { 
	return "success"; 
}
```

### 4.3 void原始ServletAPI

我们可以通过request、response对象实现响应

```
	/**
     * 通过原始servletAPI进行页面跳转
     */
    @RequestMapping("/returnVoid")
    public void returnVoid(HttpServletRequest request, 
    	HttpServletResponse response) throws ServletException, IOException {

        //借助request对象完成请求转发一次请求     一次请求
        request.getRequestDispatcher("/WEB-INF/pages/success.jsp").
        forward(request,response);

        //借助response对象完成重定向 两次请求
        // WEB-INF：安全目录：不允许外部请求直接访问该目录资源，只可以进行服务器每部转发
        //response.sendRedirect(request.getContextPath()+"/index.jsp");
    }
```

### 4.4转发和重定向

企业开发我们一般使用返回字符串逻辑视图实现页面的跳转，这种方式其实就是请求转发。

**我们也可以写成：forward转发**

如果用了forward：则路径必须写成实际视图url，不能写逻辑视图。它相当于：

```
request.getRequestDispatcher("url").forward(request,response)
```

使用请求转发，既可以转发到jsp，也可以转发到其他的控制器方法。

```
	/**
     * 使用forward关键字进行请求转发
     */
    @RequestMapping("/forward")
    public String forward(Model model){

        //还想再模型中设置一些值   怎么做？
        //然后再页面中使用el表达式取出值 ${userName}
        model.addAttribute("userName","拉钩招聘");

        //使用请求转发，既可以转发到jsp，也可以转发到其他的控制器方法
        return "forward:/WEB-INF/pages/success.jsp";
    }
```

**Redirect重定向**

我们可以不写虚拟目录，springMVC框架会自动拼接，并且将Model中的数据拼接到url地址上

```
/**
     * 使用redirect关键字进行请求转发
     */
    @RequestMapping("/redirect")
    public String redirect(Model model){

        //底层使用的时request.setAttribute    域范围：一次请求
        //但是重定向是两次请求，因此这个userName的值取不出来
        model.addAttribute("userName","拉钩教育");
        return "redirect:index.jsp";
    }
```



### 4.5 ModelAndView

#### 4.4.1方式一

在Controller中方法创建并返回ModelAndView对象，并且设置视图名称

```
	/**
     * modelAndView进行页面跳转：方式一
     */
    @RequestMapping("/returnModelAndView1")
    public ModelAndView returnModelAndView1(){
        /**
         * model:模型 作用：封装数据
         * View：视图  作用：展示数据
         */
        ModelAndView modelAndView = new ModelAndView();
        //设置模型数据
        modelAndView.addObject("userName","modelAndView进行页面跳转：方式一");

        //设置视图名称    视图解析器解析modelAndView 会拼接前缀和后缀
        modelAndView.setViewName("success");
        return modelAndView;
    }
```

#### 4.4.2方式二

在Controller中方法形参上直接声明ModelAndView，无需在方法中自己创建，在方法中直接使用该对象设置视图，同样可以跳转页面

```
	/**
     * modelAndView进行页面跳转：方式二
     */
    @RequestMapping("/returnModelAndView2")
    public ModelAndView returnModelAndView2(ModelAndView modelAndView){
        /**
         * model:模型 作用：封装数据
         * View：视图  作用：展示数据
         */
        //设置模型数据
        modelAndView.addObject("userName","modelAndView进行页面跳转：方式二");

        //设置视图名称    视图解析器解析modelAndView 会拼接前缀和后缀
        modelAndView.setViewName("success");
        return modelAndView;
    }
```

### 4.6 @SessionAttributes

如果在多个请求之间共用数据，则可以在控制器类上标注一个 @SessionAttributes,配置需要在

session中存放的数据范围，Spring MVC将存放在model中对应的数据暂存到 HttpSession 中。

**注意：@SessionAttributes只能定义在类上**

```
@SessionAttributes("userName") 
//向request域中(model)中存入key为userName时，会同步到session域中
```

```
@Controller
@RequestMapping("/user") //一级访问目录
@SessionAttributes("userName") 
//向request域中(model)中存入key为userName时，会同步到session域中
public class UserController {
	/**
     * 使用forward关键字进行请求转发
     */
    @RequestMapping("/forward")
    public String forward(Model model){

        //还想再模型中设置一些值   怎么做？
        //然后再页面中使用el表达式取出值 ${userName}
        model.addAttribute("userName","拉钩招聘");

        //使用请求转发，既可以转发到jsp，也可以转发到其他的控制器方法
        return "forward:/WEB-INF/pages/success.jsp";
    }
	/**
     * @SessionAttributes的效果：多个请求之间共享数据
     */
    @RequestMapping("/returnString")
    public String returnString(){
        return "success";
    }

}
```



### 4.7知识小结

```
* 页面跳转采用返回字符串逻辑视图 
	1.forward转发 
		可以通过Model向request域中设置数据 
		
	2.redirect重定向 
		直接写资源路径即可，虚拟目录springMVC框架自动完成拼接 
		
* 数据存储到request域中 
	Model model 
		model.addAttribute("username", "子慕");
```

## 五 静态资源访问的开启

```
当有静态资源需要加载时，比如jquery文件，通过谷歌开发者工具抓包发现，没有加载到jquery文件，原因是SpringMVC的前端控制器DispatcherServlet的url-pattern配置的是 /（缺省）,代表对所有的静态资源都进行处理操作，这样就不会执行Tomcat内置的DefaultServlet处理，我们可以通过以下两种方式指定放行静态资源：
```

**方式一**

```
<!--在springmvc配置文件中指定放行资源--> 
<mvc:resources mapping="/js/**" location="/js/"/> 
<mvc:resources mapping="/css/**" location="/css/"/> 
<mvc:resources mapping="/img/**" location="/img/"/>
```

**方式二**

```
<!--在springmvc配置文件中开启DefaultServlet处理静态资源--> 
<mvc:default-servlet-handler/>
```





# springmvc进阶



## 一、ajax异步交互

Springmvc默认用MappingJackson2HttpMessageConverter对json数据进行转换，需要加入

jackson的包；同时使用 <mvc:annotation-driven />

**坐标引入**

```
<dependency> 
	<groupId>com.fasterxml.jackson.core</groupId> 
	<artifactId>jackson-databind</artifactId>
    <version>2.9.8</version> 
</dependency> 
<dependency> 
	<groupId>com.fasterxml.jackson.core</groupId> 
	<artifactId>jackson-core</artifactId> 
	<version>2.9.8</version> 
</dependency> 
<dependency> 
	<groupId>com.fasterxml.jackson.core</groupId> 
	<artifactId>jackson-annotations</artifactId> 
	<version>2.9.0</version>
</dependency>
```



### 1.1 @RequestBody

该注解用于Controller的方法的形参声明，当使用ajax提交并指定contentType为json形式时，通过

HttpMessageConverter接口转换为对应的POJO对象。

```
<body>
<script src="${pageContext.request.contextPath}/js/jquery-3.5.1.js"></script>
    <%--ajax异步交互--%>
    <button id="btn1">ajax异步提交</button>

    <script>
        $("#btn1").click(function(){
            let url = '${pageContext.request.contextPath}/user/ajaxRequest';
            let data = '[{"id":1,"userName":"迪丽热巴"},{"id":2,"userName":"古力娜扎"}]';

            $.ajax({
                type:'post',
                url:url,
                data:data,
                contentType:'application/json;charset=utf-8',
                success:function(resp){
                    alert(resp);
                }
            })
        })
    </script>

</body>
```

```
	/**
     * ajax异步交互
     * 将传递过来的字符串转化为集合
     * “[{"id":1,"userName":"迪丽热巴"},{"id":2,"userName":"古力娜扎"}]”
     */
    @RequestMapping("/ajaxRequest")
    public void ajaxRequest(@RequestBody List<User> list){
        System.out.println(list);
    }
```

### 1.2 @ResponseBody

该注解用于将Controller的方法返回的对象，通过HttpMessageConverter接口转换为指定格式的数

据如：json,xml等，通过Response响应给客户端。

```
<body>
<script src="${pageContext.request.contextPath}/js/jquery-3.5.1.js"></script>
    <%--ajax异步交互--%>
    <button id="btn1">ajax异步提交</button>

    <script>
        $("#btn1").click(function(){
            let url = '${pageContext.request.contextPath}/user/ajaxRequest';
            let data = '[{"id":1,"userName":"迪丽热巴"},{"id":2,"userName":"古力娜扎"}]';

            $.ajax({
                type:'post',
                url:url,
                data:data,
                contentType:'application/json;charset=utf-8',
                success:function(resp){
                    alert(JSON.stringify(resp));
                }
            })
        })
    </script>

</body>
```

```
	/**
     * ajax异步交互
     * 将集合对象转换为JSON字符串的形式
     */
    @RequestMapping("/ajaxRequest")
    @ResponseBody
    public List<User> ajaxRequest(@RequestBody List<User> list){
        System.out.println(list);
        return list;
    }
```

## 二、RESTful

### 2.1什么是RESTful

Restful是一种软件架构风格、设计风格，而不是标准，只是提供了一组设计原则和约束条件。

主要用于客户端和服务器交互类的软件，基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存机制等。

Restful风格的请求是使用“url+请求方式”表示一次请求目的的，HTTP 协议里面四个表示操作方式的动词如下：

```
   	GET：读取（Read）
​   	POST：新建（Create）
​    	PUT：更新（Update）
​   	DELETE：删除（Delete）
```

 

| 客户端请求 | 原来风格URL地址     | RESTful风格URL地址[地址+请求方式] |
| ---------- | ------------------- | --------------------------------- |
| 查询所有   | /user/findAll       | GET /user                         |
| 根据ID查询 | /user/findById?id=1 | GET /user/{1}                     |
| 新增       | /user/save          | POST /user                        |
| 修改       | /user/update        | PUT /user                         |
| 删除       | /user/delete?id=1   | DELETE /user/{1}                  |

### 2.2代码实现

**@PathVariable**

用来接收RESTful风格请求地址中占位符的值

```
@Controller 
@RequestMapping("/restful")
public class RestfulController  {

    /**
     * 根据id进行查询
     * localhost:8080/项目名/restful/user/2+get请求方式
     *
     * @ResponseBody作用：
     *  如果返回的是集合或者对象类型，则将其转化为JSON字符串
     *  如果返回的是字符串的形式，则会直接相应，直接把响应体相应给浏览器，就不会再走视图解析器
     */
    @RequestMapping(value = "/user/{id}",method = RequestMethod.GET)
    @ResponseBody
    public String findById(@PathVariable Integer id){
        //调用service方法完成对id为2的这条记录的查询
        //问题：findById方法中怎么才能获取到resful编程风格中url中占位符的值
        return "findById:"+id;
    }

}
```

**@RestController**

RESTful风格多用于前后端分离项目开发，前端通过ajax与服务器进行异步交互，我们处理器通常返

回的是json数据所以使用@RestController来替代@Controller和@ResponseBody两个注解。

```
@RestController //组合注解：相当于@Controller+@ResponsBody
@RequestMapping("/restful")
public class RestfulController  {

    /**
     * 根据id进行查询
     * localhost:8080/项目名/restful/user/2+get请求方式
     *
     * @ResponseBody作用：
     *  如果返回的是集合或者对象类型，则将其转化为JSON字符串
     *  如果返回的是字符串的形式，则会直接相应，直接把响应体相应给浏览器，就不会再走视图解析器
     */
    @GetMapping("user/{id}")    //只能通过get请求，相当于下面一条注解
    //@RequestMapping(value = "/user/{id}",method = RequestMethod.GET)
    public String findById(@PathVariable Integer id){
        //调用service方法完成对id为2的这条记录的查询
        //问题：findById方法中怎么才能获取到resful编程风格中url中占位符的值
        return "findById:"+id;
    }
}
```

## 三 文件上传

### 3.1文件上传三要素

表单项 type="file"

表单的提交方式 method="POST"

表单的enctype属性是多部分表单形式 enctype=“multipart/form-data"

```

```

### 3.2文件上传原理

```
当form表单修改为多部分表单时，request.getParameter()将失效。

当form表单的enctype取值为 application/x-www-form-urlencoded 时，
​	form表单的正文内容格式是： name=value&name=value 

当form表单的enctype取值为 mutilpart/form-data 时，请求正文内容就变成多部分形式：
```

### 3.3 单文件上传

**步骤分析**

```
1. 导入fileupload和io坐标 
2. 配置文件上传解析器 
3. 编写文件上传代码
```

1）导入fileupload和io坐标 

```
<dependency> 
	<groupId>commons-fileupload</groupId> 
	<artifactId>commons-fileupload</artifactId> 
	<version>1.3.3</version>
</dependency> 
<dependency> 
	<groupId>commons-io</groupId> 
	<artifactId>commons-io</artifactId> 
	<version>2.6</version> 
</dependency>
```

2）配置文件上传解析器 

```
	<!--配置文件上传解析器-->
    <bean id="multipartResolver" 
    class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!--设定文件上传的最大值为5M：5*1024*1024-->
        <property name="maxUploadSize" value="5242880"></property>
        <!--设置文件上传时写入内存的最大值，如果小于这个参数不会生成临时文件，默认10240-->
        <property name="maxInMemorySize" value="40960"></property>
    </bean>
```

3）编写文件上传代码

```
@Controller
public class FileUploadController {

    /**
     * 单文件上传
     */
    @RequestMapping("/fileUpload")
    public String fileUpload(String userName, MultipartFile filePic) throws IOException {
        //获取表单提交参数,完成文件上传

        System.out.println(userName);

        //获取原始的文件上传名    a.txt abc
        String originalFilename = filePic.getOriginalFilename();


        filePic.transferTo(new File("E:/upload/"+originalFilename));
        return "success";
    }
}
```



### 3.4多文件上传

```
	<%--多文件上传--%>
    <form action="${pageContext.request.contextPath}/filesUpload" method="post" enctype="multipart/form-data">
        名称：<input type="text" name="userName"><br>
        文件1：<input type="file" name="filePic"> <br>
        文件2：<input type="file" name="filePic"> <br>
        <input type="submit" value="单文件上传">
    </form>
```

```
	/**
     * 多文件上传
     */
    @RequestMapping("/filesUpload")
    public String filesUpload(String userName, MultipartFile[] filePic) 
    throws IOException {
        //获取表单提交参数,完成文件上传

        System.out.println(userName);

        //获取原始的文件上传名    a.txt abc
        String originalFilename = null;
        for (MultipartFile multipartFile : filePic) {
            originalFilename = multipartFile.getOriginalFilename();
            multipartFile.transferTo(new File("E:/upload/"+originalFilename));
        }
        return "success";
    }
```

## 四 异常处理

### 4.1异常处理的思路

在Java中，对于异常的处理一般有两种方式:

```
	一种是当前方法捕获处理（try-catch），这种处理方式会造成业务代码和异常处理代码的耦合。

​	另一种是自己不处理，而是抛给调用者处理（throws），调用者再抛给它的调用者，也就是一直向上抛。
```

在这种方法的基础上，衍生出了SpringMVC的异常处理机制。

系统的dao、service、controller出现都通过throws Exception向上抛出，最后由springmvc前端控

制器交由异常处理器进行异常处理，如下图：

### 4.2自定义异常处理器

**步骤分析**

```
1. 创建异常处理器类实现HandlerExceptionResolver 
2. 配置异常处理器 
3. 编写异常页面 
4. 测试异常跳转
```

1）创建异常处理器类实现HandlerExceptionResolver 

```
public class GlobalExceptionResolver implements HandlerExceptionResolver {
    /**
     *Exception e   实际抛出的 异常对象
     */
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        //具体的异常处理   产生异常后，跳转到到一个最终的异常页面
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("error",e.getMessage());
        modelAndView.setViewName("error");

        return null;
    }
}
```

2）配置异常处理器 

注解形式

```
@Component
public class GlobalExceptionResolver implements HandlerExceptionResolver {}
```

xml形式

```
<!--配置自定义的异常处理器-->
    <bean id="globalExceptionResolver" class="com.lagou.exception.GlobalExceptionResolver"></bean>
```

3）编写异常页面 

```
<body>
    这是一个最终的异常显示页面
    ${error}
</body>
```

4）测试异常跳转

```
@Controller
public class ExceptionController {

    @RequestMapping("/testException")
    public String testException(){
        int i = 1/0;
        return "success";
    }
}
```



### 4.3 web的处理异常机制

在web.xml添加

```
	<!--处理404异常-->
    <error-page>
        <error-code>404</error-code>
        <location>/404.jsp</location>
    </error-page>
    <!--处理500异常-->
    <error-page>
        <error-code>500</error-code>
        <location>/500.jsp</location>
    </error-page>
```



## 五 拦截器

### 5.1拦截器（interceptor）的作用

Spring MVC 的**拦截器**类似于 Servlet 开发中的过滤器 Filter，用于对处理器进行**预处理**和**后处理**。

将拦截器按一定的顺序联结成一条链，这条链称为**拦截器链（****InterceptorChain****）**。在访问被拦截

的方法或字段时，拦截器链中的拦截器就会按其之前定义的顺序被调用。拦截器也是AOP思想的具体实

现。

### 5.2拦截器和过滤器区别

关于interceptor和filter的区别，如图所示：

| 区别 | 过滤器 | 拦截器 |
| ---- | ------ | ------ |
|      |        |        |
|      |        |        |

### 5.3快速入门

**步骤分析**

```
1. 创建拦截器类实现HandlerInterceptor接口 
2. 配置拦截器 
3. 测试拦截器的拦截效果
```

1)创建拦截器类实现HandlerInterceptor接口 

```
public class MyInterceptor1 implements HandlerInterceptor {

    /**
     * preHandle:在目标方法发执行之前 进行拦截
     * return false:不放行
     */
    @Override
    public boolean preHandle(HttpServletRequest request, 
    HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle。。。");
        return true;
    }

    /**
     * postHandle:在目标方法执行之后，视图对象返回之前，执行的代码
     */
    @Override
    public void postHandle(HttpServletRequest request, 
    HttpServletResponse response, Object handler, ModelAndView modelAndView) 
    throws Exception {

    }

    @Override
    public void afterCompletion(HttpServletRequest request, 
    HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}
```

这里以servletAPI方法为例

```
	//preHandle	执行了之后在执行servletAPI方法
	@RequestMapping("/servletAPI")
    public String servletAPI(HttpServletRequest request, HttpServletResponse response,
                             HttpSession session){
        System.out.println(request);
        System.out.println(response);
        System.out.println(session);
        //postHandle	在此处执行
        return "success";
    }
    //跳转到success页面之后执行的方法
```

2)配置拦截器 

spring-mvc.xml

```
	<!--配置拦截器-->
    <mvc:interceptors>
        <mvc:interceptor>
            <!--对所有Controller类里面的所有方法都进行拦截-->
            <mvc:mapping path="/**"/><!--对访问目录一级和二级等都进行拦截-->
            <bean class="com.lagou.interceptor.MyInterceptor1"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

3)测试拦截器的拦截效果

编写Controller,发请求到controller,跳转页面

```
@Controller
public class TargetController {

    @RequestMapping("/target")
    public String targetMethod(){
        System.out.println("目标方法执行了...");
        return "success";
    }
}
```

编写jsp页面

```
<body>
<h3>success请求成功</h3>
<% System.out.println("视图执行了...");%>
</body>
```

访问页面http://localhost/MVC_quickstart/target之后，控制台打印

```
preHandle。。。
目标方法执行了...
postHandle....
视图执行了...
afterCompletion。。。
```

### 5.4拦截器链

开发中拦截器可以单独使用，也可以同时使用多个拦截器形成一条拦截器链。开发步骤和单个拦截器

是一样的，只不过注册的时候注册多个，注意这里注册的顺序就代表拦截器执行的顺序。

同上，再编写一个MyHandlerInterceptor2操作，测试执行顺序：

```
<!--配置拦截器-->
    <mvc:interceptors>
        <mvc:interceptor>
            <!--对所有Controller类里面的所有方法都进行拦截-->
            <mvc:mapping path="/**"/><!--对访问目录一级和二级等都进行拦截-->
            <bean class="com.lagou.interceptor.MyInterceptor1"></bean>
        </mvc:interceptor>
        <mvc:interceptor>
            <!--对所有Controller类里面的所有方法都进行拦截-->
            <mvc:mapping path="/**"/><!--对访问目录一级和二级等都进行拦截-->
            <bean class="com.lagou.interceptor.MyInterceptor2"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

访问页面http://localhost/MVC_quickstart/target之后，控制台打印

```
preHandle1。。。
preHandle2。。。
目标方法执行了...
postHandle2....
postHandle1....
视图执行了...
afterCompletion2.。。
afterCompletion1。。。
```

### 

### 5.5知识小结

拦截器中的方法说明如下：

| 方法名            | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| preHandle()       | 方法将在请求处理之前进行调用，该方法返回值时布尔类型，<br />当它返回false时，表示请求结束，后续的Interceptor和Controller都不会再执行；<br />当返回值为true时，就会继续调用下一个Interceptor的preHandle方法 |
| postHandle()      | 该方法是在当前请求进行处理之后被调用，前提是preHandle方法的返回值为true时才能被调用，且它会在DispatcherServlet进行视图返回渲染之前被调用，所以我们可以在这个方法中对Controller处理之后的ModelAndView对象进行操作 |
| afterCompletion() | 该方法将在整个请求结束之后，也就是DispatcherServlet渲染了对应的视图之后执行<br />前提时preHandle方法的返回值为true时才能被调用 |

