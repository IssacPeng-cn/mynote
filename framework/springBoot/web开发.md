## springboot下的springmvc

### restful访问

#### 返回字符串或json

```java
@RestController
public class RESTFullController {
    //http://locahost:8080/boot/user/106
    @RequestMapping("/boot/user/{id}")
    public Object user(@PathVariable("id") Integer id){
        return null;
    }
}
```

效果同下

```java
@Controller
@RequestMapping(value = "/hello")
public class TestController {

    @RequestMapping("/test1")
    @ResponseBody
    public String test1(){
        return "Hello World!";
    }
}
```

#### 返回前端页面地址

```java
@Controller
@RequestMapping(value = "/hello")
public class TestController {
    @RequestMapping(value = "test")
    public String test(){
        return "index";
    }
}
```

返回前端页面地址：并带参数返回

```java
@Controller
@RequestMapping(value = "/user")
public class UserController {
    @Autowired
    private UserService userService;

    @RequestMapping(value = "/show")
    public String show() {
        //返回user页面内容
        return "user";
    }

    @PostMapping(value = "/getUser")
    public String getuser(Integer id, Model model) {
        User user = userService.selectUserById(id);
        model.addAttribute("users", user);
        if (user != null) {
            return "userInfo";
        } else {
            return "errorinfo";
        }
    }

}
```

#### 传参

1、单一参数

```java
@RequestMapping(path = "/{id}", method = RequestMethod.GET)
public String getUser(@PathVariable String id ) {}
```

2.多参数

```java
@RequestMapping(path = "/{depid}/{userid}", method = RequestMethod.GET)
public String getUser(@PathVariable("depid") String departmentID,@PathVariable("userid") String userid){}
```

### 扫描包

原配置：

```xml
<context:component-scan base-package="com.taotao.cart.controller"/>
```

现在自动扫描@Controller的类，不需要配置

### 注解驱动

原配置：

```xml
<mvc:annotation-driven />
```

现在自动配置，不需要配置

### 自定义拦截器

原配置

```xml
<mvc:interceptors>
	<mvc:interceptor>
		<mvc:mapping path="/cart/**"/>
		<bean class="com.taotao.cart.interceptors.LogionInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```

现在：

1.按照springmvc的方式编写一个拦截器

```java
package com.uwo9.springboot.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component
public class LogionInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("已经进入了登录拦截器");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}
```

2.编写一个配置类基础WebMvcConfigurerAdapter类
3.为该配置添加@Configuration注解，标注此类为配置类，让springboot扫描到
4.覆盖其中的方法并添加已经编写好的拦截器;

```java
package com.uwo9.springboot.config;

import com.uwo9.springboot.interceptor.LogionInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;

@Configuration
public class WebConfig extends WebMvcConfigurerAdapter {

    @Autowired
    private LogionInterceptor logionInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //需要拦截路径
        String[] addPathPatterns={
                "/boot/*"
        };
        //不需要拦截路径
        String[] excludePathPatterns={
                "/boot/hello",
                "/boot/index"
        };
        //注册登录拦截器
        registry.addInterceptor(logionInterceptor).addPathPatterns(addPathPatterns).excludePathPatterns(excludePathPatterns);
    }
}
```

### 使用Filter

#### 方法1：通过注解方式实现

注解方式实现一个Filter

```java
package com.uwo9.springboot.Filter;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;

@WebFilter(urlPatterns = "/*")
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("您已进入过滤器");
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {

    }
}
```

2.在main方法的主类上添加注解

```java
@ServletComponentScan(basePackages = "com.uwo9.springboot.Filter")
```

#### 方法2：通过springboot的配置实现

1.编写一个普通的Filter：

```java
package com.uwo9.springboot.Filter;

import javax.servlet.*;
import java.io.IOException;

public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("您已进入过滤器");
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {

    }
}
```

2.编写一个Springboot的配置类

```java
package com.uwo9.springboot.config;

import com.uwo9.springboot.Filter.HeFilter;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;


@Configuration
public class ServletConfig {
    @Bean
    public FilterRegistrationBean heFilterRegistration(){
        FilterRegistrationBean registration=new FilterRegistrationBean(new HeFilter());
        registration.addUrlPatterns("/*");
        return registration;
    }
}
```

### 配置静态资源

#### 方法一：修改配置文件

进入规则为 /

如果进入SpringMVC的规则为/时，Spring Boot的默认静态资源的路径为：

```yaml
spring.resources.static-locations=classpath:/META-INF/resources/,classpath:/resources/,classpath:/static/,classpath:/public/
```

测试：

127.0.0.1:8088/timg.jpg

进入规则为.xxx或者不指定静态文件路径时

将静态资源放置到webapp下的static目录中即可通过地址访问：

