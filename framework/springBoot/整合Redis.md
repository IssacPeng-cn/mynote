## ﻿方法一：Springboot集成Redis

1.在pom.xml中配置相关的jar依赖；

```xml
<!--加载springboot redis包-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

2.在springboot核心配置文件application.properties中配置redis连接信息：

```yaml
#配置redis连接信息
spring.redis.host=192.168.47.14
spring.redis.port=6379
spring.redis.password=123456
```

3.配置了上面的步骤,springboot将自动配置redis Temolate,在需要操作redis的类中注入redis Template;
在使用类中注入

```java
//注入springboot自动配置好的RedisTemplate
@Autowired
private RedisTemplate<String, String> redisTemplate;
//注入springboot自动配置好的RedisTemplate
@Autowired
private RedisTemplate<Object, Object> redisTemplate;
```

springboot帮我们注入redis Template类，泛型里面只能写<String,String>、<Object,Object>

```java
@Override
public  List<Student> getAllStudent() {
    //字符串的序列化器
    RedisSerializer redisSerializer = new StringRedisSerializer();
    redisTemplate.setKeySerializer(redisSerializer);
    //高并发条件下，此处有点问题，缓存穿透
    //解决方法1：在方法上加synchronized,最简单的方式,但效率低
    //解决方法2：在方法体上加synchronized (this){}，效率会提升。 
    //查询缓存
    List<Student> studentList = (List<Student>) redisTemplate.opsForValue().get("allStudents");
    //双重检测锁
    if(null == studentList){
        synchronized (this){
            studentList = (List<Student>) redisTemplate.opsForValue().get("allStudents");
            if (null == studentList) {
                //缓存为空，查询一遍数据库
                studentList = studentMapper.selectAllStudent();
                //
                redisTemplate.opsForValue().set("allStudents", studentList);
            }
        }
    }
    return studentList;
}
```

可以使用多线程测试缓存穿透问题

```yaml
#redis集群中的哨兵模式配置
spring.redis.password=123456
spring.redis.sentinel.master=mymaster
spring.redis.sentinel.nodes=192.168.47.14:26380,192.168.47.14:26382,192.168.47.14:26384
```

```yaml
#redis-cluster集群配置
spring.redis.cluster.nodes=192.168.47.14:7001,192.168.47.14:7002,192.168.47.14:7003,192.168.47.14:7004,192.168.47.14:7005,192.168.47.14:7006
spring.redis.password=123456
spring.redis.cluster.max-redirects=3
```

## 方法二：Spring整合方式

```java
import java.util.ArrayList;
import java.util.List;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

import redis.clients.jedis.JedisPoolConfig;
import redis.clients.jedis.JedisShardInfo;
import redis.clients.jedis.ShardedJedisPool;

@Configuration
@PropertySource(value = "classpath:redis.properties")
public class RedisSpringConfig {

    @Value("${redis.maxTotal}")
    private Integer redisMaxTotal;

    @Value("${redis.node1.host}")
    private String redisNode1Host;

    @Value("${redis.node1.port}")
    private Integer redisNode1Port;

    private JedisPoolConfig jedisPoolConfig() {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        jedisPoolConfig.setMaxTotal(redisMaxTotal);
        return jedisPoolConfig;
    }

    @Bean
    public ShardedJedisPool shardedJedisPool() {
        List<JedisShardInfo> jedisShardInfos = new ArrayList<JedisShardInfo>();
        jedisShardInfos.add(new JedisShardInfo(redisNode1Host, redisNode1Port));
        return new ShardedJedisPool(jedisPoolConfig(), jedisShardInfos);
    }
}
```