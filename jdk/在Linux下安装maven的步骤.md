# Linux环境下maven的安装

## 第一步：下载maven
下载tar.gz格式
```
	https://maven.apache.org/download.cgi
```

## 第二步：编辑配置文件
修改/etc/profile文件，追加如下内容
"MAVEN_HOME"指的是maven解压的路径
```
# maven_path

export MAVEN_HOME=/home/lorenzo/developer/apache-maven-3.5.2
export PATH=$PATH:$MAVEN_HOME/bin
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