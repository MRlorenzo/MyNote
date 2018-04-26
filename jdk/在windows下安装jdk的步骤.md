# windows环境下jdk的安装

## 第一步：下载Windows版的jdk
下载EXE格式
```
	http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
```

## 第二步：解压并运行安装程序
步骤略

## 第三步：配置环境变量
修改如下变量值(如果没有就新建一个)
JAVA_HOME
```
jdk的安装目录
```
CLASS_PATH
```
.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;
```
Path
```
%JAVA_HOME%/bin
```


