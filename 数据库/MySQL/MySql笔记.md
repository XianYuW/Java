

window服务关闭MySql:

```
	net stop mysql57
	启动： net start mysql57
```



命令行登录数据库：

```
	mysql -u用户名 -p密码
	mysql -h主机ip -u用户名 -p密码
```



其中数据库的注释方式有：

```
	1.单行注释：	--	以及	#
	2.多行注释：/*  */
```

## DDL

### DDL操作数据库

对数据库操作的分类包含：

```
		CRUD
​		C	create	创建
​		R	retrieve	查询
​		U	update	修改
​		D	delete	删除
```

创建数据库：

```
#创建数据库 方式一：指定名称的数据库 默认编码latin1编码
CREATE DATABASE db1;

#创建数据库 方式二：指定名称的数据库且指定字符集
CREATE DATABASE db2 CHARACTER SET utf8
```



 查看数据库：

```
#切换数据库
USE db1;
​```
#查询当前正在使用的数据库
SELECT DATABASE();
​```
#查看MySQL中有那些数据库
SHOW DATABASES;
```

数据库的使用：

```
	#修改数据库的字符集
	#语法格式 alter database 数据库名 character set utf8;
 	ALTER DATABASE db1 CHARACTER SET utf8;

​	#查询当前数据库的基本信息
	 SHOW CREATE DATABASE db1;

​	#删除数据库(慎用)
	#语法格式 drop database 数据库名称    将数据库从MySQL中永久删除
	DROP DATABASE db2;
```

### DDL操作表

创建表的语法格式：

```
	create table 表名{
		字段名称 字段类型(长度),
		字段名称1 字段类型,
		字段名称2 字段类型 最后一个字段不要加逗号
	};
```

	MySQL中常见的数据类型
		int 整形
		double 浮点型
		char 可变长度字符串型
		varchar 固定长度字符串型
		date 日期类型 只显示年月日 没有时分秒 yyyy-MM-dd
		datetime 年月日时分秒 yyyy-MM-dd HH:mm:ss
		
	varchar 和char的区别？
		varchar是可变长度，存储字符串时只是用所需的空间
		char类型是固定长度的，指定多少长度，创建时就使用多少
```
CREATE TABLE test1(
	tid VARCHAR,
	tdate DATE
);
```

```
   -- 快速创建一个表结构相同的表
    -- 语法结构 create table 新表名称 like 旧表名称
    CREATE TABLE test2 LIKE test1;

​   -- 查看表结构
    DESC test1;

​   -- 查看表
    -- 查看当前数据库中所有的数据表名
    SHOW TABLES;

​   -- 查看创建表的 sql
    SHOW CREATE TABLE test1;

​   -- 查看表结构
    DESC test1;
```

表的删除：

```
	 语法格式
	     drop table 表名;从数据库中永久删除一张表
	     drop table if exists 表名;判断表是否存在，如果存在就删除；不存在就不执行删除
```

```
   -- 删除test1表
    DROP TABLE test1;

​   -- 前一种如果执行多次会报错，因此我们使用先判断后删除的方式
    DROP TABLE IF EXISTS test1;
```

/*
    修改表的名称
    修改表的字符集
    修改表的某一列(数据类型 名称 长度)
    向表中添加某一列
    删除表中的某一列
*/

```
   -- 修改表名称 
    -- 语法格式： rename table 旧表名称 to 新表名称;
    RENAME TABLE test2 TO test;
    
​   -- 修改表的字符集
    -- 语法格式：alter table 表名 character set 字符集
    ALTER TABLE test CHARACTER SET gbk;

​   -- 向表中添加一个字段 关键字：add
    -- 语法格式：alter table 表名 add 字段名称 字段类型(长度)
    -- 添加分类描述字段
    ALTER TABLE test ADD tname VARCHAR(20);

​   -- 修改表的某一列(数据类型 名称 长度) 关键字：modify 
    -- 语法格式： alter table 表名 modify 字段名称 字段类型
    -- 修改tname字段的类型为char 长度为50
    ALTER TABLE test MODIFY tname CHAR(50);

​   -- 修改tname字段的名称  关键字：change
    -- 语法格式：alter table 表名 change 旧列名 新列名 类型(长度)
    -- 修改tname 字段的名称 数据类型 长度为tName varchar(20)
    ALTER TABLE test CHANGE tname tName VARCHAR(20);

​   -- 删除列 关键字：drop 
    -- 语法格式：alter table 表名 drop 列名
    ALTER TABLE test DROP tName;
```

