### 1.JSP 和 servlet 有什么区别

JSP 是 servlet 技术的扩展，本质上就是 servlet 的简易方式。servlet 和 JSP 最主要的不同点在于，servlet 的应用逻辑是在 Java 文件中，并且完全从表示层中的 html 里分离开来，而 JSP 的情况是 Java 和 html 可以组合成一个扩展名为 JSP 的文件。JSP 侧重于视图，servlet 主要用于控制逻辑。

###  2.JSP 有哪些内置对象？作用分别是什么

JSP 有 9 大内置对象：

request：封装客户端的请求，其中包含来自 get 或 post 请求的参数；

response：封装服务器对客户端的响应；

pageContext：通过该对象可以获取其他对象；

session：封装用户会话的对象；

application：封装服务器运行环境的对象；

out：输出服务器响应的输出流对象；

config：Web 应用的配置对象；

page：JSP 页面本身（相当于 Java 程序中的 this）；

exception：封装页面抛出异常的对象。

### 3.说一下 JSP 的 4 种作用域

page：代表与一个页面相关的对象和属性。

request：代表与客户端发出的一个请求相关的对象和属性。一个请求可能跨越多个页面，涉及多个 Web 组件；需要在页面显示的临时数据可以置于此作用域。

session：代表与某个用户与服务器建立的一次会话相关的对象和属性。跟某个用户相关的数据应该放在用户自己的 session 中。

application：代表与整个 Web 应用程序相关的对象和属性，它实质上是跨越整个Web 应用程序，包括多个页面、请求和会话的一个全局作用域。

### 4.session 和 cookie 有什么区别

存储位置不同：session 存储在服务器端；cookie 存储在浏览器端。

安全性不同：cookie 安全性一般，在浏览器存储，可以被伪造和修改。

容量和个数限制：cookie 有容量限制，每个站点下的 cookie 也有个数限制。

存储的多样性：session 可以存储在 Redis 中、数据库中、应用程序中；而 cookie 只能存储在浏览器中。

### 5.说一下 session 的工作原理

session 的工作原理是客户端登录完成之后，服务器会创建对应的 session，session 创建完之后，会把 session 的 id 发送给客户端，客户端再存储到浏览器中。这样客户端每次访问服务器时，都会带着 sessionid，服务器拿到 sessionid 之后，在内存找到与之对应的 session 这样就可以正常工作了。

### 6.如果客户端禁止 cookie 能实现 session 还能用吗

可以用，session 只是依赖 cookie 存储 sessionid，如果 cookie 被禁用了，可以使用url 中添加 sessionid 的方式保证 session 能正常使用。

### 7.如何避免 SQL 注入

使用预处理 PreparedStatement。

使用正则表达式过滤掉字符中的特殊字符。

### 8.什么是 XSS 攻击，如何避免

XSS 攻击：即跨站脚本攻击，它是 Web 程序中常见的漏洞。原理是攻击者往 Web 页面里插入恶意的脚本代码（css 代码、Javascript 代码等），当用户浏览该页面时，嵌入其中的脚本代码会被执行，从而达到恶意攻击用户的目的，如盗取用户 cookie、破坏页面结构、重定向到其他网站等。

预防 XSS 的核心是必须对输入的数据做过滤处理。

### 9.什么是 CSRF 攻击，如何避免

CSRF：Cross-Site Request Forgery（中文：跨站请求伪造），可以理解为攻击者盗用了你的身份，以你的名义发送恶意请求，比如：以你名义发送邮件、发消息、购买商品，虚拟货币转账等。

防御手段：

​	验证请求来源地址；

​	关键操作添加验证码；

​	在请求地址添加 token 并验证。

### 10.过滤器和拦截器的区别

过滤器是基于函数回调的，而拦截器是基于java反射的；

过滤器依赖于servlet容器，而拦截器不依赖与Servlet容器，拦截器依赖SpringMVC；

过滤器几乎对所有的请求都可以起作用，而拦截器只能对SpringMVC请求起作用；

拦截器可以访问处理方法的上下文，而过滤器不可以；

触发时机不同，过滤器Filter是在请求进入容器后，但在进入servlet之前进行预处理，请求结束是在servlet处理完以后。拦截器 Interceptor 是在请求进入servlet后，在进入Controller之前进行预处理的，Controller 中渲染了对应的视图之后请求结束。

控制执行顺序不同：过滤器用@Order注解控制执行顺序，通过@Order控制过滤器的级别，值越小级别越高越先执行。拦截器默认的执行顺序，就是它的注册顺序，也可以通过Order手动设置控制，值越小越先执行。