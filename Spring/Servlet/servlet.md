# Servlet核心技术

## HTTP协议（熟悉）

### 1.HTTP协议的概念

```
HTTP协议（HyperText Transfer Protocol，超文本传输协议）是由W3C（万维网联盟）组织制定
的一种应用层协议，是用来规范浏览器与Web服务器之间如何通讯的数据格式，主要涉及浏览器
的发请求格式和服务器的响应格式。

HTTP协议通常承载于TCP协议之上，而承载于TLS或SSL协议层之上的协议就是常说的HTTPS协
议。

HTTP默认的端口号为80，HTTPS默认的端口号为443。
```

### 2. HTTP请求格式

客户端发送一个HTTP请求到服务器的请求消息主要包括：请求行、请求头、空白行和请求体。

```
请求行用来说明请求类型和要访问的资源以及所使用的HTTP版本，格式如下： 
	请求类型 请求的路径 协议的版本(1.1) 
请求头是紧接着请求行（即第一行）之后的部分，用来说明服务器要使用的附加信息，格式（key:value）如下： 
	主机 请求长度 请求的浏览器相关信息 
	空白行就是请求头部的空行，即使后面的请求数据为空则必须有空行。
    
	请求体也叫请求数据，可以添加任意的其他数据。
	
	举例如下：
	POST /task01_demo01/demo1.html HTTP/1.1 
	Host: localhost:8088 
	Content-Length: 21 
	Cache-Control: max-age=0 
	User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) 
	
	name=scott&pwd=123456
```

### 3.HTTP响应格式

通常情况下服务器接收并处理客户端发过来的请求后会返回一个HTTP的响应消息，主要包括：响

应行、响应头、空白行和响应体。

```
响应行用来说明HTTP协议版本号和状态码以及状态消息，格式如下： 
	协议的版本(1.0 1.1) 状态码 (200 成功 404 路径错误 500 服务错误) 状态信息 
响应头用来说明客户端要使用的一些附加信息，格式（key:value）。 
	空白行就是响应头部的空行，即使后面的请求数据为空则必须有空行。 
	
响应体用来服务器返回给客户端的文本信息。

举例如下：
HTTP/1.1 200 OK 
Content-Type: text/html 
Content-Length: 588 
Date: Thu, 08 Sep 2021 12:59:54 GMT 

<html><head><title>示例1</title></head> 
<body><h1>这是一个HTML页面</h1></body> 
</html>
```

## Tomcat服务器（重点）

### 1.基本概念

```
Tomcat本意为公猫的含义，最初是由Sun公司的软件架构师詹姆斯·邓肯·戴维森开发的，后来他帮
助将其变为开源项目并由Sun公司贡献给Apache软件基金会。

Tomcat 服务器是一个开源的轻量级Web应用服务器，在中小型系统和并发量小的场合下被普遍使
用，是开发和调试Servlet、JSP 程序的首选
```

### 2.目录结构

```
bin 主要存放二进制可执行文件和脚本。
conf 主要存放各种配置文件。
lib 主要用来存放Tomcat运行需要加载的jar包。
logs 主要存放Tomcat在运行过程中产生的日志文件。
temp 主要存放Tomcat在运行过程中产生的临时文件。
webapps 主要存放应用程序，当Tomcat启动时会去加载该目录下的应用程序。
work 主要存放tomcat在运行时的编译后文件，例如JSP编译后的文件。
```

### 3.启动和关闭

```
启动方式

​	使用bin目录下的批处理文件startup.bat来启动Tomcat服务器，若出现一个毫秒数说明启动成
功。

关闭方式 

​	使用bin目录下的批处理文件shutdown.bat来关闭Tomcat服务器。 

注意事项

​	启动之前首先安装JDK并配置环境变量JAVA_HOME，若希望Tomcat服务器可以在任意路径启动，
则需要配置环境变量CATALINA_HOME。
	双击startup.bat闪退的原因是没有安装JDK/没有配置环境变量JAVA_HOME

​	启动信息乱码的处理方式：conf/logging.properties文件修改为java.util.logging.ConsoleHandler.encoding = GBK
	乱码原因是因为win默认编码是gbk，而配置文件里面的编码是UTF-8
```

