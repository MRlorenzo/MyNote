# Spring开启定时任务的步骤

## 第一步：启用定时任务注解驱动
在Spring核心配置文件中
```
<task:annotation-driven/>
```
## 第二步：使用@Scheduled
```
@Component
public class TestJob {

    @Autowired
    private TestService service;

    /**
     * 使用@Scheduled注解标识当前方法是一个定时任务
     */
    @Scheduled(cron = "0/5 * * * * ?")
    public void run(){
        service.test();
    }

}
```