### Git公钥私钥生成

#### 1.生成密匙对

进入git安装目录的bin文件夹目录下,也可以直接cmd进入

![image-20201204174807883](git%E7%A7%81%E9%92%A5.assets/image-20201204174807883.png)

地址框中输入cmd进入命令行界面

![image-20201204174843115](git%E7%A7%81%E9%92%A5.assets/image-20201204174843115.png)

![image-20201204174854093](git%E7%A7%81%E9%92%A5.assets/image-20201204174854093.png)

输入

```
ssh-keygen -t rsa
```

三次回车,可以获取两个文件,文件地址会打印在命令行界面中

私钥id_rsa、公钥id_rsa.pub



#### 2.把公钥给gitolite管理员配置权限

#### 3配置本地信息并测试配置效果

git命令行下窗口运行以下命令:

```
git config --global user.name "账号"  -- 设置全局用户名
git config --global user.email "邮箱" -- 设置全局邮箱
git clone git@192.168.150.36:testing.git 
	-- 克隆git@192.168.150.36:testing.git地址的文件
	-- 该地址下的文件只有一个testing
	
添加{账号}.txt文件到testing文件夹   -- 手动创建就好
git add .
git commit -m”测试提交”
git push origin master
换一个文件夹再次克隆查看刚才的提交是否生效
git clone git@192.168.150.36:testing.git
```



## git私钥转换格式

#### 1.说明

TortoiseGit通过Putty进行私钥/公钥验证的,而且只能使用.ppk文件的私钥,因此需要把生成的私钥转换为Putty能够识别的私钥

#### 2.方式

使用TortoiseGit自带的PuTTY Key Generator工具，
把git生成的私钥转换为Putty使用的.ppk文件，
然后在拉取Git代码时，
加载对应的.ppk文件，
就能成功拉取代码。

#### 3.转换私钥

运行TortoiseGit的bin目录下的puttygen.exe工具：

![image-20201204175733522](git%E7%A7%81%E9%92%A5.assets/image-20201204175733522.png)

点击Load,选择需要加载的文件，
这里选择ssh-keygen生成的id_rsa私钥：

![image-20201204175937695](git%E7%A7%81%E9%92%A5.assets/image-20201204175937695.png)

私钥加载成功，提示保存为Putty私有的格式，
点击确认即可：

![image-20201204180005029](git%E7%A7%81%E9%92%A5.assets/image-20201204180005029.png)

![image-20201204180022584](git%E7%A7%81%E9%92%A5.assets/image-20201204180022584.png)

然后选择Save private key，
弹出告警，提示未设置保护密码，点击是即可：

![image-20201204180048315](git%E7%A7%81%E9%92%A5.assets/image-20201204180048315.png)

保存为.ppk格式的私钥：

![image-20201204180112055](git%E7%A7%81%E9%92%A5.assets/image-20201204180112055.png)



## 加载新的私钥

在TortoiseGit拉取代码界面：

先创建空的文件夹,然后在右键选择git在这里创建版本库

![image-20201204180256835](git%E7%A7%81%E9%92%A5.assets/image-20201204180256835.png)

点击确定

![image-20201204180313013](git%E7%A7%81%E9%92%A5.assets/image-20201204180313013.png)

然后点击拉取

![image-20201204180411086](git%E7%A7%81%E9%92%A5.assets/image-20201204180411086.png)

![image-20201204180430815](git%E7%A7%81%E9%92%A5.assets/image-20201204180430815.png)

点击管理远端,就能进行配置