###  4.配置文件

server.xml文件是服务器的主配置文件，可以设置端口号、设置域名或IP、默认加载的项目、请求

编码等。

访问的默认端口是80，

例如：www.baidu.com:80

```
<Connector port="8888" protocol="HTTP/1.1" 
				connectionTimeout="20000" 
				redirectPort="8443" />
```

tomcat-users.xml文件用来配置管理Tomcat服务器的用户与权限 。

```
<role rolename="manager-gui"/> 
<user username="admin" password="123456" roles="manager-gui"/>
```

##  Servlet的概念和使用（重点）

### 1.基本概念

```
Servlet（Server Applet）是Java Servlet的简称，称为小服务程序或服务连接器，是Java语言编写的服务器端程序，换句话说，Servlet就是运行在服务器上的Java类。

Servlet用来完成B/S架构下客户端请求的响应处理，也就是交互式地浏览和生成数据，生成动态Web内容。
```

### 2.Servlet的编程步骤

建立一个Java Web Application项目并配置Tomcat服务器。

自定义类实现Servlet接口或继承 HttpServlet类（推荐） 并重写service方法。

将自定义类的信息配置到 web.xml文件并启动项目，配置方式如下：

```
<!-- 配置Servlet --> 
<servlet> 
	<!-- HelloServlet是Servlet类的别名 --> 
	<servlet-name> HelloServlet </servlet-name> 
	<!-- com.lagou.task01.HelloServlet是包含路径的真实的Servlet类名 --> 
	<servlet-class> com.lagou.task01.HelloServlet </servlet-class> 
</servlet> 

<!-- 映射Servlet --> 
<servlet-mapping> 
	<!-- HelloServlet是Servlet类的别名，与上述名称必须相同 --> 
	<servlet-name> HelloServlet </servlet-name> 
	<!-- /hello是供浏览器使用的地址 --> 
	<url-pattern> /hello </url-pattern> 
</servlet-mapping>
```

在浏览器上访问的方式为：

```
http://localhost:8080/工程路径/url-pattern的内容
```

### 创建servlet的三种方式

```
1.实现Servlet接口，重写所有的抽象方法
2.继承GenericServlet类，重写其中的抽象方法即可
3.继承HttpServlet类，重写 其中的一个方法即可
```



### 3.Servlet接口

```
基本概念
​	javax.servlet.Servlet接口用于定义所有servlet必须实现的方法。

常用的方法

​	void init(ServletConfig config)
		由servlet容器调用，以向servlet指示servlet正在被放入服务中
		
​	void service(ServletRequest req,ServletResponse res)
		由servlet容器调用，以允许servlet响应请求
		
​	ServletConfig getServletConfig()
		返回ServletConfig对象，该对象包含此servlet的初始化和启动参数
		
​	String getServletInfo()
		返回有关servlet的信息，如作者、版本和版权
		
​	void destroy()
		由servlet容器调用，以向servlet指示该servlet正在退出服务
```

### 4.GenericServlet类

#### 基本概念

```
javax.servlet.GenericServlet类主要用于定义一个通用的、与协议无关的servlet，该类实现了
Servlet接口。

​若编写通用servlet，只需重写service抽象方法即可。
```

#### 常用的方法

```
abstract void service(ServletRequest req,ServletResponse res)
		由servlet容器调用允许servlet响应请求
```

### 5.HttpServlet类

#### 基本概念

```
javax.servlet.http.HttpServlet类是个抽象类并继承了GenericServlet类。

​用于创建适用于网站的HTTP Servlet，该类的子类必须至少重写一个方法。
```

#### 常用的方法

