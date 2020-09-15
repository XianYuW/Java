# BootStrap

## 1.bootstrap简介

### 1.1什么是Bootstrap？

Bootstrap来自 Twitter，是目前最受欢迎的**响应式**前端框架。

Bootstrap是基于 HTML、CSS、JavaScript的，它简洁灵活，使得 Web 开发更加快捷。

### 1.2为什么使用Bootstrap？

```
	移动设备优先：自 Bootstrap3 起，框架包含了贯穿于整个库移动设备优先的样式。
​	不是简单的增加一些可选的针对移动设备的样式，而是直接融合进了框架的内核中。
​	也就是说，针对移动设备的样式融合进了框架的每个角落，而不是增加一个额外的文件。
​	浏览器支持：所有的主流浏览器都支持 Bootstrap。
​	容易上手：只要您具备 HTML 和 CSS 的基础知识，您就可以开始学习 Bootstrap。
​	【响应式设计】：Bootstrap 的响应式 CSS 能够自适应于台式机、平板电脑和手机。
​	它为开发人员创建接口提供了一个简洁统一的解决方案。
​	它包含了功能强大的内置组件，易于定制。
​	它还提供了基于 Web 的定制。
​	它是开源的。
```

## 2.Bootstrap的使用

### 2.1表格

和之前的表格相比，标签更丰富更加语义化，效果更佳美观

#### 2.1.1丰富的标签

```
	<table> 为表格添加基础样式
​	<thead> 表格**标题行**的容器元素（<tr>） 
​	<tbody> 表格**主体**中的表格行的容器元素（<tr>） 
​	<tr> 表格行
​	<td> 默认的表格单元格。
​	<th> 特殊的表格单元格，（居中和加粗的效果）。必须在<thead> 内使用。
​	<caption> 关于表格存储内容的描述或**总结**。
```

#### 2.1.2好看的类样式

```
	.table 为任意 <table> 添加基本样式 (只有横向分隔线)
​	.table-striped 在 <tbody> 内添加斑马线形式的条纹 ( IE8 不支持) ，**隔行变色**
​	.table-bordered 为所有表格的单元格添加边框
​	.table-hover 在 <tbody> 内的任一行启用鼠标悬停状态，鼠标悬停高亮突出显示
​	.table-condensed 让表格更加紧凑
```

#### 2.1.3情景色类样式

```
适合应用在<th>、<tr>，<td>

​	.active 激活效果（悬停颜色）
​	.success 表示成功或积极的动作
​	.info 表示普通的提示信息或动作
​	.warning 表示警告或需要用户注意
​	.danger 表示危险或潜在的带来的负面影响的动作
```

#### 2.1.4响应式表格

表格的父元素设置响应式，小于768px，出现边框

```html
    <table class="table table-responsive">
        <caption >目录</caption>
        <thead>
            <tr>
                <th>产品</th>
                <th>付款日期</th>
                <th>状态</th>
            </tr>
        </thead>
        <tr class="info">
            <td>产品</td>
            <td>2020-1-1</td>
            <td>待发货</td>
        </tr>
        <tr class="active">
            <td>产品2</td>
            <td>2020-1-1</td>
            <td>发货</td>
        </tr>
        <tr class="success">
            <td>产品3</td>
            <td>2020-1-1</td>
            <td>待发货</td>
        </tr>
        <tr class="warning">
            <td>产品4</td>
            <td>2020-1-1</td>
            <td>待发货</td>
        </tr>
        <tr class="danger">
            <td>产品5</td>
            <td>2020-1-1</td>
            <td>待发货</td>
        </tr>
    </table>
```

## 2.2表单

### 2.2.1表单布局

#### 2.2.1.1默认布局

```
<body style="padding:100px;">
    <form class="form-inline">
        <div class="form-group">
            <label >邮箱</label> 
            <input type="email" class = "form-control"placeholder="输入邮箱">
        </div>
        <div class="form-group">
            <label for="">密码</label> 
            <input type="password" class="form-control" placeholder="输入密码">
        </div>
        <button class="btn btn-primary">提交</button>
    </form>
</body>
```



#### 2.2.1.2内联布局

```
	让所有表单元素居于一行
​	注意：当小于768px时,会自动还原成移动端样式.

<form class="form-inline">
```

### 2.2.2表单控件

##### 2.2.2.1输入框

```
	<form>
        <div class="form-gruop">
            <label for="">输入框</label>
            <input type="text" class="form-control" placeholder="请输入...">
        </div>
    </form>
```



