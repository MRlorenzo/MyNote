# Spring的参数映射

## @RequestParam
 * @RequestParam 用于映射表单input元素的name属性
 * 如果不使用这个注解，那么方法的参数名就必须和表单input元素的name对应
```
    @PostMapping("/test1")
    public String mapping1(@RequestParam("userName") String userName , int age){
        System.out.println("userName:"+userName);
        System.out.println("age:"+age);
        return "index";
    }
```
* 将表单数据映射为实体对象
* 注意：表单Input的name属性只需要对应实体中的字段名即可
```
    @PostMapping("/test2")
    public String mapping2(Users user){
        System.out.println("userName:"+user.getUserName());
        System.out.println("age:" + user.getAge());
        return "index";
    }
```
## @PathVariable
* 将请求路径作为参数映射，使用{}来定义变量
* 方法参数必须使用@PathVariable注解来对应{}中的名称
```
@GetMapping("/user/{userId}")
public String mapping3(@PathVariable("userId") String uid){
	System.out.println(uid);
	return "index";
}
```

## @RequestBody
 * 使用@RequestBody将请求体中的那内容转换为方法的参数对象
 * 这里是将请求体中的JSON字符串转换为Users对象
```
    @PostMapping("/add")
    public String addUser(@RequestBody Users user){
        System.out.println(user.getUserName());
        System.out.println(user.getTel());
        System.out.println(user.getAge());
        System.out.println(user.getBirth());
        return "ok";
    }
```


## 原生servlet-API
直接定义在方法的参数即可
```
    @GetMapping("test1")
    public String mapping1(HttpServletRequest request, HttpSession session){
        return "index";
    }
```

## 参数传递
使用model封装视图数据(在requestScope中可以获取)
```
    @GetMapping("test2")
    public String mapping2(Model model){

        model.addAttribute("info","用户信息");
        Users user = new Users();
        user.setUserName("GouZi");
        user.setAge(23);
        model.addAttribute("user",user);

        return "index2";
    }

```
使用ModelMap封装视图数据(在requestScope中可以获取)
```
    @GetMapping("/test3")
    public String mapping3(ModelMap modelMap){
        modelMap.addAttribute("info","用户信息");
        Users user = new Users();
        user.setUserName("GouZi");
        user.setAge(23);
        modelMap.addAttribute("user",user);
        return "index2";
    }
```
使用Map封装视图数据(在requestScope中可以获取)
```
    @GetMapping("/test4")
    public String mapping4(Map<String , Object> map){
        map.put("info","用户信息");
        Users user = new Users();
        user.setUserName("GouZi");
        user.setAge(23);
        map.put("user",user);
        return "index2";
    }
```
使用ModelAndView来绑定视图,那么方法放回值必须是ModelAndView的类型
ModelAndView有很多构造方法的重载，可以传入视图名称以及Model对象等等按需选择即可
```
    @GetMapping("/test5")
    public ModelAndView mapping5(){

        Users user = new Users();
        user.setAge(23);
        user.setUserName("GouZi");
        ModelAndView mav = new ModelAndView("index2");
        //可以通过getModel的方法来获取一个Model对象
        //也可以通过getModelMap方法来获取一个ModelMap对象
        Map<String , Object> model = mav.getModel();
        model.put("info","用户信息");
        model.put("user",user);
        return mav;
    }
	
	@GetMapping("/test6")
    public ModelAndView mapping6(){
        Map<String , Object> map = new HashMap<>();
        Users user = new Users();
        user.setUserName("GouZi");
        user.setAge(23);
        map.put("user",user);
        return new ModelAndView("index2",map);
    }
```
### modelAttribute注解使用
方式一：将注解声明在方法的参数上
表示将参数对象放入到Model中
```
    @PostMapping("/test7")
    public String mapping7(@ModelAttribute("user") Users user){

        return "index3";
    }
```
方式二，将注解声明在一个方法上
Spring会在调用具体请求处理方法之前，
先执行所有带有@ModelAttribute注解所修饰的方法，并将这个方法的返回值存放到Model中
注意：如果遇到@ModelAttribute注解修饰的参数，那么表单映射的数据会覆盖这个Model中的数据
```
 	@ModelAttribute("user")
    public Users getUser(){
        Users user = new Users();
        user.setUserName("GouZi");
        user.setAge(23);
        return user;
    }

```
因此，当执行这个请求处理方法时，会先从之前的Model中取出USER对象并复制到方法的参数中
```
    @GetMapping("/test8")
    public String mapping8(@ModelAttribute("user") Users user){

        return "index3";
    }

```