## DML

### DML插入，修改，删除数据

```
增加操作：

​	语法格式：insert into 表名 (字段名1，字段名2，...) values(字段值1，字段值2...)
    方式一：插入全部字段
        insert into Student(sid,sname,sage) values(1,'迪丽热巴',18)
    方式二：插入全部字段，省略字段名
        insert into Student values(2,'古力娜扎',18)
    方式三：插入指定字段
    	insert into Student(sid,sname) values(3,'马儿扎哈') 
```

```
注意事项：
	1.值与字段必须对应
	2.在插入varchar  char  date 类型的时候，必须使用单引号 或者双引号(尽量使用单引号)
	3.如果插入空值，可以忽略不写 或者写null
```



```
修改操作：
	语法格式1：update 表名 set 列名 = 值
	语法格式2：update 表名 set 列名 = 值 where 条件表达式：字段名 = 值
	实例：
		-- 修改表中的所有学生性别为女
		update Student set sex = '女'; -- (慎用)
		
		-- 带条件的修改 将sid为1的数据，性别改为男
		update Student set sex = '男' where sid = 1
		
		-- 一次性修改多个列
		-- 修改sid为5的这条数据，将年龄改为20，地址改为’大唐‘
		update Student set age = 20,address = '大唐' where sid = 5;
```

```
删除操作：
	语法格式一：delete from 表名;
	语法格式二：delete from 表名 where 条件;
	
	-- 删除所有数据的方式有两种
		-- 1.delete from 表; 不推荐，对表中的数据逐条删除，效率低
		-- 2.truncate table 表;推荐，删除整张表，然后创建一个一摸一样的新表。
```



```
distinct 语法：
		SELECT DISTINCT 列名称 FROM 表名称
		
between 语法：
        SELECT column_name(s)
        FROM table_name
        WHERE column_name
        BETWEEN value1 AND value2
```

```
默认值：
	特点 用来指定 某一列的默认值
	语法格式：
		字段名 字段类型 default 默认值
​		例如：sex CHAR(1) DEFAULT '男' CHECK(sex IN ('男','女'))
	
```

```
limit关键字：
	通过limit去指定要查询的数据条数 行数
	语法格式：
		select 字段 from 表名 limit offset，length；
		参数说明：
			offset ：起始行数 ，默认从0开始计数(既offect可以省略)
			length： 返回的行数(要查询几条数据)
		实例：
			# 查询emp表的前5条数据
			select * from emp limit 0,5;
			select * from emp limit 5;
			# 查询emp表 从第四条开始，查询6条
			select * from emp limit 3,6;
			
			-- limit分页操作 ，每页显示3条
			select * from emp limit 0,3; -- 第一页
			select * from emp limit 3,3; -- 第二页
			select * from emp limit 6,3; -- 第三页
			
			--分页公式：起始行数 = (当前页码 -1)*每页显示条数
```

### DML--约束

```
约束：
	约束是指对数据进行一定的限制，来保证数据的完整性 有效性 正确性
	
	常见的约束：
		主键约束	primary key 
		唯一约束	unique
		非空约束	not null
		外键约束	foreign key
```

#### 主键约束

```
特点：
	不可重复，唯一i非空
作用：
	用来表示数据库当中的每一条记录
语法格式：
	字段名 字段类型 primary key
	
	方式一：创建表的创建主键
		create table 表名(
			字段名 字段类型 primary key,
			字段名 字段类型,
			...
		); -- 可以通过 desc 表名;查看表的结构
	方式二：创建表，最后添加主键
		create table 表名(
			id 字段类型,
			字段名 字段类型,
			...
			primary key(id) -- 指定id为主键
		);
	方式三：创建表之后再添加主键
		create table 表名(
			id 字段类型,
			字段名 字段类型,
			...
		);
		-- 通过DDL语句添加主键约束
		alter table 表名 add primary key(id);
		-- 通过DDL语句删除主键
		alter table 表名 drop primary key;
		
	主键的自增：
		关键字：auto_increment 主键的自动增长(字段类型必须是整数类型),默认从1开始
		-- 修改自增的起始值
		-- 自定义自增的起始位置
			create table 表名(
                id 字段类型 primary key auto_increment,
                字段名 字段类型,
                ...
            )auto_increment=100;
```

