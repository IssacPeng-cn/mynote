# application配置

﻿﻿﻿Spring Boot项目使用一个全局的配置文件application.properties或者是application.yml，

在resources目录下或者类路径下的/config下，一般我们放到resources下。

## application文件名格式

```
/{application}.yml
/{application}-{profile}.yml
/{label}/{application}-{profile}.yml/{application]-{profile}.properties
/{label}/{application}-{profile}.properties
```

## 配置方式

### 1.多环境配置到多个文件

```
application-prod.yml
application-test.yml
```

### 2.多个环境写入一个配置文件

```yaml
spring:
  profiles: prod
    #prod环境相关配置

spring:
  profiles: test
    #test环境相关配置
```

### 3.代码方式控制配置

#### ﻿﻿控制Bean

```java
@Configuration
public class DataSourceConfig {

    @Bean
    @Profile("dev")
    public DataSource devDataSource(){
        //创建dev环境下的DataSource
    }

    @Bean
    @Profile("prod")
    public DataSource prodDataSource(){
        //创建prod环境下的DataSource
    }

}
```

#### 控制类

```java
@Profile("dev")
@Configuration
public class DevDataInitConfig {

    @Bean
    public CommandLineRunner dataInit(){
        return new CommandLineRunner() {
            @Override
            public void run(String... args) throws Exception {
                //执行Dev环境的数据初始化
            }
        };
    }
}
```



## 配置文件的激活

激活其中一个

```yaml
spring:
  profiles:
    active: prod
```

也可以同时启用多个,逗号分隔

```yaml
spring:
  profiles:
    active: prod,test
```

运行的时候，也可以在命令中指定配置

```
java -jar demo.jar --spring.profiles.active=prod
```

## 读取配置信息

### 默认方式

﻿#### ﻿1.使用@Value

不带默认值

```java
@Value("${mydemo.name}")
private String name;
```

带默认值

```java
@Value("${mydemo.name:}")
private String name;

@Value("${mydemo.age:18}")
private int age;
```

#### 2.使用@ConfigurationProperties注解嵌套

```java
@Configuration
@ConfigurationProperties(prefix = "mail") 
public class ConfigProperties {
 
    private String host;
    private int port;
    private String from;
    private List<String> defaultRecipients;
    private Map<String, String> additionalHeaders;
    private Credentials credentials;
 
    // standard getters and setters
}
```

以下属性文件将设置所有字段

```properties
#Simple properties
mail.hostname=mailer@mail.com
mail.port=9000
mail.from=mailer@mail.com
  
#List properties
mail.defaultRecipients[0]=admin@mail.com
mail.defaultRecipients[1]=owner@mail.com
 
#Map Properties
mail.additionalHeaders.redelivery=true
mail.additionalHeaders.secure=true
 
#Object properties
mail.credentials.username=john
mail.credentials.password=password
mail.credentials.authMethod=SHA
```

yaml文件list写法

```yaml
mail.
  defaultRecipients:
    - book
    - move
    - hh
```



#### 3.使用@ConfigurationProperties作用在@Bean注释的方法上

这种方式非常适合绑定到我们无法控制的第三方组件上

```java
#Item 类
public class Item {
    private String name;
    private int size;
 
    // standard getters and setters
}
 
#将属性绑定到Item实例上
@Configuration
public class ConfigProperties {
 
    @Bean
    @ConfigurationProperties(prefix = "item")
    public Item item() {
        return new Item();
    }

}
```

```properties
item.name=apple
item.size=100
```

#### 4.不可变的@ConfigurationProperties绑定

从 Spring Boot 2.2 开始，我们可以使用@ConstructorBinding注释来绑定我们的配置属性。
这实质上意味着@ConfigurationProperties注释的类现在可能是不可变的。需要强调的是，要使用构造函数绑定,
我们需要使用@EnableConfigurationProperties 或 @ConfigurationPropertiesScan显式启用我们的配置类
#ImmutableCredentials 类

```java
@ConfigurationProperties(prefix = "mail.credentials")
@ConstructorBinding
public class ImmutableCredentials {
 
    private final String authMethod;
    private final String username;
    private final String password;
 
    public ImmutableCredentials(String authMethod, String username, String password) {
        this.authMethod = authMethod;
        this.username = username;
        this.password = password;
    }
 
    public String getAuthMethod() {
        return authMethod;
    }
 
    public String getUsername() {
        return username;
    }
 
    public String getPassword() {
        return password;
    }
}
```

### 读取外部配置文件

#### 多个配置文件的同时加载

﻿可以通过配置spring.config.location来改变配置文件的默认加载位置从而实现对多个配置文件的同时加载

eg:

```yaml
spring:
  config:
    location: file:///D:/test.properties,classpath:/config/
```

#### 通过@PropertySource注解

可以查看源码：支持数组方式配置多个配置

```java
@Component
@PropertySource(value = "classpath:test.properties")
public class SpringCssSource {
    @Value("${springcss.order2.aaa}")
    private String aaa;

    public void start(){
        System.out.println(this.aaa);
    }

}
```

