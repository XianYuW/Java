# Redis

## 1.概念

redis是一款高性能的NOSQL的非关系型数据库，是用c语言开发的一个开源的高性能键值对数据库



### NoSQL数据库的特点：

​	1.数据之间没有关联关系

​	2.数据存储在内存中，而关系型数据库存储在硬盘中，查询速度远远不如nosql数据库

​	3.存储格式：采用key，value的形式、文档形式、图片形式等。因为它是基于键值对的形式，类似于表中主键和值的对应关系，不需要经过sql层的解析，所以性能非常高

​	4.可扩展性也是基于键值对，数据之间没有耦合性，所以非常容易水平扩展



### 为什么选择NOSQL？

当客户端向服务端发送请求时，服务端会向数据库发送查询请求，但是**操作关系型数据库是非常耗时的**，

而且**查询的是一些不太经常发生变化的数据**，这个时候要想优化查询速度，就采用了**缓存思想**来解决问题。

而从缓存中获取数据：

​	*有数据时：

​		1.直接返回

​	*没有数据：

​		1.从数据库查询

​		2.将数据放入缓存中

​		3.返回数据



### 总结

一般会将数据存储到关系型数据库中，在nosql中备份关系数据库的数据



## 2.下载安装

1.官网：https://redis.io

2.中文网：http://www.redis.net.cn/

3.解压直接可以使用：

​		*redis.windows.conf	:配置文件

​		*redis-cli.exe	:redis的客户端

​		*redis.server.exe	:redis的服务器端





## 3.命令操作

### 	3.1redis的数据结构

​		*redis存储的是：key，value格式的数据，其中key都是字符串，value有5中不同的数据结构

​			*value的数据结构：

```
1）字符串类型	string
2）哈希类型	hash	：map格式
3）列表类型	list	:linkerlist格式
4）集合类型	set		：不允许重复元素
5）有序集合类型	sortedset	：不允许重复元素，且元素有序
```

### 3.2字符串类型	string

*存储：set key value，重复set 会将前面的覆盖掉

*获取：get key

*删除：del key

### 3.3哈希类型hash

*存储：hset key field value，存储key中field属性的值value

*获取：hget key field 		获取key 中field属性的值value

​			hgetall key		获取key中所有属性field和对应的值value

*删除：del key field 

### 3.4列表类型 list	类似于队列

可以添加一个元素到列表的头部(左边)或者尾部(右部)

左边：lpush mylist a

右边：rpush mylist b

*添加：

​	*lpush key value	：将元素加入列表左边

​	*rpush key value	：将元素加入列表右边

*获取：

​	*lrange key start end	：范围获取

*删除：

​	*lpop key	：从列表的左边删除（弹出）一个元素，并将元素返回。类似于队列的弹出

​	*rpop key	：从列表的右边删除（弹出）一个元素，并将元素返回。类似于队列的弹出

​	

### 3.5集合类型	set	不允许重复元素

*存储 	：sadd key value

```
sadd myset b c d
```

*获取	：smember key：获取set集合中所有元素

*删除	：	srem	key value：删除set集合中的元素



### 3.6有序集合类型	sortedset	：不允许重复元素，且元素有序

*存储：zadd key score value

```
zadd mysort 99 zhangsan
zadd mysort 60 lisi
zadd mysort 50 wangwu
```

*获取	zrange key start end 

```
zrange mysort 0 -1
    1) "wangwu"
    2) "lisi"
    3) "zhangsan"
    
zrange mysort 0 -1 withscores
	1) "wangwu"
    2) "50"
    3) "lisi"
    4) "60"
    5) "zhangsan"
    6) "99"
```

*删除	zrem key value

```
 zrem mysort lisi
 zrange mysort 0 -1 withscores
 	1) "wangwu"
    2) "50"
    3) "zhangsan"
    4) "99"
```

### 3.7通用命令

```
	keys *	:查询所有的健

​	type key :	获取健对应的value的类型

​	del	key :删除指定的key value
```



## 4.持久化

4.1.redis是一个内存数据库，当redis服务器重启，获取电脑重启，数据会丢失，

我们可以将redis内存中的数据持久化保存到硬盘的文件中



4.2.redis持久化机制：

​	*RDB：默认方式，不需要进行配置，默认使用这种机制

​		>在一定的时间间隔中，检测key值的变化情况，然后持久化数据

​	使用方式：

​	1）编辑redis.windows.conf文件

```




#   In the example below the behaviour will be to save:
#   after 900 sec (15 min) if at least 1 key changed
save 900 1
#   after 300 sec (5 min) if at least 10 keys changed
save 300 10
#   after 60 sec if at least 10000 keys changed
save 60  10000

更改为：
save 900 1
save 300 10
save 10  5
```

​	2）更新配置文件后，要重新启动redis服务器并指定配置文件

​	不能直接双击启动，而是要通过命令行启动(cmd)

```

redis-server.exe  redis.windows.conf
```

​	3）点击使用客户端，添加5个键值对

​	4）然后会创建一个.rdb文件

​	

*AOF:日志记录的 方式，可以记录每一条命令的操作，可以每一次命令操作后，持久化数据

​		>每操作一次就持久化一次，对性能影响比较大

​	使用方式：

​	1）编辑redis.windows.conf文件

```
392行：appendonly no
要将no改为yes
no-》表示关闭AOF
yes-》表示开启AOF
```

```
420行：
# appendfsync always	：每一次操作都进行持久化
appendfsync everysec	：每隔一秒进行一次持久化
# appendfsync no		：不进行持久化
```

2）更新配置文件后，要重新启动redis服务器并指定配置文件

​	不能直接双击启动，而是要通过命令行启动(cmd)

```
redis-server.exe  redis.windows.conf
```

3）添加键值对，会创建一个.aof文件