测试：
127.0.0.1:8088/static/test.js

优先级顺序：

```
classpath:/META-INF/resources/,classpath:/resources/,classpath:/static/,classpath:/public/
```



#### 方法二：通过代码修改

以增加 /myres/* 映射到 classpath:/myres/* 为例的代码处理为： 

实现类继承 WebMvcConfigurerAdapter 并重写方法 addResourceHandlers

```java
@Configuration
public class MyWebAppConfigurer 
        extends WebMvcConfigurerAdapter {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/testStatic/**").addResourceLocations("classpath:/testStatic/");
        super.addResourceHandlers(registry);
    }
}
```

### 自定义错误界面

注册错误页面

```java
@Component
public class ErrorPageConfig implements ErrorPageRegistrar {
    @Override
    public void registerErrorPages(ErrorPageRegistry registry) {
        ErrorPage error400Page = new ErrorPage(HttpStatus.BAD_REQUEST, "/error/404");
        ErrorPage error404Page = new ErrorPage(HttpStatus.NOT_FOUND, "/error/404");
        ErrorPage error500Page = new ErrorPage(HttpStatus.INTERNAL_SERVER_ERROR, "/error/500");
        registry.addErrorPages(error400Page,error404Page,error500Page);
    }
}
```

controller进行拦截

然后你只需要写个controller拦截不同请求然后跳到不同的自定义错误页面即可，如下所示

```java
@RequestMapping("/error/{status}")
public String errorPage(@PathVariable Integer status){
    switch (status){
        case 401:
        case 400:return "/error/404";
        case 500:return "/error/500";
        default:return "/error/default";
    }
}
```

### 使用JSP

在springboot中使用jsp,按如下步骤进行：

1.在pom.xml文件中配置依赖

```xml
<!--引入springboot内嵌的tomcat对jsp的解析包-->
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
</dependency>

<!--jsp依赖jar包start-->
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>javax.servlet.jsp-api</artifactId>
    <version>2.3.1</version>
</dependency>

<!--jstl标签依赖jar包start-->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
</dependency>
```

2.在application.properties文件配置springmvc的视图展示为jsp:

```properties
spring.mvc.view.prefix=/WEB-INF/JSP/
spring.mvc.view.suffix=.jsp
```

3.在src/main下创建一个webapp目录，然后在该目录下新建jsp页面

```xml
<resources>
    <resource>
        <directory>src/main/java</directory>
        <includes>
            <include>**/*.xml</include>
        </includes>
    </resource>
    <resource>
        <directory>src/main/resources</directory>
        <includes>
            <include>**/*.*</include>
        </includes>
    </resource>
    <resource>
        <directory>src/main/webapp</directory>
        <targetPath>META-INF/resources</targetPath>
        <includes>
            <include>**/*.*</include>
        </includes>
    </resource>
</resources>
```

### 使用Servlet

#### 方法1：通过注解方式实现

1.Servlet3的注解方式编写一个Servlet

```java
package com.uwo9.springboot.Servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(urlPatterns = "/myServlet")
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().print("hello world");
        resp.getWriter().flush();
        resp.getWriter().close();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

2.在main方法的主类上添加注解：

```java
@ServletComponentScan(basePackages = "com.uwo9.springboot.Servlet")
```

#### 方法2：通过Springboot的配置实现

1.编写一个普通的Servlet：

```java
package com.uwo9.springboot.Servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class HeServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().print("hello world");
        resp.getWriter().flush();
        resp.getWriter().close();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

2.编写一个Springboot的配置类：

```java
package com.uwo9.springboot.config;

import com.uwo9.springboot.Servlet.HeServlet;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ServletConfig {
    @Bean
    public ServletRegistrationBean heServletRegistrationBean(){
        return new ServletRegistrationBean(new HeServlet(),"/servlet/heServlet");
    }
}
```

### 使用Listener

﻿listener是servlet规范定义的一种特殊类，用于监听servletContext,HttpSession和ServletRequest等域对象的创建和销毁事件。监听域对象的属性发生修改的事件，用于在事件发生前、发生后做一些必要的处理。可用于以下方面：1、统计在线人数和在线用户2、系统启动时加载初始化信息3、统计网站访问量4、记录用户访问路径。

编写监听器类

```java
package com.mashibing.config;

import javax.servlet.http.HttpSessionEvent;
import javax.servlet.http.HttpSessionListener;

public class MyHttpSessionListener implements HttpSessionListener {

    public static int online=0;

    @Override
    public void sessionCreated(HttpSessionEvent se) {
        System.out.println("创建session");
        online++;
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        System.out.println("销毁session");
    }
}

```

添加到配置类

```java
package com.mashibing.config;

