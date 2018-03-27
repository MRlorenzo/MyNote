# Spring-MVC拦截器的配置

## 定义拦截器
Spring-MVC的拦截器必须实现HandlerInterceptor接口
```

/**
 * 自定义拦截器，实现HandlerInterceptor接口
 */
public class TestInterceptor implements HandlerInterceptor{

    /**
     * preHandle在调用请求处理方法之前先执行，它的返回值为boolean类型
     * 只有为true的时候，请求才会继续往下执行，如果返回false，则立即结束当前请求处理
     * 而不会调用请求处理方法继续执行
     * @param httpServletRequest
     * @param httpServletResponse
     * @param o
     * @return
     * @throws Exception
     */
    @Override
    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
        System.out.println("请求处理之前...");
        return false;
    }

    /**
     * 这个方法在请求处理之后,解析视图之前执行(注意：如果preHandler方法返回false，这个方法也不会被执行)
     * @param httpServletRequest
     * @param httpServletResponse
     * @param o
     * @param modelAndView
     * @throws Exception
     */
    @Override
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
        System.out.println("请求处理之后...");
    }

    /**
     * 这个方法在请求处理完成之后，也就是解析视图之后执行(注意：如果preHandler方法返回false，这个方法也不会被执行)
     * @param httpServletRequest
     * @param httpServletResponse
     * @param o
     * @param e
     * @throws Exception
     */
    @Override
    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
        System.out.println("完成请求处理之后...");
    }

}

```

## 注册拦截器
在核心配置文件中
```
    <!-- 配置拦截器 -->
    <mvc:interceptors>
        <!-- 可以配置多个interceptor表示多个拦截器 -->
        <mvc:interceptor>
            <!-- 哪些请求映射到拦截器中 -->
            <mvc:mapping path="/user/**"/>
            <bean class="edu.nf.ch10.interceptors.TestInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>

```