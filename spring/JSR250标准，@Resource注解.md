# JSR250标准，@Resource注解

### 1.使用@Resource注解注入对象

```
@Component("worker")
public class Worker {

    @Resource(name = "wrench")
    private Tools tools;

    /**
     * 使用@Resource通过set方法注入
     * @param tools
     */
    @Resource(name = "wrench")
    public void setTools(Tools tools) {
        this.tools = tools;
    }

    public void work(){
        tools.fix();
    }
}
```

### 2.示例

```
ApplicationContext context = new AnnotationConfigApplicationContext("edu.nf.demo");
Worker worker = context.getBean("worker",Worker.class);
worker.work();
```