##### 2.2.2.2 文本框

```
	<div class="form-gruop">
            <label for="">文本框</label>
            <textarea class="form-control" cols="30" rows="10"></textarea>
        </div>
```



##### 2.2.2.3复选框

```
	checkbox 默认
​	checkbox-inline 内联
```

```
        <div class="checkbox">
            <label>
                <input type="checkbox">抽烟
            </label>
        </div>
        <div  class="checkbox">
            <label>
                <input type="checkbox">喝酒
            </label>
        </div>

        <hr color="red">
        <div class="checkbox-inline">
            <label>
                <input type="checkbox">洗澡澡
            </label>
        </div>
        <div  class="checkbox-inline">
            <label>
                <input type="checkbox">烫头头  
            </label>
        </div>
```



##### 2.2.2.4复选按钮

```
	<form>
		<div class="btn-group" data-toggle="buttons"> 
            <!--data-toggle="buttons" 忽略复选框按钮-->
            <label class="btn btn-primary">
                <input type="checkbox">吃饭
            </label>
            <label class="btn btn-primary">
                <input type="checkbox">看书
            </label>
            <label class="btn btn-primary">
                <input type="checkbox">学习
            </label>
            <label class="btn btn-primary">
                <input type="checkbox">快乐
            </label>
        </div>
    </form>
    <!--引入js文件实现复选功能 -->
    <script src="js/jquery-3.3.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
```



##### 2.2.2.5单选框

```
		<!-- 单选框-->
        <label class="radio">
            <input type="radio" name="sex">male
        </label>
    
        <label class="radio">
            <input type="radio" name="sex">female
        </label>
        <hr>
        <!--inline-->
        <label class="radio-inline">
            <input type="radio" name="gender">male
        </label>
        <label class="radio-inline">
            <input type="radio" name="gender">female
        </label>
```



##### 2.2.2.6 单选按钮

```
		<!--单选按钮-->
        <div class="btn-gruop" data-toggle="buttons">
            <label class="btn btn-primary">
                <input type="radio" name="gender">male
            </label>
            <label class="btn btn-primary">
                <input type="radio" name="gender">female
            </label>
        </div>
```



### 2.3 按钮

按钮样式

```
		<!--按钮样式-->
        <div>
            <button class="btn btn-default">默认按钮</button>
            <button class="btn btn-primary">主要按钮</button>
            <button class="btn btn-success">成功按钮</button>
            <button class="btn btn-info">信息按钮</button>
            <button class="btn btn-warning">警告按钮</button>
            <button class="btn btn-danger">危险按钮</button>
            <button class="btn btn-link">连接按钮</button>
        </div>
```

按钮大小

```
		<!--按钮大小-->
        <div>
            <button class="btn btn-primary btn-lg">超大按钮</button>
            <button class="btn btn-primary">大按钮</button>
            <button class="btn btn-primary btn-sm">小按钮</button>
            <button class="btn btn-primary btn-xs">超小按钮</button>
        </div>
```

按钮状态：

​	激活状态：按钮在激活时将呈现为被按压的外观（深色的背景、深色的边框、阴影）;

​	禁用状态：当您禁用一个按钮时，它的颜色会变淡 50%，并失去渐变;

```
		<!--按钮状态-->
        <div>
            <button class="btn btn-default">默认按钮</button>
            <button class="btn btn-default active">激活按钮</button>
            <button class="btn btn-default" disabled>禁用按钮</button>
        </div>
```



### 2.4图片

```
	.img-rounded：添加 border-radius:6px 来获得图片圆角。
​	.img-circle：添加 border-radius:50% 来让整个图片变成圆形。
​	.img-thumbnail：添加一些内边距（padding）和一个灰色的边框
```

响应式图片

```
<img src="img/1.jpg" class="img-responsive">
```

图片练习：

```
<body style="padding: 100px;">
    <div>
        <img src="img/2.jpg" class="img-rounded" width="33%">
        <img src="img/2.jpg" class="img-circle" width="33%">
        <img src="img/2.jpg" class="img-thumbnail" width="33%">
        <!--响应式图片-->
        <img src="img/2.jpg" class="img-responsive">
    </div>
</body>
```



### 2.5下拉菜单组件

