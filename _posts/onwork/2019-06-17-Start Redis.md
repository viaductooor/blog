---
title: Start Redis
tags: [sql,redis]
categories: [sql]
layout: post
---

* content
{:toc}

# Tutorial

Tutorial是 <try.redis.io> 中入门内容的总结。

## 开始

Redis使用“键值对”存储数据。Redis中的命令大小写皆可。

使用`SET key value`存储值，使用`GET key`取值。

```redis
> SET server:name "fido"
OK
> GET server:name
"fido"
```

其他一些简单的命令：

命令|含义
---|---
INCR|使这个键对应的值增加1。如果这个键之前不存在，则设置为1（不会报错）。
DEL|删除这个键

使用INCR命令实现自增，而不使用传统的“取值->加一->设定值”，原因是这样可以保证这一操作的原子性，避免多线程读写数据可能会发生的冲突。

```redis
> GET newkey
(nil)
> INCR newkey
(integer) 1
```

Redis中的数据（键值对）可以通过`EXPIRE`设置有效时间和用`TTL`查看剩余时间（单位是秒）。

```redis
> SET resource:lock "demo" 
OK
> EXPIRE resource:lock 120
(integer) 1
> TTL resource:lock
(integer) 109
```

## 列表

Redis支持列表作为值，列表相关的操作有：

命令|含义
---|---
RPUSH|在列表末端添加值
LPUSH|在列表头部添加值
LRANGE|获取子列表，两端包含
LLEN|获取列表长度
LPOP|获取第一个元素并将其从列表中删除
RPOP|获取最后一个元素并将其从列表中删除


需要注意的是：

- 如果之前没有这个键，也是可以直接push的
- 不能通过`GET`操作来获取所有的元素，只能用`LRANGE key 0 -1`

```
> RPUSH students "Alice"
(integer) 1
> RPUSH students "Bob"
(integer) 2
> RPUSH students "Harry"
(integer) 3
> RPUSH students "Ron"
(integer) 4
> GET students
(error) WRONGTYPE Operation against a key holding the wrong kind of value
> LRANGE students 0 -1
1) "Alice"
2) "Bob"
3) "Harry"
4) "Ron"
```

## 集合

Redis支持无序集合作为值，相关的操作有：

操作|含义
---|---
SADD|添加元素
SREM|删除元素
SISMEMBER|查询集合是否包含某元素
SMEMBERS|查看集合中的所有元素
SUNION|求两个集合的并集

```redis
> SADD fruits "apple"
(integer) 1
> SADD fruits "pear"
(integer) 1
> SADD fruits "banana"
(integer) 1
> SREM fruits "apple"
1
> SMEMBERS fruits
1) "banana"
2) "pear"
> SISMEMBER fruits "apple"
(integer) 0
```

Redis中的无序集合可能存在一些问题，所以从1.2版本之后引入了有序集合的概念（“Sorted Sets”）。有序集合添加元素的方式为：`ZADD key score value`，其中key为键，score为分数（用于排序），value为值。

使用`ZRANGE key begin_index end_index`可以查看key对应的集合中第begin_index到第end_index的元素。

```redis
> ZADD scores 89 "math"
(integer) 1
> ZADD scores 100 "nature"
(integer) 1
> ZADD scores 33 "physics"
(integer) 1
> ZADD scores 33 "chemistry"
(integer) 1
> ZRANGE scores 0 -1
1) "chemistry"
2) "physics"
3) "math"
4) "nature"
```

## 哈希（对象）

Redis同样支持哈希数据结构（Hashes）作为值。类似于javascript中的对象类型（或者JSON），哈希结构包含多个键值对，非常适合用于存储对象。

哈希对象的键可以不加双引号，值要加双引号。

哈希结构相关的操作有：

命令|含义
---|---
HSET|设置（或添加）属性
HGETALL|获取所有属性
HMSET|一次设置多个属性
HGET|获取一个哈希对象的某一个属性
HINCRBY|将某一个值增加一个量
HDEL|删除这个对象的某一个属性

```redis
> HSET mycomputer memory "16GB"
(integer) 1
> HSET mycomputer graphic-card "Nvidia"
(integer) 1
> HSET mycomputer hard-disk "1TB Seagate"
(integer) 1
> HGETALL mycomputer
1) "memory"
2) "16GB"
3) "graphic-card"
4) "Nvidia"
5) "hard-disk"
6) "1TB Seagate"
> HGET mycomputer graphic-card
"Nvidia"
```

## 更多

Tutorial of Redis到此为止，更多内容参考：

- [Redis Documentation](http://redis.io/documentation)
- [Command Reference](http://redis.io/commands)
- [Implement a Twitter Clone in Redis](http://redis.io/topics/twitter-clone)
- [Introduction to Redis Data Types](http://redis.io/topics/data-types-intro)
