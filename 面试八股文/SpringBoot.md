### 1.什么是 spring boot？

spring boot 是为 spring 服务的，是用来简化新 spring 应用的初始搭建以及开发过程的。

### 2.为什么要用 spring boot？

• 配置简单

• 独立运行

• 自动装配

• 无代码生成和 xml 配置

• 提供应用监控

• 易上手

• 提升开发效率

### 3.spring boot 核心配置文件是什么？

spring boot 核心的两个配置文件：

• bootstrap (. yml 或者 . properties)：boostrap 由父 ApplicationContext 加载的，比 applicaton 优先加载，且 boostrap 里面的属性不能被覆盖；

• application (. yml 或者 . properties)：用于 spring boot 项目的自动化配置。

### 4.spring boot 配置文件有哪几种类型？它们有什么区别？

配置文件有 . properties 格式和 . yml 格式，它们主要的区别是书法风格不同。

. properties 配置如下：

```properties
spring.RabbitMQ.port=5672
```

. yml 配置如下：

```yaml
spring:
	RabbitMQ:
		port: 5672
```

. yml 格式不支持 @PropertySource 注解导入。

### 5.spring boot 有哪些方式可以实现热部署？

• 使用 devtools 启动热部署，添加 devtools 库，在配置文件中把 spring. devtools. restart. enabled 设置为 true；

• 使用 Intellij Idea 编辑器，勾上自动编译或手动重新编译。

