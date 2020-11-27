# AJAX

## 1.概念：ASynchronous JavaScript And XML

**(异步的JavaScript和XML)**

异步和同步：客户端和服务器相互通信的基础上

​	*客户端必须等待服务器的响应。在等待期间客户端不能做其他操作

​	*客户端不需要等待服务器的响应，在服务器处理请求的过程中，客户端可以进行其他操作



AJAX是一种在无需加载整个网页的情况下，能跟新部分网页的技术。

通过在后台与服务器进行少量的数据交换。AJAX可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新，提升用户的体验。



## 2.实现方式

*原生的js实现方式

```
<scrpit>
	//定义方法
	function fun(){
		//发送异步请求
		//1.创建核心对象
		var xmlhttp;
        if (window.XMLHttpRequest)
          {// code for IE7+, Firefox, Chrome, Opera, Safari
          xmlhttp=new XMLHttpRequest();
          }
        else
          {// code for IE6, IE5
          xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
          }
          
         //2.建立连接
         /*
         	参数：
         		1.请求方式：GET、POST
         			*get方式：请求参数在url后面拼接，send方法为空参
         			*post方式：请求参数在send方法中定义
         		2.请求的url
         		3.同步或异步请求：true(异步)、false(同步)
         */
         xmlhttp.open("GET","test1.txt",true);
         
         //3.发送请求
         xmlhttp.send();
         
         //4.接收并处理来自服务器的响应结果
	}
	
      
</scrpit>
```

*JQuery的实现方式

```
	$.ajax()
		语法：$.ajax(url,[settings])或者$.ajax({键值对})
		
<scrpit>
	//定义方法
	function fun(){
		//使用$.ajax()方式发送异步请求
		$.ajax({
			url:"。。。",//请求路径
			type:"POST",//请求方式
			//data:"username=jack",//请求参数方式1
			data:{
				//里面是JSON格式的数据
			},
			success:function(data){
				alert(data);
			},//请求响应成功后的回调函数
			error:function(){
				alert("出错。。。");
			},//表示请求响应出现错误，会执行的回调函数
			dateType:“JSON”,//接收到的响应数据的格式
			
			
		})
		
	}
	
      
</scrpit>

```

```
	$.get()
		语法：$.get(url,[data],[callback],[type])
			*参数：
				*url：请求路径
				*data：请求参数
				*callback：回调函数
				*type：响应结果的类型
				
<scrpit>
	//定义方法
	function fun(){
		//使用$.get()方式发送异步请求
		$.get("url",{
			//JSON的形式
		},function(data){
			alert(data);
		},"JSON");
		
</scrpit>

```

```
	$.post()

<scrpit>
	//定义方法
	function fun(){
		//使用$.get()方式发送异步请求
		$.post("url",{
			//JSON的形式
		},function(data){
			alert(data);
		},"JSON");
		
</scrpit>
```

