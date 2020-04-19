# Redis

https://www.jianshu.com/p/56999f2b8e3b

## Redis 原理

### NoSQL 技术

Java Web开发中，如果面对高性能高并发请求（比如商品抢购，微博爆点），需要数据库在极短的时间内完成大量的读/写操作，受限于磁盘较慢的读写速度，极其容易造成数据库瘫痪，最终导致服务宕机的严重生产问题。

为了克服上述问题，Java Web 开发通常会引入 NoSQL：一种**基于内存**的数据存储技术，以满足高速响应的需求。Redis 和MongoDB 是当前使用最广泛的 NoSQL。

### Redis 流程

1. 对单次数据库访问请求，服务器只在 Redis 上读/写相应业务数据，而不对数据库进行操作。

2. 完成 Redis 的读/写之后，会进行判断（如商品余额是否为0），一旦触发事件再将 Redis 的缓存数据批量写入数据库。

这样就不用每次内存读写都经过磁盘，大大提升了数据交互的速度。但限于内存成本的原因，一般我们只使用 Redis 存储高频使用的关键数据。

### Redis 特征

1. 性能极好。

2. 以 key-value 的形式保存数据，同时支持 二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
   
3. 单个操作都是原子性的。多个操作通过 MULTI 和 EXEC 指令包装，同样支持事务。

4. Redis还支持 publish/subscribe, 通知, key 过期等等特性。





Redis 只能支持六种数据类型（string/hash/list/set/zset/hyperloglog）的操作，但在 Java 中我们却通常以类对象为主，所以在需要 Redis 存储的五中数据类型与 Java 对象之间进行转换，如果自己编写一些工具类，比如一个角色对象的转换，还是比较容易的，但是涉及到许多对象的时候，这其中无论工作量还是工作难度都是很大的，所以总体来说，就操作对象而言，使用 Redis 还是挺难的，好在 Spring 对这些进行了封装和支持。

## Redis 使用

### Java 使用

第一步：添加 Jedis 依赖

第二步：使用 Redis 连接池

Java Redis也同样提供了类redis.clients.jedis.JedisPool来管理Redis连接池对象，并且我们可以使用redis.clients.jedis.JedisPoolConfig来对连接池进行配置，代码如下：

```java
JedisPoolConfig poolConfig = new JedisPoolConfig();
// 最大空闲数
poolConfig.setMaxIdle(50);
// 最大连接数
poolConfig.setMaxTotal(100);
// 最大等待毫秒数
poolConfig.setMaxWaitMillis(20000);
// 使用配置创建连接池
JedisPool pool = new JedisPool(poolConfig, "localhost");
// 从连接池中获取单个连接
Jedis jedis = pool.getResource();
// 如果需要密码
//jedis.auth("password");
```


### Spring Boot 使用

（1）在SpringBoot中添加Redis依赖：
```xml
<!-- Radis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
（2）添加配置文件：

在SpringBoot中使用.properties或者.yml
```properties
# REDIS配置
# Redis数据库索引（默认为0）
spring.redis.database=0
# Redis服务器地址
spring.redis.host=localhost
# Redis服务器连接端口
spring.redis.port=6379
# Redis服务器连接密码（默认为空）
spring.redis.password=
# 连接池最大连接数（使用负值表示没有限制）
spring.redis.pool.max-active=8
# 连接池最大阻塞等待时间（使用负值表示没有限制）
spring.redis.pool.max-wait=-1
# 连接池中的最大空闲连接
spring.redis.pool.max-idle=8
# 连接池中的最小空闲连接
spring.redis.pool.min-idle=0
# 连接超时时间（毫秒）
spring.redis.timeout=0
```

（3）测试访问

实体类要求序列化

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest()
public class ApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void test() throws Exception {

        User user = new User();
        user.setName("我没有三颗心脏");
        user.setAge(21);

        redisTemplate.opsForValue().set("user_1", user);
        User user1 = (User) redisTemplate.opsForValue().get("user_1");

        System.out.println(user1.getName());
    }
}
```
]



通过上面这段极为简单的测试案例演示了如何通过自动配置的StringRedisTemplate对象进行Redis的读写操作，该对象从命名中就可注意到支持的是String类型。原本是RedisTemplate<K, V>接口，StringRedisTemplate就相当于RedisTemplate<String, String>的实现。