import org.springframework.boot.web.servlet.ServletListenerRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    @Bean
    public ServletListenerRegistrationBean listenerRegist(){
        ServletListenerRegistrationBean srb = new ServletListenerRegistrationBean();
        srb.setListener(new MyHttpSessionListener());
        System.out.println("listener");
        return srb;
    }
}
```

添加控制层代码

```java
package com.mashibing.controller;

import com.mashibing.config.MyHttpSessionListener;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

@Controller
public class ThymeleafController {
    @RequestMapping("/login")
    public String login(HttpServletRequest request){
        HttpSession session = request.getSession(true);
        return "login";
    }

    @RequestMapping("online")
    @ResponseBody
    public String online(){
        return "当前在线人数："+MyHttpSessionListener.online +"人";
    }
}
```

先发送login请求，然后再发送online的请求

### 拓展springmvc



```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/msb").setViewName("success");
    }
}
```

注意：需要添加模板引擎

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

### 配置字符编码

#### 方法一：传统Spring提供的字符编码过滤器

```java
@Bean
public FilterRegistrationBean filterRegistrationBean() {
    FilterRegistrationBean registration = new FilterRegistrationBean();
    CharacterEncodingFilter characterEncodingFilter=new CharacterEncodingFilter();
    characterEncodingFilter.setForceEncoding(true);
    characterEncodingFilter.setEncoding("UTF-8");
    registration.setFilter(characterEncodingFilter);
    registration.addUrlPatterns("/");
    return registration;
}
```

在主类上需要扫描此过滤器，扫描包

注意：只有当spring.http.encoding.enabled=false 配置为false后，过滤器才会起作用；

#### 方法二：在application.properties中配置字符编码

从springboot1.4.2之后开始新增的一种配置字符编码；

```yaml
spring.http.encoding.charset=utf-8
spring.http.encoding.enabled=true
spring.http.encoding.force=true
```

### 非web应用程序

在springboot框架中，要创建一个非web应用程序（纯java程序）

#### 方法一：springboot的入口类实现CommandLineRunner接口

1.java项目起步依赖配置：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
```

2.覆盖CommandLineRunner接口的run()方法，run方法中编写具体的处理逻辑即可。

```java
package com.uwo9.springboot;

import com.uwo9.springboot.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application implements CommandLineRunner {

    @Autowired
    private  UserService userService;
    //相当于纯java程序main方法
    @Override
    public void run(String... args) throws Exception {
        System.out.println(userService.sayHi("zhangshan"));
    }
    public static void main(String[] args) {
        //启动springboot，启动的spring容器
        SpringApplication.run(Application.class, args);

    }
}
```

#### 方法二：

直接在main方法中，根据SpringApplication.run()方法获取返回Spring容器对象，再获取业务bean进行调用；

```java
package com.uwo9.springboot;

import com.uwo9.springboot.service.UserService;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        //返回spring容器对象
        ConfigurableApplicationContext context= SpringApplication.run(Application.class, args);
        UserService userService=(UserService)context.getBean("userServiceImpl");
        System.out.println(userService.sayHi("zhangshan"));
    }
}
```

### 程序包部署

﻿Spring Boot的项目一般都会有*Application的入口类，入口类中会有main方法，这是一个标准的Java应用程序的入口方法。

@SpringBootApplication注解是Spring Boot的核心注解，它其实是一个组合注解：

该注解主要组合了以下注解：

@ComponentScan

@EnableAutoConfiguration

@SpringBootConfiguration

```java
@SpringBootApplication
public class HelloApplication {
    
    public static void main(String[] args) {
        SpringApplication.run(HelloApplication.class, args);
    }

}
```

#### war包部署

﻿1.程序入口类需扩展继承SpringBootServletInitializer类

2.程序入口类覆盖如下方法：

```java
@Override
protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
    return builder.sources(Application.class);
}
```

3.更新包为war，在pom.xml中修改

```xml
<packaging>war</packaging>
```

4.配置springboot打包的插件

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```

5.在项目中通过Maven install在本地maven仓库安装成一个war包，然后将war包部署到tomcat下运行。

#### 发布到独立的tomcat中运行

﻿将spring-boot-starter-tomcat的范围设置为provided

设置为provided是在打包时会将该包排除，因为要放到独立的tomcat中运行，是不需要的。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <scope>provided</scope>
</dependency>
```

#### 打jar包与运行

﻿1.springboot程序打包，在pom.xml文件加如下Springboot的maven插件：

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```

2.在项目中使用Maven install在本地maven仓库安装成jar;

3.使用java -jar运行第2步生成jar包，从而可以启动springboot程序

4.访问第3步运行起来的springboot程序;

### 热部署

﻿该热部署插件在实际使用中会有一些小问题，明明已经重启，但没有生效，这种情况下，手动重启一卜程序

```xml
<!--springboot开发自动热部署-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```