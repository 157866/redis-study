# Redis

前言：

> 与传统数据关系（MySQL）
>
> ​	Redis是key-value数据库（NoSQL一种），MySQL是关系数据库
>
> ​	Redis数据库操作主要在内存，而MySQL主要存储在磁盘
>
> ​	Redis在某一些场景使用中要明显优于MySQL，比如计数器和排行榜



本次Redis版本：7.0.11

本次Linux版本： centOS 7

  ([Download | Redis](https://redis.io/download/#redis-downloads))

[Redis中文学习网 - Redis开发与运维技术、Redis教程、使用手册](https://www.redis.com.cn/)

[Redis 命令参考 — Redis 命令参考 (redisfans.com)](http://doc.redisfans.com/)

[Redis测试](https://try.redis.io/)



redis版本命名规则：

版本第二位是奇数则为非稳定版本如2.7，2.9

版本第二位为偶数则为稳定版本如：2.6，2.8，3.0



## 安装redis



- 查看Linux版本

```
[root@hadoop100 ~]# getconf LONG_BIT
64

```



- Linux安装redis必须先具备gcc编译环境

  - 什么是gcc

   ```
  gcc是linux下的-一个编译程序，是C程序的编译工具。
  GCC(GNU Compiler Collection)是GNU(GNU's Not Unix)计划提供的编译器家族，它能够支持C, C++, Objective-C, Fortran, Java和
  Ada等等程序设计语言前端，同时能够运行在x86, x86-64, IA-64, PowerPC, SPARC和Alpha等等几乎目前所有的硬件平台上。鉴于这些特征，
  以及GCC编译代码的高效性，使得GCC成为绝大多数自由软件开发编译的首选工具。虽然对于程序员们来说，编译器只是-一个工具，除了开发和维
  护人员，很少有人关注编译器的发展，但是GCC的影响力是如此之大，它的性能提升甚至有望改善所有的自由软件的运行效率，同时它的内部结构的
  变化也体现出现代编译器发展的新特征。
  
   ```

- 实际操作

  - 先查看gcc是否安装   gcc -v
    - 查看版本4.8.5以上就可以了（包括4.8.5）

    ```
  [root@hadoop100 ~]# gcc -v
  gcc 版本 4.8.5 20150623 (Red Hat 4.8.5-44) (GCC)
    ```

  - 如果没有gcc 环境安装

    - yum -y install gcc-c++

    ```
    [root@hadoop100 ~]# yum -y install gcc-c++
    ```

  - 查看是否有安装了redis

    - 官网建议升级到redis6.0.8 及以上 不然有安全问题

    ```
    [root@hadoop100 ~]# redis-server -v
    
    ```

  - 把官网下的redis放在Linux中/opt目录下面

    ```
    [root@hadoop100 opt]# ls
    containerd redis-7.0.11.tar.gz  rh
    ```

  - 解压文件

    ```
    [root@hadoop100 opt]# tar -zxvf redis-7.0.11.tar.gz 
    
    [root@hadoop100 opt]# ls
    containerd  redis-7.0.11  redis-7.0.11.tar.gz  rh
    
    ```

  - 进入文件

  ```
  [root@hadoop100 opt]# cd redis-7.0.11/
  [root@hadoop100 redis-7.0.11]# 
  ```

  - 安装redis
    - 默认安装到:/usr/local/bin

  ```
  [root@hadoop100 redis-7.0.11]# make && make install
  Hint: It's a good idea to run 'make test' ;)
  
  ```

  - 查看安装 进入/usr/local/bin
    - cd /usr/local/bin

  ```
  [root@hadoop100 bin]# ll -s
  总用量 21552
   5084 -rwxr-xr-x. 1 root root  5205152 4月  30 10:47 redis-benchmark
      0 lrwxrwxrwx. 1 root root       12 4月  30 10:47 redis-check-aof -> redis-server
      0 lrwxrwxrwx. 1 root root       12 4月  30 10:47 redis-check-rdb -> redis-server
   5296 -rwxr-xr-x. 1 root root  5422568 4月  30 10:47 redis-cli
      0 lrwxrwxrwx. 1 root root       12 4月  30 10:47 redis-sentinel -> redis-server
  11172 -rwxr-xr-x. 1 root root 11436944 4月  30 10:47 redis-server
  
  ```

  >   redis-benchmark              性能测试工具，服务器启动后运行该命令
  >
  >   redis-check-aof                 修复有问题的AOF文件，rdb和aof后面讲
  >
  >   redis-check-rdb                修复有问题的RDB文件
  >
  >   redis-cli                              客户端，操作入口
  >
  >   redis-sentinel                    redis集群使用
  >
  >   redis-server                       redis服务器启动命令



#### 配置reids配置文件

- 创建配置文件备份

```
#进入安装位置
[root@hadoop100 redis-7.0.11]# pwd
/opt/redis-7.0.11
#查看配置文件
[root@hadoop100 redis-7.0.11]# ll
-rw-rw-r--.  1 root root 106545 4月  17 20:54 redis.conf
#创建文件进行备份
[root@hadoop100 redis-7.0.11]# mkdir myconf
#复制
[root@hadoop100 redis-7.0.11]# cp redis.conf myconf/myredis7.conf

```

- 配置 myredis7.conf 

```
[root@hadoop100 myconf]# vim myredis7.conf 

#配置如下
daemonize no                          改成   yes                         成为后台运行

protected-mode yes                    改成    no                          保护模式

bind 127.0.0.1 -::1                   改成 注释                         注释掉不然会影响远程ip连接

requirepass 123456                    设置密码


```

- 运行redis

  ```
  [root@hadoop100 myconf]# redis-server /opt/redis-7.0.11/myconf/myredis7.conf 
  ```

  -  查看启动

  ```
  [root@hadoop100 myconf]# ps -ef | grep redis
  polkitd   19724  19703  0 4月29 ?       00:01:19 redis-server *:6379
  root      19847  19839  0 4月29 pts/0   00:00:00 redis-cli
  root      37596  32133  0 11:33 pts/1    00:00:00 grep --color=auto redis
  
  ```

- 进行连接

  - -a                     auth  密码认证

```
[root@hadoop100 myconf]# redis-cli -a 123456  -p 6379
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379> 
#检测是否成功
127.0.0.1:6379> ping
PONG

```

- 退出客户端
  - quit



- 关闭客户端
  - 如果在客户端里面
    -  SHUTDOWN
  - 如果下远程关闭
    - redis-cli -a 123456 shutdown

```
127.0.0.1:6379> SHUTDOWN
not connected> quit

```

- 卸载redis

```
ls -l /usr/local/bin/redis-*
rm -rf /usr/local/bin/redis-*
```



## Redis十大数据类型



> string

string是redis最基本的类型，- -个key对应- -个value。

string类型是二进制安全的，意思是redis的string可以包含任何数据，比如jpg图片或者序列化的对象。

string类型是Redis最基本的数据类型，一 个redis中字符串value最多可以是512M

二进制安全表示可以序列化



> list

Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部(左边)或者尾部(右边)

它的底层实际是个双端链表，最多可以包含2^32 - 1个元素(4294967295,每个列表超过40亿个元素)



> hash

Redis hash是一个string类型的field (字段) 和value (值) 的映射表，hash特别适合用于存储对象。

Redis中每个hash可以存储2^32 - 1键值对(40多亿)



> set

Redis的Set是String类型的无序集合。集合成员是唯一的， 这就意味着集合中不能出现重复的数据，集合对象的编码可以是intset或者hashtable.

Redis中Set集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是0(1)。

集合中最大的成员数为2^32 - 1 (4294967295,每个集合可存储40多亿个成员)



> sorted set

zset(sorted set: 有序集合)

Rediszset和set一样也是string类型元素的集合,且不允许重复的成员。

不同的是每个元素都会关联一个double类型的分数，redis正是通过分数来为集合中的成员进行从小到大的排序。

zset的成员是唯一 的,但分数(score)却可以重复。

zset集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是0(1)。 集合中最大的成员数为2^32 - 1

k1 score v1 



> GEO

Redis GEO主要用于存储地理位置信息，并对存储的信息进行操作，包括

添加地理位置的坐标

获取地理位置的坐标

计算两个位置之间的距离

根据用户给定的经纬度坐标来获取指定范围内的地理位置集合



> HyperLogLog

HyperLogLog是用来做**基数统计**的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定且是很小的。

在Redis里面，每个HyperLogLog键只需要花费12 KB内存，就可以计算接近2^64个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为HyperLogLog只会根据输入元素来计算基数，而不会储存输入元素本身，所以HyperLogLog不能像集合那样，返回输入的各个元素。

**基数表示不重复的**



> bitmap

![image-20230504142653307](..\docker_study\imgs\image-20230504142653307.png)





> bitfield

通过bitfield命令可以**一次性操作多个比特位域(指的是连续的多个比特位)**，它会执行一系列操作 并返回- -个响应数组，这个数组中的元

素对应参数列表中的相应操作的执行结果。

说白了就是通过bitfield命令我们可以一次性对多个比特位域进行操作。



> stream

redis 5.0之后的

Redis Stream主要用于消息队列( MQ，Megssage Queue)，Redis 本身是有一一个Redis发布订阅(pub/sub)来实现消息队列的功能，但它有个

缺点就是消息无法持久化，如果出现网络断开、Redis 宕机等，消息就会被丢弃。

简单来说发布订阅(pub/sub)可以分发消息，但无法记录历史消息。

而Redis Stream提供了消息的持久化和主备复制功能，可以让任何客户端访问任何时刻的数据，并且能记住每-一个 客户端的访问位置，还能保证消息不丢失





## Redis 命令

**命令不区分大小写  ，key值区分大小写**



> 帮助命令

help @类型

### key



- `keys *`
  - 基本语法

    - keys *                                                         查看所有的key

    - 实际操作

      ```
      127.0.0.1:6379> keys *
      1) "user:1"
      ```

      

- `exists`

  - 基本语法

    - exists  key                                                   查看是否存在

    - 实际操作

      ```
      127.0.0.1:6379> EXISTS user:1
      (integer) 1
      ```

      

- `del`

  - 基本语法

    - del key                                                         删除key         原子	

    - 实际操作 

      ```
      127.0.0.1:6379> del user:1
      (integer) 1
      127.0.0.1:6379> keys *
      (empty array)
      
      ```

      

- `unlink`

  - 基本语法

    - unlink  key                                               非阻塞删除

    - 实际操作

      ```
       127.0.0.1:6379> unlink user:1
      (integer) 1
      127.0.0.1:6379> keys *
      (empty array)
      ```

      

- `ttl`

  - 基本操作

    - ttl key                                                查看还有多少秒过期， -1永不过期  -2已经过期

    - 实际操作

      ```
      127.0.0.1:6379> ttl k1
      (integer) -1
      ```

      

- `expire`

  - 基本语法

    - expire key  秒 

    - 实际操作

      ```
      127.0.0.1:6379> EXPIRE k2 7
      (integer) 1
      127.0.0.1:6379> ttl k2
      (integer) 4
      127.0.0.1:6379> ttl k2
      (integer) 0
      127.0.0.1:6379> ttl k2
      (integer) -2
      
      ```

      

- `move`

  - 基本语法

    - move  key  db                                            移动key值到其它数据库       db默认redis有【0-15】 个数据库

    - 实际操作

      ```
      127.0.0.1:6379> move k1 2
      (integer) 1
      127.0.0.1:6379> keys *
      (empty array)
      127.0.0.1:6379> select 2
      OK
      127.0.0.1:6379[2]> keys *
      1) "k1"
      ```

      

- `select`

  - 基本语法

    - select dbID                                                    使用第几个数据库   

    - 实际操作

      ```
      127.0.0.1:6379> select 2
      OK
      127.0.0.1:6379[2]> keys *
      1) "k1"
      ```

      

- `dbsize`

  - 基本语法

    - dbsize                                                        查看数据库有几个key

    - 实际操作

      ```
      127.0.0.1:6379[15]> DBSIZE
      (integer) 0
      127.0.0.1:6379[15]> set k1 v1
      OK
      127.0.0.1:6379[15]> DBSIZE
      (integer) 1
      
      ```

      



- `flushdb`
  - 基本语法
    - flushdb                                                         清空当前库





- `flushall`
  - 基本语法
    - flushall                                                          通杀全部库







### String




SET key value [NX | XX] [GET] [EX seconds | PX milliseconds | EXAT unix-time-seconds | PXAT unix-time-milliseconds | KEEPTTL]

#### 基本操作



**options**

The `SET` command supports a set of options that modify its behavior:

- `EX` *seconds* -- Set the specified expire time, in seconds.
- `PX` *milliseconds* -- Set the specified expire time, in milliseconds.
- `EXAT` *timestamp-seconds* -- Set the specified Unix time at which the key will expire, in seconds.
- `PXAT` *timestamp-milliseconds* -- Set the specified Unix time at which the key will expire, in milliseconds.
- `NX` -- Only set the key if it does not already exist.
- `XX` -- Only set the key if it already exist.
- `KEEPTTL` -- Retain the time to live associated with the key.
- `GET` -- Return the old string stored at key, or nil if key did not exist. An error is returned and `SET` aborted if the value stored at key is not a string.





##### `ex`

  - 基本语法

    - set key   value  ex   number                             设置过期时间  单位为秒

    - 实际操作

      ```
      127.0.0.1:6379> set k1 v1 ex 10
      OK
      127.0.0.1:6379> TTL k1
      (integer) 2
      127.0.0.1:6379> TTL k1
      (integer) -2
      ```



##### `px`

  - 基本语法

    - set key value px  number                                  设置过期时间   单位为毫秒    

    - 实际操作

      ```
      127.0.0.1:6379> set k1 v1 px 10000
      OK
      127.0.0.1:6379> ttl k1
      (integer) 6
      127.0.0.1:6379> ttl k1
      (integer) -2
      127.0.0.1:6379> 
      ```

      

##### `exat`

  - 基本语法

    - set key value exat unix时间戳                                                     单位秒                     

    - 实际操作

      - 从Java中获取时间戳

      ```Java
        System.out.println(Long.toString(System.currentTimeMillis()/1000L));
      ```

      - redis中操作

      ```
      127.0.0.1:6379> set k2 v2 exat 1683197431
      OK
      127.0.0.1:6379> TTL k2
      (integer) 9794
      ```

      

##### `pxat`

  - 基本语法

    - set key value pxat unix时间                                                      单位毫秒

    - 实际操作

      ```
      127.0.0.1:6379> set k2 v2 pxat 1683290696852
      OK
      127.0.0.1:6379> ttl k2
      (integer) 90949
      
      ```



##### `nx`

  - 基本语法

    - set key value nx                                 没有则创建 有则返回为nil

    - 实际操作

      ```
      127.0.0.1:6379> set k1 v1 nx
      OK
      127.0.0.1:6379> set k1 v1 nx
      (nil)
      
      ```



##### `xx`

  - 基本语法

    - set key value xx                                 有则继续创建  没有则为nil      

    - 实际操作

      ```
      127.0.0.1:6379> set k1 v1xx xx
      OK
      127.0.0.1:6379> get k1
      "v1xx"
      
      127.0.0.1:6379> keys *
      (empty array)
      127.0.0.1:6379> set k1 v1 xx
      (nil)
      
      
      ```



##### `keepttl`

  - 基本语法

    - set key  value   keepttl

    - 实际操作

      ```redis
      #最开始操作 会覆盖掉之前设置的时间
      127.0.0.1:6379> set k1 v1 ex 30
      OK
      127.0.0.1:6379> ttl k1
      (integer) 26
      127.0.0.1:6379> set k1 v2
      OK
      127.0.0.1:6379> ttl k1
      (integer) -1
      
      #会继续保存之前的时间
      127.0.0.1:6379> set k1 v1 ex 30
      OK
      127.0.0.1:6379> ttl k1
      (integer) 27
      127.0.0.1:6379> set k1 v1keepttl keepttl
      OK
      127.0.0.1:6379> ttl k1
      (integer) 8
      ```

      

#### mset/mget

- `mset和mget`

  - 基本语法 

    - mset / mget  

    - 实际操作

      ```
      127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3
      OK
      
      127.0.0.1:6379> mget k1 k2 k3
      1) "v1"
      2) "v2"
      3) "v3"
      
      ```



- `msetnx`

  - 基础语法

  - msetnx  key value   key value                                                      如果是一个整体如果有一个已经有了则全部创建失败

    - 实际操作

      ```
      127.0.0.1:6379> keys *
      1) "k2"
      2) "k3"
      3) "k1"
      127.0.0.1:6379> MSETNX k1 v1 k4 v4
      (integer) 0
      127.0.0.1:6379> keys *
      1) "k2"
      2) "k3"
      3) "k1"
      
      ```



#### getrange/setrange

- `getrange`

  - 基础语法

    - getrange key  start  end                                    start 从0开始下标     -1代表到末尾

    - 实际操作

      ```
      127.0.0.1:6379> set k1 abcdefg12345678
      OK
      127.0.0.1:6379> GETRANGE k1 0 -1
      "abcdefg12345678"
      127.0.0.1:6379> GETRANGE k1 0 3
      "abcd"
      127.0.0.1:6379> GETRANGE k1 0 7
      "abcdefg1"
      
      ```

      

- `setrange`

  - 基础语法

    - setrange  key  offset  value

    - 实际操作

      ```
      127.0.0.1:6379> SETRANGE k1 1 xxxx
      (integer) 15
      127.0.0.1:6379> get k1
      "axxxxfg12345678"
      
      ```

#### 自增 



- `incr`

  - 基本语法

    - incr key                                                                 必须为数字       increase增大

    - 实际操作

      ```
      127.0.0.1:6379> set k2 100
      OK
      127.0.0.1:6379> get k2
      "100"
      127.0.0.1:6379> INCR k2
      (integer) 101
      127.0.0.1:6379> INCR k2
      (integer) 102
      
      ```

- `incrby`

  - 基本语法

    - incrby key  increment                                          定量增加           increment定量的增长

    - 实际操作

      ```
      127.0.0.1:6379> INCRBY k2 3
      (integer) 108
      127.0.0.1:6379> INCRBY k2 3
      (integer) 111
      127.0.0.1:6379> INCRBY k2 3
      (integer) 114
      
      ```



#### 自减

- `decr`

  - 基本语法

    - decr key                                                                value必须为数字  自减一

    - 实际操作

      ```
      127.0.0.1:6379> decr k2
      (integer) 113
      127.0.0.1:6379> decr k2
      (integer) 112
      127.0.0.1:6379> decr k2
      (integer) 111
      
      ```



- `decrby`

  - 基础语法

    - decrby key decrement                                         定量减少         decrement定量的减少

    - 实际操作

      ```
      127.0.0.1:6379> DECRBY k2 3
      (integer) 108
      127.0.0.1:6379> DECRBY k2 3
      (integer) 105
      ```




#### 获取字符串长度内容追加

- `STRLEN`

  - 基础语法

    -  STRLEN key                                                        获取key的长度

    - 实际操作

      ```
      127.0.0.1:6379> STRLEN k1 
      (integer) 15
      ```



- `APPEND`
  - 基础语法
    - APPEND		key             values                          追加在key中的value值里面

		```
127.0.0.1:6379> APPEND k1 sadawds
(integer) 22
127.0.0.1:6379> get k1
"axxxxfg12345678 sadawds"
    ```
```





#### 分布式锁

- `setnx`

  - 基础语法

    - setnx key value                                                无则创建有则为空

    - 实际操作

```
      #setnx  就是 set key values nx   的简写
      127.0.0.1:6379> set k3 v3 nx
      OK
      127.0.0.1:6379> set k3 v3 nx
      (nil)
      127.0.0.1:6379> setnx k4 v4 
      (integer) 1
      127.0.0.1:6379> setnx k4 v4 
      (integer) 0
      
      ```


​      

- `setex`

  - 基础语法

    - setex  key  seconds  value                                         创建key并且设计时间

    - 实际操作

      ```
      #相当于之前的： 
      # set  key  value    
      # expire   key   secodes
      
      127.0.0.1:6379> setex k5 5 v5
      OK
      127.0.0.1:6379> ttl k5
      (integer) 2
      
      ```

      

#### getset

> 先get再set

一个双端链表的结构，容量是2的32次方减1个元素，大概40多亿，主要功能有push/pop等，一 般用在栈、队列、消息队列等场景。
left、right都可 以插入添加;

如果键不存在，创建新的链表;

如果键已存在，新增内容;

如果值全移除，对应的键也就消失了。

它的底层实际是 个双向链表,对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差。



- `getset`

  - 基础语法

    - getset   key values                                           先获取之前的值然后再覆盖之前的值

    - 实际操作

      ```
      #如果key为空则返回nil
      127.0.0.1:6379> get k1
      "axxxxfg12345678sadawds"
      127.0.0.1:6379> getset k1 hhhh
      "axxxxfg12345678sadawds"
      127.0.0.1:6379> get k1
      "hhhh"
      #另一个操作
      127.0.0.1:6379> set k1 wmt get 
      "hhhh"
      127.0.0.1:6379> get k1
      "wmt"
      ```

      



### list

> 单key 多values

 **Limits**

The max length of a Redis list is 2^32 - 1 (4,294,967,295) elements.



 #### **Basic commands**

- [`LPUSH`](https://redis.io/commands/lpush) adds a new element to the head of a list; [`RPUSH`](https://redis.io/commands/rpush) adds to the tail.
- [`LPOP`](https://redis.io/commands/lpop) removes and returns an element from the head of a list; [`RPOP`](https://redis.io/commands/rpop) does the same but from the tails of a list.
- [`LLEN`](https://redis.io/commands/llen) returns the length of a list.
- [`LMOVE`](https://redis.io/commands/lmove) atomically moves elements from one list to another.
- [`LTRIM`](https://redis.io/commands/ltrim) reduces a list to the specified range of elements.





####  **Blocking commands**

Lists support several blocking commands. For example:

- [`BLPOP`](https://redis.io/commands/blpop) removes and returns an element from the head of a list. If the list is empty, the command blocks until an element becomes available or until the specified timeout is reached.
- [`BLMOVE`](https://redis.io/commands/blmove) atomically moves elements from a source list to a target list. If the source list is empty, the command will block until a new element becomes available.

See the [complete series of list commands](https://redis.io/commands/?group=list).



#### 基础操作



#####  `lpush`

  - 基础语法

    - lpush key value value...                                                 创建一个list从左边进入

    - 实际操作

      ```
      127.0.0.1:6379> lpush list1 1 2 3 4 5
      (integer) 5
      ```

      

#####  `rpush`

  - 基础语法

    - rpush key value value...                                                 创建一个list从右边进入

    - 实际操作

      ```
      127.0.0.1:6379> rpush list2 11 22 33 44 55
      (integer) 5
      ```







##### `lrange`

  - 基础语法

    - lrange  key   start    end                                                       遍历集合    -1表示遍历到最后一个

    - 实际操作

      ```
      127.0.0.1:6379> LRANGE list1 0 -1
      1) "5"
      2) "4"
      3) "3"
      4) "2"
      5) "1"
      127.0.0.1:6379> LRANGE list2 0 -1
      1) "11"
      2) "22"
      3) "33"
      4) "44"
      5) "55"
      
      ```



##### ` lpop` 

  - 基础语法

    - lpop  key                                                     移除上面的一个元素           

    - 实际操作

      ```
      127.0.0.1:6379> lrange list1 0 -1
      1) "5"
      2) "4"
      3) "3"
      4) "2"
      5) "1"
      127.0.0.1:6379> lpop list1
      "5"
      127.0.0.1:6379> lrange list1 0 -1
      1) "4"
      2) "3"
      3) "2"
      4) "1"
      
      ```



##### `rpop`

  - 基础语法

    - rpop   key                                             移除下方的一个元素

    - 实际操作

      ```
      127.0.0.1:6379> lrange list1 0 -1
      1) "4"
      2) "3"
      3) "2"
      4) "1"
      127.0.0.1:6379> rpop list1
      "1"
      127.0.0.1:6379> lrange list1 0 -1
      1) "4"
      2) "3"
      3) "2"
      ```



##### `lindex`

- 基础语法

  - lindex  key  index                                            通过下标获取元素

  - 实际操作

    ```
    127.0.0.1:6379> lindex list1 0
    "4"
    ```

    

  

##### `llen`

- 基础语法

  - llen key                                                             获取list的长度

  - 实际操作

    ```
    127.0.0.1:6379> llen list1
    (integer) 3
    ```

    

##### `lrem`

- 基础语法

  - lrem  key   count  element                               去重    

  - 实际操作

    ```
    127.0.0.1:6379> lpush list1 1 1 1 1 1 1 1 2 2 2 2 2 3 3 3
    (integer) 15
    127.0.0.1:6379> lrange list1 0 -1
     1) "3"
     2) "3"
     3) "3"
     4) "2"
     5) "2"
     6) "2"
     7) "2"
     8) "2"
     9) "1"
    10) "1"
    11) "1"
    12) "1"
    13) "1"
    14) "1"
    15) "1"
    
    127.0.0.1:6379> lrem list1 6 1
    (integer) 6
    127.0.0.1:6379> lrange list1 0 -1
    1) "3"
    2) "3"
    3) "3"
    4) "2"
    5) "2"
    6) "2"
    7) "2"
    8) "2"
    9) "1"
    
    ```

    

##### `ltrim`

- 基础语法

  - ltrim key  startIndex    endIndex                                 截取元素并返回给原来的key

  - 实际操作

    ```
    127.0.0.1:6379> lrange list1 0 -1
    1) "3"
    2) "3"
    3) "3"
    4) "2"
    5) "2"
    6) "2"
    7) "2"
    8) "2"
    9) "1"
    127.0.0.1:6379> ltrim list1 2 5
    OK
    127.0.0.1:6379> lrange list1 0 -1
    1) "3"
    2) "2"
    3) "2"
    4) "2"
    ```

    
##### `rpoplpush`

- 基础语法

  - rpoplpush  source  destination                                 从集合最下面的一个元素转移到目标集合中去

  - 实际操作

    ```
    127.0.0.1:6379> lrange list1 0 -1
    1) "3"
    2) "2"
    3) "2"
    4) "2"
    127.0.0.1:6379> lrange list2 0 -1
    1) "11"
    2) "22"
    3) "33"
    4) "44"
    5) "55"
    127.0.0.1:6379> rpoplpush list1 list2
    "2"
    127.0.0.1:6379> lrange list1 0 -1
    1) "3"
    2) "2"
    3) "2"
    127.0.0.1:6379> lrange list2 0 -1
    1) "2"
    2) "11"
    3) "22"
    4) "33"
    5) "44"
    6) "55"
    
    ```




##### `lset`

- 基础语法

  - lset key  index  element                             通过下表来设置值

  - 实际操作

    ```
    127.0.0.1:6379> lrange list1 0 -1
    1) "3"
    2) "2"
    3) "2"
    127.0.0.1:6379> lset list1 1 redis
    OK
    127.0.0.1:6379> lrange list1 0 -1
    1) "3"
    2) "redis"
    3) "2"
    
    ```







##### `linsert`

- 基础语法

  - linsert  key  before/after   pivot    element                            通过已经知晓的元素在它之前或者之后插入

  - 实际操作

    ```
    127.0.0.1:6379> lrange list1 0 -1
    1) "3"
    2) "redis"
    3) "2"
    127.0.0.1:6379> linsert list1 before redis mysql
    (integer) 4
    127.0.0.1:6379> lrange list1 0 -1
    1) "3"
    2) "mysql"
    3) "redis"
    4) "2"
    ```



##### 实际使用在

微信公众号订阅





### hash



> hash 相当于 Map<string , map<object, object>>



#### Basic commands

- [`HSET`](https://redis.io/commands/hset) sets the value of one or more fields on a hash.
- [`HGET`](https://redis.io/commands/hget) returns the value at a given field.
- [`HMGET`](https://redis.io/commands/hmget) returns the values at one or more given fields.
- [`HINCRBY`](https://redis.io/commands/hincrby) increments the value at a given field by the integer provided.

See the [complete list of hash commands](https://redis.io/commands/?group=hash).





#### 基础操作



##### `hset`

- 基础命令

  - hset key  field value  [field value ....]                       创建hash

  - 实际操作

    ```
    127.0.0.1:6379> hset wmt age 18 sex man length 175
    (integer) 3
    127.0.0.1:6379> hget wmt age
    "18"
    127.0.0.1:6379> hget wmt man
    (nil)
    127.0.0.1:6379> hget wmt sex
    "man"
    
    ```





##### `hget`

- 基础命令

  - hget key  field                                                               获取hash里面的元素        

  - 实际操作

    ```
    127.0.0.1:6379> hset wmt age 18 sex man length 175
    (integer) 3
    127.0.0.1:6379> hget wmt age
    "18"
    127.0.0.1:6379> hget wmt man
    (nil)
    127.0.0.1:6379> hget wmt sex
    "man"
    ```



##### `hmget`

- 基础命令

  - hmget  key   field  [field....]                                        获取多个元素

  - 实际操作

    ```
    127.0.0.1:6379> hmget wmt age sex length
    1) "18"
    2) "man"
    3) "175"
    ```



##### `hgetall`

- 基础命令

  - hgetall key                                                                    获取hash里面的全部值

  - 实际操作

    ```
    127.0.0.1:6379> hgetall wmt
    1) "age"
    2) "18"
    3) "sex"
    4) "man"
    5) "length"
    6) "175"
    ```

    

##### `hdel`

- 基础命令

  - hdel    key    field                                                  删除指定字段

  - 实际操作

    ```
    127.0.0.1:6379> hdel wmt age
    (integer) 1
    ```



##### `hlen`

- 基础命令

  - hlen  key                                                              获取字段的长度

  - 实际操作

    ```
    127.0.0.1:6379> hlen wmt
    (integer) 2
    
    ```



##### `hexists`

- 基础命令

  - hexists   key   field                                                 判断hash里面是否有这个字段

  - 实际操作

    ```
    127.0.0.1:6379> hexists wmt age
    (integer) 0
    127.0.0.1:6379> hexists wmt sex
    (integer) 1
    ```



##### `hkeys`

- 基础命令

  - hkeys  key                                                     获取全部的key值

  - 实际操作

    ```
    127.0.0.1:6379> hkeys wmt
    1) "sex"
    2) "length"
    
    
    ```



##### `hvals`

- 基础命令

  - hvals    key                                                        获取全部的valus 

  - 实际操作

    ```
    127.0.0.1:6379> hvals wmt
    1) "man"
    2) "175"
    ```



##### `hincrby`

- 基础命令

  - hincby  key   field   increment                       增加指定的元素

  - 实际操作

    ```
    127.0.0.1:6379> HINCRBY wmt length 2
    (integer) 177
    ```



##### `hincrbyfloat`

- 基础命令

  - HINCRBYFLOAT key  field   0.5                       浮点数的增加

  - 实际操作

    ```
    127.0.0.1:6379> hgetall wmt
    1) "sex"
    2) "man"
    3) "length"
    4) "177"
    5) "grade"
    6) "18.5"
    7) "age"
    8) "19"
    127.0.0.1:6379> HINCRBYFLOAT wmt grade 0.5
    "19"
    
    ```



##### `hsetnx`

- 基础命令

  - hsetnx  key  field   element                     不存在则创建存在则不创建

  - 实际操作

    ```
    127.0.0.1:6379> hsetnx wangmengtao name wmt 
    (integer) 1
    127.0.0.1:6379> hsetnx wangmengtao name wmt 
    (integer) 0
    
    ```



##### 使用场景

> 早期购物车 目前中小厂还在使用







### set

>  无序无重复



#### Limits

The max size of a Redis set is 2^32 - 1 (4,294,967,295) members.



#### Basic commands

- [`SADD`](https://redis.io/commands/sadd) adds a new member to a set.
- [`SREM`](https://redis.io/commands/srem) removes the specified member from the set.
- [`SISMEMBER`](https://redis.io/commands/sismember) tests a string for set membership.
- [`SINTER`](https://redis.io/commands/sinter) returns the set of members that two or more sets have in common (i.e., the intersection).
- [`SCARD`](https://redis.io/commands/scard) returns the size (a.k.a. cardinality) of a set.

See the [complete list of set commands](https://redis.io/commands/?group=set).







#### 基础命令



##### `sadd`

- 基础命令

  - sadd  key member [ member....]                                      创建set集合无序无重复

  - 实际操作

    ```
    127.0.0.1:6379> sadd set 1 2 3 1
    (integer) 3
    ```

    

##### `smembers`

- 基础命令

  - smembers key                                                                          遍历set集合

  - 实际操作

    ```
    127.0.0.1:6379> SMEMBERS set
    1) "1"
    2) "2"
    3) "3"
    ```

    

##### `sismember `

- 基础命令

  - sismember kye  member                                                      判断该成员是否存在

  - 实际操作

    ```
    127.0.0.1:6379> sismember set 1
    (integer) 1
    127.0.0.1:6379> sismember set 3
    (integer) 1
    127.0.0.1:6379> sismember set 4
    (integer) 0
    
    ```

    

##### `srem`

- 基础操作

  - srem key member [member...]                                               去除多个成员

  - 实际操作

    ```
    127.0.0.1:6379> srem set 1 2
    (integer) 2
    127.0.0.1:6379> smembers set
    1) "3"
    ```

    

##### `scard`

- 基础操作

  - scard key                                                                                      获取集合里面的元素个数

  - 实际操作

     ```
    127.0.0.1:6379> scard set
    (integer) 1
    ```



##### `srandmember `

- 基础操作

  - srandmember key count                                                       从集合中随机展示设置的数字个数元素，元素不删除

  - 实际操作

    ```
    127.0.0.1:6379> srandmember set 2
    1) "5"
    2) "6"
    127.0.0.1:6379> srandmember set 3
    1) "4"
    2) "5"
    3) "3"
    ```

    

##### `spop`

- 基础操作

  - spop  key  count                                                                       从集合弹出设置的数字个数元素，并且删除元素

  - 实际操作

    ```
    127.0.0.1:6379> spop set 1
    1) "3"
    127.0.0.1:6379> spop set 3
    1) "1"
    2) "12"
    3) "5"
    127.0.0.1:6379> smembers set
    1) "2"
    2) "4"
    3) "6"
    4) "7"
    ```

    

##### `smove`

- 基础操作

  - smove source  destination member                                          把source里面指定的member转移到destination

  - 实际操作

    ```
    127.0.0.1:6379> smembers set
    1) "2"
    2) "4"
    3) "6"
    4) "7"
    127.0.0.1:6379> smembers set1
    1) "c"
    2) "a"
    3) "b"
    127.0.0.1:6379> smove set set1 2 
    (integer) 1
    127.0.0.1:6379> smembers set1
    1) "c"
    2) "a"
    3) "2"
    4) "b"
    ```

    



#### 集合运算

> a                   1 2 3 l p
>
> b                   a c x 1 2



##### `sdiff`

>  集合的差集运算A-B

- 基础操作

  - sdiff    key    key                                               第一个key和第二个key相对比取出第一个特有的key

  - 实际操作

    ```
    127.0.0.1:6379> sadd a 1 2 3 l p
    (integer) 5
    127.0.0.1:6379> sadd b a c x 1 2
    (integer) 5
    
    127.0.0.1:6379> sdiff a b 
    1) "l"
    2) "p"
    3) "3"
    
    127.0.0.1:6379> sdiff b a 
    1) "c"
    2) "x"
    3) "a"
    ```

    

##### `sunion`

> 集合的并集运算 A ∪ B

- 基础操作

  - sunion    key    key                                                              去掉2个set重复的部分并且合并起来合并起来

  - 实际操作

    ```
    127.0.0.1:6379> sunion a b
    1) "c"
    2) "1"
    3) "x"
    4) "a"
    5) "p"
    6) "2"
    7) "l"
    8) "3"
    ```

    

##### `sinter`

> 集合的交集运算 A ∩ B

- 基础操作

  - sinter    key     key                                                                把2个set共同的部分提取出来

  - 实际操作

    ```
    127.0.0.1:6379> sinter a b
    1) "1"
    2) "2"
    ```



##### `sintercard`

> Redis 7 的新特性

- 基础操作

  - sintercard  numkeys   key   [key....] [LIMIT  limit]               计算集合A 和 B 的交集个数                 

  - 实际操作

    ```
    127.0.0.1:6379> sintercard 2 a b
    (integer) 2
    ```

    



#### 实际使用场景

> 抽奖 spop
>
> 微信推荐好友   sdiff
>
> 微信点赞  sadd







### sorted set

> 有序集合
>
> set        k1    v1  v2   v3
>
> zset        k1  score v1   score v2   score v3

#### Basic commands

- [`ZADD`](https://redis.io/commands/zadd) adds a new member and associated score to a sorted set. If the member already exists, the score is updated.
- [`ZRANGE`](https://redis.io/commands/zrange) returns members of a sorted set, sorted within a given range.
- [`ZRANK`](https://redis.io/commands/zrank) returns the rank of the provided member, assuming the sorted is in ascending order.
- [`ZREVRANK`](https://redis.io/commands/zrevrank) returns the rank of the provided member, assuming the sorted set is in descending order.

See the [complete list of sorted set commands](https://redis.io/commands/?group=sorted-set).Basic commands



#### 基础操作



##### `zadd`

- 基础操作

  - ZADD key [NX | XX] [GT | LT] [CH] [INCR] score member [score member...]

  - 实际操作

    ```
    127.0.0.1:6379> zadd zset 10 v1 30 v3 20 v4 59 v5 60 v6
    (integer) 5
    ```

  - 选项

  - **XX**: Only update elements that already exist. Don't add new elements.
  - **NX**: Only add new elements. Don't update already existing elements.
  - **LT**: Only update existing elements if the new score is **less than** the current score. This flag doesn't prevent adding new elements.
  - **GT**: Only update existing elements if the new score is **greater than** the current score. This flag doesn't prevent adding new elements.
  - **CH**: Modify the return value from the number of new elements added, to the total number of elements changed (CH is an abbreviation of *changed*). Changed elements are **new elements added** and elements already existing for which **the score was updated**. So elements specified in the command line having the same score as they had in the past are not counted. Note: normally the return value of `ZADD` only counts the number of new elements added.
  - **INCR**: When this option is specified `ZADD` acts like [`ZINCRBY`](https://redis.io/commands/zincrby). Only one score-element pair can be specified in this mode.





##### `zrange`

- 基本操作

  - ZRANGE key start stop [BYSCORE | BYLEX] [REV] [LIMIT offset count] [WITHSCORES]                             遍历zset

  - 实际操作

    ```
  127.0.0.1:6379> zrange zset 0 -1
    1) "v1"
    2) "v2"
    3) "v4"
    4) "v3"
    5) "v5"
    6) "v6"
    ```
  



##### `zrevrange`

- 基础操作

  - zrevrange key start stop  withscores                               列表反向输出

  - 实际操作

    ```
    127.0.0.1:6379> zrevrange zset 0 -1 withscores
     1) "v6"
     2) "60"
     3) "v5"
     4) "59"
     5) "v3"
     6) "30"
     7) "v4"
     8) "20"
     9) "v2"
    10) "10"
    11) "v1"
    12) "10"
    ```

    