```
	void doGet(HttpServletRequest req,HttpServletResponse resp)
		处理客户端的GET请求
		
​	void doPost(HttpServletRequest req,HttpServletResponse resp)
		处理客户端的POST请求
		
​	void init()
		进行初始化操作
		
​	void service(HttpServletRequest req,HttpServletResponse resp)
		根据请求决定调用doGet还是doPost方法
		
​	void destroy()
		删除实例时释放资源
```



### 6.Servlet的生命周期

```
实例化---------->Servlet容器创建servlet的实例，调用构造方法
初始化---------->该容器调用init()方法
服务------------>如果请求Servlet，则容器调用servlet()方法
销毁------------>销毁实例之前调用destroy()方法
不可用---------->销毁实例并标记为垃圾收集
```

```
构造方法只被调用一次，当第一次请求Servlet时调用构造方法来创建Servlet的实例。
​init方法只被调用一次，当创建好Servlet实例后立即调用该方法实现Servlet的初始化。
​service方法被多次调用，每当有请求时都会调用service方法来用于请求的响应。
​destroy方法只被调用一次，当该Servlet实例所在的Web应用被卸载前调用该方法来释放当前占用
 的资源。
```

## POST和GET请求（重点）

### 1.GET请求

```
发出GET请求的主要方式： 
	（1）在浏览器输入URL按回车 
	（2）点击<a>超链接 
	（3）点击submit按钮，提交 <form method=“get”>表单 
	
GET请求特点： 
	会将请求数据添加到请求URL地址的后面，只能提交少量的数据、不安全
```

### 2.POST请求

```
发出POST请求的方法如下： 
	点击submit按钮，提交 <form method=“post”>表单 

POST请求的特点： 
	请求数据添加到HTTP协议体中，可提交大量数据、安全性好
```



### 3.ServletRequest接口

#### 基本概念

```
	javax.servlet.ServletRequest接口主要用于向servlet提供客户端请求信息，可以从中获取		到任何请求信息。

​	Servlet容器创建一个ServletRequest对象，并将其作为参数传递给Servlet的service方法。
```

#### 常用的方法

```
	String getParameter(String name)
		以字符串形式返回请求参数的值，如果该参数不存在，则返回空值
		
​	String[] getParameterValues(String name)
		返回一个字符串对象数组，其中包含给定请求参数所具有的所有值，
		如果该参数不存在，则返回空值
		
​	Enumeration getParameterNames()
		返回包含此请求中包含的参数名称的字符串对象的枚举。
		如果请求没有参数，则方法返回空枚举
		
​	Map<String, String[]> getParameterMap()
		返回请求参数的键值对，一个键可以对应多个值
		
​	String getRemoteAddr()
		返回发送请求的客户端或最后一个代理的IP地址
		
​	int getRemotePort()
		返回发送请求的客户端或最后一个代理的端口号
		
		​为什么这里返回的地址和端口号是这个？
			地址：0:0:0:0:0:0:0:1	端口号60693
```

### 4.HttpServletRequest接口

#### 基本概念

```
javax.servlet.http.HttpServletRequest接口是ServletRequest接口的子接口，主要用于提供HTTP请求信息的功能。

​不同于表单数据，在发送HTTP请求时，HTTP请求头直接由浏览器设置。

​可直接通过HttpServletRequest对象提供的一系列get方法获取请求头数据。
```

#### 常用的方法

```
String getRequestURI()			返回此请求的资源路径信息

StringBuffer getRequestURL()	返回此请求的完整路径信息

String getMethod()				返回发出此请求的HTTP方法的名称，例如GET、POST

String getQueryString()			返回路径后面请求中附带的参数

String getServletPath()			返回此请求中调用servlet的路径部分
```

### 5.ServletResponse接口

#### 基本概念

javax.servlet.ServletResponse接口用于定义一个对象来帮助Servlet向客户端发送响应。

Servlet容器创建ServletResponse对象，并将其作为参数传递给servlet的service方法。