#### 通过@PropertySources注解

```java
@Component
@PropertySources({
        @PropertySource(value = "classpath:test.properties"),
        @PropertySource(value = "classpath:test2.properties"),
})
public class SpringCssSource {
    @Value("${springcss.order2.aaa}")
    private String aaa;

    public void start(){
        System.out.println(this.aaa);
    }

} 
```

### 读取xml文件

﻿Spring Boot 提倡零配置，即无xml配置，但是在实际项目中，可能有一些特殊要求你必
须使用xml配置，这时我们可以通过Spring提供的@ImportResource来加载xml配置，例如:

```java
@ImportResource ({"classpath:some-context.xml" ,"classpath:another-context.xml"})
```

### 应用系统中嵌入系统配置信息

```properties
myapplication.name:${spring.application.name}
```

eg:

```properties
system.name=springcss
system.domain=health
system.description=The system ${name} is used for ${domain}.
```

maven项目获取与构建系统相关的配置信息

```yaml
info:
  app:
    encoding: @project.build.sourceEncoding@
    java:
      source: @java.version@
      target: @java.version@
```

## 其它

### 修改端口

```properties
server.port=8088
```

### 修改DispatcherServlet规则

﻿﻿修改进入DispatcherServlet的规则为：\*.html

```
server.servlet-path=*.html
```

测试：127.0.0.1:8088/hello.html

### 日志

﻿Spring Boot对各种日志框架都做了支持，我们可以通过配置来修改默认的日志的配置：

```properties
#设置日志级别
logging.level.org.springframework=DEBUG

格式：
logging.level.*= # Log levels severity mapping. For instance `logging.level.org.springframework=DEBUG`
```

#### 日志级别

﻿日志记录器(Logger)是日志处理的核心组件。log4j具有5种正常级别(Level)。

1. static Level DEBUG :

  DEBUG Level指出细粒度信息事件对调试应用程序是非常有帮助的,一般认为比较重要的方法执行需要详细查看运行情况的则开启debug。

2. static Level INFO

  INFO level表明消息在粗粒度级别上突出强调应用程序的运行过程，只需要了解该方法是否运行的可以使用INFO

3. static Level WARN

  WARN level表明会出现潜在错误的情形。

4. static Level ERROR

  ERROR level指出虽然发生错误事件，但仍然不影响系统的继续运行。一般异常处理等情况都需要ERROR

5. static Level FATAL

  FATAL level指出每个严重的错误事件将会导致应用程序的退出。

另外，还有两个可用的特别的日志记录级别: 

1.static Level ALL 

    ALL Level是最低等级的，用于打开所有日志记录。 

2.static Level OFF 

    OFF Level是最高等级的，用于关闭所有日志记录。 


日志记录器（Logger）的行为是分等级的： 

分为OFF、FATAL、ERROR、WARN、INFO、DEBUG、ALL或者您定义的级别。Log4j建议只使用四个级别，优先级 从高到低分别是 ERROR、WARN、INFO、DEBUG。 

通过在这里定义的级别，您可以控制到应用程序中相应级别的日志信息的开关。比如在这里定义了INFO级别， 则应用程序中所有DEBUG级别的日志信息将不被打印出来。 

优先级高的将被打印出来。项目上生产环境时候建议把debug的日志级别重新调为warn或者更高，避免产生大量日志。

### 自定义banner

﻿﻿启动Spring Boot项目后会看到banner图案：

这个图片其实是可以自定义的：
1. 打开网站：

   ```
   http://patorjk.com/software/taag/#p=display&h=3&v=3&f=4Max&t=itcast%20Spring%20Boot
   https://bootschool.net/ascii
   ```

2. 拷贝生成的字符到一个文本文件中，并且将该文件命名为banner.txt

3. 将banner.txt拷贝到项目的resources目录中：

4.	重新启动程序，查看效果：

如果不想看到任何的banner，也是可以将其关闭的

```java
public static void main(String[] args) {
    SpringApplication application = new SpringApplication(Application.class);
    application.setBannerMode(Banner.Mode.OFF);
    application.run(args);
}
```

# 加载顺序

﻿1.项目根目录下的config目录。

2.项目根目录。

3.classpath下的config目录。

4.classpath目录。

Spring Boot会从这4个位置全部加载主配置文件，这4个位置中的application.properties文件的优先级按照上面列出的顺序依次降低。如果同一个属性都出现在这4个文件中，则以优先级高的文件为准。

```
-file:./config
-file:./
-classpath:/config
-classpath:/ 
```

# 自动配置

Spring Boot在进行SpringApplication对象实例化时会加载META-INF/spring.factories文件，将该配置文件中的配置载入到Spring容器

关闭自动配置

如果我们不需要Spring Boot自动配置，想关闭某一项的自动配置，该如何设置呢？

比如：我们不想自动配置Redis，想手动配置。

```java
@SpringBootApplication(exclude = {RedisAutoConfiguration.class})
```

当然了，其他的配置就类似了。