```
delete和truncate对自增长的的影响：
	delete 删除表中所有的数据，将表中的数据逐条删除
	truncate 删除表中的所有数据，将整个表删除，然后创建一个结构相同的表
	
	因此：
		delete删除对自增没有影响，自增接着之前的值往下加
		truncate删除所有数据，自增从1开始
```

```
非空约束：
	特点：某一列	不允许为空
	语法： 字段名 字段类型 not null
	
唯一约束：
	特点：表中的某一列不能重复 (duinull值 不作唯一判断)
	语法：	字段名 字段类型 unique
	
	例：create table 表名(
                id int primary key auto_increment,
                ename varchar(20) unique ,
            )
​主键约束与唯一约束的区别：
	1.主键约束 是唯一且不能为空的
	2.唯一约束  唯一，可以为空
	3.一个表中只能有一个主键 ，但是可以有多个唯一约束
```

```
外键约束：
​	作用：外键约束可以让两张表之间产生有一个对应的关系，从而保证了主从表引用的完整性
	外键：
		外键指的是在从表中与主表的主键对应的字段
	主表和从表
		主表：主键id所在的表，一的一方
		从表：外键字段所在的表，多的一方
​	添加外键的语法格式
		1.创建表的时候添加：
			create table 表名(
				字段...
				[constraint] [外键约束名] foreign key(外键字段名) references 
				主表(主键字段)
			);
			-- 创建员工表，添加外键
			create table emp(
				eid int primary key,
				ename varcar(20),
				age int,
				dept_id int ,-- 外键字段 指向了主表的主键
				-- 添加外键约束
				constraint emp_dept_fk foreign key(dept_id) references dept(id) 
			);
		2.已有表添加外键
			alter table 从表 add [constraint] [外键约束名称] foreign key(外键字段名)
			references 主表(主键字段名);
```



```
添加外键的语法：alter table 从表名 add foreign key(外键字段名) references 主表(主键名)

删除外键的语法：alter table 表名 drop foreign key 外键名
```

```

delete和drop的区别：
1.drop是用于删除表结构的，外键属于表结构的一部分，所以删除外键要使用drop
2.delete是用于删除表中的数据
```

### MySQL--事务

```
事务：
	在业务中执行一个功能通常需要执行多条SQL语句，因此可以通过创建事务来进行sql语句的运行，
	以防止在执行中出错，从而只执行了部分的sql语句，产生错误问题
		开启事务之后，执行多条sql语句，成功则提交事务；
					   			  失败则回滚事务。 
事务的操作：
	手动提交事物：
		语法格式：
			开启事务	start transaction;或者begin;
			提交事务	commit;
			回滚事务	rollback;
	自动提交事务
		MySQL默认的提交方式	自动提交事务
		每执行一条DML语句	都是一个单独的事务
		
		查看MySQL的自动提交状态：
			show variables like 'autocommit';
			-- on :自动提交
			-- off:手动提交
			
			可以将autocommit改成off：
			set @@autocommit = off;
			
事务的四大特性：
	原子性：每个事务都是一个整体，不可以再拆分，事务中的sql要么全都执行成功，要么全都执行失败
	一致性：事务在执行之前，数据库的状态与事务执行之后的状态要保持一致
	隔离性：事务与事务之间不应该相互影响，执行时要保证隔离状态
	持久性：一旦事务执行成功，对数据的修改时持久的
	
并发访问的问题：(写后读和读后写问题)
	脏读：一个事务读取到了另一个事务没有提交的数据 ，写-读-写撤销
	不可重复读：一个事务中，两次读取的数据不一致(A事务读取了B事务已经提交的更改数据)，读-写-读
	幻读：一个事务中一次查询的结果，无法支撑后续的业务操作，读-写-读
		幻读-例：
			假设银行系统在同一个事务中两次统计存款的总金额，
			在两次统计过程中，刚好新增了一个存款账户，并存入100元，
			这时两次统计的总金额将不一致。

设置隔离级别：
	read uncommitted：读未提交
		可以防止哪些问题：无
	read committed：读已提交(Oracle默认的隔离级别)
		可以防止：脏读
	repeatable read:可重复读(MySQL默认的隔离级别)
		可以防止：脏读，不可重复读
	serializable：串行化
		可以防止：脏读，不可重复读，幻读
	注意：
		隔离级别 从小到大 安全性是越来越高的，但是效率时越来越低的
		因此要根据不同情况来选择对应的隔离级别
		
隔离级别的相关命令：
	1.查看隔离级别
		select @@tx_isolation;
	2.设置隔离级别
		set global transaction isolation level 级别名称;
			read uncommitted：读未提交
			read committed：读已提交
			repeatable read:可重复读
			serializable：串行化
```