#### 常用方法

```
PrintWriter getWriter()				返回可向客户端发送字符文本的PrintWriter对象

String	getCharacterEncoding()		获取响应内容的编码方式

void  setContentType(String type)	如果尚未提交响应，则设置发送到客户端响应的内容类					型。内容类型可以包括字符编码规范，例如text/html;charset=UTF-8
```

```
		//获取响应数据的编码方式
        System.out.println("默认编码方式是："+response.getCharacterEncoding());//ISO-8859-1
        //设置服务器和浏览器的编码方式以及文本类型
        response.setContentType("text/html;charset=UTF-8");
```



### 6.HttpServletResponse接口

#### 基本概念

javax.servlet.http.HttpServletResponse接口继承ServletResponse接口，以便在发送响应时提供

特定于HTTP的功能。

#### 常用的方法

```
void sendRedirect(String location)	使用指定的重定向位置URL向客户端发送临时重定向响应
```

### **7**.使用示例

```
案例题目
​	使用Servlet获取在服务器获取一个1~100之间的随机数并发送给客户端进行显示。
```

```
protected void doPost(HttpServletRequest request, 
					HttpServletResponse response) 
					throws ServletException, IOException {
		//5.向浏览器发送响应数据
        //获取响应数据的编码方式
        System.out.println("默认编码方式是："+response.getCharacterEncoding());//ISO-8859-1
        //设置服务器和浏览器的编码方式以及文本类型
        response.setContentType("text/html;charset=UTF-8");

        PrintWriter writer = response.getWriter();
        writer.write("收到!!");
        System.out.println("我将返回一个随机数");
        Random random =new Random();
        int num = random.nextInt(100)+1;
        writer.write("<h1>随机数为："+num+"</h1>");
        System.out.println("发送数据成功");
        writer.close();
    }
```



## Servlet接收中文乱码（重点）

### **1**.接收乱码原因

浏览器在提交表单时，会对中文参数值进行自动编码。当Tomcat服务器接收到浏览器请求后自动

解码，当编码与解码方式不一致时,就会导致乱码。

### 2.解决POST接收乱码

```
接收之前设置编码方式： 
	request.setCharacterEncoding(“utf-8”) 
提示：
	必须在调用request.getParameter(“name”)之前设置
```

### 3.解决GET接收乱码

```
将接收到的中文乱码重新编码: 
	// 接收到get请求的中文字符串 
	String name = request.getParameter("name"); 
	// 将中文字符重新编码，默认编码为ISO-8859-1 
	String userName = new String(name.getBytes(“ISO-8859-1”),“utf-8");
```

## ServletConfifig接口（熟悉）

#### 基本概念

javax.servlet.ServletConfifig接口用于描述Servlet本身的相关配置信息，在初始化期间用于将信息

传递给Servlet配置对象。

#### 配置方式

```
<!-- 在web.xml中配置ServletConfig初始化参数 --> 
<servlet> 
	<servlet-name>actionservlet</servlet-name> 
	<servlet-class>com.lagou.demo01.ActionServlet</servlet-class> 
	<!-- 配置 Serlvet 的初始化参数 --> 
	<init-param> 
		<!-- 参数名 --> 
		<param-name>config</param-name> 
		<!-- 参数值 --> 
		<param-value>struts.xml</param-value> 
	</init-param> 
</servlet>
```

#### 常用的方法

```
String getServletName()						返回Servlet的别名

String getInitParameter(String name)		返回包含初始化参数值的字符串，
											如果该参数不存在，则返回null

Enumeration getInitParameterNames()			将servlet的初始化参数的名称作为字符串对								象的枚举返回，如果servlet没有初始化参数，则返回空枚举

ServletContext getServletContext()			
								返回对调用方正在其中执行的ServletContext的引用
```



## ServletContext接口（熟悉）

#### 基本概念

