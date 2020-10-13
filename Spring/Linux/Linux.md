### shell命令：

#### 10)查找操作：

| 命令                           | 功能描述                 |
| ------------------------------ | ------------------------ |
| find ./ -name passwd[完整名称] | 查找passwd文件           |
| find ./ -name "*.txt"          | 查找.txt文件             |
| find ./ -type f                | 查找所有文件             |
| find ./ -type d                | 查找所有目录             |
| grep 内容 FileName             | 在指定文件中查找指定内容 |

#### 11)权限管理

| 命令                   | 功能描述                                             |
| ---------------------- | ---------------------------------------------------- |
| chmod 权限 文件/目录名 | 管理指定文件的权限信息；r读：4    w写：2    x执行：1 |

```
原来的权限
[admin@localhost ~]$ ls -l b.txt
-rw- rw- r--, 1 admin admin 6  0ct 9:06:43 b.txt

修改【属主】 权限之后:
[admin@localhost ~]$ chmod u+x b.txt
-rwx rw- r--,1 admin admin 6  0ct 9:06:43 b.txt
修改【属组】 权限之后
[admin@localhost ~]$ chmod g+x b.txt
-rwx rwx r--,1 admin admin 6  0ct 9:06:43 b.txt
修改【其他】 权限之后
[admin@localhost ~]$ chmod o+x b.txt
-rwx rwx r-x,1 admin admin 6  0ct 9:06:43 b.txt

注意：
	加权限用 + ，减少权限用 -

也能一次性给所有权限：
(其中r读：4    w写：2    x执行：1)
[admin@localhost ~]$ chmod 777 b.txt
-rwx rwx rwx,1 admin admin 6  0ct 9:06:43 b.txt
恢复初始权限：
[admin@localhost ~]$ chmod 664 b.txt
-rw- rw- r--,1 admin admin 6  0ct 9:06:43 b.txt
```

#### 12)进程管理（ps:process show）

| 命令           | 功能描述                 |
| -------------- | ------------------------ |
| ps             | 查看当前终端窗口里的进程 |
| ps -aux        | 查看系统中的所有进程     |
| ps -ef         | 查看进程列表             |
| kill 进程号    | 杀死指定的进程           |
| kill -9 进程号 | 强制杀死指定的进程       |

#### 13)其他命令

| 命令                                    | 功能描述                   |
| --------------------------------------- | -------------------------- |
| ifconfig                                | 查看当前系统的IP地址等信息 |
| tar -zcvf 压缩文件名.tar.gz 路径1 路径2 | 实现一组文件/文件夹的打包  |
| tar -zxvf 压缩文件名.tar.gz             | 实现解压缩解包             |
| man [章节号] 命令/函数名                | 查询指定的命令或参数       |

#### 14）vi操作

```
插入操作： a
		 i
		 o，
	
	编辑操作按esc退出
		 
删除（命令模式下）：
	删除单个字符：x
	删除多个字符：d选中光标之后的字符，w删除 ，删除3个单词：3dw
	删除一行：dd
	删除多行：num + dd ，比如删除两行  先按2 ，然后按d d
	
	删除模式操作的可以按u撤回
	
替换字符：
	替换单个字符：先按r，然后输入要替换的字符；
				比如将s换成大写，先将光标移动到s的位置，按r，然后输入S
	替换单词：c + w 会替换光标以及之后的字符，
				比如 main，将光标移动到m，然后按c和w，就会进入输入模式，可以输入要替换的字符
	替换一行：c c，相当于先删除一行，然后再进入编辑模式
	 c ：替换从光标到行尾
	
copy操作：
	拷贝多个字符：y + w 拷贝光标以及之后的字符，按p指令 粘贴
	拷贝一行： y y，拷贝一行
	
	剪切操作：d d ，粘贴按p
	
	u撤销操作
	ctrl+r 取消撤销
```

```
底行命令(:进入)：
	w	保存
	q	退出 ，q！	强制退出，wq！	保存并强制退出
	set nu	显示行数，set nonu	不显示行数
	1，5co11		将1到5行的内容copy到11行之后
	8，9mo16		将8到9行的内容移动到16行之后
	%s 字符串处理
	%s /原始字符串/目标字符串		在每行中，将首个原始字符串替换为目标字符串
	%s /原始字符串/目标字符串/g	将所有原始字符串替换为目标字符串，/g代表全局
```

vimtutor	进入vim练习

vim 用户主目录.vimrc文件可以配置vi的简单设置

​			比如设置显示行数(set nu)等等



## Xshell连接虚拟机

首先打开虚拟机里面的文件：

```
/etc/sysconfig/network-scripts/ifcfg-eno16777736
```

然后修改文件内容；

