# @RequestBody和@RequestParam区别

# @RequestParam

注解@RequestParam接收的参数是来自HTTP请求体或请求url的QueryString中。

RequestParam可以接受简单类型的属性，也可以接受对象类型。

@RequestParam有三个配置参数：

- `required` 表示是否必须，默认为 `true`，必须。
- `defaultValue` 可设置请求参数的默认值。
- `value` 为接收url的参数名（相当于key值）。 

**@RequestParam用来处理 `Content-Type` 为 `application/x-www-form-urlencoded` 编码的内容，`Content-Type`默认为该属性****。@RequestParam也可用于其它类型的请求，例如：POST、DELETE等请求**。



@RequestParam 接受JSON的字符串：https://blog.csdn.net/qq_40470612/article/details/104225419

 

不推荐使用@RequestParam接收application/json，这时候就需要使用到@RequestBody。

# @RequestBody


注解@RequestBody接收的参数是**来自requestBody**中，即**请求体**。

```
一般用来处理 Content-Type: 为application/json
```

```
GET请求中，因为没有HttpEntity，所以@RequestBody并不适用。

POST请求中，通过HttpEntity传递的参数，必须要在请求头中声明数据的类型Content-Type，SpringMVC通过使用

HandlerAdapter 配置的HttpMessageConverters来解析HttpEntity中的数据，然后绑定到相应的bean上。
```



## **从content-type方面总结：**



## **从两种注解方式总结：**