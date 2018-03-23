# spring-mvc中文件的上传和下载

在Spring-MVC中常用的上传下载有两种
* servlet3.0后提供的API下载方式
* commons-fileupload插件下载

## 上传
### servlet3.0方式上传
在web.xml中配置multipart-config开启文件上传功能
```
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:dispatcher-servlet.xml</param-value>
        </init-param>
        <!--  开启上传  -->
        <multipart-config>
            <!--设置临时路径目录-->
            <location>/</location>
        </multipart-config>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

```
配置文件上传解析器，这里使用spring封装的Servlet3的上传解析器
注意：(解析器的ID不能为'multipartResolver',一个莫名其妙的错误待解决。)
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="edu.nf.ch07.controller"/>

    <mvc:annotation-driven/>

    <!-- 配置文件上传解析器，这里使用spring封装的Servlet3的上传解析器 -->
    <bean id="multiResolver" class="org.springframework.web.multipart.support.StandardServletMultipartResolver"/>


    <mvc:default-servlet-handler/>


</beans>
```
使用MultipartFile对象封装上传数据
```
    @PostMapping("/upload")
    public String upload(String readme,
                         HttpServletRequest request,
                         Model model,
                         @RequestParam("file") MultipartFile uploadFile) {
        System.out.println(readme);
        //获取文件上传路径
        String path = request.getServletContext().getRealPath("/files");
        File dir = new File(path);
        //如果上传目录不存在，则创建出来
        if(!dir.exists()){
            dir.mkdir();
        }
        //获取上传的文件名
        String fileName = uploadFile.getOriginalFilename();
        //构建一个完整的文件信息
        File file = new File(path, fileName);
        System.out.println(file.getAbsolutePath());
        //执行上传
        try {
            uploadFile.transferTo(file);
            model.addAttribute("fileName",fileName);
        } catch (IOException e) {
            e.printStackTrace();
            throw new RuntimeException("上传失败！");
        }


        return "/WEB-INF/jsp/index.jsp";
    }

```

### commons-fileupload插件上传
添加依赖
```
<!-- 文件上传 -->
<dependency>
	<groupId>commons-fileupload</groupId>
	<artifactId>commons-fileupload</artifactId>
	<version>1.3.2</version>
</dependency>
```
在Spring核心配置文件中配置文件上传解析器
```
    <!--文件上传属性-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 上传文件大小上限，单位为字节（10MB） -->
        <property name="maxUploadSize">
            <value>10485760</value>
        </property>
        <property name="defaultEncoding" value="utf-8"/>
    </bean>

```
使用MultipartFile对象封装数据
```
    @PostMapping("/upload")
    public ResultVo upload(HttpServletRequest request , @RequestParam("file") MultipartFile file) throws IOException {
        ResultVo vo = new ResultVo();

        if (!file.isEmpty()) {
            String path = request.getServletContext().getRealPath("/upload/");
            File mkdir = new File(path);
            if (!mkdir.exists()){
                mkdir.mkdir();
            }
            File newFile = new File(path , file.getOriginalFilename());

            file.transferTo(newFile);
            vo.setValue("上传成功");
        }else{
            vo.setCode(500);
            vo.setMessage("上传失败");
        }
        return vo;
    }
```

## 下载
使用ResponseEntity\<byte[]>对象
```
    @GetMapping("/download")
    public ResponseEntity<byte[]> download(HttpServletRequest request,String fileName){

        //获取下载路径
        String path = request.getServletContext().getRealPath("/files");
        //构建下载文件
        File file = new File(path , fileName);
        //设置响应头信息，设置文件流的类型
        HttpHeaders httpHeaders = new HttpHeaders();
        ResponseEntity<byte[]> entity = null;
        try {
            //设置在响应头中的文件名称，这里进行重新编码，防止中文乱码。
            String headerFileName = new String(fileName.getBytes("UTF-8"),"ISO-8859-1");
            //设置内容描述
            httpHeaders.setContentDispositionFormData("attachment",headerFileName);
            //设置响应类型为Stream流
            httpHeaders.setContentType(MediaType.APPLICATION_OCTET_STREAM);
            //创建ResponseEntity对象

            entity = new ResponseEntity<>(FileUtils.readFileToByteArray(file),httpHeaders, HttpStatus.CREATED);
        } catch (Exception e) {
            e.printStackTrace();
            throw new RuntimeException("文件下载失败！");
        }
        return entity;
    }

```
FileUtils.readFileToByteArray(file)方法需要额外添加依赖
```
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.4</version>
        </dependency>

```