```
BOOTPROT=static		将默认的DHCP动态分配改成静态static
ONBOOT=yes		是否重启
IPADDR=192.168.xxx.xxx		添加ip地址，根据虚拟机的设置来配置
GATEWAY=192.168.xxx.xxx		添加网关，根据虚拟机的设置来配置
NETMASK=255.255.255.0		添加子网掩码
DNS1=114.114.114.114		配置DNS
```

## Xshell装jdk

首先先传输文件，解压到主目录下

然后获取root权限，一般是将其移动到/usr/目录下

```
mv jdk-11.0.7 /usr/
移动之后重命名一下：
mv jdk-11.0.7/ javajdk
然后进行环境变量的配置
vi /etc/profile
打开之后，像文件末尾追加内容：
export JAVA_HOME=/usr/javajdk
export PATH=$JAVA_HOME/bin:$PATH      :路径分隔符
	 $PATH 表示把PATH变量原来的值取出来，因为新建了PATH变量的值，要保存原有的环境变量
```

配置完之后，让配置文件立即生效：

```
source /etc/profile
```

接着输入javac查看是否配置成功



## Xshell安装Tomcat

首先传输文件，在主目录下将其解压

```
tar -zxvf apache-tomcat-8.5.55.tar.gz
```

然后将其移动到/usr/目录下

```
mv apache-tomcat-8.5.55 /usr/
```

然后重命名

```
mv apache-tomcat-8.5.55/ tomcat
```

然后进入其中，运行(./表示当前目录)

```
cd tomcat/
cd bin/
./startup.sh
此时tomcat已经运行成功，但是由于防火墙，不能直接访问，因此配置防火墙端口打开
/sbin/iptables -I INPUT -p tcp --dport 8080 -j ACCEPT 
表示开启8080端口(暂时开通)	
/iptables  -I	INPUT	-p	tcp	--dport 8080 	-j	ACCEPT
ip防火墙   允许	输入		基于tcp协议 端口号为8080	是允许的
```

#### 布置项目

可以访问之后，将之前写的项目war文件夹打包到虚拟机

```
文件路径类似于：
D:\Workspaces\JavaWeb\out\artifacts
```

将刚才的文件移动到/tomcat/webapps文件夹下

```
cd webapps/
mv /home/admin/task05_demo01_war_explored/ ./
```

接着退出当前目录，进入bin目录，关闭tomcat，接着重新启动

```
cd ..
cd bin
./shutdown.sh
./startup.sh
```

## MySQL的下载和安装

### 1)下载MySQL的repo源

```
wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
```

### 2)安装rpm包

```
rpm -ivh mysql57-community-release-el7-8.noarch.rpm
```

### 3）安装MySQL

```
yum install mysql-server

查看是否安装成功(grep 命令用于查找文件里符合条件的字符串)
rpm -qa | grep mysql
```

### 4）启动服务

```
service mysqld start
启动之后登陆服务器会报错，因为登陆的话要指定密码，但是密码一开始我么是不知道的
因此要输入命令查看
grep password /var/log/mysqld.log

然后输入登陆命令进入mysql
mysql -u root -p

进入mysql之后，执行不了任何操作，因为要重置密码

```

### 5）查看服务状态

```
systemctl status mysqld
```

### 6）使用root登陆

```
mysql -u root
```

### 7）修改临时密码

```
alter user 'root'@'localhost' identified by 'Jijun@666'
```

连上之后用sqlYog图形界面连接

```
首先先测试
show databases;
然后选择mysql
use mysql;
查询连接的用户
select user ,host from user;
要让远程连接能访问，就得让root用户支持远程连接，
查询出来的表里面root 的host为localhost，说明只支持本地连接，因此要修改
update user set host = '%' where user = 'root';
flush privileges; -- 刷新权限信息

查看修改结果并重新测试
select user,host from user;
```

```
由于防火墙屏蔽了3306端口，本地cmd"ping 192.168.1.166"能够Ping通，再用”telnet 192.168.1.166 3306“命令检查端口是否被屏蔽，结果为"正在连接192.168.1.166...无法打开到主机的连接。 在端口 3306: 连接失败",说明是防火墙的问题

因此暂时打开3306端口
/sbin/iptables -I INPUT -p tcp --dport 3306 -j ACCEPT 
打开之后就可以连接数据库了

```

## 编写和执行shell程序

```
vi hello.sh
进入编辑操作

第一行指定：
#!/bin/bash
第二行输出helloworld
echo helloworld!
然后进入底行模式，保存退出	:wq

执行sh脚本
方式一：/bin/bash hello.sh
	   bash hello.sh
	   sh hello.sh
	   
方式二：./hello.sh
		这种方式需要有执行权限，如果没有的话就需要加上执行权限，chmod 775 hello.sh
		
```

