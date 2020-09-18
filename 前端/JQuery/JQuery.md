# JQuery

##  >jQuery能做什么?

```
	访问和操作DOM元素
​	控制页面样式
​	对页面事件进行处理
​	扩展新的jQuery插件
​	与Ajax技术完美结合
```

## >jQuery的优势

```
	体积小，压缩后只有100KB左右
​	强大的选择器
​	出色的DOM封装
​	可靠的事件处理机制
​	出色的浏览器兼容性
```

## 1.jQuery的使用

jQuery作为一个单独存在的js文件，并不会与其他的js文件发生冲突

在页面中使用传统引入js文件的方式引入即可。

```
<script src="js/jquery-3.4.1.min.js"></script>
```

### 1.1基本的语法介绍

```
<script> 
	$(selector).action(); 
</script>

说明：
​工厂函数 $() ：将DOM对象转化为jQuery对象
​选择器 selector：获取需要操作的DOM 元素（没错，用法基本上和css一致 ）
​方法action()：jQuery中提供的方法，其中包括绑定事件处理的方法“$”等同于“ jQuery ”
```

### 1.2 jQuery对象与DOM对象

DOM对象和jQuery对象分别拥有一套独立的方法，不能混用

```
$("#title").html(); 
// 等同于 
document.getELementById("title").innerHTML;
```

```
想混用，要转换
​	DOM对象转jQuery对象
        var a = document.getElementById("name"); // a是DOM对象 
        var b = $(a); // b是jQuery对象
        
​	jQuery对象转DOM对象
		var jqDom = $(jsDom) ;//将js对象---转换-->jq对象
        // jqDom是jQuery对象 
		var jsDom = jQueryObject.get(0);//将jq对象---转换-->js对象
        // jsDom是DOM对象
```

### 1.3选择器

#### 1.3.1 基本选择器

基本选择器包括标签选择器、类选择器、ID选择器、并集选择器、交集选择器和全局选择器

```
标签选择器		element		根据给定的标签名匹配元素	$("h2" )选取所有h2元素

类选择器		.class		根据给定的class匹配元素		$(" .title")选取所有class
													为title的元素
ID选择器		#id			根据给定的id匹配元素		$(" #title")选取id为title的元素

并集选择器	selector1,...,selectorN 将每一个选择器匹	$("div,p,.title" )选取所有
									配的元素合并后一	div、p和拥有class为title
									起返回				的元素
交集选择器	element.class	匹配指定class或id		$("h2.title")选取所有拥有
			或element#id	   的某元素或元素集合		class为title的h2元素
```

```html
	<p>迪丽热巴</p>
    <p>古力娜扎</p>
    <p class="a">马尔扎哈</p>
    <p id="name">虚空恐惧</p>
    <h3 class="c">hello world</h3>
    <script>
        $("p").css("color","black");
        //标签选择器,全部的p标签丢设置为黑色
        $(".a").css("font-family","字魂49号-逍遥行书");
        //类选择器,将类为a的标签的字体设置为fon-family
        $("#name").css("background","red");
        //ID选择器,更具备唯一性,将id为name的标签背景颜色设置为红色
        $(".a,#name,.c").css("border"," 2px solid black");
        //并集选择器,.a 和#name和.c 都设置黑色边界
        $("h3.c").css("font-size","90px");
        //交集选择器,将h3.c的标签字体大小设置为90像素
    </script>
```

#### 1.3.2 层次选择器

| 名称           | 语法构成              | 描述                                           | 实例                                                         |
| -------------- | --------------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| 后代选择器     | ancestor   descendant | 选取ancestor元素里的所有descendant（后代）元素 | $("#menu span" )选取#menu下的元素                            |
| 子选择器       | parent>child          | 选取parent元素下的child（子）元素              | $(" #menu>span" )选取#menu的子元素                           |
| 相邻元素选择器 | prev+next             | 选取紧邻prev元素之后的next元 素                | $(" h2+dl " )选取紧邻              【元素之后的同辈元素】    |
| 同辈元素选择器 | prev~sibings          | 选取prev元素之后的所有siblings元素             | $(" h2~dl " )选取                                              【元素之后所有的同辈元素】 |

```
	<h3>000</h3> 
    <div id="x">
         111 
         <p>p1</p> 
         <div>
             <p>p2</p> 
         </div> 
    </div> 
    <h3>222</h3> 
    <h3>333</h3> 
    <script>
        $("#x p").css("font-size","40px"); 
        // 后代选择器，忽略层级 
        $("#x>p").css("color","red"); 
        // 子代选择器，只负责子级
        $("#x+h3").css("color","green"); 
        // 相邻元素选择器，下一个紧邻的兄弟h3 
        $("#x~h3").css("color","blue"); 
        // 同辈元素选择器，下面的所有兄弟h3 
    </script>
```

