# Redis作用

​	对于常用数据，第一次从数据库读出，然后就存放在NoSQL中，这样以后就无须再访问数据库，只需从NoSQL中读出即可，性能就会比数据库快得多。对于那些高并发的操作，可以在NoSQL上先完成写入，等待某一个时刻再批量写入数据库，这样就能满足系统的性能要求了。

# Redis特点

* 响应快速

* 支持5种数据类型（字符串、哈希结构、列表、集合、可排序集合）

* 操作都是原子的

  所有Redis的操作都是原子的，从而确保当两个客户同时访问Redis服务器时，得到的是更新后的值（最新值）。在需要高并发的场合可以考虑使用Redis的事务，处理一些需要锁的业务。

* MultiUtility工具

  Redis可以在如缓存、消息传递队列中使用（Redis支持“发布+订阅“的消息模式），在应用程序如Web应用程序会话、网站页面点击数等任何短暂的数据中使用。

​	一方面，使用NoSQL从数据库中读取数据进行缓存，就可以从内存中读取数据了，而不像数据库一样读磁盘。现实是读操作远比写操作要多得多，所以缓存很多常用的数据，提高其命中率有助于整体性能的提高，并且能减缓数据库的压力，对互联网系统架构是十分有利的。
​        另一方面，它也能满足互联网高并发需要高速处理数据的场合，比如抢红包、商品秒杀等场景，这些场合需要高速处理，并保证并发数据安全和一致性；在一些需要高速运算的场合中，也可以先用它来完成运算，再把数据批量存入数据库，这样便能极大地提升互联网系统的性能和响应能力。



# 连接Redis

* 开启Redis

  redis-server.exe

* 连接入Redis

  redis-cli -h 127.0.0.1 -p 6379

# 数据类型

## 通用的操作

keys * :查看所有的key
del key:删除指定的key
exists key:判断一个key是否存在
rename oldkey newkey:重命名
expire key 秒数:
ttl key :查看一个key剩余存活时间
	-1:持久存活
	-2:不存在
type 判断一个可以属于什么类型

## String

### 赋值
格式: set key value
例如:set username tom
### 取值
格式: get key
例如: get username

### 先获取再设置
格式:getset key value
例如: getset username jack	

### 删除
格式:del key
例如: del d

### 数字类型

自增和自减
incr key   ： ++			
decr key  ： --

增加或减少指定的数量
incrby key int
decrby key int

拼接字符串
append key value

## list

### 赋值
左边:lpush key value value2 value3
右边:rpush key value value2 value3
### 取值
左边:lpop key  
右边:rpush key

### 获取所有元素

lrange 0 -1

### 获取元素的个数

llen key
### 扩展
lpushx key value :若有则添加 若没有则不添加
rpushx key value :若有则添加 若没有则不添加
lrem key count value:从左边移除count个value
	若count>0 :从左边移除count个value
	若count<0 :从右边移除count个value
	若count=0 :从右边移除所有的value
lset key index value
	设置链表中指定索引的元素值  0 代表是第一个 -1代表的是最后一个

## hash
### 存值
存入一个值
hset key subkey subvalue
存入多个值
hmset key subkey1 subvalue1 subkey2 subvalue2	
### 获取
获取一个值
hget key subkey
获取多个值
hmget key subkey1 subkey2
### 移除值
hdel key subkey subkey
给一个key添加指定的数字
hincrby key subkey int

## set
### 添加
sadd key value1 valuse2
### 删除
srem key value1 valuse2
### 获取
smembers key 
### 判断是否是是set中的一员
sismember key value

### 运算

* 差集: sdiff s1 s2

* 交集: sinter s1 s2 

* 并集: sunion s3 s4​	

### 获取数量
scard key

srandmember key:随机获取一个

## sortedSet

### 添加元素
zadd key score m1 score m2
### 获取元素
zscore key m:获取指定成员的得分
zcard key:获取key的长度	
### 删除元素
zrem



