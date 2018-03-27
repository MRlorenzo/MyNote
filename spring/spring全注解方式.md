# spring全注解方式

### 1.标记要管理的类

```
@Component
@Controller
@Service
@Repository
以上任意一种即可
```

```
@Controller("userAction")
public class UserAction {

    public void execute(){
        System.out.println("这是控制器");
    }
}

```

### 2.使AnnotationConfigApplicationContext类创建对象

```
//创建注解解析工厂，在构建方法中传入需要扫描的包
ApplicationContext context = new AnnotationConfigApplicationContext("xxx");

UserAction action = context.getBean("userAction",UserAction.class);
action.execute();
```