#### 1.3.3属性选择器

```
属性选择器	[attribute] 	选取包含给定属性的元素			$(" [href]" )选取含有															href属性的元素
```

#### 1.3.4过滤选择器

语法：$("元素:过滤选择器");

```
<script> 
	//$("li:first").css("color","red"); // 第一个li 								//$("li:last").css("color","red"); // 最后一个li 							//$("li:even").css("color","red"); // 偶数行的li 							//$("li:odd").css("color","red"); // 奇数行的li 							//$("li:eq(2)").css("color","red"); // 下标为2的li 							//$("li:gt(1)").css("color","red"); // 下标大于1的li 						//$("li:lt(2)").css("color","red"); // 下标小于2的li 						$("#h2\\#x").css("color","red"); // 使用转义符 
</script>
```

### 1.4事件

#### 1.4.1鼠标事件

```
鼠标事件是当用户在文档上移动或单击鼠标时而产生的事件，常用鼠标事件有：

​	click()		单击鼠标时触发指定元素的click事件
​	mouseover()		鼠标移动到元素上方触发绑定到指定元素上的mouse over事件
​	mouseout()		鼠标移出元素时触发绑定到指定元素上的mouse over事件
```



```
	<img src="img/1.jpg" width="300"> 
    <img src="img/1.jpg" width="300"> 
    <img src="img/1.jpg" class = "a" width="300"> 
    <script src="js/jquery-3.4.1.min.js"></script> 
    <script> 
        $("img").click( function(){ 
            //点击一下，换照片
            $(this).attr( "src","img/2.jpg" ); 
            //this就是事件触发的源头 
        } ); 
        $("img").mouseover( function(){ 
            //移动到元素上 
            $(this).css( "border","2px solid red" ); 
        } ); 
        $("img").mouseout( function(){ 
            //离开元素 
            $(this).css( "border","2px solid white" ); 
        } ); 
    </script>
```

**问题：**
	**如何实现点击鼠标将图片1更换为图片2，再次点击就换回来**

#### 1.4.2键盘事件

用户每次按下或者释放键盘上的键时都会产生事件，常用键盘事件有：

```
keydown()	按下键盘时，触发或将函数绑定到指定元素的keydown事件
keyup()		释放按键时，触发或将函数绑定到指定元素的keyup事件
```

```
	<input /> 
    <h3></h3> 
    <script src="js/jquery-3.4.1.min.js"></script> 
    <script> 
        $("input").keyup( function(){ 
            var str = $(this).val(); 
            // 获取框中的值 
            $("h3").text( str ); 
            // 将h3元素中的文本内容更改为str 
        } ); 
    </script>
```

```
jQuery实现按enter键登录
 

 <script> 

$(document).keydown(function (event) {
    if (event.keyCode == 13) {
    $("#btn_Login").click();
    }
});

 </script>
```



#### 1.4.3表单事件

当元素获得焦点时，会触发focus事件，失去焦点时，会触发blur事件

```
focus( )   获得焦点/点击一下	触发或将函数绑定到指定元素的focus事件
blur( )		失去焦点/未点击	触发或将函数绑定到指定元素的blur事件
```

```
	<form action=""> 
        <p>帐号: <input id="a" value="请输入帐号..."> </p> 
        <p>电话: <input id="b"> </p> 
    </form> 
    <script src="js/jquery-3.4.1.min.js"></script> 
    <script> 
        //获得焦点（激活/点击一下） 
        $("#a").focus(function(){ 
            $(this).val(""); 
        }); 
        //失去焦点（未激活/未点击） 
        $("#a").blur(function(){ 
            $(this).val("请输入帐号..."); 
        });
```

#### 1.4.4鼠标悬停复合事件

hover()方法相当于mouseover与mouseout事件的组合

```
<img src="img/3.jpg" width="400"> 
    <form action=""> 
        <p>帐号: <input id="a" value="请输入帐号..."> </p> 
        <p>电话: <input id="b"> </p> 
    </form> 
    <script src="js/jquery-3.4.1.min.js"></script> 
    <script> 
        $("img").hover( 
            function(){ 
                $(this).css("border","5px solid red"); 
            },
            function(){ 
                $(this).css("border","5px solid white"); 
            } 
        ); 
        $("#a").hover(
            function(){
                $(this).val("放入");
            },
            function(){
                $(this).val("移出");
            }
        );
    </script>
```