```
	javax.servlet.ServletContext接口主要用于定义一组方法，Servlet使用这些方法与它的Servlet容器通信。

​	服务器容器在启动时会为每个项目创建唯一的一个ServletContext对象，用于实现多个Servlet之间的信息共享和通信。

​	在Servlet中通过this.getServletContext()方法可以获得ServletContext对象。
```

#### 配置方式

```
<!--在web.xml中配置ServletContext初始化参数 --> 
<context-param> 
	<param-name>username</param-name> 
	<param-value>scott</param-value> 
<context-param> 
<context-param> 
	<param-name>password</param-name> 
	<param-value>tiger</param-value> 
<context-param>
```

#### 常用的方法

```
String getInitParameter(String name)
			返回包含初始化参数值的字符串，如果该参数不存在，则返回null

Enumeration getInitParameterNames()
			将servlet的初始化参数的名称作为字符串对象的枚举返回，
			如果servlet没有初始化参数，则返回空枚举
			
String getRealPath(String path)
			返回包含给定虚拟路径的实际路径的字符串
String getContextPath()
			返回与此上下文关联的主路径
InputStream getResourceAsStream(String path)
			将位于指定路径的资源作为InputStream对象返回
void setAttribute(String name, Object object)
			将指定的属性名和属性值绑定到当前对象
Object getAttribute(String name)
			根据执行的属性名获取属性值
			
void removeAttribute(String name)			删除指定的属性名信息
```

# Servlet+JDBC应用（重点）

```
在Servlet中可以使用JDBC技术访问数据库，常见功能如下：

​	查询DB数据，然后生成显示页面，例如：列表显示功能。
​	接收请求参数，然后对DB操作，例如：注册、登录、修改密码等功能。

为了方便重用和便于维护等目的，经常会采用DAO（Data Access Object）模式对数据库操作进行
独立封装。
	请求---Servlet <==> DAO <==>DB
	响应--|

DAO工厂(工厂模式) 
​	工厂类：封装了对象的创建细节，为调用者提供符合要求的对象。
```

## 1.重定向和转发（重点）

### 1.重定向的概述

#### (1).重定向的概念

```
首先客户浏览器发送http请求，当web服务器接受后发送302状态码响应及对应新的location给客
户浏览器，客户浏览器发现是302响应，则自动再发送一个新的http请求，请求url是新的location
地址，服务器根据此请求寻找资源并发送给客户。
```

#### (2).重定向的实现

实现重定向需要借助javax.servlet.http.HttpServletResponse接口中的以下方法：

```
void sendRedirect(String location)
									使用指定的重定向位置URL向客户端发送临时重定向响应
```

#### (3).重定向的原理

```
类似于A向B借钱，B想到C有，就跟A说C有钱，A又去跟C借
```



#### (4).重定向的特点

```
重定向之后，浏览器地址栏的URL会发生改变。
重定向过程中会将前面Request对象销毁，然后创建一个新的Request对象。
重定向的URL可以是其它项目工程。
```

### 2.转发的概述

#### (1)转发的概念

一个Web组件（Servlet/JSP）将未完成的处理通过容器转交给另外一个Web组件继续处理，转发
的各个组件会共享Request和Response对象。

#### (2)转发的实现

```
绑定数据到Request对象:
	Object getAttribute(String name)	
			将指定属性值作为对象返回，若给定名称属性不存在，则返回空值
			
	void setAttribute(String name,Object o)			在此请求中存储属性值

获取转发器对象:
	RequestDispatcher getRequestDispatcher(String path)
			返回一个RequestDispatcher对象，该对象充当位于给定路径上的资源的包装器

转发操作:
	void forward(ServletRequest request, ServletResponse response)
			将请求从一个servlet转发到服务器上的另一个资源（Servlet、JSP文件或HTML文件）
```

#### 转发的原理

```
类似于A跟B借钱，B想到C有钱，B去跟说A要跟C借钱，然后C借给了A
```

#### (3)转发的特点

