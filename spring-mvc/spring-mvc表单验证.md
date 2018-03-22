# Spring-MVC对表单数据的验证

表单验证所需依赖：
```
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>5.4.1.Final</version>
        </dependency>

```

## 第一步：指定验证规则
在实体类的字段上加入验证注解
```
public class User {

    //消息引用properties资源文件的key
    @NotEmpty(message = "{user.userName.notEmpty}")
    private String userName;

    @NotNull(message = "{user.age.notNull}")
    @Min(value = 18,message = "{user.age.min}")
    @Max(value = 80,message = "{user.age.max}")
    private Integer age;

    @NotEmpty(message = "{user.email.notEmpty}")
    @Email(message = "{user.email.email}")
    private String email;
	
	//.....略....
}
```
## 第二步：错误消息的国际化处理
将不同国家的错误消息写入不同的properties文件中
例如：在resource目录下新建message.properties文件
```
user.userName.notEmpty = 用户不能为空
user.age.notNull = 请输入年龄
user.age.min = 年龄不能小于18岁
user.age.max = 年龄不得大于80岁
user.email.notEmpty = 请输入Email地址 
user.email.email = 请输入合法的Email地址
```

## 第三步：配置Spring核心配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="edu.nf.ch06"/>

    <mvc:default-servlet-handler/>

</beans>
```
配置消息资源
```
    <bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
        <!-- 配置消息资源文件的路径 -->
        <property name="basename" value="classpath:message"/>
		
		<!-- 如果有多个资源文件则这样配置 -->
        <!--<property name="basenames">
            <list>
                <value>classpath:message</value>
            </list>
        </property>-->
		
        <!-- 指定编码格式 -->
        <property name="defaultEncoding" value="utf-8"/>
    </bean>
```

配置LocalValidatorFactoryBean
```
    <bean id="validator" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
        <!-- 指定验证器的提供方 -->
        <property name="providerClass" value="org.hibernate.validator.HibernateValidator"/>
        <!-- 指定验证消息的资源 -->
        <property name="validationMessageSource" ref="messageSource"/>
    </bean>
```
配置MVC注解驱动
```
    <!-- validator属性引用下面验证器的bean的ID -->
    <mvc:annotation-driven validator="validator"/>
```

## 第四步：使用BindingResult对象获取验证信息
在请求方法的参数中加入BindingResult对象
通过hasErrors方法判断是否验证失败
通过getFieldErrors方法获取错误消息
@Valid注解标识验证的实体
```
    @PostMapping("/reg2")
    public @ResponseBody ResultVo reg2(@Valid User user, BindingResult result){
        ResultVo vo = new ResultVo();
        if (result.hasErrors()){
            vo.setCode(500);
            vo.setMessage(result.getFieldErrors());
        }else{
            vo.setValue("success");
        }
        return vo;
    }
```
ResultVo:
```
public class ResultVo {

    private int code = 200;
    private Object message;
    private Object value;
	
	//.....略...
}
```