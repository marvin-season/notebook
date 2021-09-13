## spring mvc

### 环境配置

#### **web.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
        http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            //	指定mvc配置类的位置
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <!--配置过滤器---编码-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <!--    配置监听器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener> <!-- 配置加载类路径的配置文件 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
</web-app>
```

<url-pattern>/</url-pattern>

这个配置可以拦截/login /hello类型的url,但是不会拦截到index.jsp类型的url，不会拦截jsp,.css的静态资源页面

<url-pattern>/*</url-pattern>

会拦截jsp,.css的静态资源页面

#### **spring-mvc.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 视图解析器对象 -->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    <!--前端控制器，哪些静态资源不拦截(指哪些可以访问)-->
    <!--    爷吐了，tmd还要重启,cao -->
    <mvc:resources location="/css/" mapping="/css/**"/>
    <mvc:resources location="/images/" mapping="/images/**"/>
    <mvc:resources location="/js/" mapping="/js/**"/>

    <mvc:annotation-driven/>
</beans>
```

### 常用注解

`@RequestParam`

`@RequestBody`

`ModelAttribute`

```java
//	init 方法将在所有方法被调用前先执行，并且将返回值init以键值对的形式放入resquestScope中，key=init, value=init
@ModelAttribute("init")
public User init(String name, Date birth){
    System.out.println("init: " + name);
    System.out.println("init: " + birth);
    System.out.println("初始化中... ...");
    User init = new User();
    init.setBirth(new Date());
    init.setName("马文澍");
    return init;
}

@RequestMapping("handle2")
//	将user对象以键值对的形式放入resquestScope中
public String handle2(@ModelAttribute("user") User user){
    System.out.println("handle2: " + user);
    return "success";
}
@PostMapping("handle3")
public String handle3(@ModelAttribute("user") User user){
    System.out.println("handle3: " + user);
    return "success";
}
```

<hr>

### 文件上传

#### 导入依赖

```xml
<dependency>
  <groupId>commons-io</groupId>
  <artifactId>commons-io</artifactId>
  <version>2.6</version>
</dependency>
<dependency>
  <groupId>commons-fileupload</groupId>
  <artifactId>commons-fileupload</artifactId>
  <version>1.3.3</version>
</dependency>
```

#### 表单准备

```html
<form action="${pageContext.request.contextPath}/file/upload" method="post" enctype="multipart/form-data">
    选择文件<input type="file" name="file">
    文件描述<input type="text" name="description">
    <input type="submit" value="提交">
</form>
```

#### 实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class MyFile {
    private String description;
    private MultipartFile file;
}
```

#### 传统上传方式

```java
public String fileUpload(HttpServletRequest request) {
//        使用file upload
//        上传位置
        String path = request.getSession().getServletContext().getRealPath("/files/");
        File file = new File(path);
        if (!file.exists()) {
            file.mkdirs();
        }
//        解析request对象
        DiskFileItemFactory factory = new DiskFileItemFactory();
        ServletFileUpload upload = new ServletFileUpload(factory);
//        开始解析
        try {
            List<FileItem> fileItems = upload.parseRequest(request);
            System.out.println("fileItems = " + fileItems);
//            遍历
            for (FileItem fileItem : fileItems) {
                System.out.println("fileItem = " + fileItem);
                if (fileItem.isFormField()) {
                    System.out.println("fileItem = " + fileItem);
//                    普通表单项
                } else {
//                      上传文件项
                    String fileName = fileItem.getName();
                    System.out.println("fileName = " + fileName);
                    System.out.println("path = " + path);
                    /*
                     * path:上传文件位置，文件名称
                     *
                     */
                    fileItem.write(new File(path, fileName));
                    fileItem.delete();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return "上传成功";
    }
```

#### 单文件上传

`multipartResolver`id必须是这个名字

```xml
<bean id="multipartResolver"
      class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <property name="maxUploadSize" value="5400000"/>
    <property name="defaultEncoding" value="UTF-8"/>
</bean>
```

```java
@Controller
@RequestMapping("file")
public class FileController {

    @PostMapping("upload")
    public String upload(MyFile myFile){
        MultipartFile file = myFile.getFile();
        String name = file.getName();
        String originalFilename = file.getOriginalFilename();
        String[] strings = originalFilename.split("\\.");
        System.out.println("string1 = " + strings[1]);
        File path = new File("D:\\mawenshu\\dev\\workspace\\springmvc\\study", UUID.randomUUID() + "." + strings[1]);
        try {
            myFile.getFile().transferTo(path);
            System.out.println("文件上传成功");
        } catch (IOException e) {
            System.out.println("文件上传失败");
            e.printStackTrace();
        }
        return "redirect:/index.jsp";
    }
}
```

#### 多文件上传

将文件存放到一个链表当中

```html
<form action="${pageContext.request.contextPath}/file/uploads" method="post" enctype="multipart/form-data">
    选择文件<input type="file" name="myFiles[0].file">
    文件描述<input type="text" name="myFiles[0].description"><br>
    选择文件<input type="file" name="myFiles[1].file">
    文件描述<input type="text" name="myFiles[1].description"><br>
    选择文件<input type="file" name="myFiles[2].file">
    文件描述<input type="text" name="myFiles[2].description"><br>
    <input type="submit" value="提交">
</form>
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class MyFiles {
    private List<MyFile> myFiles;
}
```

```java
@PostMapping("uploads")
public String uploads(MyFiles myFiles) {
    Iterator<MyFile> iterator = myFiles.getMyFiles().iterator();
    //	遍历每个文件
    while (iterator.hasNext()){
        MyFile myFile = iterator.next();
        String originalFilename = myFile.getFile().getOriginalFilename();
        assert originalFilename != null;
        String[] strings = originalFilename.split("\\.");
        String storePath = "D:\\mawenshu\\dev\\workspace\\springmvc\\study";
        File file = new File(storePath, UUID.randomUUID() + "." + strings[1]);
        try {
            myFile.getFile().transferTo(file);
            System.out.println(myFile.getFile().getOriginalFilename()+"上传成功");
        } catch (IOException e) {
            System.out.println(myFile.getFile().getOriginalFilename()+"上传失败");
            e.printStackTrace();
        }
    }
    return "redirect:/index.jsp";
}
```

### 文件下载

```java
@GetMapping("download")
public void download(HttpServletRequest request,
                     HttpServletResponse response,
                     @RequestParam("filename") String filename) throws Exception {
    ServletContext servletContext = request.getSession().getServletContext();
    String mimeType = servletContext.getMimeType(filename);
    //	设置响应格式
    response.setContentType(mimeType);
    // 弹出  中文不能正常显示，需要URLEncoder 或者base64进行编码
    response.setHeader("content-disposition", "attachment;filename=" + filename);
    String pathDir = ResourceUtils.getURL("classpath:").getPath() + "static/files/20201118/";
    String realPath = pathDir + filename;
    //	提取文件流
    FileInputStream fis = new FileInputStream(realPath);
	 //	  输出流
    ServletOutputStream sos = response.getOutputStream();

    //	① 将数据从fis 流中转移到sos流中
    byte[] buff = new byte[1024 * 8];
    int len = 0;
    while ((len = fis.read(buff)) != -1) {
        sos.write(buff, 0, len);
    }
    fis.close();
}
```

`IOUtils.copy(fis, sos);`此工具类可以替换==①==将输入流的东西copy到输出流



