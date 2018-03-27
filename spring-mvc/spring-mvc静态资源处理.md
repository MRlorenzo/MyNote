# spring-mvc静态资源处理

## 第一种：使用servlet容器的默认servlet处理
Spring配置：
```
<mvc:default-servlet-handler/>
```
## 第二种：基于Spring MVC本身来处理静态资源
使用resources将mapping请求映射到具体的location本地资源目录,并且可以配置多个，
        并且location可以同时指定多个本地资源目录，使用逗号分离
```
<mvc:resources mapping="/app/**" location="/static/"/>
```
        