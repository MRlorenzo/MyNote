# Spring-MVC异常处理

## @ControllerAdvice ，@ExceptionHandler
@ControllerAdvice 是Spring在3.2之后提供的一个注解
用于标识在类上，标识当前类是一个控制器的切面
```
@ControllerAdvice
public class GlobalExceptionHandler {


    /**
     * 当控制层方法抛出DataAccessException异常时，将会执行这个方法
     * 在@ExceptionHandler()这个注解中定义要捕获的异常类class,可以指定多个例如：
     *
     * @param e
     * @return
     * @ExceptionHandler({DataAccessException.class，IOException.class}) 并且，可以在这个方法中传入已捕获的异常对象来获取相关的异常消息
     */
    @ExceptionHandler(DataAccessException.class)
    public @ResponseBody
    ResultVo handleDataAccessException(DataAccessException e) {
        ResultVo vo = new ResultVo();
        vo.setCode(500);
        vo.setMessage(e.getMessage());
        return vo;
    }

    /**
     * 处理校验异常信息，在这里需要捕获一个校验绑定的类BingException
     * 通过这个异常对象可以获取到验证信息
     * 注意：在控制层方法就不需要再在定义BindingResult这个参数了，
     * 因为当校验不通过时，会自动抛出BindException
     * @return
     */
    @ExceptionHandler(BindException.class)
    public @ResponseBody
    ResultVo handleBindingException(BindException e) {
        ResultVo vo = new ResultVo();
        vo.setCode(401);
        Map<String, Object> message = new HashMap<>();
        //获取字段校验信息封装到Map中
        for (FieldError error : e.getFieldErrors()) {
            message.put(error.getField() , error.getDefaultMessage());
        }
        vo.setMessage(message);
        return vo;
    }
}

```

## 示例：
在controller层只要抛出了相应的异常就会调用@ExceptionHandler注解标识的异常相匹配的方法
```
@Controller
@RequestMapping("/user")
public class UserController{
    
    @GetMapping("/{uid}")
    public @ResponseBody ResultVo findUserById(@PathVariable("uid") String uid) {

        if ("1001".equals(uid)){
            ResultVo vo = new ResultVo();
            User user = new User();
            user.setUid(uid);
            user.setUserName("user1");
            user.setPassword("123");
            vo.setValue(user);
            return vo;
        }
        throw new DataAccessException("用户不存在");

    }

    @PostMapping("/login")
    public @ResponseBody ResultVo login(@Valid User user){
        ResultVo vo = new ResultVo();
        vo.setValue("success");
        return vo;
    }
    
}
```