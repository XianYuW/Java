

### MySQL 5.7 解压版 安装教程

[参考]: https://www.cnblogs.com/horvey/p/10151706.html#idx_1



### 解压版下载地址：https://dev.mysql.com/downloads/mysql/

## 安装(解压+优化)

下载完成后我们得到的是一个压缩包(这不是废话吗)，将其解压，我们就可以得到MySQL 5.7的软件本体了(就是一个文件夹)，我们可以把它放在你想安装的位置(为了演示方便，我放在了D盘根目录下)。有的同学可能看了一眼目录的大小，1.64G...(内心OS：我了个去，怎么300M左右的东西解压后这么大！)

其实不用担心，文件夹里面的一些东西是可以删掉的，并不会影响正常使用，至少在我自己使用的时候没有问题(*如果在某种情况下可能有问题，恳请大神指教*)，我们可以删除文件夹里的`lib目录`(约1.28G)和bin目录下的`mysqld.pdb文件`(约162M)，根据在[stackoverflow](https://stackoverflow.com/questions/42301953/how-to-install-a-light-version-on-mysql/42303246#42303246)找到的说法，这是为了可以重新编译MySQL和调试而存在的，我们只是为了使用它，并不打算修改，所以可以直接删掉。删除后再看看文件夹的大小，是不是小了很多(大约只剩199M)。

## 配置

把软件解压好，该删的东西都删了，下一步就是配置MySQL了。

### 添加环境变量

打开MySQL的`bin`目录，点击红框的位置，此时MySQL的bin目录的路径会被选中，先**复制**此路径

右键`此电脑`→`属性`，点击`高级系统设置`

点击`环境变量`

在`系统变量`中新增系统变量

```
MTSQL_HOME
D:\software\mysql-8.0.22-winx64
```

找到path编辑，点击`新建`

```
%MYSQL_HOME%\bin
```

最后点击确定。

**如何验证是否添加成功？**

右键开始菜单(就是屏幕左下角)，选择`命令提示符(管理员)`，打开黑框，敲入`mysql`，回车。
如果提示`Can't connect to MySQL server on 'localhost'`则证明添加成功；
如果提示`mysql不是内部或外部命令，也不是可运行的程序或批处理文件`则表示添加添加失败，请重新检查步骤并重试。

### 新建配置文件

新建一个文本文件，内容如下：

```
[mysql]
default-character-set=utf8

[mysqld]
character-set-server=utf8
default-storage-engine=INNODB
```

把上面的文本文件另存为，在保存类型里选`所有文件 (*.*)`，文件名叫`my.ini`，存放的路径为MySQL的`根目录`(例如我的是`D:\software\mysql-8.0.22-winx64\`,根据自己的MySQL目录位置修改)。

上面代码意思就是配置数据库的默认编码集和默认存储引擎，关于`my.ini`文件的内容，网上有很多更详细的介绍，可以配置的选项有很多，这里我就简单地配置一下，不做展开，如果有兴趣可以在百度上找找相关的资料。



### 初始化MySQL

在刚才的黑框中敲入

```
mysqld --initialize-insecure
```

回车，稍微等待一会，如果出现没有出现报错信息(如下图)则证明data目录初始化没有问题，此时再查看MySQL目录下已经有data目录生成。

### 注册MySQL服务

在黑框里敲入

```
mysqld -install

会回显：Service successfully installed.
```

## 方式一：

### 启动MySQL服务

在黑框里敲入

```
net start mysql

回显：
MySQL 服务正在启动 ....
MySQL 服务已经启动成功。
```

### 修改默认账户密码

在黑框里敲入

```
mysqladmin -u root password 123456

回显：
mysqladmin: [Warning] Using a password on the command line interface can be insecure.
Warning: Since password will be sent to server in plain text, use ssl connection to ensure password safety.
```

`这里的`123456 	就是指默认管理员(即root账户)的密码，可以自行修改成你喜欢的。

## 方式二：

### 初始化mysql

在这里，初始化会产生一个随机密码,如下图框框所示，记住这个密码，后面会用到

```
mysqld --initialize --console
```

### 启动MySQL服务

在黑框里敲入

```
net start mysql

回显：
MySQL 服务正在启动 ....
MySQL 服务已经启动成功。
```

### 登录验证，mysql是否安装成功！

要注意上面产生的随机密码，不包括前面符号前面的空格

### 修改密码：

由于初始化产生的随机密码太复杂，，不便于我们登录mysql，因此，我们应当修改一个自己能记住的密码！！

```
alert user 'root'@'localhost' identified by 'root'
```

再次登录验证新密码





## 标记删除mysql服务

```
sc delete mysql
```



**至此，MySQL 5.7 解压版安装完毕！**