#### 1.4.5连续点击复合事件

toggle()        --> 连续点击，可以进行 【可见和隐藏 】的切换 

```
    <h2>修仙小说</h2> 
    <ul>
        <li>凡人修仙传</li>
         <li>遮天</li> 
         <li>神墓</li> 
         <li>残袍</li> 
         <li>大主宰</li> 
    </ul>
    <button>img</button><br>
    <img src="img/1.jpg" width="300px">
    
    <script src="js/jquery-3.4.1.min.js"></script> 
    <script> 
        $("h2").click(function(){ 
            $("ul").toggle(); // 连续点击，ul的可见和隐藏进行切换 
        }); 
        $("button").click(function(){
            $("img").toggle();
        })
    </script>
```

#### 1.4.6事件的动态绑定

```
对dom元素绑定事件的另一种写法
​	绑定一个事件

​	绑定多个事件
```

```
    <h1 class="del">hello world</h1>
    <h1 class="del">hello java!!</h1>
    <script src="js/jquery-3.4.1.min.js"></script>
    <script>
    	//只绑定了一个事件：点击
        $(".del").on("click",function(){
            alert($(this).text());
        });
        //绑定了两个事件：鼠标悬停/移开
        $(".del").on("mouseover mouseout",function(){
            alert("鼠标悬停或者移开了");
        });
    </script>
```

### 1.5元素的隐藏和显示

#### 1.5.1改变元素的宽和高（带动画效果）

```
	show( speed )：显示
​	hide( speed )：隐藏
​	toggle( speed )等价于show+hide : 显示的隐藏，隐藏的显示

可选的 speed 参数规定隐藏/显示的速度，可以取以下值："slow"、"fast" 或毫秒
```

```
<style>
    div{
        width: 200px;
        height: 200px;
        background-color:black;
    }
</style>
<body>
    
    <button class="btn btn-default" id="btn1">显示</button>
    <button class="btn btn-default active" id="btn2">隐藏</button>
    <button class="btn btn-primary" id="btn3">切换</button>
    <div></div>
    <script src="js/jquery-3.4.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script>
        //fast:快速的
        //slow：缓慢的
        //毫秒：自定义
        $("#btn1").click(function(){
            $("div").show(2000);
        });
        $("#btn2").click(function(){
            $("div").hide(`slow`);
        });
        $("#btn3").click(function(){
            $("div").toggle(`slow`);
        });

    </script>
    
</body>
```

#### 1.5.2改变元素的高（拉伸效果)

```
	slideDown( speed ) ：显示
​	slideUp( speed )：隐藏
​	slideToggle( speed )等价于slideDown+slideUp

可选的 speed 参数规定隐藏/显示的速度，可以取以下值："slow"、"fast" 或毫秒
```

```
<style>
    div{
        width: 200px;
        height: 200px;
        background-color:black;
    }
</style>
<body>
    <button class="btn btn-default active" id="btn1">显示</button>
    <button class="btn btn-primary" id="btn2">隐藏</button>
    <button class="btn btn-primary" id="btn3">切换</button>
    <div></div>
    <script src="js/jquery-3.4.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script>
        //slideDown( speed ) ：显示
        //slideUp( speed )：隐藏
        //slideToggle( speed )等价于slideDown+slideUp
        // speed 参数规定隐藏/显示的速度，可以取以下值："slow"、"fast" 或毫秒
        $("#btn1").on("click",function(){
            $("div").slideDown("slow");
        });
        $("#btn2").on("click",function(){
            $("div").slideUp("slow");
        });
        $("#btn3").on("click",function(){
            $("div").slideToggle("slow");
        });
    </script>
</body>
```

#### 1.5.3不改变元素的大小（淡入淡出效果）

```
	fadeIn( speed ) 显示
​	fadeOut( speed ) 隐藏
​	fadeToggle( speed ) 等价于fadeIn+fadeOut动画
​	fadeTo( speed , 透明度 ) 方法允许渐变为给定的不透明度（值介于 0 与 1 之间）

可选的 speed 参数规定隐藏/显示的速度，可以取以下值："slow"、"fast" 或毫秒
```

