# 七天玩转Redis | Day5、Java操作Redis

![image-20220125182513414](七天玩转Redis  Day5、Java操作Redis.assets/42c614870cffc69e8b1a37fe648ae48b.png)

## 1、Redis的Java客户端

Redis的Java客户端也有很多: https://redis.io/clients#java，其中比较受欢迎的是Jedis和Lettuce。

- Jedis在实现上是直接连接的redis server，如果在多线程环境下是非线程安全的，这个时候只有使用连接池，为每个Jedis实例增加物理连接，**官方推荐。** https://mvnrepository.com/search?q=jedis
- Lettuce的连接是基于Netty的，连接实例(StatefulRedisConnection)可以在多个线程间并发访问，应为
  StatefulRedisConnection是线程安全的，所以一个连接实例(StatefulRedisConnection）就可以满足多线程环境下的并发访问，当然这个也是可伸缩的设计，一个连接实例不够的情况也可以按需增加连接实例。
- 在SpringBoot Data Redis 1.x之前默认使用的是Jedis，但目前最新版的修改成了Lettuce。
- 之前公司使用Jedis居多，Lettuce近两年在逐步上升，总的来讲Jedis的性能会优于Lettuce(因为它是直接操作Redis)。

**JedisTest.java** 

```java
package com.xiao;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import redis.clients.jedis.Jedis;

import java.util.Set;

public class JedisTest {

    Jedis jedis = null;

    // 建立连接
    @Before
    public void init() {
        // 初始化 Jedis 客户端，声明主机和端口
        jedis = new Jedis("192.168.65.3", 6379);
        // 身份认证
        jedis.auth("123456");
        // PING PONG 心跳机制检测是否连接成功
        String pong = jedis.ping();
        System.out.println("pong = " + pong);
    }

    @Test
    public void testString() {
        //选择数据库
        jedis.select(2);
        //插入一条数据
        String result = jedis.set("username", "xiao");
        System.out.println("result = " + result);
        //获取一条数据
        String username = jedis.get("username");
        System.out.println("username = " + username);
    }

    @Test
    public void testKeys() {
        jedis.select(2);
        Set<String> keys = jedis.keys("*");
        System.out.println(keys);
    }
    // 释放资源
    @After
    public void close(){
        if (jedis != null) {
            jedis.close();
        }
    }

}

```

**JedisPoolConnectRedis.java**

```java
package com.xiao;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

// 连接池工具类
public class JedisPoolConnectRedis {

        private static JedisPool jedisPool;

        static {
            // 创建连接池配置对象
            JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
            // 设置最大连接数，默认8
            jedisPoolConfig.setMaxTotal(5);
            // 设置最大空闲数量，默认8
            jedisPoolConfig.setMaxIdle(5);
            // 设置最小空闲数量，默认0
            jedisPoolConfig.setMinIdle(0);
            // 设置等待时间 ms
            jedisPoolConfig.setMaxWaitMillis(100);
            // 初始化 JedisPool 对象
            jedisPool = new JedisPool(jedisPoolConfig,
                    "192.168.65.3", 6379,
                    100,"123456");
        }
        /**
        * 获取Jedi对象
        * @return
        */
        public  static Jedis getJedis() {
            return jedisPool.getResource();
        }
}
```

## 2、 环境准备

### 2.1、 引入依赖

```xml
<!--引入jedis连接依赖-->
<dependency>
  <groupId>redis.clients</groupId>
  <artifactId>jedis</artifactId>
  <version>2.9.0</version>
</dependency>
```

### 2.2、创建jedis对象

```java
 public static void main(String[] args) {
   //1.创建jedis对象
   Jedis jedis = new Jedis("192.168.40.4", 6379);//1.redis服务必须关闭防火墙  2.redis服务必须开启远程连接
   jedis.select(0);//选择操作的库默认0号库
   //2.执行相关操作
   //....
   //3.释放资源
   jedis.close();
 }
```

## 3、常用API

### 3.1、 操作key相关API

```java
private Jedis jedis;
    @Before
    public void before(){
        this.jedis = new Jedis("192.168.202.205", 7000);
    }
    @After
    public void after(){
        jedis.close();
    }

    //测试key相关
    @Test
    public void testKeys(){
        //删除一个key
        jedis.del("name");
        //删除多个key
        jedis.del("name","age");

        //判断一个key是否存在exits
        Boolean name = jedis.exists("name");
        System.out.println(name);

        //设置一个key超时时间 expire pexpire
        Long age = jedis.expire("age", 100);
        System.out.println(age);

        //获取一个key超时时间 ttl
        Long age1 = jedis.ttl("newage");
        System.out.println(age1);

        //随机获取一个key
        String s = jedis.randomKey();

        //修改key名称
        jedis.rename("age","newage");

        //查看可以对应值的类型
        String name1 = jedis.type("name");
        System.out.println(name1);
        String maps = jedis.type("maps");
        System.out.println(maps);
    }
```

### 3.2、操作String相关API

```java
//测试String相关
    @Test
    public void testString(){
        //set
        jedis.set("name","小陈");
        //get
        String s = jedis.get("name");
        System.out.println(s);
        //mset
        jedis.mset("content","好人","address","海淀区");
        //mget
        List<String> mget = jedis.mget("name", "content", "address");
        mget.forEach(v-> System.out.println("v = " + v));
        //getset
        String set = jedis.getSet("name", "小明");
        System.out.println(set);

        //............
    }
```

### 3.3、操作List相关API

```java
//测试List相关
    @Test
    public void testList(){

        //lpush
        jedis.lpush("names1","张三","王五","赵柳","win7");

        //rpush
        jedis.rpush("names1","xiaomingming");

        //lrange

        List<String> names1 = jedis.lrange("names1", 0, -1);
        names1.forEach(name-> System.out.println("name = " + name));

        //lpop rpop
        String names11 = jedis.lpop("names1");
        System.out.println(names11);

        //llen
        jedis.linsert("lists", BinaryClient.LIST_POSITION.BEFORE,"xiaohei","xiaobai");

      	//........

    }

```

### 3.4、操作Set的相关API

```java
//测试SET相关
@Test
public void testSet(){

  //sadd
  jedis.sadd("names","zhangsan","lisi");

  //smembers
  jedis.smembers("names");

  //sismember
  jedis.sismember("names","xiaochen");

  //...
}
```

### 3.5、 操作ZSet相关API

```java
//测试ZSET相关
@Test
public void testZset(){

  //zadd
  jedis.zadd("names",10,"张三");

  //zrange
  jedis.zrange("names",0,-1);

  //zcard
  jedis.zcard("names");

  //zrangeByScore
  jedis.zrangeByScore("names","0","100",0,5);

  //..

}
```

### 3.6、 操作Hash相关API

```java
//测试HASH相关
@Test
public void testHash(){
  //hset
  jedis.hset("maps","name","zhangsan");
  //hget
  jedis.hget("maps","name");
  //hgetall
  jedis.hgetAll("mps");
  //hkeys
  jedis.hkeys("maps");
  //hvals
  jedis.hvals("maps");
  //....
}
```

