# Linux环境jdk的安装

## 第一步：下载linux版的jdk
下载tar.gz格式
```
	http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
```

## 第二步：编辑配置文件
修改/etc/profile文件，并在末尾追加内容
“JAVA_HOME”指的是jdk的解压路径
```
# jdk_path

export JAVA_HOME=/home/lorenzo/developer/jdk1.8.0_161
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/libs
```

## 第三步：生效配置
输入如下命令
```
source profile
```