```
<style>
    div{
        width: 200px;
        height: 200px;
        background-color: black;
    }
</style>
<body>
    <button class="btn btn-default" id="btn1">显示</button>
    <button class="btn btn-primary" id="btn2">隐藏</button>
    <button class="btn btn-primary" id="btn3">切换</button>
    <button class="btn btn-primary" id="btn4">淡化</button>
    <button class="btn btn-primary" id="btn5">恢复</button>
    <div></div>
    <script src="js/jquery-3.4.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script>
        
        $("#btn1").on("click",function(){
            $("div").fadeIn("slow");
        });
        $("#btn2").on("click",function(){
            $("div").fadeOut("slow");
        });
        $("#btn3").on("click",function(){
            $("div").fadeToggle("slow");
        });
        $("#btn4").on("click",function(){
            $("div").fadeTo("slow",0.5);
        });
        $("#btn5").on("click",function(){
            $("div").fadeTo("slow",1);
        });
    </script>
</body>
```



#### 1.5.4链

```
链是允许我们在同一个元素上在一条语句中运行多个jQuery方法，可以把动作/方法链接在一起 ；

例如：点击一次按钮，让div完成4个指定动作

​	1.背景变粉
​	2.字体变绿
​	3.收缩
​	4.拉伸
```

```
<style>
    div{
        width: 200px;
        height: 200px;
        background-color: black;
        color: white;
        font-size: 3em;
    }
</style>
<body>
    <button class="btn btn-default" id="btn1">点击使他变绿,变淡</button>
    <button class="btn btn-warning" id="btn2">点击恢复</button>
    <button class="btn btn-info" id="btn3">点击</button>
    <div>
        hello world
    </div>
    <script src="js/jquery-3.4.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script>
        $("#btn1").on("click",function(){
            $("div").css("background-color","white").css("color","green").hide("slow").slideDown("slow").fadeTo("slow",0.6);
        });
        $("#btn2").on("click",function(){
            $("div").css("background-color","black").css("color","white").fadeTo("slow",1).slideUp("slow").show("slow");
        });
        $("#btn3").click(function(){ 
            $("div").css("background- color","pink").css("color","green").slideUp(1000).slideDown(1000); 
        });
    </script>
</body>
```



### 1.6 DOM和CSS的操作

#### 1.6.1属性函数

```
attr( "属性" ); 【获得】元素的属性值
​	attr( "属性" , "新值" ); 【修改】元素的属性值
​	attr( 样式参数 ) ：样式参数可以写成json格式

val() ; 获得表单元素中的value值
​	val("x") 修改表单元素中的value值

html(); 获得元素中的内容（标签+文本）
​	html("x") 修改元素中的内容（标签+文本）

text(); 获得元素中的文本
​	text("x") 修改元素中的文本

<img title=""/>    title为图片的标题
```

```
<body>
    <button class="btn btn-default" id="btn1">点我试试</button>
    <button class="btn btn-primary" id="btn2">复原</button>
    <hr>
    <img src="img/1.jpg" title="二次元" width="300px">
    <hr>

    <button class="btn btn-default" id="btn3">点击就送</button>
    <hr>
    <input id="username">
    <div>
        <h1><i>请先在输入框中输入！！，再点击</i></h1>
    </div>

    <script src="js/jquery-3.4.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script>
        $("#btn1").on("click",function(){
            $("img").attr("src","img/2.jpg").attr("title","三次元").attr( {width:"200",height:"200"} );
            
        });
        $("#btn2").on("click",function(){
            $("img").attr("src","img/1.jpg").attr("title","二次元").attr( {width:"300"} );
        });
        $("#btn3").on("click",function(){
            if($("input").val() == ""){
                alert("输入为空,请先在输入框中输入！！，再点击");
                return;
            }
            alert($("input").val());
            $("input").val("我就改你的值怎么了");
            alert("我不仅改，我还弹窗，你气不气...");

            alert($("div").html());
            alert($("div").text());
            $("div").text("我不仅改，我还改下面的，你气不气...");
        });
    </script>
</body>
```

#### 1.6.2样式函数

```
	css( "属性"); 获得该属性值
​	css( "属性","值"); 设置属性的值
​	css( { json} ); 设置多个属性的值
```

```
	width(); 获得元素的宽度
​	width( number ); 修改元素的宽度
​	height(); 获得元素的高度
​	height( number ); 修改元素的高度
```

