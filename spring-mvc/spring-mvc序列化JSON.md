# Spring-MVC中的序列化和反序列化

## @ResponseBody
 * 使用@ResponseBody注解，将方法的返回值序列化成JSON对象并写回客户端
 * 这个注解可以声明在方法上，也可以声明在方法的返回值前面。
 * 注意：Spring序列化JSON使用的是JackSon工具，因此需要依赖相应的jar
```
@Controller
public class UserController {

    @GetMapping("/findUser")
    public @ResponseBody Users findUser(){
        Users user = new Users();
        user.setUserName("GouZo");
        user.setAge(23);
        user.setTel("9328-323");
        user.setBirth(new Date());
        return user;
    }

}

```
## @XmlRootElement
```
@XmlRootElement
public class Student {
    private String stuName;
    private int age;

    private StuCard card;

	//getter,setter略.......
}
```
* 使用@ResponseBody将对象序列化成xml响应客户端
* 因此需要在序列化的对象上使用@XmlRootElement
* 以及@XmlElement注解标识
```
@Controller
public class StuController {
    @GetMapping("/findStu")
    public @ResponseBody Student findStu(){
        Student student = new Student();
        StuCard c = new StuCard();
        c.setCardNum("238742398");
        student.setCard(c);
        student.setAge(23);
        student.setStuName("GouZo");
        return student;
    }
}

```
## @RestController
 * 在Spring4.0之后提供了@RestController注解，
 * 这个注解在原有的注解就已经表示了@Controller和@ResponseBody
 * 因此，使用@RestController注解之后，该类的所有方法默认就是使用了@ResponseBody
```
@RestController
public class ResController {

    @GetMapping("/findUser")
    public Users findUser(){
        Users user = new Users();
        user.setBirth(new Date());
        user.setTel("234324");
        user.setUserName("迈阿密");
        user.setAge(23);
        return user;
    }

}
```
##  页面提交JSON数据的处理
## @RequestBody
* 使用@RequestBody将请求体中的那些内容转换为方法的参数对象
* 这里是将请求体中的JSON字符串转换为Users对象
```
@RestController
public class BodyController {

    @PostMapping("/add")
    public String addUser(@RequestBody Users user){
        System.out.println(user.getUserName());
        System.out.println(user.getTel());
        System.out.println(user.getAge());
        System.out.println(user.getBirth());
        return "ok";
    }

}

```