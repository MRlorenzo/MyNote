# spring-aop的基本配置与使用

## 第一步：
依赖aspectJ的weaver的包

``` stylus
<dependencies>

        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.10</version>
        </dependency>

    </dependencies>
```
spring指定切入点需要使用aspectJ的execution表达式支持，因此必须依赖aspectJ的weaver的包

## 第二步：
编写切面
 * 定义一个切面，
 * 这个类其实就类似动态代理的回调处理器(InvocationHandler)
 * 或者是cglib的MethodInterceptor
 *
 * 在切面中定义的方法，统称为通知或者增强
 * 类似于InvocationHandler的invoke方法
 * 或者是MethodInterceptor的intercept方法
 *
 * 在AOP中的通知包括五种类型：
 * 1.前置通知
 * 2.环绕通知
 * 3.后置通知
 * 4.异常通知
 * 5.最终通知
 *
 * 这些通知在切面中可以全部定义，也可以定义其中一种
 *
 * 其实在InvocationHandler和MethodInterceptor中的通知方法就是一种环绕通知
 * 它们也只能支出环绕通知。
``` 
public class UserServiceAspect {


    /**
     * 前置通知
     * 可以指定一个JoinPoint参数
     * 通过这个参数可以得到目标方法的参数信息
     */
    public void before(JoinPoint jp){
        System.out.println("执行前置通知....");
        System.out.println("拦截目标方法参数："+jp.getArgs()[0]);
    }


    /**
     * 环绕通知
     * 需要指定一个ProceedingJoinPoint参数
     * 通过这个参数来调用目标方法。
     * 并且还可以通过这个参数得到目标方法的Method对象以及方法的参数信息
     */
    public Object around(ProceedingJoinPoint jp) throws Throwable{
        System.out.println("执行环绕通知前...");
        System.out.println("获取目标方法参数："+jp.getArgs()[0]);
        MethodSignature signature = (MethodSignature) jp.getSignature();
        System.out.println("获取目标方法的method对象："+signature.getMethod());
        //调用目标的方法
        Object returnVal = jp.proceed();
        System.out.println("执行环绕通知后...");
        return returnVal;
    }

    /**
     * 后置通知
     */
    public void afterReturning(String returnVal){
        System.out.println("执行后置通知...");
        System.out.println("返回值："+returnVal);
    }

    /**
     * 异常通知
     */
    public void afterThrows(Throwable e){
        System.out.println("执行异常通知...");
        System.out.println("产生的异常对象:"+e);
    }

    /**
     * 最终通知
     */
    public void after(){
        System.out.println("执行最终的通知...");
    }
}
```


## 第三步：
配置切面

``` 
<!-- 配置切面，切面同样交给容器管理-->
<bean id="aspect" class="edu.nf.ch12.demo.UserServiceAspect"/>

```
配置aop,proxy-target-class属性表示是否强制只用cglib作为动态代理设置为true表示使用cglib作为动态代理，如果为false则根据目标对象有无实现接口来决定使用jdk还是cglib(有实现接口就是用jdk代理，没有则使用cglib) 默认值是false

``` 
    <aop:config>
        <!-- 1.配置切入点(pointcut),id表示切入点的唯一标识，
         expression指定切入点的表达式，这个表达式使用的就是aspectJ的表达式语言
         execution表达式是针对方法级别范围的切入。
         -->
        <aop:pointcut id="pointCut" expression="execution(* edu.nf.ch12.demo.*.*(..))"/>

        <!-- 也可以使用within表达式，它是针对类级别的切入，表达式中不需要指定到方法级别,并且不需要指定返回值和访问修饰符 -->
        <!--<aop:pointcut id="pointCut" expression="within(edu.nf.ch12.demo.UserService)"/>-->
		
        <!-- 2.配置切面,切面通常使用一个类来描述，在切面类中会定义不同的通知 -->
        <aop:aspect ref="aspect">
            <!-- 配置切面中的各种通知方法,method指定通知的方法名
                 pointcut-ref引用上面配置的切入点的id   -->
            <!-- 前置通知 -->
            <!--<aop:before method="before" pointcut-ref="pointCut"/>-->
            <!-- 也可以在通知中使用pointcut自定义切入表达式，来切入具体的目标 -->
            <aop:before method="before" pointcut="execution(void edu.nf.ch12.demo.UserService.say())"/>
            <!-- 环绕通知 -->
            <aop:around method="around" pointcut-ref="pointCut"/>
            <!-- 后置通知,可以通过returning属性获取目标方法的返回值,returnVal对应参数名 -->
            <aop:after-returning method="afterReturning" pointcut-ref="pointCut" returning="returnVal"/>
            <!-- 异常通知，可以通过throwing属性获取目标方法的参数，e对应参数名 -->
            <aop:after-throwing method="afterThrows" pointcut-ref="pointCut" throwing="e"/>
            <!-- 最终通知 -->
            <aop:after method="after" pointcut-ref="pointCut"/>
        </aop:aspect>
    </aop:config>
```