```
用法的关键核心

​	1.外围容器使用 class="dropdown"包裹
​	2.内部点击按钮事件绑定 data-toggle="dropdown" 
​	3.菜单元素使用 class="dropdown-menu"

class="dropdown":下拉菜单
span标签class="caret"：显示一个倒三角符号

按钮事件：点击显示隐藏的界面
<button class="btn btn-primary" data-toggle="dropdown"
```

```
<body style="padding: 100px;">
    <div  class="dropdown">
    <!--class="dropdown":下拉菜单--> 
        <button class="btn btn-primary" data-toggle="dropdown">
            热门
            <span class="caret"></span>
            <!--span标签class="caret"：显示一个倒三角符号-->
        </button>
        
        <ul class="dropdown-menu">
            <li><a href="#">1</a></li>
            <li><a href="#">2</a></li>
            <li><a href="#">3</a></li>
        </ul>
    </div>
    <script src="js/jquery-3.3.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
</body>
```



### 2.6分页组件

分页：<ul class="pagination"></ul>

```
<body>
    <form>
        <div>
            <ul class="pagination">
                <li class="previous"> <a href="#"> &laquo; </a> </li>
                <!--左箭头-->
    
                <li class="active"><a href="#">1</a></li>
                <li><a href="#">2</a></li>
                <li><a href="#">3</a></li>
                <li class="next"><a href="#">&raquo;</a></li>
                <!--右箭头-->
            </ul>
        </div>
        <div>
            <ul class="pager">
                <li><a href="#">上一页</a></li>
                <li class="active"><a href="#">1</a></li>
                <li><a href="#">2</a></li>
                <li><a href="#">3</a></li>
                <li><a href="#">下一页</a></li>
            </ul>
        </div>
    </form>
</body>
```



### 2.7栅格系统

class="container":外围容器 栅格化布局

class="col-lg-3 col-md-4 col-sm-6": 界面布局

​		col-lg-3：大屏幕，一行显示4个

​		col-md-4：中屏幕，一行显示4个

​		col-sm-6：小屏幕，一行显示6个

```
<body>
    <form action="">
        <div  class="container">
            <div class="row">
                <!-- col-lg-3 当大屏幕时，一个div占3份，一行显示4个div --> 
                <!-- col-md-4 当中屏幕时，一个div占4份，一行显示3个div --> 
                <!-- col-sm-6 当小屏幕时，一个div占6份，一行显示2个div --> 
                <!-- 超小屏幕时，一个div占12份，一行显示1个div，默认 -->
                <div class="col-lg-3 col-md-4 col-sm-6 a">1</div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">2</div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">3</div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">4</div>
                
                <div class="col-lg-3 col-md-4 col-sm-6 a">5</div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">6</div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">7</div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">8</div>

                <div class="col-lg-3 col-md-4 col-sm-6 a">9</div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">10</div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">11</div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">12</div>

            </div>

        </div>
    </form>
    
</body>
```



### 2.8缩略图组件

class="thumbnail"：缩略图

class="caption"：<caption> 标签定义表格的标题。

```
配合响应式的栅格系统
```

```
<body>
    <form action="">
        <div class="container">
            <div class="row">
                <div class="col-lg-3 col-md-4 col-sm-6 a">
                    <div class="thumbnail">
                        <img src="img/2.jpg" >
                        <div class="caption">
                            <h3>视频/图片标题</h3>
                            <p>视频/图片介绍...</p>
                            <p>
                                <a href="#" class="btn btn-primary">试看</a>
                                <a href="#" class="btn btn-warning">购买</a>
                            </p>
                        </div>
                    </div>
                </div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">
                    <div class="thumbnail">
                        <img src="img/2.jpg" >
                        <div class="caption">
                            <h3>视频/图片标题</h3>
                            <p>视频/图片介绍...</p>
                            <p>
                                <a href="#" class="btn btn-primary">试看</a>
                                <a href="#" class="btn btn-warning">购买</a>
                            </p>
                        </div>
                    </div>
                </div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">
                    <div class="thumbnail">
                        <img src="img/2.jpg" >
                        <div class="caption">
                            <h3>视频/图片标题</h3>
                            <p>视频/图片介绍...</p>
                            <p>
                                <a href="#" class="btn btn-primary">试看</a>
                                <a href="#" class="btn btn-warning">购买</a>
                            </p>
                        </div>
                    </div>
                </div>
                <div class="col-lg-3 col-md-4 col-sm-6 a">
                    <div class="thumbnail">
                        <img src="img/2.jpg" >
                        <div class="caption">
                            <h3>视频/图片标题</h3>
                            <p>视频/图片介绍...</p>
                            <p>
                                <a href="#" class="btn btn-primary">试看</a>
                                <a href="#" class="btn btn-warning">购买</a>
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </form>
</body>
```

