## 什么是Lettuce

如果你在网上搜索 Redis 的 Java 客户端，你会发现，大多数文献介绍的都是 Jedis，不可否认，Jedis 是一个优秀的基于 Java 语言的 Redis 客户端，但是，其不足也很明显：Jedis 在实现上是直接连接 Redis-Server，在多个线程间共享一个 Jedis 实例时是线程不安全的，如果想要在多线程场景下使用 Jedis，需要使用连接池，每个线程都使用自己的 Jedis 实例，当连接数量增多时，会消耗较多的物理资源。

与 Jedis 相比，Lettuce 则完全克服了其线程不安全的缺点：Lettuce 是一个可伸缩的线程安全的 Redis 客户端，支持同步、异步和响应式模式。多个线程可以共享一个连接实例，而不必担心多线程并发问题。它基于优秀 Netty NIO 框架构建，支持 Redis 的高级功能，如 Sentinel，集群，流水线，自动重新连接和 Redis 数据模型。

## Jedis与Lettuce区别

Lettuce 和 Jedis 的定位都是Redis的client，所以他们当然可以直接连接redis server。

Jedis在实现上是直接连接的redis server，如果在多线程环境下是非线程安全的，这个时候只有使用连接池，为每个Jedis实例增加物理连接

Lettuce的连接是基于Netty的，连接实例（StatefulRedisConnection）可以在多个线程间并发访问，应为StatefulRedisConnection是线程安全的，所以一个连接实例（StatefulRedisConnection）就可以满足多线程环境下的并发访问，当然这个也是可伸缩的设计，一个连接实例不够的情况也可以按需增加连接实例。

springboot2之前redis的连接池为jedis，2.0以后redis的连接池改为了lettuce，lettuce能够支持redis4，需要java8及以上。lettuce是基于netty实现的与redis进行同步和异步的通信。

## Lettuce基本使用

* 引入依赖包

```xml
<dependencies>
    <dependency>
        <groupId>biz.paluch.redis</groupId>
        <artifactId>lettuce</artifactId>
        <version>5.0.0.Beta1</version>
    </dependency>
</dependencies>
```

* 连接的创建

  三种方式

```java
RedisURI.create("redis://localhost/");
RedisURI.Builder.redis("localhost", 6379).auth("password").database(1).build();
new RedisURI("localhost", 6379, 60, TimeUnit.SECONDS);
```

* 基本使用

  ```java
  package com.capol.redis;
  
  import com.lambdaworks.redis.RedisClient;
  import com.lambdaworks.redis.api.StatefulRedisConnection;
  import com.lambdaworks.redis.api.sync.RedisCommands;
  
  /**
   * @author fuzihao
   * @date 2019/7/12 15:35
   */
  public class RedisTest1 {
      public static void main(String[] args) {
          RedisClient redisClient=RedisClient.create("redis://localhost");
          StatefulRedisConnection<String,String> connection=redisClient.connect();
          System.out.println(connection);
          connection.close();
          redisClient.shutdown();
      }
  }
  ```

  ​	创建连接

* 同步set测试

  ```java
  package com.capol.redis;
  
  import com.lambdaworks.redis.RedisClient;
  import com.lambdaworks.redis.api.StatefulRedisConnection;
  import com.lambdaworks.redis.api.sync.RedisCommands;
  
  /**
   * @author fuzihao
   * @date 2019/7/12 15:35
   */
  public class RedisTest1 {
      public static void main(String[] args) {
          RedisClient redisClient=RedisClient.create("redis://localhost");
          StatefulRedisConnection<String,String> connection=redisClient.connect();
  //        System.out.println(connection);
          //同步方式
          RedisCommands<String,String> commands=connection.sync();
          commands.set("a","2321");
          connection.close();
          redisClient.shutdown();
      }
  }
  ```

  打开Redis Desktop Managerment，发现 设置成功！

