### 1.为什么要使用 hibernate

 hibernate 是对 jdbc 的封装，大大简化了数据访问层的繁琐的重复性代码。

 hibernate 是一个优秀的 ORM 实现，很多程度上简化了 DAO 层的编码功能。

可以很方便的进行数据库的移植工作。

提供了缓存机制，是程序执行更改的高效。

### 2.什么是 ORM 框架

ORM（Object Relation Mapping）对象关系映射，是把数据库中的关系数据映射成为程序中的对象。

使用 ORM 的优点：提高了开发效率降低了开发成本、开发更简单更对象化、可移植更强。

### 3.hibernate 中如何在控制台查看打印的 SQL 语句

在 Config 里面把 hibernate. show_SQL 设置为 true 就可以。但不建议开启，开启之后会降低程序的运行效率。

### 4.hibernate 有几种查询方式

三种：hql、原生 SQL、条件查询 Criteria。

### 5.hibernate 实体类可以被定义为 final 吗

实体类可以定义为 final 类，但这样的话就不能使用 hibernate 代理模式下的延迟关联提供性能了，所以不建议定义实体类为 final。

### 6.在 hibernate 中使用 Integer 和 int 做映射有什么区别

Integer 类型为对象，它的值允许为 null，而 int 属于基础数据类型，值不能为 null。

### 7.hibernate 是如何工作的

读取并解析配置文件。

读取并解析映射文件，创建 SessionFactory。

打开 Session。

创建事务。

进行持久化操作。

提交事务。

关闭 Session。

关闭 SessionFactory。

### 8.get()和 load()的区别

数据查询时，没有 OID 指定的对象，get() 返回 null；load() 返回一个代理对象。

load()支持延迟加载；get() 不支持延迟加载。

### 9.说一下 hibernate 的缓存机制

hibernate 常用的缓存有一级缓存和二级缓存：

一级缓存：也叫 Session 缓存，只在 Session 作用范围内有效，不需要用户干涉，由hibernate 自身维护，可以通过：evict(object)清除 object 的缓存；clear()清除一级缓存中的所有缓存；flush()刷出缓存；

二级缓存：应用级别的缓存，在所有 Session 中都有效，支持配置第三方的缓存，如：EhCache。

### 10.hibernate 对象有哪些状态

临时/瞬时状态：直接 new 出来的对象，该对象还没被持久化（没保存在数据库中），不受 Session 管理。

持久化状态：当调用 Session 的 save/saveOrupdate/get/load/list 等方法的时候，对象就是持久化状态。

游离状态：Session 关闭之后对象就是游离状态。

### 11.在 hibernate 中 getCurrentSession 和 openSession 的区别是什么

getCurrentSession 会绑定当前线程，而 openSession 则不会。

getCurrentSession 事务是 Spring 控制的，并且不需要手动关闭，而 openSession 需要我们自己手动开启和提交事务。

### 12.hibernate 实体类必须要有无参构造函数吗？为什么

hibernate 中每个实体类必须提供一个无参构造函数，因为 hibernate 框架要使用reflection api，通过调用 ClassnewInstance() 来创建实体类的实例，如果没有无参的构造函数就会抛出异常。