### DML索引

```
索引概念：
	我们可以对数据库表中的字段来创建索引 来提高查询速度
常见的索引分类：
	主键索引 (primary key) 主键是一个唯一性的索引  每个表中只能由一个主键
	唯一索引 (unique) 索引列的所有数据只能出现一次，必须是唯一
	普通索引(index) 最常见的索引，提高对数据的访问速度

表对应的索引被保存在一个索引文件中，如果对数据进行增删改查操作，那么mysql就需要对索引进行更新
索引文件的位置：
	数据文件的位置：c:/ProgramData/MySQL/MySQL Server 5.7/Data
	索引文件:对应数据文件中数据库里的 .ibd文件
```

```
创建索引
	主键索引：
		方式一：创建表的时候直接添加
			create table 表名{
				-- 添加主键
				字段名 类型 primary key,
			};
		方式二：创建表之后添加索引 使用DDL
			alter table 表名 add primary key(列名)
		
	唯一索引：
		方式一：创建表的时候直接添加
			create table 表名{
				列名 类型(长度),
				-- 添加唯一索引
				UNIQUE [索引名称](列名)
			}；
		方式二：在已有的表上创建索引
			create unique index 索引名 on 表名(列名(长度))
		方式三：修改表结构添加索引
			alter table 表名 add unique (列名)
			
		添加唯一索引的列，列的所有值都只能出现一次
		唯一索引，保证了数据的唯一性，同时也提升了查询效率
		
	普通索引：
		方式一：
			create index 索引名 on 表名(列名(长度))
		方式二：
			alter table 表名 add index 索引名(列名)
			
```

```
删除索引：
	alter table 表名 drop index 索引名；
```

```
案例：
	-- 500万条数据
    SELECT COUNT(*) FROM test_index;

    -- 通过id查询 0.001sec
    SELECT * FROM test_index WHERE id= '100001';

    -- 通过dname字段查询 耗时2秒左右
    SELECT * FROM test_index WHERE dname = 'name5200';

    -- 执行分组查询 dname 没有添加索引1min16sec
    SELECT * FROM test_index GROUP BY dname;

    -- 为dname 添加索引  38sec
    -- 因此我们应该在创建表的时候就添加好了索引，不然数据量变大时可能会添加不上索引
    ALTER TABLE test_index ADD INDEX dname_index(dname);

    -- 执行分组查询 dname 添加了索引 0.066sec
    SELECT * FROM test_index GROUP BY dname;
    
    因此我们可以通过索引提高查询速度
    
索引的总结：
	创建索引的原则
		优先选择为 经常出现在 查询条件或者排序  分组后面的字段 创建索引
		
	索引的优点：
		1.可以大大的提高查询速度
		2.减少查询中分组和排序的时间
		3.通过创建唯一索引保证数据的唯一性
	
	索引的缺点：
		1.创建和维护索引需要的时间 ，数据量越大 时间越长
		2.表中的数据进行增删改操作时，索引也需要进行维护，降低了维护的速度
		3.索引文件需要占据磁盘空间
```

# MySQL--视图

```
什么是视图？
	视图是由查询结果形成的一张虚拟的表
使徒的作用
	如果某个查询的结果出现的十分频繁，并且查询结果比较复杂
	那么这个时候，就可以根据这条查询语句构建一张视图	方便查询
视图的语法：
	create view 视图名[字段列表] as select 查询语句;	
	view	表示视图
	字段列表	一般跟后面的查询语句相同
	as select 查询语句	表示给视图提供数据的	查询语句
```

### MySQL--存储过程

