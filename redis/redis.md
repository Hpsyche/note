# 连接redis

* 进入redis目录

* 启动redis服务

  ./bin/redis-server ./redis.conf

* 可查看redis状态

  ps -ef | grep -i redis

* 连接redis 

  ./bin/redis.cli

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

赋值：

格式：set key value

例如：set username tom

取值：

格式：get key

例如：get username

先获取再设置

格式：getset key value

例如：getset username jack（可先输出tom，再赋值为jack）

### 对于其中的数组类型，自增或自减

incr key  ： ++

decr key ：  --

增加或减少指定的数量

incrby key int

incrby key int

拼接字符串

append key value

## list

赋值：

左边：lpush key value value2 value3

右边：rpush key value value2 value3

取值：

左边：lpop key

右边：rpop key

获取所有元素：

lrange key 0 -1

获取元素的个数：

llen key

扩展：

lpushx key value：若有则添加 若没有则不添加

rpushx key value：若有则添加 若没有则不添加

lrem key count value：从左边移除count个value

若count>0，从左边移除count个value

若count<0，从右边移除count个value

若count=0，从右边移除所有的value

lset key index value

设置链表中指定索引的元素值   ，0代表是第一个，-1代表是最后一个

## hash

存值

存入一个值

hset key subkey subvalue

存入多个值

hmset key subkey1 subvalue1 subkey2 subvalue2

获取:

获取一个值

hget key subkey

获取多个值

hmget key subkey1 subkey2

移除值

hdel key subkey subkey

给一个key添加指定的数字

hincrby key subkey int

## set

添加

sadd key value1 valuse2

删除

srem key value1 valuse2

获取

smembers key

判断是否是是set中的一员

sismember key value

运算

* 差集: sdiff s1 s2

* 交集: sinter s1 s2 

* 并集: sunion s3 s4		

## sortedSet

添加元素

zadd key score m1 score m2

获取元素

zscore key m:获取指定成员的得分

zcard key:获取key的长度	

删除元素

zrem