* 同步sget测试

  ```java
  package com.capol.redis;
  
  import com.lambdaworks.redis.RedisClient;
  import com.lambdaworks.redis.api.StatefulRedisConnection;
  import com.lambdaworks.redis.api.sync.RedisCommands;
  
  /**
   * @author fuzihao
   * @date 2019/7/12 15:35
   */
  public class RedisTest1 {
      public static void main(String[] args) {
          RedisClient redisClient=RedisClient.create("redis://localhost");
          StatefulRedisConnection<String,String> connection=redisClient.connect();
  //        System.out.println(connection);
          //同步方式
          RedisCommands<String,String> commands=connection.sync();
          System.out.println(commands.get("a"));
          connection.close();
          redisClient.shutdown();
      }
  }
  ```

* 异步方式

  ```java
  package com.capol.redis;
  
  import com.lambdaworks.redis.RedisClient;
  import com.lambdaworks.redis.RedisFuture;
  import com.lambdaworks.redis.api.StatefulRedisConnection;
  import com.lambdaworks.redis.api.async.RedisAsyncCommands;
  import com.lambdaworks.redis.api.sync.RedisCommands;
  
  import java.util.concurrent.ExecutionException;
  
  /**
   * @author fuzihao
   * @date 2019/7/12 15:35
   */
  public class RedisTest1 {
      public static void main(String[] args) {
          RedisClient redisClient=RedisClient.create("redis://localhost");
          StatefulRedisConnection<String,String> connection=redisClient.connect();
          //异步方式
          RedisAsyncCommands<String, String> redisAsync = connection.async();
          RedisFuture<String> redisFuture = redisAsync.get("a");
          try {
              //RedisFuture的get方法是阻塞方法，会一直阻塞到返回结果，可以添加超时时间 
              String a = redisFuture.get();
              System.out.println(a);
          } catch (InterruptedException e) {
              e.printStackTrace();
          } catch (ExecutionException e) {
              e.printStackTrace();
          }
          connection.close();
          redisClient.shutdown();
      }
  }
  ```

* 关闭连接

  ```java
  connection.close();
  redisClient.shutdown();
  ```

## 连接池的使用

Lettuce 连接被设计为线程安全,所以一个连接可以被多个线程共享,同时lettuce连接默认是自动重连.虽然连接池在大多数情况下是不必要的，但在某些用例中可能是有用的.lettuce提供通用的连接池支。

lettuce是线程安全的，可以被多个线程同时使用，所以线程池不是必须的。lettuce提供了一般的连接池支持。

lettuce的连接池依赖common-pool2

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
    <version>2.4.3</version>
</dependency>
```

```java
package com.capol.redis;

import com.lambdaworks.redis.RedisClient;
import com.lambdaworks.redis.api.StatefulRedisConnection;
import com.lambdaworks.redis.api.sync.RedisCommands;
import com.lambdaworks.redis.support.ConnectionPoolSupport;
import org.apache.commons.pool2.impl.GenericObjectPool;
import org.apache.commons.pool2.impl.GenericObjectPoolConfig;

/**
 * @author fuzihao
 * @date 2019/7/12 15:50
 */
public class RedisTest2 {
    public static void main(String[] args) throws Exception {
        RedisClient client = RedisClient.create("redis://localhost");

        GenericObjectPoolConfig poolConfig = new GenericObjectPoolConfig();
        poolConfig.setMaxIdle(10);

        GenericObjectPool<StatefulRedisConnection<String, String>> pool = ConnectionPoolSupport.createGenericObjectPool(
                () -> client.connect(), poolConfig);
        for (int i = 0; i < 20; i++) {
            StatefulRedisConnection<String, String> connection = pool.borrowObject();
            RedisCommands<String, String> sync = connection.sync();
            sync.ping();
            //以下报错
//            connection.close();
            //主动将连接归还到连接池
            pool.returnObject(connection);
        }
    }
}
```

