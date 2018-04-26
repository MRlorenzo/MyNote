# Windows环境下安装mysql

## 第一步：下载MySQL

```
https://dev.mysql.com/downloads/mysql
```

## 第二步：解压并配置默认文件
找到my.ini文件,修改内容
```
[mysql]

# 设置mysql客户端默认字符集

default-character-set=utf8 

[mysqld]

#设置3306端口

port = 3306 

# 设置mysql的安装目录

# basedir=D:\mysql\mysql-5.6.17-winx64

# 设置mysql数据库的数据的存放目录

# datadir=D:\mysql\mysql-5.6.17-winx64\data

# 允许最大连接数

max_connections=200

# 服务端使用的字符集默认为8比特编码的latin1字符集

character-set-server=utf8

# 创建新表时将使用的默认存储引擎

default-storage-engine=INNODB 
```

## 第三步：配置环境变量
Path
```
【MySQL的解压路径】
```

##  第四步：安装MySQL服务
进入MySQL安装目录下的bin文件夹，在此打开终端
```
mysqld --initialize-insecure --user=mysql

mysqld install
```
修改用户名和密码
```
use mysql;

UPDATE user SET authentication_string=password('123456') where user='root';

FLUSH PRIVILEGES;
```

## 第五步：启动服务
```
net start mysql
```

## 在博客园有关于mysql5.7安装的详细教程
```
	http://www.cnblogs.com/Salicejy/p/7903929.html
```