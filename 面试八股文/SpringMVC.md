### 1.spring mvc 和 struts 的区别是什么

拦截级别：struts2 是类级别的拦截；spring mvc 是方法级别的拦截。

数据独立性：spring mvc 的方法之间基本上独立的，独享 request 和 response 数据，请求数据通过参数获取，处理结果通过 ModelMap 交回给框架，方法之间不共享变量；而 struts2 虽然方法之间也是独立的，但其所有 action 变量是共享的，这不会影响程序运行，却给我们编码和读程序时带来了一定的麻烦。

拦截机制：struts2 有以自己的 interceptor 机制，spring mvc 用的是独立的 aop 方式，这样导致 struts2 的配置文件量比 spring mvc 大。

对 ajax 的支持：spring mvc 集成了 ajax，所有 ajax 使用很方便，只需要一个注解 @ResponseBody 就可以实现了；而 struts2 一般需要安装插件或者自己写代码才行。

### 2.说一下 spring mvc 运行流程？

• spring mvc 先将请求发送给 DispatcherServlet。

• DispatcherServlet 查询一个或多个 HandlerMapping，找到处理请求的 Controller。

• DispatcherServlet 再把请求提交到对应的 Controller。

• Controller 进行业务逻辑处理后，会返回一个 ModelAndView。

• Dispathcher 查询一个或多个 ViewResolver 视图解析器，找到 ModelAndView 对象指定的视图对象。

• 视图对象负责渲染返回给客户端。

### 3.spring mvc 有哪些组件？

• 前置控制器 DispatcherServlet。

• 映射控制器 HandlerMapping。

• 处理器 Controller。

• 模型和视图 ModelAndView。

• 视图解析器 ViewResolver。

### 4.@RequestMapping 的作用是什么？

将 http 请求映射到相应的类/方法上。

### 5.@Autowired 的作用是什么？

@Autowired 它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作，通过@Autowired 的使用来消除 set/get 方法。