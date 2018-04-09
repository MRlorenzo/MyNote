# Spring-boot整合ssm

## 选择模块
Web、MySql、MyBatis、

## 添加连接池依赖
```
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid-spring-boot-starter</artifactId>
			<version>1.1.9</version>
		</dependency>
```

## 指定mapper扫描路径
在SpringBoot启动类中额外添加@MapperScan注解
```
@SpringBootApplication
@MapperScan("edu.work.weather.dao")
public class WeatherApplication {

	public static void main(String[] args) {
		SpringApplication.run(WeatherApplication.class, args);
	}
}
```

## 配置属性
在resource/application.properties中
```
# 设置web容器端口
server.port=8080
# 配置数据源
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/weather?useUnicode=true&characterEncoding=UTF-8
spring.datasource.username=root
spring.datasource.password=root
# 配置DBCP2连接池
# spring.datasource.type=org.apache.commons.dbcp2.BasicDataSource
# spring.datasource.dbcp2.initial-size=5
# spring.datasource.dbcp2.max-total=50
# spring.datasource.dbcp2.max-idle=20
# spring.datasource.dbcp2.min-idle=5
# spring.datasource.dbcp2.max-wait-millis=2000

# 配置druid连接池
spring.datasource.druid.initial-size=5
spring.datasource.druid.max-active=50
spring.datasource.druid.min-idle=5
spring.datasource.druid.max-wait=2000
spring.datasource.druid.time-between-eviction-runs-millis=60000
spring.datasource.druid.min-evictable-idle-time-millis=300000
spring.datasource.druid.validation-query=select 1
spring.datasource.druid.test-while-idle=true
spring.datasource.druid.test-on-borrow=false
spring.datasource.druid.test-on-return=false
spring.datasource.druid.max-pool-prepared-statement-per-connection-size=0

# 配置druid监控
spring.datasource.druid.stat-view-servlet.url-pattern=/druid/*
spring.datasource.druid.stat-view-servlet.reset-enable=false
spring.datasource.druid.stat-view-servlet.login-username=admin
spring.datasource.druid.stat-view-servlet.login-password=admin
spring.datasource.druid.stat-view-servlet.allow=127.0.0.1
spring.datasource.druid.web-stat-filter.url-pattern=/*
spring.datasource.druid.web-stat-filter.exclusions=/druid/*,*.js,*.css,*.png,*.jpg
# 配置mybatis
mybatis.mapper-locations=classpath:/mapper/*.xml
mybatis.type-aliases-package=edu.nf.ch02.entity
```