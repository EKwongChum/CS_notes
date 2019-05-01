# Redis学习

## 类型

Redis支持五种数据类型：string（字符串）、hash（哈希）、list（列表）、set（集合）和 zset（有序集合）

#### String

String 是Redis的基本类型，一个key对应一个value；

二进制安全的，可以包含任何数据，例如jpg图片或者序列化的对象；

给key为name的string设置value为ekwong：

``` shell
SET name "ekwong" // OK
```

获取key为name的string的value值：

```shell
GET name // "ekwong"
```

#### Hash

在Redis中，hash是一个string类型的field和value的映射表；

创建一个key为ek的hash类型：

```shell
HSET ek field1 "Good" field2 "morning" // (integer) 1
```

获取key为ek的hash的field1域的值：

```shell
HGET ek field1 // "Good"
```

#### List

是简单的字符串列表，按插入顺序排序，可以添加一个元素在头部或者尾部；

#### Set

在Redis中，Set是string类型的无序集合，通过哈希表实现，添加新的string元素在某个set中，如果已经存在，则无法重复添加并返回0。

例如建一个language的key

```shell
127.0.0.1:6379> sadd language java
(integer) 1
127.0.0.1:6379> sadd language python
(integer) 1
127.0.0.1:6379> sadd language c++
(integer) 1
127.0.0.1:6379> sadd language c++
(integer) 0
127.0.0.1:6379> sadd language c#
(integer) 1
127.0.0.1:6379> sadd language Rust
(integer) 1
127.0.0.1:6379> sadd language java
(integer) 0
127.0.0.1:6379> smembers language
1) "c++"
2) "python"
3) "java"
4) "Rust"
5) "c#"
```

#### zset

和set一样，是string的集合，无重复元素；

每个元素都会关联一个double类型的分数，Redis正是通过分数来为集合中的成员进行从小到大排序的；

例如：

```shell
127.0.0.1:6379> zadd nl 0 cn
(integer) 1
127.0.0.1:6379> zadd nl 1 us
(integer) 1
127.0.0.1:6379> zadd nl 3 uk
(integer) 1
127.0.0.1:6379> zadd nl 3 es
(integer) 1
127.0.0.1:6379> zadd nl 8 de
(integer) 1
127.0.0.1:6379> zadd nl 18 fr
(integer) 1
127.0.0.1:6379> zadd nl 88 pt
(integer) 1
127.0.0.1:6379> zadd nl 100 it
(integer) 1
127.0.0.1:6379> ZRANGEBYSCORE nl 0 10
1) "cn"
2) "us"
3) "es"
4) "uk"
5) "de"
127.0.0.1:6379> ZRANGEBYSCORE nl 8 88
1) "de"
2) "fr"
3) "pt"
127.0.0.1:6379> ZRANGEBYSCORE nl 0 100
1) "cn"
2) "us"
3) "es"
4) "uk"
5) "de"
6) "fr"
7) "pt"
8) "it"
```

