# Http请求的工具类整合
## 第一步：添加依赖
Spring核心依赖
```
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>4.3.14.RELEASE</version>
        </dependency>
```
httpclient所需依赖
```
		<dependency>
			<groupId>org.apache.httpcomponents</groupId>
			<artifactId>httpclient</artifactId>
			<version>4.5.3</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.springframework/spring-web -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>4.3.14.RELEASE</version>
		</dependency>
```

## 第二步：配置Spring核心配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:contxt="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

</beans>
```
开启扫描：
```
    <contxt:component-scan base-package="edu.nf.ch01"/>
```
配置HttpClient的工厂实例HttpClientBuilder,用于创建HttpClient对象
```
<bean id="clientBuilder" class="org.apache.http.impl.client.HttpClientBuilder"/>
```
配置HttpClient，通过上面的builder工厂来构建实例，
        factory-bean属性指定上面配置的builder工厂的id,
        factory-method指定HttpClientBuilder类中的build方法名，这个方法就是工厂方法
```
    <bean id="httpClient" factory-bean="clientBuilder" factory-method="build"/>
```
配置Spring的Http装配工厂，Spring提供了两种工厂分别用于封装HttpURLConnection和
     HttpClient两种实现,如果不进行配置，默认是使用HttpURLConnection的装配工厂
```
    <bean id="httpClientRequestFactory" class="org.springframework.http.client.HttpComponentsClientHttpRequestFactory">
        <!-- 为这个工厂注入一个HttpClient对象 -->
        <property name="httpClient" ref="httpClient"/>
        <!-- 设置请求连接的超时时间，单位：毫秒 -->
        <property name="connectTimeout" value="2000"/>
        <!-- 设置请求读写超时时间，单位:毫秒 -->
        <property name="readTimeout" value="5000"/>
    </bean>
```
配置RestTemplate
```
    <bean class="org.springframework.web.client.RestTemplate">
        <!-- 注入上面配置的httpClientRequestFactory -->
        <property name="requestFactory" ref="httpClientRequestFactory"/>
        <!-- 设置消息转换的默认编码-->
        <property name="messageConverters">
            <list>
                <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                    <property name="defaultCharset" value="utf-8"/>
                </bean>
            </list>
        </property>
    </bean>
```