```
	转发之后浏览器地址栏的URL不会发生改变。
​	转发过程中共享Request对象。
​	转发的URL不可以是其它项目工程。
```

#### (4)重定向和转发的比较

## 2.Servlet线程安全(重点)

```
	服务器在收到请求之后，会启动一个线程来进行相应的请求处理。
​	默认情况下，服务器为每个Servlet只创建一个对象实例。当多个请求访问同一个Servlet时，会有
多个线程访问同一个Servlet对象，此时就可能发生线程安全问题。
​	多线程并发逻辑，需要使用synchronized对代码加锁处理，但尽量避免使用。
```

## 3.状态管理（重点）

```
	Web程序基于HTTP协议通信，而HTTP协议是”无状态”的协议，一旦服务器响应完客户的请求之
后，就断开连接，而同一个客户的下一次请求又会重新建立网络连接。

​	服务器程序有时是需要判断是否为同一个客户发出的请求，比如客户的多次选购商品。因此，有必
要跟踪同一个客户发出的一系列请求。

​	把浏览器与服务器之间多次交互作为一个整体，将多次交互所涉及的数据保存下来，即状态管理。

​	多次交互的数据状态可以在客户端保存，也可以在服务器端保存。状态管理主要分为以下两类：
        ​客户端管理：将状态保存在客户端。基于Cookie技术实现。
        ​服务器管理：将状态保存在服务器端。基于Session技术实现。
```

## 4.Cookie技术（重点）

### 1. 基本概念

```
	Cookie本意为”饼干“的含义，在这里表示客户端以“名-值”形式进行保存的一种技术。

​	浏览器向服务器发送请求时，服务器将数据以Set-Cookie消息头的方式响应给浏览器，然后浏览器
会将这些数据以文本文件的方式保存起来。

​	当浏览器再次访问服务器时，会将这些数据以Cookie消息头的方式发送给服务器。
```

### 2.相关的方法

```
使用javax.servlet.http.Cookie类的构造方法实现Cookie的创建。

	Cookie(String name, String value)		根据参数指定数值构造对象	
	
​使用javax.servlet.http.HttpServletResponse接口的成员方法实现Cookie的添加。

	void addCookie(Cookie cookie)			添加参数指定的对象到响应
	
​使用javax.servlet.http.HttpServletRequest接口的成员方法实现Cookie对象的获取。
	Cookie[] getCookies()					返回此请求中包含的所有Cookie对象
	
​使用javax.servlet.http.Cookie类的构造方法实现Cookie对象中属性的获取和修改。
	String getName()						返回此Cookie对象中的名字
	String getValue()						返回此Cookie对象的数值
	void setValue(String newValue)			设置Cookie的数值
```

###  3.Cookie的生命周期

```
	默认情况下，浏览器会将Cookie信息保存在内存中，只要浏览器关闭，Cookie信息就会消失。
​	如果希望关闭浏览器后Cookie信息仍有效，可以通过Cookie类的成员方法实现。
		int getMaxAge()						返回cookie的最长使用期限（以秒为单位）
		void setMaxAge(int expiry)			设置cookie的最长保留时间（秒）
```

### 4.Cookie的路径问题

```
	浏览器在访问服务器时，会比较Cookie的路径与请求路径是否匹配，只有匹配的Cookie才会发送
给服务器。

​	Cookie的默认路径等于添加这个Cookie信息时的组件路径，例如：/项目名/目录/add.do请求添加了一个Cookie信息，则该Cookie的路径是 /项目名/目录。

​	访问的请求地址必须符合Cookie的路径或者其子路径时，浏览器才会发送Cookie信息。
		void setPath(String uri)			设置cookie的路径信息
```

### 5.Cookie的特点

```
Cookie技术不适合存储所有数据，程序员只用于存储少量、非敏感信息，原因如下：

​	将状态数据保存在浏览器端，不安全。
​	保存数据量有限制，大约4KB左右。
​	只能保存字符串信息。
​	可以通过浏览器设置为禁止使用。
```