```
存储过程其实就是一堆sql语句的合并。中间加入了一些逻辑控制

存储过程的优缺点：
	优点：
		1.调试完成就可以稳定运行(在业务需求相对稳定的情况下)
		2.存储过程可以减少	业务系统和数据库的交互
	缺点：
		1.互联网项目中较少使用存储过程，因为	业务需求变化较快
		2.存储过程中的移植十分困难
		
创建存储过程的方式1：
	语法格式
		delimiter $$ -- 声明语句的结束符号，因为如果不定义，在sql语句加了;则会直接停止
		create procedure 存储过程名称() -- 声明存储过程
		begin -- 开始编写存储过程
			-- 要执行的sql语句
		end $$ -- 存储过程结束
		
	-- 调用存储过程
	call 存储过程名称;
创建存储过程的方式二,可以接收参数的存储过程：
	语法格式：
		create procedure 存储过程名(in 参数名	参数类型)
	例如：
	-- 创建存储过程 接受一个商品id，根据id删除数据
	delimiter $$
	create procedure goods_product(in goods_id int)-- 要接受的参数为 goods_id
	begin 
		-- 根据id删除商品数据
		delete from goods where gid = goods_id
	end $$
	
	-- 调用存储过程 传递参数
	call goods_product(1); -- 会将goods表中gid = 1 的那一条数据删除
	
存储过程创建方式三，可以获取存储过程的返回值：
	1.变量的赋值
		set @变量名 = 值
	2.out	输出参数
		out 变量名 数据类型
	例：
	-- 向订单表中插入一条数据，返回为1，表示插入成功
	delimiter $$
	create procedure order_product(in o_oid int ,
									in o_gid int ,
									in o_price int，
									out out_num int) -- 接收三个参数，返回一个参数
	begin 
		-- 执行插入操作
		insert into order values(o_oid,o_gid,o_price);
		-- 设置out_num的值为1
		set @out_num = 1;
		-- 返回out_num
		select @out_num;
	end $$
	
	--调用存储过程
	call order_product(1,2,50,@out_num);
```

### 触发器

```
触发器是由时间触发的，简单理解为：
当我们执行一条sql语句的时候，这条sql语句的执行会自动去触发执行其他的sql语句

触发器创建的四要素：
	1.监视地点(table)
	2.监视事件(insert/update/delete)
	3.触发时间(before/after)
	4.触发事件(insert/update/delete)

创建触发器
	语法格式：
		delimiter $		-- 将MySQL的结束符号从;改为$，避免执行出现的错误
		create trigger Trigger_Name	-- 触发器名，，在一个数据库中触发器名是唯一的
		before/after (insert/update/delete) -- 触发的时机  和	触发的事件
		on table_name	-- 触发器所在的表
		for	each row 	--固定写法  叫 行触发器 ，每一行受影响，触发事件都执行
		begin
			-- begin 和end 之间写触发时间
		end
		$ -- 结束标记
		
		例如：
		-- 需求：在下订单的时候，对应的商品(gid = 4)的库存量要相应的减少，卖出商品之后减少库存量
		# 监视的表：oeders  监视的事件：insert 
		# 出发的时间：after 触发的事件：update 
		delimiter $
		create trigger t1
		after insert on orders
		for each row 
		begin 
			-- 一旦订单加一，库存减一
			update goods set num = num -1 where gid = 4;
		end $
```

## DCL(数据控制语言)

### DCL创建用户

```
创建用户
​	语法格式：
		create user '用户名'@'主机名' identified by '密码'；	
		
		-- 创建一个admin用户，只能在localhost这个服务器登陆MySQL服务器
        CREATE USER 'admin'@'localhost' IDENTIFIED BY '123456';

        -- 创建一个damin2用户可以在任意电脑上登陆MySQL服务器
        CREATE USER 'admin2'@'%' IDENTIFIED BY '123456'; -- %表示任意电脑都可以登陆
```

### DCL用户授权

```
语法格式：

​	grant 权限1，权限2...on 数据库名.表 to '用户名'@'主机名'

例如：
	-- 给admin用户分配对db4数据库中products表的操作权限：查询
	grant select on db4.products to 'admin'@'localhost';
	
	-- 给admin2用户分配所有权限，对所有数据库的所有表
	grant all on *.* to 'admin2'@'%';
```

### DCL查看用户权限

```
语法格式：
	show grants for '用户名'@'主机名';
	
	-- 查看root用户的权限
	SHOW GRANTS FOR 'root'@'localhost';
```

### DCL删除用户

```
语法格式：

​	drop user '用户名'@'主机名';
```

### DCL查询用户

```
语法格式：
	select * from User;
```



## 数据库的备份&还原

### SQLYog数据备份

​	选择要备份的数据库，右键->备份导出->选择备份数据库->指定文件位置，选择导出

### 命令行方式--数据备份

语法格式

​	备份：	mysqldump -u用户名 -p密码 数据库名 > 文件路径

​	还原：	source sql文件所在地址

​	注意：命令行的方式备份数据，不会创建数据库，要自己先创建好数据库，再导入

​				还原的时候要先进入该数据库(use 数据库名)，在执行还原语句