### 2.9模态框组件

```
data-toggle="modal" 触发类型:模态框modal
					HTML5 自定义的 data 属性 data-toggle 用于打开模态窗口

data-target="#myModal" 触发的节点
tabindex 属性规定元素的 tab 键控制次序（当 tab 键用于导航时）

data-backdrop="static" 点击黑灰色背景，不会关闭模态框
id="mymodal" 
tabindex="-1"		可以实现esc退出弹框，但是原理未知

class="modal-dialog"   表示默认类型的弹出框 
class="modal-full"     表示增大的弹出框

class="modal-content"    弹框内容的声明

<div class="modal-header">，modal-header 是为模态窗口的头部定义样式的类。
class="close"			作用：显示关闭按钮
&times;    表示x符号
data-dismiss="modal"	点击则会关闭当前弹窗

<button class="close" data-dismiss="modal">
	<span>&times;</span>
</button>             用来在右上角显示x，表示关闭

class="modal-body"     用于为模态窗口的主体设置样式。
class="modal-footer"	用于为模态窗口的底部设置样式。



data-dismiss="modal"
```



```
<body style="padding:100px;">
    <button class="btn btn-warning" data-toggle="modal" data-target="#mymodal">弹框</button>
    <!--声明定义模态框组件-->
    <div class="modal" id="mymodal"   tabindex="-1" data-backdrop="static" >
        <!--窗口声明:默认弹框-->
        <div class="modal-dialog">
            <!--内容声明-->
            <div class="modal-content">
                <!--1.框的标题-->
                <div class="modal-header">
                    <!--按钮  class="close"-->
                    <!--data-dismiss="modal"-->
                    <button class="close" data-dismiss="modal">
                        <span>&times;</span>
                    </button>
                    <h4 class="modal-title">友情提示，我是弹框标题</h4>
                </div>
                <!-- 2.框内信息 -->
                <div class="modal-body">
                    框内信息
                    <hr>
                    我还能显示图片<br>
                    <img src="img/2.jpg" width="20%">
                    <hr>
                    还有输入框<br>
                    <input type="text">
                </div>
                <!-- 3.模态窗口的底部设置样式 -->
                <div class="modal-footer">
                    <button class="btn btn-info">确定</button>
                    <button class="btn btn-default" data-dismiss="modal" >
                    	取消
                    </button>
                </div>
            </div>
            
        </div>
    </div>
    <script src="js/jquery-3.3.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
</body>
```



## 3.案例

### 3.1首页轮播大图

```
大小屏幕自动适应,扩大缩小浏览器
​滚动数字区点击左右切换图片
​左右区域点击切换
​默认5秒钟自动切换下一张
​最后一张,回到第一张
```

```
题目分析：
​ 	容器：最外层div，需要一个data-ride=”carousel”来指定为轮播放插件，并且提供一个Id，方			便圆圈指示符的关联
​	图片列表部分，用一个外层div包裹所有，然后每个img会被一个div，则class为item的包裹住
​	圆圈指示符：用一个ol列表来显示其各图形列表项，每个列表项需要指定data-slide-to=”index”				属性，用于标记当前圆圈的索引号
​	左右控制按钮：实现向左、向右移动的功能
```



```
data 属性解释：

1.data-slide 接受关键字 prev 或 next，用来改变幻灯片相对于当前位置的位置；
2.data-slide-to 来向轮播底部创建一个原始滑动索引，data-slide-to="end" 将把滑动块移动到一个特定的索引，索引从 0 开始计数。
3.data-ride="carousel"	属性用户标记轮播在页面加载时开始动画播放
						默认使用Bootstrap的Carousel组件
						
Carousel-caption：表示每个item项应该有标题信息，默认显示下、中位置
slide，他实现了图片的切换

```

```
滚动圆点区 
ol
class="carousel-indicators"				用于创建这个指示器
									圆圈部分样式，都是绝对定位，每个li设置为行内块元素
li
data-slide-to="0" 					属性用于指引位置 
class="active" 						定义默认激活状态
data-slide-to="0"					轮播底部创建一个原始滑动索引，从0开始，每多一个+1
class="active"						初始小圆点激活

<!-- 图片区 -->
div  class="carousel-inner"			将div的内容放在同一个div中
									旋转图片列表区域，其中每项有item来修饰

<!-- 左右切换 -->
Carousel-control			设置向左、向右按钮的样式，其中会设置渐变、透明度等信息，
							提供了icon-prev、icon-next两种额外样式
```