## Session技术（重点）

### 1.基本概念

```
	Session本意为"会话"的含义，是用来维护一个客户端和服务器关联的一种技术。

​	浏览器访问服务器时，服务器会为每一个浏览器都在服务器端的内存中分配一个空间，用于创建一个Session对象，该对象有一个id属性且该值唯一，我们称为SessionId，并且服务器会将这个SessionId以Cookie方式发送给浏览器存储。

​	浏览器再次访问服务器时会将SessionId发送给服务器，服务器可以依据SessionId查找相对应的
Session对象
```

### 2.相关的方法

```
	使用javax.servlet.http.HttpServletRequest接口的成员方法实现Session的获取。
		HttpSession getSession()		返回此请求关联的当前Session，
										若此请求没有则创建一个	
										
​	使用javax.servlet.http.HttpSession接口的成员方法实现判断和获取。
		boolean isNew()					判断是否为新创建的Session
		String getId()					获取Session的编号
		
​	使用javax.servlet.http.HttpSession接口的成员方法实现属性的管理。
		Object getAttribute(String name)
			返回在此会话中用指定名称绑定的对象，如果没有对象在该名称下绑定，则返回空值
			
		void setAttribute(String name, Object value)
			使用指定的名称将对象绑定到此会话
			
		void removeAttribute(String name)	
			从此会话中删除与指定名称绑定的对象
```



### 3.Session的生命周期

```
	为了节省服务器内存空间资源，服务器会将空闲时间过长的Session对象自动清除掉，服务器默认
的超时限制一般是30分钟。

​	使用javax.servlet.http.HttpSession接口的成员方法实现失效实现的获取和设置。
		int getMaxInactiveInterval()						获取失效时间
		
		void setMaxInactiveInterval(int interval)			设置失效时间
		
​	可以配置web.xml文件修改失效时间。
	<session-config> 
		<session-timeout>30</session-timeout> 
	</session-config>

```

### 4.Session的特点

```
	数据比较安全。
​	能够保存的数据类型丰富，而Cookie只能保存字符串。
​	能够保存更多的数据，而Cookie大约保存4KB。

​	数据保存在服务器端会占用服务器的内存空间，如果存储信息过多、用户量过大，会严重影响服务
器的性能。
```

# Cookie和Session的作用

### 1、cookie机制和session机制的区别

```
cookie机制采用的是在客户端保持状态的方案，相当于保存到本地，因此cookie不安全，同时cookie只能保存字符串。
而session机制采用的是在服务器端保持状态的方案

同时我们也看到，由于在服务器端保持状态的方案在客户端也需要保存一个标识，所以session机制可能需要借助于cookie机制来达到保存标识的目的，但实际上还有其他选择。
```

### 2、会话cookie和持久cookie的区别

```
不设置cookie的过期时间：
    则表示这个cookie的生命周期为浏览器会话期间，只要关上了浏览器窗口，这个cookie就消失了，	这种生命周期为浏览器的会话期的cookie称为会话cookie。会话cookie通常不保存在硬盘上。而是	保存在内存里。
    
设置了cookie的过期时间，则浏览器会把cookie保存在硬盘上，关闭再次打开浏览器，这些cookie依然有效知道超过设置的过期时间。【例如登陆一个网站，下次就不需要再次填写用户名和密码了】

存储在硬盘上的cookie可以在不同的浏览器进程之间共享，比如两个浏览器窗口，
而对于保存在内存的cookie，不同的浏览器有不同的处理方式
```

### 案例：如何利用实现自动登录

```
当在某个网站上进行了登陆之后，会接受一个带有用户id的cookie。
客户再次连接的时候，服务器会对cookie进行遍历，找到这个带有用户id的cookie。
	如果找得到这个cookie，则用户无需再次填写用户名和密码，可以访问服务器
	如果未找到这个cookie，说明cookie已过期或者是其他的，则需要重新登陆
```

