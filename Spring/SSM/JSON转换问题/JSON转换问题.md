JSON传参：

```
问题描述：
如果直接传递JSON格式的数据，JS会将其解析成JacaScript值，传输数据就会出错，因此要进行处理
```

数组类传参参照
前端传参：var par = JSON.stringify(new Array());

```
前端传输：JSON.stringify() 方法用于将 JavaScript 值转换为 JSON 字符串。
如果格式为：
	"pk_corp":"d633d5c2987b447b95db9649c372ce4f"
	后端接收的字符串会为：'这是乱码'pk_corp'这是乱码'：'这是乱码'd633d5c2987b447b95db9649c372ce4f'这是乱码'
	
	因此需要将其转化为JSON字符，再进行发送：
		JSON.stringify("pk_corp":"d633d5c2987b447b95db9649c372ce4f")；
		
语法：
JSON.stringify(value[, replacer[, space]])
```

后端接收：

```
问题描述：
前端传递过来的数据是通过JSON.stringify()处理过的，但是特殊字符会有转义的问题，因此也要进行处理
```

List<HashMap> list = JacksonUtils.readValueList(workstate, HashMap.class);

这个一般都有工具类，封装好了直接调用

核心思路：就是将request获取到的字符串通过转义特殊字符，将其还原

```
前端发送过来的数据如果不经过处理，后端接受会发现特殊字符是转义以后的，例如：
	例如“<”,保存到数据库时会变成“&lt;”,但是你想保存到数据库的就是“<”
因此可以用HtmlUtils.htmlUnescape()进行转义，
如果直接调用:
	String dataTemplate = request.getParameter("dataTemplate");
	//dataTemplate=&quot;pk_corp&quot;：&quot;d633d5c2987b447b95db9649c372ce4f&quot;
	dataTemplate = HtmlUtils.htmlUnescape(dataTemplate);
	//dataTemplate = "pk_corp":"d633d5c2987b447b95db9649c372ce4f";
```

```
但是公司这里使用的是工具类，朱熠大佬让我用HtmlUtils.htmlUnescape()替换掉，这个封装的工具类有什么问题吗？
dataTemplate = dataTemplate.replaceAll("&quot;", "\"");
```

