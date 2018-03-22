# 全注解方式配置spring-aop
## 定义配置类
@Configuration          //标识当前类为一个Java配置类
@ComponentScan("edu.nf.ch15")          //启用扫描注解
@EnableAspectJAutoProxy  //启用aspectJ注解
```
@Configuration          //标识当前类为一个Java配置类
@ComponentScan("edu.nf.ch15")          //启用扫描注解
@EnableAspectJAutoProxy  //启用aspectJ注解
public class AppConfig {
}
```
## 定义切面类
```
@Component
@Aspect  //这个注解声明当前类为一个切面
public class UserServiceAspect {


    /**
     * 定义一个切入点表达式，这个表达式需要声明在一个方法上，
     * 这个方法就是一个普通的方法即可，不需要任何的业务含义
     * 仅仅是用于标明是一个切入点,接下来就可以被所有的通知来引用
     */
    @Pointcut("execution(* edu.nf.ch15.service.UserService.*(..))")
    public void pointCut(){}

    /**
     * 前置通知
     * 使用before注解标识
     * 引用上面的切入点表达是，对应的是pointCut()方法
     * 也可以在不同的通知使用自定义的切入点表达式，如下：
     */
    @Before("pointCut()")
    public void before(JoinPoint jp){
        System.out.println("前置通知....获取目标方法的第一个参数： "+jp.getArgs()[0]);
    }

    /**
     * 环绕通知
     * 使用Around注解标识
     * @param pjp
     * @throws Throwable
     */
    @Around("pointCut()")
    public void around(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("环绕通知前");
        MethodSignature ms = (MethodSignature) pjp.getSignature();
        Method method = ms.getMethod();
        System.out.println("环绕通知....,获取目标方法的第一个参数: "+pjp.getArgs()[0] + ",目标方法："+method);
        pjp.proceed();
        System.out.println("环绕通知后....");
    }


    /**
     * 后置通知
     * 使用@AfterReturning注解标识
     * returning属性用于获取目标方法的返回值
     * @param returnVal
     */
    @AfterReturning(value = "pointCut()",returning = "returnVal")
    public void afterReturning(String returnVal){
        System.out.println("获取目标方法的返回值:"+returnVal);
    }


    /**
     * 异常通知
     * 使用@AfterThrowing注解标识
     * throwing属性用于获取目标方法产生的异常对象
     * @param e
     */
    @AfterThrowing(value = "pointCut()" , throwing = "e")
    public void afterThrowing(Throwable e){
        System.out.println("异常通知....：获取目标产生的异常对象："+e);
    }

    /**
     * 最终通知
     * 使用@After注解标识
     */
    @After("pointCut()")
    public void after(){
        System.out.println("最终通知...");
    }

}

```