```
<body style="padding: 70px;">
    <div id="myCarousel" class="carousel slide" data-ride="carousel">
        <!-- 滚动圆点区 --> 
        <ol class="carousel-indicators">
            <li data-slide-to="0" data-target="#myCarousel"class="active">				</li>
            <li data-slide-to="1" data-target="#myCarousel"></li>
            <li data-slide-to="2" data-target="#myCarousel"></li>
            <li data-slide-to="3" data-target="#myCarousel"></li>
            <li data-slide-to="4" data-target="#myCarousel"></li>
        </ol>
        <!-- 图片区 -->
        <div class="carousel-inner">
            <div class="item active"><img src="img/2.jpg" ></div>
            <div class="item"><img src="img/1.jpg" ></div>
            <div class="item"><img src="img/2.jpg" ></div>
            <div class="item"><img src="img/1.jpg" ></div>
            <div class="item"><img src="img/2.jpg" ></div>
        </div>
        <!-- 左右切换 -->
        <a href="#myCarousel"data-slide="prev" class="carousel-control left">
            <span class="glyphicon glyphicon-chevron-left"></span>
        </a>
        <a href="#myCarousel"data-slide="next" class="carousel-control right">
            <span class="glyphicon glyphicon-chevron-right"></span>
        </a>
    </div>
    <script src="js/jquery-3.3.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
</body>
```



### 3.2响应式导航条

```
class="navbar navbar-default"		相应的导航条样式
class="container"					响应式的容器
class="navbar-header"				导航条的头部
class="navbar-brand"				设置导航条组件内的品牌图标
	navbar-brand默认是放文字的,也可以放图片，但必须是小图片,如果图片太大，位置就会靠下.

data-toggle告诉脚本当按钮被点击时该做什么,而data-target则告诉脚本点击时切换哪个部分。
(Collapse) 							Bootstrap 折叠插件
collapsed							设置button元素为在视口小于768px时才显示
class="navbar-toggle"				设置button元素为导航条组件的切换钮
data-toggle="collapse" 				数据切换的事件，特效是折叠
data-target="#nav" 					折叠效果的目标是下面的#nav容器

<span class="sr-only"></span>
<span class="icon-bar"></span>
<span class="icon-bar"></span>
<span class="icon-bar"></span>

导航项
div	class="collapse navbar-collapse"	折叠导航栏
ul	class="nav navbar-nav"			设置ul为nav样式，导航条-nav
	navbar-right					右对齐
	
<span class="glyphicon glyphicon-home"></span> 
									bootstrap官网的组件/图标
```



```
<body>
    <!--相应的导航条样式-->
    <div class="navbar navbar-default">
        <!-- 响应式的容器 -->
        <div class="container">
            <!-- 导航条的头部 -->
            <div class="navbar-header">
                <a href="#" class="navbar-brand">
                    科技有限公司
                </a>
                <!-- data-toggle="collapse" 数据切换的事件，特效是折叠--> 
                <!-- data-target="#nav" 折叠效果的目标是下面的#nav容器-->
                <button class="navbar-toggle" data-toggle="collapse" data-target="#nav"> 
                    <span class="sr-only"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
            </div>

            <!-- 导航项 -->
            <div id="nav" class="collapse navbar-collapse">
                <ul class="nav navbar-nav navbar-right">
                    <li class="active"> 
                        <a href="#"> 
                        	<span class="glyphicon glyphicon-home"></span> 
                        	主页
                        </a> 
                    </li>
                    <li> 
                    	<a href="#"> 
                    		<span class="glyphicon glyphicon-camera"> </span> 
                    		产品
                    	</a> 
                    </li> 
                    <li> 
                    	<a href="#"> 
                    		<span class="glyphicon glyphicon-tint"></span> 
                    		竞争
                    	</a> 
                    </li> 
                    <li> 
                    	<a href="#"> 
                    		<span class="glyphicon glyphicon-earphone"> 
                    		</span> 
                    		联系我们
                    	</a> 
                    </li>
                </ul>

            </div>
        </div>
    </div>
    <script src="js/jquery-3.3.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
</body>
```