```
<style>
    div{
        width: 200px;
        height: 200px;
        background-color: black;
    }
</style>
<body>
    <button class="btn btn-default" id="btn1">改变颜色</button>
    <button class="btn btn-info" id="btn2">改变大小</button>
    <button class="btn btn-warning" id="btn3">改变颜色和大小</button>
    <button class="btn btn-success" id="btn4">复原</button>
    <div></div>

    <script src="js/jquery-3.4.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script>
        //css( "属性"); 获得该属性值
        //css( "属性","值"); 设置属性的值
        //css( { json} ); 设置多个属性的值
        $("#btn1").on("click",function(){
            alert('当前颜色为\n'+$("div").css("background-color"));
            $("div").css("background-color","red");
        });
        // width(); 获得元素的宽度
        // width( number ); 修改元素的宽度
        // height(); 获得元素的高度
        // height( number ); 修改元素的高度
        $("#btn2").on("click",function(){
            alert('当前的大小参数为\n宽度: '+$("div").width() + '\n高度：'+$("div").height());
            $("div").width(300);
            $("div").height(300);
            alert('改变后的大小参数为\n宽度: '+$("div").width() + '\n高度：'+$("div").height());
        });
        $("#btn3").on("click",function(){
            $("div").css({"background-color":"red","width":"300px","height":"300px"});
        });
        $("#btn4").on("click",function(){
            $("div").css("background-color","black");
            $("div").width(200);
            $("div").height(200);
        });
    </script>
</body>
```

#### 1.6.3类样式函数

addClass(); 为元素添加类样式

removeClass(); 将元素的类样式移除

toggleClass(); 样式的切换（有->无，无->有）

```
<style>
    div{
        width: 100px; 
        height: 100px; 
        background-color: #000; 
    }
    .a{ 
        background: palevioletred; 
        border-radius: 50%; 
    }
    .b{ 
        border:5px dashed darkcyan; 
        opacity: 0.6; 
    }
    .cn{
        background: #000; 
        color:#FFF; 
        font-family: 字魂49号-逍遥行书; 
    }
</style>
<body>
    <button class="btn btn-default" id="btn1">试试</button> 
    <button id="btn2">取消透明度</button> 
    <button id="btn3">样式切换</button> 
    <button id="btn4">复原</button> 
    <hr> 
    <div></div> 
    <h1>你好，世界！</h1>
    <script src="js/jquery-3.4.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script>
        //addClass(); 为元素添加类样式
        //removeClass(); 将元素的类样式移除
        //toggleClass(); 样式的切换（有->无，无->有）
        $("#btn1").on("click",function(){
            $("div").addClass(" a b");
        });
        $("#btn2").on("click",function(){
            $("div").removeClass("b");
        });
        $("#btn3").on("click",function(){
            $("h1").toggleClass("cn");
        });
        $("#btn4").on("click",function(){
            $("div").removeClass("a b");
            $("h1").removeClass("cn");
        });
    </script>
</body>
```



#### 2.6.4节点操作

```
创建节点
​	工厂函数$()用于获取或创建节点

插入节点
​	插入子节点
append(content)				$(A).append(B)表示将B追加到A中
appendTo(content)			$(A).appendTo(B)表示把A追加到B中
prepend(content)			$(A). prepend (B)表示将B前置插入到A中
prependTo(content)			$(A). prependTo (B)表示将A前置插入到B中


插入同辈节点

after(content)				$(A).after (B)表示将B插入到A之后 
insertAfter(content)		$(A). insertAfter (B)表示将A插入到B之后
before(content)				$(A). before (B)表示将B插入至A之前
insertBefore(content)		$(A). insertBefore (B)表示将A插入到B之前


替换节点
​    replaceWith()
​    replaceAll()

复制节点
​    clone()

删除节点
​    remove()删除整个节点
​    empty()清空节点内容
```

#### **7.4** **元素的过滤**

```
first()：过滤第一个元素
last()：过滤最后一个元素
eq(index)：过滤到下标为index的元素
filter() ：允许您规定一个标准。不匹配这个标准的元素会被从集合中删除，匹配的元素会被返回。
​		例如：$("p").filter(".url");返回带有类名 "url" 的所有 <p> 元素

:contains() 选择器选取包含指定字符串的元素
		$("p:contains(is)")    表示选择所有包含 "is" 的 <p> 元素

	可以将jquery的filter方法和contains方法一起使用来达到更加模糊匹配的目的
		$(".box").filter(":contains(李)").css("color", "#f00"); 
		
		var $d = $("table tr:gt(0)").
filter(":contains('"+$.trim($("input#txtName").val())+"')");
		给input输入的值去空格：$.trim($('#txtUserName').val())
		注：只能去掉左右两边空格，中间的空格去不掉。
	
not()：除了什么之外的元素
is()：返回布尔，判断是不是这种元素
```

