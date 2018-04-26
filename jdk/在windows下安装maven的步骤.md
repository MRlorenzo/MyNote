# Windows环境下maven的安装

## 第一步：下载maven
下载Zip格式
```
https://maven.apache.org/download.cgi
```

## 第二步：配置环境变量
修改如下变量值(如果没有就新建一个)
MAVEN_HOME
```
【maven的解压路径】
```
Path
```
%MAVEN_HOME%\bin;
```
## 第三步：创建一个本地maven仓库
在指定的目录中创建一个文件夹

## 第四步：配置maven属性
修改mavenwb仓库的默认地址
在maven的解压缩路径下找到conf/settings.xml，修改节点：
```
	<localRepository>【maven仓库路径】</localRepository>
```
在国内可以使用阿里云的中央仓库下载，修改节点：
```
  <mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
    </mirror>
  </mirrors>
```