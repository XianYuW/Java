## SSM实现文件上传

### 1.配置springMVC.xml

```xml
	<!--5.配置文件解析器-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 设置最大文件上传限制：5M-->
        <property name="maxUploadSize" value="5242800"/>
    </bean>
```



### web层

图片上传接口：

```
1.判断文件是否为空，对项目路径的路径进行获取(获取需要的路径)，同时获取文件名
2.生成新的文件名：策略--可以为时间戳，或者时间戳+文件名
3.设置文件上传路径，同时在该路径下创建文件以及文件夹
4.使用file.transferTo(文件上传路径)
5.将文件以及文件路径返回集合，进行响应
```

```java
	/**
     * 课程图片上传
     */
    @RequestMapping(value = "/courseUpload")
    public ResponseResult fileUpload(@RequestParam("file") MultipartFile file, HttpServletRequest request){
        //判断上传文件是否为空
        if(file.isEmpty()){
            throw new RuntimeException();
        }
        //获取项目部署路径
        //D://software//apache-tomcat-9.0.41//webapps//ssm_web
        String realPath = request.getServletContext().getRealPath("/");
        //D:\software\apache-tomcat-9.0.41\webapps
        String substring = realPath.substring(0, realPath.indexOf("ssm_web"));

        //获取文件名     如：a.jpg
        String originalFilename = file.getOriginalFilename();

        //生成新文件名 策略 如：时间戳+文件名  time.jpg
        String newFilename =
            System.currentTimeMillis()+originalFilename.substring(originalFilename.lastIndexOf("."));

        //文件上传
        // D://software//apache-tomcat-9.0.41//webapps//upload
        String uplodaPath = substring + "upload//";
        File filePath = new File(uplodaPath,newFilename);

        //如果目录不存在就创建目录
        if(!filePath.getParentFile().exists()){
            filePath.getParentFile().mkdirs();
            System.out.println("创建目录"+filePath);
        }

        //图片进行了上传
        try {
            file.transferTo(filePath);
        } catch (IOException e) {
            e.printStackTrace();
        }

        //将文件名和文件路径返回，进行相应
        Map<String, String> map = new HashMap<>();
        map.put("fileName",newFilename);
        //filePath: http://localhost:8080/upload/time.jpg
        map.put("fielPath","http://localhost:8080/upload/"+ newFilename);


        ResponseResult result = new ResponseResult(true,200,"上传成功",map);
        return result;
    }
```





实现新增课程，这里	CourseVo拷贝到新建的Course实体	有个坑，导入的BeanUtils.cpoyProperties()方法，

引入的BeanUtils类，只能是org.apache.commons.beanutils.BeanUtils;该方法是	简单的复制嵌套bean的引用

```
	System.out.println(student);    	
    Student stu = new Student();
    BeanUtils.copyProperties(stu, student);
    System.out.println(stu);
    stu.getPeople().setName("ttff");
    System.out.println(student);
    System.out.println(stu);
```

输出结果

```
Student [score=80, className=ll, name=tf, people=People [name=tt, age=20], createDate=null]
Student [score=80, className=ll, name=tf, people=People [name=tt, age=20], createDate=null]
Student [score=80, className=ll, name=tf, people=People [name=ttff, age=20], createDate=null]
Student [score=80, className=ll, name=tf, people=People [name=ttff, age=20], createDate=null]
```

[参考]: https://blog.csdn.net/u011079274/article/details/52243940?utm_medium=distribute.pc_relevant.none-task-blog-title-3&amp;spm=1001.2101.3001.4242

