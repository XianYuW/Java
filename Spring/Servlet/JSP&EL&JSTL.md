# EL和JSTL核心技术

## 1.EL表达式（熟悉）

### 1.1基本概念

```
EL（Expression Language）表达式提供了在JSP中简化表达式的方法，
可以方便地访问各种数据并输出。
```

### 1.2主要功能

```
	依次访问pageContext、request、session和application作用域对象存储的数据。
​	获取请求参数值。
​	访问Bean对象的属性。
​	访问集合中的数据。
​	输出简单的运算结果。
```

### 1.3访问内置对象的数据

#### (1)访问方式

```
	JSP: <%=request.getAttribute(“ varName”)%>

​	用EL实现: ${ varName }
```

#### (2)执行流程

```
从PageContext->HttpServletRequest->HttpSession->ServletContext
```

#### 1.4访问请求参数的数据

```
在EL之前使用下列方式访问请求参数的数据

    ​request.getParameter(name);
    ​request.getParameterValues(name);

在EL中使用下列方式访问请求参数的数据

    ​param：接收的参数只有一个值。
    ​paramValues：接受的参数有多个值。
```

```
<!-- 获取指定参数的数值 --> 
	${param.name} 
<!-- 获取指定参数中指定下标的数值 --> 
	${paramValues.hobby[0]}
```

### 1.5访问Bean对象的属性

#### (1))访问方式

```
方式一： $ { 对象名 . 属性名 }，例如：${user.name}
方式二： $ { 对象名 [“属性名”] }，例如：${user["name"]}
```

#### (2))主要区别

```
	当要存取的属性名中包含一些特殊字符，如： . 或 ,等并非字母或数字的符号，就一定要使用 [ ]	而不是.的方式

​	使用[]的方式可以动态取值，具体方式如下：
	<% 
		request.setAttribute(“prop”,“age”); 
	%>
	<!-- 相当于表达式中写一个变量 --> 
	${ user[prop] }
```

### 1.6访问集合中的数据

```
<!-- student为ArrayList类型的对象 --> 
${student[0].name}
```

### 1.7常用的内置对象

| 类别       | 标识符           | 描述                                              |
| ---------- | ---------------- | ------------------------------------------------- |
| JSP        | pageContext      | PageContext处理当前页面                           |
| 作用域     | pageScope        | 同页面作用域属性名和值有关的Map类                 |
|            | requestScope     | 同请求作用域属性的名称和值有关的Map类             |
|            | sessionScpe      | 同会话作用域属性的名称和值有关的Map类             |
|            | applicationScope | 同应用程序作用域属性的名称和值有关的Map类         |
| 请求参数   | param            | 根据名称存储请求参数的值Map类                     |
|            | paramValues      | 把请求参数的所有值作为一个String数组来存储的Map类 |
| 请求头     | header           | 根据名称存储请求头主要值的Map类                   |
|            | headerValues     | 把请求头的所有值作为一个String数组来存储的Map类   |
| Cookie     | cookie           | 根据名称存储请求附带的cookie的Map类               |
| 初始化参数 | initParam        | 根据名称存储web应用程序上下文初始化参数的Map类    |

### 1.8常用的运算符

#### （1）常用的算术运算符

```
和JavaSE中一样，+，-，*，/或div，%或mod
这里的除运算(/或者div)，是会保留小数的，例如：${16/5}  结果为3.2
```

#### (2))常用的关系运算符

```
和JavaSE中一样,
==或eq		!=或ne		<或lt		>或gt		<=或le		>=或ge
```

#### (3)常用的逻辑运算符

```
和JavaSE中一样
&&或and		||或or		!或not
```

#### (4)条件运算符

```
和JavaSE中三目运算符一样
${条件表达式? 语句1 : 语句2}
```

#### (5)验证运算符

```
和JavaSE中一样
${empty 表达式} 
返回布尔值判断表达式是否为"空"值，null值、无元素的集合或数组、长度为零的String被认为是空值。
```

## 2.JSTL标签（熟悉)

### 2.1基本概念

```
	JSTL( JSP Standard Tag Library ) 被称为JSP标准标签库。
​	开发人员可以利用这些标签取代JSP页面上的Java代码，从而提高程序的可读性，降低程序的维护
	难度。
```

### 2.2使用方式

下载JSTL的jar包并添加到项目中,地址为：https://tomcat.apache.org/download-taglibs.cgi

在JSP页面中使用taglib指定引入jstl标签库，方式为：

```
<!-- prefix属性用于指定库前缀 --> 
<!-- uri属性用于指定库的标识 --> 
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

### 2.3常用核心标签

#### (1)输出标签

```
<c:out></c:out> 用来将指定内容输出的标签
```

#### (2))设置标签

```
<c:set></c:set> 用来设置属性范围值的标签
```

#### (3))删除标签

```
<c:remove></c:remove> 用来删除指定数据的标签
```

#### (4))单条件判断标签

```
<c:if test =“EL条件表达式”> 
	满足条件执行 
</c:if >
```

#### (5)多条件判断标签

```
<c:choose > 
	<c:when test =“EL表达式”> 
		满足条件执行 
	</c:when> 
	…
	<c:otherwise> 
		不满足上述when条件时执行 
	</c:otherwise> 
</c:choose >
```

#### (6))循环标签

```
<c:forEach var=“循环变量” items=“集合”> 
	… 
</c:forEach>
```

### 2.4常用函数标签

```
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
```



### 2.5常用格式化标签

```
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
```

如果上面几个标签不能满足需求，也可以自定义标签，步骤如下：

### 2.6自定义标签

编写标签类继承SimpleTagSupport类或TagSupport类并重写doTag方法或doStartTag方法。

```
public class HelloTag extends SimpleTagSupport { 

	private String name; 
	
	public String getName() { 
		return name; 
	}
	
	public void setName(String name) { 
		this.name = name; 
	}
	
	@Override 
	public void doTag() throws JspException, IOException { 
		JspWriter out = this.getJspContext().getOut(); 
		out.println("自定义标签的参数为：" + name); 
	} 
}
```

定义标签库文件（tld标签库文件）并配置标签说明文件到到WEB-INF下：

```
<tag>
	<name>helloTag</name> 
	<tag-class>com.lagou.demo02.HelloTag</tag-class> 6
	<body-content>empty</body-content> 
	<attribute> 
		<name>name</name> 
		<required>true</required> 
	</attribute> 
</tag>
```

在JSP中添加taglib指令引入标签库使用：

```
<%@ taglib prefix="hello" uri="http://lagou.com" %>
```

