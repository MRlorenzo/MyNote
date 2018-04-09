# Spring-boot整合spring-mvc
## 排除依赖
Spring boot内嵌了三种容器(Tomcat、jetty、undertow)
			 	默认使用tomcat作为web容器，可以选用其他的web容器。
			 	这里先排除Tomcat的依赖模块
```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>

	<!-- 排除Tomcat的依赖模块 -->
	<exclusions>
		<exclusion>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
		</exclusion>
	</exclusions>
</dependency>

<!-- 依赖jetty容器 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-jetty</artifactId>
</dependency>

```

## 指定web容器端口
在resource/application.properties中
```
# 设置web容器端口
server.port=8989

```
