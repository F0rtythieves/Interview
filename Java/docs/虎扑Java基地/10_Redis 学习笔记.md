# Redis 学习笔记

## 初识 Redis

Redis 是一种基于键值对的 NoSQL 数据库，Redis 中的值可以是由 string、hash、list、set、zset 等多种数据结构和算法组成，因此 Redis 可以满足很多应用场景。Redis 将所有数据都存放在内存中，所以它的读写能力也非常高。Redis 还可以将内存的数据利用快照和日志的形式保存到硬盘上，这样在发生类似断电或者机器故障的时候，内存中的数据不会丢失。除了这些功能，Redis 还提供了键过期、发布订阅、事务、流水线、Lua 等附加功能。

### Redis 的特性

**速度快**

正常情况下，Redis 执行命令的速度非常快，官方给出的数字是读写性能可以达到 10 万/秒。Redis 速度快的原因主要归纳为几点：① Redis 的所有数据都放在内存中。② Redis 是使用 C 语言实现的，一般来说 C 语言实现的程序距离底层操作系统更近，因此速度相对会更快。③ Redis 使用了单线程架构，预防了多线程的竞争问题。 

**基于键值对的数据结构服务器**

与很多键值对数据库不同的是，Redis 中的值不仅可以是字符串，还可以是具体的数据结构，这样不仅能应用于多种场景开发，也可以提高开发效率。Redis 的全称是 REmote Dictionary Server，它主要提供五种数据结构：字符串、哈希、列表、集合、有序集合，同时在字符串的基础上演变出了位图和 HyperLogLog 两种数据结构，随着 LBS 基于位置服务的发展，Redis 3.2 加入了有关 GEO 地理信息定位的功能。

**丰富的功能**

① 提供了键过期功能，可以实现缓存。② 提供了发布订阅功能，可以实现消息系统。③ 支持 Lua 脚本，可以创造新的 Redis 命令。④ 提供了简单的事务功能，能在一定程度商保证事务特性。⑤ 提供了流水线功能，这样客户端能将一批命令一次性传到 Redis，减少了网络开销。

**简单稳定**

Redis 的简单主要体现在三个方面：① 源码很少，早期只有 2 万行左右，在 3.0 版本由于添加了集群特性，增加到了 5 万行左右，相对于很多 NoSQL 数据库来说代码量要少很多。② 采用单线程模型，使得服务端处理模型更简单，也使客户端开发更简单。③ 不依赖底层操作系统的类库，自己实现了事件处理的相关功能。虽然 Redis 比较简单，但也很稳定。

**客户端语言多**

Redis 提供了简单的 TCP 通信协议，很多编程语言可以方便地接入 Redis，例如 Java、PHP、Python、C、C++ 等。

**持久化**

通常来说数据放在内存中是不安全的，一旦发生断电或故障数据就可能丢失，因此 Redis 提供了两种持久化方式 RDB 和 AOF 将内存的数据保存到硬盘中。

**主从复制**

Redis 提供了复制功能，实现了多个相同数据的 Redis 副本，复制功能是分布式 Redis 的基础。

**高可用和分布式**

Redis 从 2.8 版本正式提供了高可用实现 Redis Sentinel，能够保证 Redis 节点的故障发现和故障自动转移。Redis 从 3.0 版本正式提供了分布式实现 Redis Cluster，提供了高可用、读写和容量的扩展性。

---

### Redis 的使用场景

**缓存**

缓存机制几乎在所有大型网站都有使用，合理使用缓存不仅可以加快数据的访问速度，而且能够有效降低后端数据源的压力。Redis 提供了键值过期时间设置，并且也提供了灵活控制最大内存和内存溢出后的淘汰策略。

**排行榜系统**

排行榜系统几乎存在于所有网站，Redis 提供了列表和有序集合数据结构，合理使用这些数据结构可以方便构建各各种排行榜系统。

**计数器应用**

计数器在网站中的作用很重要，例如视频网站有播放数、电商网站有浏览数，为了保证数据实时性，每一次播放和浏览都要做加 1 的操作，如果并发量很大对于传统关系型数据库的性能是很大的挑战。Redis 天然支持计数功能而且性能也非常好。

**社交网络**

粉丝、共同好友/喜好、推送、下拉刷新等是社交网络的必备功能，由于社交网站的访问量通常很大，而且关系型数据不太适合保存这种类型的数据，Redis 提供的数据结构可以相对容易地实现这些功能。

**消息队列系统**

消息队列系统是一个大型网站的必备基础组件，因为其具有业务解耦、非实时业务削峰等特性。Redis 提供了发布订阅和阻塞队列的功能，对于一般的消息队列功能基本可以满足。

**Redis 不适合非常大的数据量，成本非常高，也不适合冷数据，会浪费内存。**

----

### Redis 安装与启动

#### 安装

在 Linux 上安装，下载 Redis 指定版本的源码压缩包到当前目录：

```
wget http://download.redis.io/releases/redis-3.0.7.tar.gz
```

下载好之后，进行解压：

```
tar xzf redis-3.0.7.tar.gz
```

建立一个 redis 目录的软连接：

```
ln -s redis-3.0.7 redis
```

进入 redis 目录：

```
cd redis
```

编译：

```
make
```

安装：

```
make install
```

安装成功后，可以在任何目录执行 `redis-cli -v `查看 Redis 的版本。

usr/local/bin 目录下的可执行文件说明：

| 可执行文件       | 作用                               |
| ---------------- | ---------------------------------- |
| redis-server     | 启动 Redis                         |
| redis-cli        | Redis 命令行客户端                 |
| redis-benchmark  | Redis 基准测试工具                 |
| redis-check-aof  | Redis AOF 持久化文件检测和修复工具 |
| redis-check-dump | Redis RDB 持久化文件检测和修复工具 |
| redis-sentinel   | 启动 Redis Sentinel                |

---

#### 启动

有三种方式：默认配置、运行配置、配置文件启动。

默认配置：会使用 Redis 的默认配置启动，直接输入 `redis-server` 即可，不推荐。

运行配置：可以在运行时自定义配置，没设置的配置依然使用默认值，例如 `redis-server --port 6380` 指定以 6380 为端口号启动，不推荐。

配置文件：生产环境中推荐的启动方式，将配置写到指定文件里，例如配置写到了 `/opt/redis/redis.conf` 中，执行如下命令即可启动 Redis：`redis-server /opt/redis/redis.conf` 

客户端连接方式有两种：

① 交互式：例如 `redis-cli -h 127.0.0.1 -p 6379` ，之后所有操作都是通过交互方式实现的，不用再执行连接了。

② 命令方式：例如 `redis-cli -h 127.0.0.1 -p 6379 set name kobe` 可以直接得到命令的返回结果。如果没有 -h 参数默认连接 127.0.0.1，如果没有 -p，默认连接 6379 端口。

#### 停止

可以使用 `redis-cli shutdown` 停止 127.0.0.1 上 6379 端口的服务。

Redis 服务端会断开与客户端的连接、生成 RDB 持久化文件，将其保存在磁盘上然后关闭。

除了 shutdown 命令也可以使用 kill 进程号的方式关闭 Redis，但不要使用 `kill -9` 强制杀死 Redis 服务，不但不会做持久化操作，还会造成缓冲区等资源不能优雅关闭，极端情况会造成 AOF 和复制丢失数据的情况。

----

### 总结

Redis 有 8 个特性：速度快、基于键值对的数据结构服务器、功能丰富、简单稳定、客户端语言多、持久化、主从复制、支持高可用和分布式。

Redis 并不是万金油，有些场景不适合使用 Redis 做开发。

生产环境中使用配置文件启动 Redis。

生产环境选取稳定版本的 Redis，版本号第二位是偶数代表稳定。

----

## API 的理解和使用

### 预备

**全局命令**

查看所有键：`keys *`

查看键总数：`dbsize` 会返回当前数据库中键的总数，不会遍历所有键而是直接获取 Redis 内置的键总数变量，所有时间复杂度是 O(1)。而 keys 会遍历所有键，时间复杂度为 O(n)，当 Redis 保存了大量的键时，线上环境禁止使用。

检查键是否存在：`exists key`，如果存在返回 1，否则返回 0。

删除键：`del key [key..]` del 是一个通用命令，无论值是什么数据类型结构都可以删除，返回结果为成功删除的键的个数。

键过期：`expire key seconds` ，Redis 支持对键添加过期时间，当超过过期时间之后会自动删除。`ttl` 会返回键的剩余过期时间，返回非负数代表键的过期时间，-1 代表键没有设置过期时间，-2 代表键不存在。

查看键的数据类型结构：`type key`，如果键不存在则返回 none。

**数据结构和内部编码**

type 命令实际上返回的是当前键的数据类型结构，它们分别是：string、hash、list、set、zset，但这些只是 Redis 对外的数据结构。实际上每种数据结构都有自己底层的内部编码实现，这样 Redis 会在合适的场景选择合适的内部编码，string 包括了 raw、int 和 embstr，hash 包括了 hashtable 和 ziplist，list 包括了 linkedlist 和 ziplist，set 包括了 hashtable 和 intset，zset 包括了 skiplist 和 ziplist。可以使用 `object encoding` 查看内部编码。

Redis 这样设计的好处是：① 可以改进内部编码，而对外的数据结构和命令没有影响。② 多种内部编码实现可以在不同场景下发挥各自的优势，例如 ziplist 比较节省内存，但在列表元素较多的情况下性能有所下降，这时 Redis 会根据配置选项将列表类型的内部实现转换为 linkedlist。

**单线程架构**

Redis 使用了单线程架构和 IO 多路复用模型来实现高性能的内存数据库服务。

每次客户端调用都经历了发送命令、执行命令、返回结果三个过程，因为 Redis 是单线程处理命令的，所以一条命令从客户端到达服务器不会立即执行，所有命令都会进入一个队列中，然后逐个被执行。客户端的执行顺序可能不确定，但是可以确定不会有两条命令被同时执行，不存在并发问题。

通常来说单线程处理能力要比多线程差，Redis 快的原因：① 纯内存访问，Redis 将所有数据放在内存中。② 非阻塞 IO，Redis 使用 epoll 作为 IO 多路复用技术的实现，再加上 Redis 本身的事件处理模型将 epoll 中的连接、读写、关闭都转换为时间，不在网络 IO 上浪费过多的时间。③ 单线程避免了线程切换和竞争产生的消耗。单线程的一个问题是对于每个命令的执行时间是有要求的，如果某个命令执行时间过长会造成其他命令的阻塞，对于 Redis 这种高性能服务来说是致命的，因此 Redis 是面向快速执行场景的数据库。

---

### 字符串

字符串类型是 Redis 最基础的数据结构，键都是字符串类型，而且其他几种数据结构都是在字符串类型的基础上构建的。字符串类型的值可以实际可以是字符串（简单的字符串、复杂的字符串如 JSON、XML）、数字（整形、浮点数）、甚至二进制（图片、音频、视频），但是值最大不能超过 512 MB。

#### 常用命令

**设置值**

`set key value [ex seconds] [px millseconds] [nx|xx]`

- ex seconds：为键设置秒级过期时间，跟 setex 效果一样
- px millseconds：为键设置毫秒级过期时间
- nx：键必须不存在才可以设置成功，用于添加，跟 setnx 效果一样。由于 Redis 的单线程命令处理机制，如果多个客户端同时执行，则只有一个客户端能设置成功，可以用作分布式锁的一种实现。
- xx：键必须存在才可以设置成功，用于更新

**获取值**

`get key`，如果不存在返回 nil

**批量设置值**

`mset key value [key value...]`

**批量获取值**

`mget key [key...]`

批量操作命令可以有效提高开发效率，假如没有 mget，执行 n 次 get 命令需要 n 次网络时间 + n 次命令时间，使用 mget 只需要 1 次网络时间 + n 次命令时间。

Redis 可以支持每秒数万的读写操作，但这指的是 Redis 服务端的处理能力，对于客户端来说一次命令处理命令时间还有网络时间。因为 Redis 的处理能力已足够高，对于开发者来说，网络可能会成为性能瓶颈。

**计数**

`incr key`

incr 命令用于对值做自增操作，返回结果分为三种：① 值不是整数返回错误。② 值是整数，返回自增后的结果。③ 值不存在，按照值为 0 自增，返回结果 1。除了 incr 命令，还有自减 decr、自增指定数字 incrby、自减指定数组 decrby、自增浮点数 incrbyfloat。

---

#### 不常用命令

**追加值**

append key  value，可以向字符串尾部追加值

**字符串长度**

`strlen key`

**设置并返回原值**

`getset key value`

**设置指定位置的字符**

`setrange key offset value`

**获取部分字符串**

`getrange key start end`，start 和 end分别是开始和结束的偏移量，偏移量从 0 开始计算。

---

#### 内部编码

字符串类型的内部编码有三种:

- int：8 个字节的长整形
- embstr：小于等于 39 个字节的字符串
- raw：大于 39 个字节的字符串

---

#### 典型使用场景

**缓存功能**

Redis 作为缓存层，MySQL 作为存储层，首先从 Redis 获取数据，如果没有获取到就从 MySQL 获取，并将结果写回到 Redis，添加过期时间。

**计数**

Redis 可以实现快速计数功能，例如视频每播放一次就用 incy 把播放数加 1。

**共享 Session**

一个分布式 Web 服务将用户的 Session 信息保存在各自服务器，但会造成一个问题，出于负载均衡的考虑，分布式服务会将用户的访问负载到不同服务器上，用户刷新一次可能会发现需要重新登陆。为解决该问题，可以使用 Redis 将用户的 Session 进行集中管理，在这种模式下只要保证 Redis 是高可用和扩展性的，每次用户更新或查询登录信息都直接从 Redis 集中获取。

**限速**

例如为了短信接口不被频繁访问会限制用户每分钟获取验证码的次数或者网站限制一个 IP 地址不能在一秒内访问超过 n 次。可以使用键过期策略和自增计数实现。

----

### 哈希

哈希类型是指键值本身又是一个键值对结构，哈希类型中的映射关系叫做 field-value，这里的 value 是指 field 对于的值而不是键对于的值。

#### 命令

**设置值**

`hset key field value`，如果设置成功会返回 1，反之会返回 0，此外还提供了 hsetnx 命令，作用和 setnx 类似，只是作用于由键变为 field。

**获取值**

`hget key field`，如果不存在会返回 nil。

**删除 field**

`hdel key field [field...]`，会删除一个或多个 field，返回结果为删除成功 field 的个数。

**计算 field 个数**

`hlen key`

**批量设置或获取 field-value**

```
hmget key field [field...]
hmset key field value [field value...]
```

hmset 需要的参数是 key 和多对 field-value，hmget 需要的参数是 key 和多个 field。

**判断 field 是否存在**

`hexists key field`，存在返回 1，否则返回  0。

**获取所有的 field**

`hkeys key`，返回指定哈希键的所有 field。

**获取所有 value**

`hvals key`，获取指定键的所有 value。

**获取所有的 field-value**

`hgetall key`，获取指定键的所有 field-value。

**计数**

`hincrby key field` 和 `hincrbyfloat key field`，作用和 incrby 和 incrbyfloat 一样，作用域是 field。

**计算 value 的字符串长度**

hstrlen key field

---

#### 内部编码

哈希类型的内部编码有两种：

- ziplist 压缩列表：当哈希类型元素个数和值小于配置值（默认 512 个和 64 字节）时会使用 ziplist 作为内部实现，使用更紧凑的结构实现多个元素的连续存储，在节省内存方面比 hashtable 更优秀。
- hashtable 哈希表：当哈希类型无法满足 ziplist 的条件时会使用 hashtable 作为哈希的内部实现，因为此时 ziplist 的读写效率会下降，而 hashtable 的读写时间复杂度都为 O(1)。

---

#### 使用场景

缓存用户信息，有三种实现：

- 原生字符串类型：每个属性一个键。

  ```
  set user:1:name tom
  set user:1:age 23
  set user:1:city xi'an
  ```

  优点：简单直观，每个属性都支持更新操作。

  缺点：占用过多的键，内存占用量较大，用户信息内聚性差，一般不会在生产环境使用。

- 序列化字符串类型：将用户信息序列化后用一个键保存。

  ```
  set user:1 serialize(userInfo)
  ```

  优点：编程简单，如果合理使用序列化可以提高内存使用率。

  缺点：序列化和反序列化有一定开销，同时每次更新属性都需要把全部数据取出进行反序列化，更新后再序列化到 Redis。

- 哈希类型：每个用户属性使用一对 field-value，但只用一个键保存。

  ```
  hmset user:1 name tom age 23 city xi'an
  ```

  优点：简单直观，如果合理使用可以减少内存空间使用。

  缺点：要控制哈希在 ziplist 和 hashtable 两种内部编码的转换，hashtable 会消耗更多内存。

----

### 列表

列表类型是用来存储多个有序的字符串，列表中的每个字符串称为元素，一个列表最多可以存储 2^32^-1 个元素。可以对列表两端插入（push）和弹出（pop），还可以获取指定范围的元素列表、获取指定索引下标的元素等。列表是一种比较灵活的数据结构，它可以充当栈和队列的角色，在实际开发中有很多应用场景。

列表类型有两个特点：① 列表中的元素是有序的，可以通过索引下标获取某个元素或者某个范围内的元素列表。② 列表中的元素可以重复。

#### 命令

**添加操作**

从右边插入元素：`rpush key value [value...]`

从左到右获取列表的所有元素：`lrange 0 -1`

从左边插入元素：`lpush key value [value...]`

向某个元素前或者后插入元素：`linsert key before|after pivot value`，会在列表中找到等于 pivot 的元素，在其前或后插入一个新的元素 value。

**查找**

获取指定范围内的元素列表：`lrange key start end`，索引从左到右的范围是 0~N-1，从右到左是 -1~-N，lrange 中的 end 包含了自身。

获取列表指定索引下标的元素：`lindex key index`，获取最后一个元素可以使用 `lindex key -1`。

获取列表长度：`llen key`

**删除**

从列表左侧弹出元素：`lpop key`

从列表右侧弹出元素：`rpop key`

删除指定元素：`lrem key count value`，如果 count 大于 0，从左到右删除最多 count 个元素，如果 count 小于 0，从右到左删除最多个 count 绝对值个元素，如果 count 等于 0，删除所有。

按照索引范围修剪列表：`ltrim key start end`，只会保留 start ~ end 范围的元素。

**修改**

修改指定索引下标的元素：`lset key index newValue`。

**阻塞操作**

阻塞式弹出：`blpop/brpop key [key...] timeout`，timeout 表示阻塞时间。

当列表为空时，如果 timeout = 0，客户端会一直阻塞，如果在此期间添加了元素，客户端会立即返回。

如果是多个键，那么brpop会从左至右遍历键，一旦有一个键能弹出元素，客户端立即返回。

如果多个客户端对同一个键执行 brpop，那么最先执行该命令的客户端可以获取弹出的值。

---

#### 内部编码

列表的内部编码有两种：

- ziplist 压缩列表：跟哈希的 zipilist 相同，元素个数和大小小于配置值（默认 512 个和 64 字节）时使用。
- linkedlist 链表：当列表类型无法满足 ziplist 的条件时会使用linkedlist。

Redis 3.2 提供了 quicklist 内部编码，它是以一个 ziplist 为节点的 linkedlist，它结合了两者的优势，为列表类提供了一种更为优秀的内部编码实现。

---

#### 使用场景

**消息队列**

Redis 的 lpush + brpop 即可实现阻塞队列，生产者客户端使用 lpush 从列表左侧插入元素，多个消费者客户端使用 brpop 命令阻塞式地抢列表尾部的元素，多个客户端保证了消费的负载均衡和高可用性。

**文章列表**

每个用户有属于自己的文章列表，现在需要分页展示文章列表，就可以考虑使用列表。因为列表不但有序，同时支持按照索引范围获取元素。每篇文章使用哈希结构存储，例如每篇文章有三个属性，title、timestamp 和 content：

`hmset article:k title t timestamp 147651524 content c`。

向用户文章列表添加文章，`user:{id}:articles` 作为用户文章列表的键：

`lpush user:k:articles article:k`。

分页获取用户文章列表，例如以下伪代码获取用户 id = 1 的前 10 篇文章。

```
articles = lrange user:1:articles 0 9
for article in {articles}
	hgetall {article}
```

使用列表类型保存和获取文章列表存在两个问题：① 如果每次分页获取的文章个数较多，需要执行多次 hgetall 操作，此时可以考虑使用 Pipeline 批量获取，或者考虑将文章数据序列化为字符串类型，使用 mget 批量获取。② 分页获取文章列表时，lrange 命令在列表两端性能较好，但如果列表大，获取中间范围的元素性能会变差，可以考虑将列表做二级拆分，或使用 Redis3.2 的 quicklist。

---

**lpush + lpop = 栈**

**lpush + rpop  = 队列**

**lpush + ltrim = 优先集合**

**lpush + brpop = 消息队列**

---

### 集合

集合类型也是用来保存多个字符串元素，和列表不同的是集合不允许有重复元素，并且集合中的元素是无序的，不能通过索引下标获取元素。一个集合最多可以存储 2^32^-1 个元素。Redis 除了支持集合内的增删改查，还支持多个集合取交集、并集、差集。

#### 命令

**集合内操作**

**添加元素**

`sadd key element [element...]`，返回结果为添加成功的元素个数。

**删除元素**

`srem key element [element...]`，返回结果为成功删除的元素个数。

**计算元素个数**

`scard key`，时间复杂度为 O(1)，会直接使用 Redis 内部的遍历。

**判断元素是否在集合中**

`sismember key element`，如果存在返回 1，否则返回 0。

**随机从集合返回指定个数个元素**

`srandmember key [count]`，如果不指定 count 默认为 1。

**从集合随机弹出元素**

`spop key`，可以从集合中随机弹出一个元素。

**获取所有元素**

`smembers key`

---

**集合间操作**

**求多个集合的交集**

`sinter key [key...]`

**求多个集合的并集**

`sunion key [key...]`

**求多个集合的差集**

`sdiff key [key...]`

**保存交集、并集、差集的结果**

```
sinterstore destination key [key...]
sunionstore destination destination key [key...]
sdiffstore destination key [key...]
```

集合间的运算在元素较多的情况下会比较耗时，所以 Redis 提供了这三个指令将集合间交集、并集、差集的结果保存在 destination key 中。

---

#### 内部编码

集合类型的内部编码有两种：

- intset 整数集合：当集合中的元素个数小于配置值（默认 512 个时），使用 intset。
- hashtable 哈希表：当集合类型无法满足 intset 条件时使用 hashtable。当某个元素不为整数时，也会使用 hashtable。

---

#### 使用场景

集合类型比较典型的使用场景是标签，例如一个用户可能与娱乐、体育比较感兴趣，另一个用户可能对例时、新闻比较感兴趣，这些兴趣点就是标签。这些数据对于用户体验以及增强用户黏度比较重要。

**给用户添加标签**

```
sadd user:1:tags tag1 tag2 tag5
sadd user:2:tags tag3 tag4 tag5
...
sadd user:k:tags tagx tagy tagz
```

**给标签添加用户**

```
sadd tag:1:users user:1 user:3
sadd tag:2:users user:1 user:4 user:5
...
sadd tag:k:users user:x user:y ...
```

用户和标签的关系维护应该在一个事务内执行，防止部分命令失败造成的数据不一致。

**删除用户标签**

```
srem user:1:tags tag1 tag5
```

**删除标签下的用户**

```
srem tag:1:users user:1
```

删除也同样应该放在一个事务中。

**求两个用户共同感兴趣的标签**

```
sinter user:1:tags user:2:tags
```

**sadd = 标签**

**spop/srandmember = 生成随机数，比如抽奖**

**sadd + sinter = 社交需求**

---

### 有序集合

有序集合保留了集合不能有重复成员的特性，不同的是可以排序。但是它和列表使用索引下标作为排序依据不同的是，他给每个元素设置一个分数（score）作为排序的依据。有序集合提供了获取指定分数和元素查询范围、计算成员排名等功能。

| 数据结构 | 是否允许元素重复 | 是否有序 | 有序实现方式 | 应用场景         |
| -------- | ---------------- | -------- | ------------ | ---------------- |
| 列表     | 是               | 是       | 下标         | 时间轴，消息队列 |
| 集合     | 否               | 否       | /            | 标签，社交       |
| 有序集合 | 否               | 是       | 分值         | 排行榜，社交     |

#### 命令

**集合内**

**添加成员**

`zadd key score member [score member...]`，返回结果是成功添加成员的个数

Redis 3.2 为 zadd 命令添加了 nx、xx、ch、incr 四个选项：

- nx：member 必须不存在才可以设置成功，用于添加
- xx：member 必须存在才能设置成功，用于更新
- ch：返回此次操作后，有序集合元素和分数变化的个数
- incr：对 score 做增加，相当于 zincrby

zadd 的时间复杂度为 O(log~n~)，sadd 的时间复杂度为 O(1)。

**计算成员个数**

`zcard key`，时间复杂度为 O(1)。

**计算某个成员的分数**

`zscore key member` ，如果不存在则返回 nil。

**计算成员排名**

`zrank key member`，从低到高返回排名

`zrevrank key member`，从高到低返回排名

**删除成员**

`zrem key member [member...]`，返回结果是成功删除的个数。

**增加成员的分数**

`zincrby key increment member`

**返回指定排名范围的成员**

`zrange key start end [withscores]`

`zrevrange key start end [withscores]`

zrange 从低到高返回，zrevrange 从高到底返回，如果加上 withscores 选项同时会返回成员的分数。

**返回指定分数范围的成员**

`zrangebyscore key min max [withscores] [limit offset count]`

`zrevrangebyscore key min max [withscores] [limit offset count]`

zrangebyscore 从低到高返回，zrevrangebyscore 从高到底返回，如果加上 withscores 选项同时会返回成员的分数。[limit offset count] 可以限制输出的起始位置和个数。

**返回指定分数范围成员个数**

`zcount key min max`

**删除指定排名内的升序元素**

`zremrangebyrank key start end`

**删除指定分数范围内的成员**

`zremrangebyscore key min max`

---

**集合间的操作**

**交集**

`zinterstore destination numkeys key [key...] [weights weight [weight...]] [aggregate sum|min|max]`

- destination：交集结果保存到这个键

- numkeys：要做交集计算键的个数

- key [key...]：需要做交集计算的键

- weights weight [weight...]：每个键的权重，默认 1

- aggregate sum|min|max：计算交集后，分值可以按和、最小值、最大值汇总，默认 sum

**并集**

`zunionstore destination numkeys key [key...] [weights weight [weight...]] [aggregate sum|min|max]`

---

#### 内部编码

有序集合的内部编码有两种：

- ziplist 压缩列表：当有序集合元素个数和值小于配置值（默认128 个和 64 字节）时会使用 ziplist 作为内部实现。
- skiplist 跳跃表：当 ziplist 不满足条件时使用，因为此时 ziplist 的读写效率会下降。

---

#### 使用场景

有序集合的典型使用场景就是排行榜系统。

例如用户 mike 上传了一个视频并添加了 3 个赞，可以使用有序集合的 zadd 和 zincrby：

```
zadd user:ranking:2020_06_19 3 mike
```

如果之后再获得一个赞，可以使用 zincrby：

```
zincrby user:ranking:2020_06_19 1 mike
```

例如需要将用户 tom 从榜单删除，可以使用 zrem：

```
zrem user:ranking:2020_06_19 tom
```

展示获取赞数最多的十个用户：

```
zrevrange user:ranking:2020_06_19 0 9
```

展示用户信息及用户分数，将用户名作为键后缀，将用户信息保存在哈希类型中，至于用户分数和排名可以使用 zscore 和 zrank：

```
hgetall user:info:tom
zscore user:ranking:2020_06_19 tom
zrank user:ranking:2020_06_19 tom
```

---

### 键管理

#### 单个键管理

**键重命名**

`rename key newkey`

如果 rename 前键已经存在，那么它的值也会被覆盖。

为了防止强行覆盖，Redis 提供了 renamenx 命令，确保只有 newkey 不存在时才被覆盖。由于重命名键期间会执行 del 命令删除旧的键，如果键对应值比较大会存在阻塞的可能。

**随机返回一个键**

`random key`

**键过期**

`expire key seconds`：键在 seconds 秒后过期

`expireat key timestamp`：键在秒级时间戳 timestamp 后过期

如果过期时间为负值，键会被立即删除，和 del 命令一样。

persist 命令可以将键的过期时间清除。

对于字符串类型键，执行 set 命令会去掉过期时间，set 命令对应的函数 setKey 最后执行了 removeExpire 函数去掉了过期时间。

Redis 不支持二级数据结构（例如哈希、列表）内部元素的过期功能，例如不能对列表类型的一个元素设置过期时间。

setex 命令作为 set + expire 的组合，不单是原子执行并且减少了一次网络通信的时间。

**键迁移**

- move

  `move key db`

  move 命令用于在 Redis 内部进行数据迁移，`move key db` 就是把指定的键从源数据库移动到目标数据库中。

- dump + restore

  `dump key`

  `restore key ttl value`

  可以实现在不同的 Redis 势力之间进行数据迁移，分为两步：

  ① 在源 Redis 上，dump 命令会将键值序列化，格式采用 RDB 格式。

  ② 在目标 Redis 上，restore 命令将上面序列化的值进行复原，ttl 参数代表过期时间， ttl = 0 则没有过期时间。

  整个迁移并非原子性的，而是通过客户端分步完成，并且需要两个客户端。

- migrate

  实际上 migrate 命令就是将 dump、restore、del 三个命令进行组合，从而简化了操作流程。migrate 具有原子性，且支持多个键的迁移，有效提高了迁移效率。实现过程和 dump + restore 类似，有三点不同：

  ① 整个过程是原子执行，不需要在多个 Redis 实例开启客户端。

  ② 数据传输直接在源 Redis 和目标 Redis 完成。

  ③ 目标 Redis 完成 restore 后会发送 OK 给源 Redis，源 Redis 接收后会根据 migrate 对应的选项来决定是否在源 Redis 上删除对应的键。

| 命令           | 作用域         | 原子性 | 支持多个键 |
| -------------- | -------------- | ------ | ---------- |
| move           | Redis 实例内部 | 是     | 否         |
| dump + restore | Redis 实例之间 | 否     | 否         |
| migrate        | Redis 实例之间 | 是     | 是         |

---

#### 遍历键

**全量遍历键**

`keys pattern`

`*  `代表匹配任意字符，`?` 匹配一个字符，`[]` 匹配部分字符，例如 `[1,3]` 匹配 1 和 3， `[1-3]` 匹配 1 到 3 的任意数字，`\`用来做转义。

`keys *` 遍历所有的键，一般不在生产环境使用，在以下情况可以使用：

① 在一个不对外提供服务的 Redis 从节点上执行，不会阻塞客户端的请求，但会影响主从复制。

② 如果确定键值总数比较少可以执行。

---

**渐进式遍历**

Redis 从 2.8 版本后提供了一个新的命令 scan，能有效解决 keys 存在的问题。和 keys 遍历所有键不同，scan 采用渐进式遍历的方式解决阻塞问题，每次 scan 的时间复杂度为 O(1)，但是要真正实现 keys 的功能可能需要执行多次 scan。

```
scan cursor [match pattern] [count number]
```

cursor 是必须参数，代表一个游标，第一次遍历从 0 开始，每次 scan 完会返回当前游标的值，直到值为 0 表示遍历结束。

match pattern 是可选参数，作用是模式匹配。

count number 是可选参数，作用是表明每次要遍历的键个数，默认值为 10。

除了 scan 外，Redis 提供了面向哈希、集合、有序集合的扫描遍历命令，解决了 hgetall、smembers、zrange 可能产生的阻塞问题，对应命令分别为 hscan、sscan、zscan。

渐进式遍历可以有效解决 keys 命令可能产生的阻塞问题，但是如果在 scan 过程中有键的变化，那么遍历效果可能会遇到问题：新增的键没有被遍历到，遍历了重复的键等情况。

---

#### 数据库管理

**切换数据库**

`select dbIndex`

Redis 中默认配置有 16 个数据库，例如 select 0 将切换到第一个数据库，数据库之间的数据是隔离的。

**flushdb/flushall**

用于清除数据库，flushdb 只清除当前数据库，flushall 会清除所有数据库。如果当前数据库键值数量比较多，flushdb/flushall 存在阻塞 Redis 的可能性。

----

### 总结

Redis 提供 5 种数据结构，每种数据结构都有多种内部编码实现。

纯内存存储、IO 多路复用计数、单线程架构是造就 Redis 高性能的三个因素。

由于 Redis 的单线程结构，所以需要每个命令能被快速执行完，否则会存在阻塞的可能。

批量操作（例如 mget、mset、hmset 等）能够有效提高命令执行的效率，但要注意每次批量操作的个数和字节数。

persist 命令可以删除任意类型键的过期时间，但 set 也会删除字符串类型键的过期时间。

move、dump + restore、migrate 是 Redis 发展过程中三种迁移键的方式，其中 move 命令基本废弃，migrate 命令用原子性的方式实现了 dump + restore，并且支持批量操作，是 Redis Cluster 实现水平扩容的重要工具。

scan 命令可以解决 keys 命令可能带来的阻塞问题，同时 Redis 还提供了 hscan、sscan、zscan 渐进式遍历 hash、set、zset。

----

## 高级功能

### 慢查询分析

许多系统（例如 MySQL）提供了慢查询日志帮助开发和运维任意定位系统存在的慢操作，慢查询日志是系统在命令执行前后计算每条命令的执行时间，当超过预设阈值，就将这条命令的相关信息（例如发生时间、耗时、命令的详细信息）记录下来，Redis 也提供了类似的功能。

Redis 客户端执行一条命令分为四步：发送命令、命令排队、命令执行、返回结果。慢查询只统计第三步的时间，所有没有慢查询不代表客户端没有超时问题。

当超过 slowlog-log-slower-than 阈值（默认 10000 微秒）时，该命令会被记录在慢查询日志，如果设置阈值为 0 会记录所有命令，如果设置为负值对所有命令都不会记录。Redis 使用了一个列表来存储慢查询日志，一个新的命令满足慢查询条件时被插入到这个列表中，当慢查询日志列表处于最大长度时，最早插入的一个命令将从列表中移出。slowlog-max-len 是慢查询列表的最大长度。

在 Redis 中有两种修改配置的方法，一种是修改配置文件，另一种是使用 config set 命令动态修改，如果要将配置持久化到本地配置文件，需要执行 config rewrite 命令。

虽然慢查询日志存放在 Redis 的内存列表中，但 Redis 并没有暴露这个列表的键，而是通过一组命令来实现对慢查询日志的访问和管理。

获取慢查询日志：`slowlog get [n]`，n 可以指定条数。

获取慢查询日志列表当前长度：`slowlog len`。

慢查询日志重置：`slowlog reset`，实际是队列做清理操作。

使用时注意：

- slowlog-max-len 配置建议：线上建议调大慢查询列表，记录慢查询时 Redis 会对长命令阶段，并不会占用大量内存。增大慢查询列表可以减缓慢查询被剔除的可能，线上可以设置为 1000 以上。
- slowlog-log-slower-than 配置建议：默认 10 毫秒判断为慢查询，需要根据 Redis 并发量调整。由于 Redis 采用单线程响应命令，对于高流量场景，可以设置为 1 毫秒。
- 慢查询只记录命令执行时间，并不包括命令排队和网络传输时间。因此客户端执行命令的时间会大于命令实际时间，因为命令执行排队机制，慢查询会导致其他所有命令级联阻塞，所以当客户端出现请求超时，需要检查该时间点是否有对应的慢查询，从而分析出是否为慢查询导致命令级联阻塞。
- 由于慢查询日志是一个 FIFO 的队列，因此慢查询比较多的情况下会丢失部分慢查询命令，为了防止这种情况可以定期执行 slow get 命令将慢查询日志持久化到其他存储中（例如 MySQL）。

-----

### Redis Shell

Redis 提供了 redis-cli、redis-server、redis-benchmark 等 Shell 工具。

#### redis-cli

redis-cli 除了 -h、-p 还有一些其他的参数：

-r：代表命令将执行多次

-i：代表每隔几秒执行一次命令

-x：代表从标准输入读取数据作为 redis-cli 的最后一个参数

-c：连接 Redis Cluster 节点时需要使用的，可以防止 moved 和 ask 异常。

-a：如果 Redis 设置了密码，可以用 -a 选项。

--scan/--pattern：用于扫描指定模式的键，相对于使用 scan 命令。

--slave：把当前客户端模拟成当前 Redis 节点的从节点，可以用来获取当前 Redis 节点的更新操作。

--rdb：该选项会请求 Redis 实例生成并发送 RBD 持久化文件，保存在本地。可使用它做持久化文件的定期备份。

--pipe：用于将命令封装成 Redis 通信协议定义的数据格式，批量发送给 Redis 执行。

--bigkeys：使用 scan 命令对 Redis 的键进行采样，从中找到内存占用比较大的键值，这些键值可能是系统的瓶颈。

--eval：用于指定 Lua 脚本。

--latency：检测网络延迟。

--stat：实时获取 Redis 的重要统计信息。

--no-raw：要求命令的返回结果必须是原始的格式。

--raw：要求返回结果是格式化后的结果。

---

#### redis-server

除了启动 Redis 外，还有一个 --test-memory 选项，该选项可以用来检测当前操作系统能否稳定地分配指定容量地内存给 Redis，通过这种检测可以有效避免因为内存问题造成 Redis 崩溃。

---

#### redis-benchmark

可以为 Redis 做基准性能测试，它提供了很多选项帮助开发和运维人员测试 Redis 的相关性能。

相关选项：

-c：代表客户端的并发数量。

-n：代表客户端请求总量。

-P：每个请求 pipeline 的数据量，默认为 1。

-k：代表客户端是否使用 keepalive，1 为使用，0 为不使用，默认 1。

-t：对指定命令进行基准测试。

--csv：将结果按照 csv 格式输出，便于后续处理。

---

### Pipeline

Redis 客户端执行一条命令需要经过发送命令、命令排队、执行命令、返回结果。其中第一步和第四步称为 RTT 往返时间。Redis 提供了批量操作命令，有效地节约 RTT。但大部分命令不支持批量操作，例如要执行 n 次 hgetall 命令，并没有 mhgetall 命令，需要消耗 n 次 RTT。

Pipelin 流水线机制能改善上述问题，它能将一组 Redis 命令进行组装，通过一次 RTT 传输给 Redis，再将这组命令地执行结果按顺序返回给客户端。

Pipeline 执行速度一般比逐条执行要快，客户端和服务端地网络延时越大效果就越明显。

和原生批量命令的区别：

- 原生批量命令是原子的，Pipeline 是非原子的。
- 原生批量命令是一个命令对应多个 key，Pipeline 支持多个命令。
- 原生批量命令是 Redis 服务端支持实现的，而 Pipeline 需要服务端和客户端共同实现。

---

### 事务

Redis 提供了简单的事务功能，将一组需要一起执行的命令放到 multi 和 exec 两个命令之间。multi 代表事务开始，exec 命令代表事务结束，它们之间的命令是原子顺序执行的。如果要停止事务的执行，可以使用 discard 命令代替 exec。

事务中的命令错误分为命令错误和运行时错误：

- 例如 set 写成了 sett，属于语法错误，会造成整个事务无法执行。
- 例如把 sadd 写成了 zadd，这种就是运行时错误，Redis 不支持回滚，错误前的语句会执行成功，开发人员需要自己修复这类问题。

有些应用场景需要在事务之前，确保事务中的 key 没有被其他客户端修改过才执行事务，否则不执行（类似乐观锁）。Redis 提供了 watch 命令解决这类问题。

例如客户端1 在执行 multi 之前执行了 watch 命令，客户端2 在客户端1 执行 exec 之前修改了 key 的值，此时事务会执行失败，返回 nil。

Redis 提供了简单的事务，之所以说简单是因为它不支持事务中的回滚特性，同时无法实现命令之间的逻辑关系计算。

----

### Bitmaps

Bitmaps 本身不是一种数据结构，实际上它就是字符串，但是它可以对字符串的位进行操作。

Bitmaps 单独提供了一套命令，所以在 Redis 使用 Bitmaps 和使用字符串的方法不太相同，可以把 Bitmaps 看作一个以位为单位的数组，数组的每个单元只能存储 0 和 1，数组的下标叫做偏移量。

#### 命令

例：将每个独立用户是否访问过网站存放在 Bitmaps 中，将访问过的用户记作 1，没有访问过的记作 0，偏移量作为用户的 id。

**设置值**

```
setbit key offset value
```

设置键的第 offset 个位的值，假设有 20 个用户，id 为 0、5、11、15、19 的用户对网站进行了访问，那么初始化如下：

```
setbit unique:users:2020-06-20 0 1
setbit unique:users:2020-06-20 5 1
setbit unique:users:2020-06-20 11 1
setbit unique:users:2020-06-20 15 1
setbit unique:users:2020-06-20 19 1
```

很多应用的用户 id 直接以一个指定数字开头，例如 10000，直接将用户 id 与 Bitmaps 的偏移量对应势必会造成一定浪费，通常做法是每次做 setbit 操作时将用户 id 减去这个指定数字。在第一次初始化 Bitmaps 时，如果偏移量非常大，那么整个初始化过程会执行比较慢，可能造成阻塞。

**获取值**

```
getbit key offset
```

获取键的第 offset 个位的值，例如获取 id 为 8 的用户是否在 2020-06-20 这天访问过：

```
getbit unique:users:2020-06-20 8
```

**获取指定范围值为 1 的个数**

```
bitcount key [start end]
```

例如获取 2020-06-20 这天访问过的用户数量

```
bitcount unique:users:2020-06-20
```

start 和 end 代表起始和结束字节数。

**Bitmaps 间的运算**

```
bitop op destkey key [key...]
```

bitop 是一个复合操作，它可以做交集、并集、非、异或并将结果保存到 destkey 中。

例如计算 2020-06-20 和 2020-06-21 都访问过网站的用户数量：

```
bitop and unique:users:and:2020-06-20_21 unique:users:2020-06-20 unique:users:2020-06-21
bitcount unique:users:and:2020-06-20_21 
```

例如计算 2020-06-20 和 2020-06-21 任意一天访问过网站的用户数量：

```
bitop or unique:users:or:2020-06-20_21 unique:users:2020-06-20 unique:users:2020-06-21
bitcount unique:users:or:2020-06-20_21
```

**计算第一个值为 tartgetBit 的偏移量**

```
bitops key targetBit [start] [end]
```

例如计算 2020-06-20 当前访问网站的最小用户 id：

```
bitops unique:users:2019-06-20 1
```

假设网站的活跃用户量很大，使用 Bitmaps 相比 set 可以节省很多内存，但如果活跃用户很少就会浪费内存。

----

### HyperLogLog

HyperLogLog 不是一种新的数据结构，实际也是字符串类型，是一种基数算法。提供 HyperLogLog 可以利用极小的内存空间完成独立总数的统计，数据集可以是 IP、Email、ID 等。

**添加**

`pfadd key element [element...]`，如果添加成功会返回 1

**计算独立用户数**

`pfcount key [key...]`

**合并**

`pfmerge destkey sourcekey [sourcekey...]`

HyperLogLog 内存占用量非常小，但是存在错误率，开发者在进行数据结构选型时只需要确认如下两条：

- 只为了计算独立总数，不需要获取单条数据。
- 可以容忍一定误差率，毕竟 HyperLogLog 在内存占用量上有很大优势。

---

### 发布订阅

Redis 提供了基于发布/订阅模式的消息机制，此种模式下，消息发布者和订阅者不进行直接通信，发布者客户端向指定的频道（channel）发送消息，订阅该频道的每个客户端都可以收到该消息。

#### 命令

**发布消息**

`publish channel message`，返回结果为订阅者的个数。

**订阅消息**

`subscribe channel [channel..]`，订阅者可以订阅一个或多个频道。

客户端在执行订阅命令后会进入订阅状态，只能接收 subscribe、psubscribe、unsubscribe、punsubscribe 的四个命令。新开启的订阅客户端，无法收到该频道之前的消息，因为 Redis 不会对法捕的消息进行持久化。

和很多专业的消息队列系统如 Kafka、RocketMQ 相比，Redis 的发布订阅略显粗糙，例如无法实现消息堆积和回溯，但胜在足够简单，如果当前场景可以容忍这些缺点，也是一个不错的选择。

**取消订阅**

`unsubscribe [channel [channel...]]`

客户端可以通过 unsubscribe 命令取消对指定频道的订阅，取消成功后不会再收到该频道的发布消息。

**按照模式订阅和取消订阅**

`psubscribe pattern [pattern...]`

`punsubscribe pattern [pattern...]`

这两种命令支持 glob 风格，例如订阅所有以 it 开头的频道：`psubscribe it*`

**查询订阅**

查看活跃的频道：`pubsub channels [pattern]`，活跃频道是指当前频道至少有一个订阅者。

查看频道订阅数：`pubsub numsub [channel ...]`

查看模式订阅数：`pubsub numpat`

----

#### 使用场景

聊天室、公告牌、服务之间利用消息解耦都可以使用发布订阅模式，以服务器解耦为例：视频管理系统负责管理视频信息，用户通过各种客户端获取视频信息。

假如视频管理员在视频管理系统中对视频信息进行了更新，希望及时通知给视频服务端，就可以采用发布订阅模式，发布视频信息变化的消息到指定频道，视频服务订阅这个频道及时更新视频信息，通过这种方式实现解耦。

视频服务订阅 video:changes 频道：

```
subscribe video:changes
```

视频管理系统发布消息到 video:changes 频道：

```
publish video:changes "video1,video3,video5"
```

视频服务收到消息，对视频信息进行更新..

---

### GEO

Redis 3.2 版本提供了 GEO 地理信息定位功能，支持存储地理位置信息用来实现诸如附近位置、摇一摇这一类依赖于地理位置信息的功能。

**增加地理位置信息**

```
geoadd key longitude latitude member [longitude latitude member...]
```

longitude、latitude、member 分别是该地理位置的经度、纬度、成员。

例如添加北京的地理位置信息：

```
geoadd cities:locations 116.28 39.55 beijing
```

返回结果表示成功添加的个数，如果需要更新地理位置信息仍然可以使用 geoadd 命令，返回结果为 0。

**获取地理位置信息**

```
getpos key member [member...]
```

**获取两个地理位置的距离**

```
geodist key member1 member2 [unit]
```

其中 unit 代表返回结果的单位，包含 m 米、km 公里、mi 英里、ft 英尺。

**删除地理位置信息**

```
zrem key member
```

GEO 没有提供删除成员的命令，但由于它底层是 zset，可以使用 zrem 删除。

----

### 总结

慢查询中有两个重要参数 slowlog-log-slower-than 和 slowlog-max-len。

慢查询不包括命令网络传输和排队时间。

有必要将慢查询定期存放。

Pipeline 可以有效减少 RTT 次数，但每次 Pipeline 的命令数量不能无节制。

Redis 可以使用 Lua 脚本创造出原子、高效、自定义命令组合。

Bitmaps 可以用来做独立用户统计，有效节省内存。

Bitmaps 中 setbit 一个大的偏移量，由于申请大量内存会导致阻塞。

HyperLogLog 虽然在统计独立总量时存在一定误差，但是节省的内存量十分惊人。

Redis 的发布订阅相比许多专业消息队列系统功能较弱，不具备息堆积和回溯能力，但胜在足够简单。

Redis 3.2 提供了 GEO 功能，用来实现基于地理位置信息的应用，底层实现是 zset。

----

## 客户端

### 客户端通信协议

客户端与服务端的通信协议是在 TCP 协议之上构建的，Redis 制定了 RESP（Redis 序列化协议）实现客户端与服务端的正常交互，这种协议简单高效，既能够被机器解析，又容易被人类识别。例如客户端发送一条 set hello world 命令给服务端，按照 RESP 的标准客户端需要将其封装为如下格式：

```
*3//表示有3个参数
$3//表示参数的字节数
SET
$5
hello
$5
world
```

这样 Redis 服务端能够按照 RESP 将其解析为 set hello world，执行后回复的格式为 +OK。

返回结果格式：

- 状态回复：RESP 中第一个字节为 `+`。
- 错误回复：RESP 中第一个字节为 `-`。
- 整数回复：RESP 中第一个字节为 `:`。
- 字符串回复：RESP 中第一个字节为 `$`。
- 多条字符串回复：RESP 中第一个字节为 `*`。

有 RESP 提供的发送命令和返回结果的协议格式，各种编程语言就可以利用其来实现相应的 Redis 客户端。

----

### Java 客户端 Jedis

在 maven 项目中添加相应的依赖即可：

```xml
<dependencies>
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>2.8.1</version>
    </dependency>
</dependencies>
```

---

启动本地 Redis 服务器后，通过以下代码连接 Redis：

```java
Jedis jedis = new Jedis("127.0.0.1", 6379);
```

#### 操作基本数据结构

操作五种数据结构的示例：

```java
public static void main(String[] args) {
    Jedis jedis = new Jedis("127.0.0.1", 6379);
    // string
    String set = jedis.set("hello", "world");
    System.out.println(set);//OK

    String hello = jedis.get("hello");
    System.out.println(hello);//world

    Long counter = jedis.incr("counter");
    System.out.println(counter);//1

    // hash
    jedis.hset("hash", "f1", "v1");
    jedis.hset("hash", "f2", "v2");
    Map<String, String> hash = jedis.hgetAll("hash");
    System.out.println(hash);//{f1=v1, f2=v2}

    // list
    jedis.rpush("list", "1");
    jedis.rpush("list", "2");
    jedis.rpush("list", "3");
    List<String> list = jedis.lrange("list", 0, -1);
    System.out.println(list);//[1, 2, 3]

    // set
    jedis.sadd("set", "a");
    jedis.sadd("set", "b");
    jedis.sadd("set", "a");
    Set<String> set1 = jedis.smembers("set");
    System.out.println(set1);//[b, a]

    // zset
    jedis.zadd("zset", 33, "tom");
    jedis.zadd("zset", 66, "peter");
    jedis.zadd("zset", 99, "james");
    Set<Tuple> zset = jedis.zrangeWithScores("zset", 0, -1);
    System.out.println(zset);//[[[116, 111, 109],33.0], [[112, 101, 116, 101, 114],66.0], [[106, 97, 109, 101, 115],99.0]]

}
```

---

#### 序列化对象

序列化 Java 对象，导入以下依赖：

```xml
<dependency>
    <groupId>com.dyuproject.protostuff</groupId>
    <artifactId>protostuff-runtime</artifactId>
    <version>1.0.11</version>
</dependency>

<dependency>
    <groupId>com.dyuproject.protostuff</groupId>
    <artifactId>protostuff-core</artifactId>
    <version>1.0.11</version>
</dependency>
```

创建一个俱乐部实体类：

```java
public class Club implements Serializable {

    private int id;
    private String name;
    private String info;
    private Date createDate;
    private int rank;

    public Club(int id, String name, String info, Date createDate, int rank) {
        this.id = id;
        this.name = name;
        this.info = info;
        this.createDate = createDate;
        this.rank = rank;
    }
    
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getInfo() {
        return info;
    }

    public void setInfo(String info) {
        this.info = info;
    }

    public Date getCreateDate() {
        return createDate;
    }

    public void setCreateDate(Date createDate) {
        this.createDate = createDate;
    }

    public int getRank() {
        return rank;
    }

    public void setRank(int rank) {
        this.rank = rank;
    }
    
    @Override
    public String toString() {
        return "Club{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", info='" + info + '\'' +
                ", createDate=" + createDate +
                ", rank=" + rank +
                '}';
    }
}
```

序列化工具类：

```java
public class ProtoStuffSerializeUtils {

    private static Schema<Club> schema = RuntimeSchema.createFrom(Club.class);

    public static byte[] serialize(final Club club) {
        final LinkedBuffer buffer = LinkedBuffer.allocate(LinkedBuffer.DEFAULT_BUFFER_SIZE);
        try {
            return serializeInternal(club, schema, buffer);
        } catch (final Exception e) {
            throw new IllegalStateException(e.getMessage(), e);
        } finally {
            buffer.clear();
        }
    }

    public static Club deserialize(final byte[] bytes) {
        try {
            Club club = deserializeInternal(bytes, schema.newMessage(), schema);
            if (club != null) {
                return club;
            }
        } catch (final Exception e) {
            throw new IllegalStateException(e.getMessage(), e);
        }
        return null;
    }

    private static <T> byte[] serializeInternal(final T source, final Schema<T> schema, final LinkedBuffer buffer) {
        return ProtostuffIOUtil.toByteArray(source, schema, buffer);
    }

    private static <T> T deserializeInternal(final byte[] bytes, final T result, final Schema<T> schema) {
        ProtostuffIOUtil.mergeFrom(bytes, result, schema);
        return result;
    }
}
```

测试：

```java
public static void main(String[] args) {
    Jedis jedis = new Jedis("127.0.0.1", 6379);

    // 序列化
    String key ="club:1";
    Club club = new Club(1, "LA", "湖人", new Date(), 1);
    byte[] clubBytes = ProtoStuffSerializeUtils.serialize(club);
    jedis.set(key.getBytes(), clubBytes);

    // 反序列化
    byte[] resultBytes = jedis.get(key.getBytes());
    Club resultClub = ProtoStuffSerializeUtils.deserialize(resultBytes);
    System.out.println(resultClub);//Club{id=1, name='LA', info='湖人', createDate=Sat Jun 20 12:26:54 CST 2020, rank=1}
}
```

---

#### 连接池

之前的连接方式是直连方式，所谓直连是指 Jedis 每次都会新建 TCP 连接，使用后再断开连接，对于频繁访问 Redis 的场景显然不是高效的使用方式。

生产方式中一般使用连接池的方式对 Jedis 连接进行管理，所有 Jedis 对象预先放在池子中，每次要连接 Redis，只需要在池子中借，用完了再归还给池子。

客户端连接 Redis 使用 TCP 连接，直连的方式每次需要建立 TCP 连接，而连接池的方式是可以预先初始化好的 Jedis 连接，所以每次只需要从 Jedis 连接池借用即可，而借用和归还操作是在本地进行的，只有少量的并发同步开销，远远小于新建 TCP 连接的开销。另外直连的方式无法限制 Jedis 对象的个数，在极端情况下可能会造成连接泄露，而连接池的形式可以有效的保护和控制资源的使用。

|        | 优点                                                         | 缺点                                                         |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 直连   | 简单方便，适用于少量长期连接的场景。                         | 存在每次连接关闭 TCP 连接的开销，资源无法控制可能出现连接泄露，Jedis 对象线程不安全 |
| 连接池 | 无需每次连接都生成 Jedis 对象降低开销，使用连接池的形式保护和控制资源的使用 | 相对于直连比较麻烦，尤其在资源的管理上需要很多参数来保证，一旦规划不合理也会出现问题 |

使用 Jedis 连接池操作的示例：

```java
public static void main(String[] args) {
    // 使用默认连接池配置
    GenericObjectPoolConfig poolConfig = new GenericObjectPoolConfig();
    // 初始化连接池
    JedisPool jedisPool = new JedisPool(poolConfig, "127.0.0.1", 6379);
    // 获取 Jedis 对象
    Jedis jedis = null;
    try{
        jedis = jedisPool.getResource();
        //。。。
    }catch (Exception e){
        e.printStackTrace();
    }finally {
        if(jedis!=null){
            // 不是关闭连接，而是归还连接池
            jedis.close();
        }
    }
}
```

---

### 客户端管理

Redis 提供了客户端相关 API 对其状态进行监控和管理。

#### client list

client list 命令能列出与 Redis 服务端相连的所有客户端连接信息，输出的每一行代表一个客户端信息，每行包括了十几个属性，重要的属性解释：

- id：客户端连接的唯一标识，这个 id 随着 Redis 的连接自增，重启 Redis 后会重置为 0。

- addr：客户端连接的 ip 和端口。

- fd：socket 的文件描述符，与 lsof 命令结果中的 fd 是同一个，如果 fd = -1代表当前客户端不是外部客户端，而是 Redis 内部的伪装客户端。

- name：客户端的名字。

- 输入缓冲区：qbuf、qbuf-free。

  Redis 为每个客户端分配了输入缓冲区，它的作用是将客户端发送的命令临时保存，同时 Redis 会从输入缓冲区拉取命令并执行，输入缓冲区为客户端发送命令到 Redis 执行命令提供了缓冲功能。qbuf 和 qbuf-free 分别代表这个缓冲区的总容量和剩余容量，Redis 没有提供相应的配置来规定每个缓冲区的大小，输入缓冲区会根据输入内容的大小的不同动态调整，只是要求每个客户端缓冲区的大小不能超过 1G，超过后客户端将被关闭。

  输入缓冲区使用不当会产生两个问题：

  - 一旦某个客户端的输入缓冲区超过 1G，客户端将被关闭。
  - 输入缓冲区不受 maxmemory 控制，假设一个 Redis 示例设置了该值为 4G，已经存储了 2G，但如果输入缓冲区使用了 3G，可能会产生数据丢失、键值淘汰、OOM 等情况。

  输入缓冲区过大主要是因为 Redis 的处理速度跟不上输入缓冲区的输入速度，并且每次进入输入缓冲区的命令包含了大量 bigkey，从而造成了输入缓冲区过大，还有一种情况就是 Redis 发生了阻塞，短期不能处理命令，造成客户端输入的命令挤压在了缓冲区。

  监控输入缓冲区异常有两种方法：

  - 定期执行 client list 命令，收集 qbuf 和 qbuf-free 找到异常的连接记录并分析，找出可能出问题的客户端。该方法可以精确分析每个客户端定位问题，但执行速度慢。
  - 通过 info 命令的 info clients 模块，找到最大的输入缓冲区，设置报警阈值。该方法执行速度快，但是不能精确定位客户端。

- 输出缓冲区：obl、oll、omem

  Redis 为每个客户端分配了输出缓冲区，它的作用是保存命令执行的结果返回给客户端，为 Redis 和客户端交互返回结果提供缓冲。输出缓冲区按照客户端的不同分为普通客户端、发布订阅客户端、slave 客户端。obl 代表固定缓冲区的长度，oll 代表动态缓冲区列表的长度，omem 代表使用的字节数。

  监控输出缓冲区的方法和输入缓冲区一样，提供 client list 和 info clients。

----

#### client setName 和 client getName

client setName 用于给客户端设置名字，这样比较容易标识出客户端的来源。

如果想直接查看当前客户端的 name，可以使用 client getName。

client setName 和 client getName 命令可以作为标识客户端来源的一种方式，但是通常来说在 Redis 只有一个应用方使用的情况下，IP 和端口作为表示会更加清晰。当多个应用共同使用一个 Redis，那么此时 client setName 可以作为标识客户端的一个依据。

---

#### client kill

```
client kill ip:port
```

此命令用于杀掉指定 IP 地址和端口号的客户端，由于一些原因需要手动杀掉客户端连接时，可以使用该命令。

----

#### client pause

```
client pause timeout
```

该命令用于阻塞客户端，timeout 是阻塞时间，单位为毫秒，在此期间客户端连接将被阻塞。

适用场景：

- client pause 只对普通和发布订阅客户端有效，对于主从复制无效，因此可以让主从复制保持一致。
- 可以用一种可控的方式将客户端连接从一个 Redis 节点切换到另一个 Redis 节点。

---

#### monitor

monitor 命令用于监控 Redis 正在执行的命令。但是一旦并发量过大，monitor 客户端的输出缓冲会暴涨，可能瞬间会占用大量内存。

---

#### 客户端相关配置

timeout：检测客户端空闲连接的超时时间，一旦空闲时间到了 timeout，客户端将被关闭，如果设置为 0 就不进行检测。

maxclients：客户端最大连接数，这个参数会受到操作系统的限制。

tcp-keepalive：检测 TCP 连接活性的周期，默认值为 0，也就是不进行检测。如果需要设置，建议为 60，Redis 每隔一分钟会对它创建的 TCP 连接进行活性检测，防止大量死连接占用系统资源。

tcp-backlog：TCP 三次握手后，会将接受的连接放入队列中，tcp-backlog 就是队列的大小，默认值是 511，通常来说这个参数不需要调整。

---

### 客户端常见异常

#### 无法从连接池获取连接

JedisPool 中的 Jedis 对象个数是有限的，默认是 8 个。如果对象全部被占用并且没有归还，调用者借用 Jedis 时就会阻塞等待，如果超过了最大等待时间 maxWaitMills 就会抛出异常。

还有一种情况就是设置了 blockWhenExhausted = false，那么调用者发现池子中没有资源时会立即抛出异常而不进行等待。

造成没有资源的原因：

- 客户端：高并发情况下连接池设置过小，供不应求，但正常情况下只需要比默认的 8 个大一点即可。
- 客户端：没有正确使用连接池，例如没有释放。
- 客户端：存在慢查询操作，这些慢查询持有的 Jedis 对象归还速度会比较慢。
- 服务端：客户端正常，服务端由于一些原因造成了客户端命令执行过程的阻塞。

----

#### 客户端读写超时

Jedis 在调用 Redis 时，如果出现了读写超时，会抛出异常，造成该异常的原因：

- 读写超时时间设置得过短。
- 命令本身比较慢。
- 客户端与服务端网络不正常。
- Redis 自身发生了阻塞。

---

#### 客户端连接超时

Jedis 在调用 Redis 时，如果出现了连接超时，会抛出异常，造成该异常的原因：

- 连接超时时间设置得过短。
- Redis 发生阻塞，造成 tcp-backlog 已满。
- 客户端与服务端网络不正常。

---

#### 客户端缓冲区异常

Jedis 在调用 Redis 时，如果出现了客户端数据流异常，会抛出异常，造成该异常的原因：

- 输出缓冲区满。
- 长时间闲置连接被服务端主动断开。
- 不正常并发读写：Jedis 对象同时被多个线程并发操作，可能会出现该问题。

---

#### Lua 脚本正在执行

如果 Redis 当前正在执行 Lua 脚本，并且超过了 lua-time-limit，此时 Jedis 调用 Redis 时就会抛出异常。

#### 加载持久化文件

Jedis 调用 Redis 时，如果 Redis 正在加载持久化文件，那么会抛出异常。

#### Redis 使用内存超过 maxmemory

Jedis 执行写操作时，如果 Redis 的使用内存大于 maxmemor 的设置，会抛出异常。

客户端连接数过大

如果客户端连接数超过了 maxclients，新申请的连接会抛出异常。此时新的客户端连接执行任何命令都会返回错误结果。

一般可从两方面解决：

- 客户端：如果 maxclients 参数不是很小的化，应用方的客户端连接数基本不会超过 maxclients，通常来看是由于应用方对于 Redis 客户端使用不当造成的。此时如果应用方是分布式结构的话，可以通过下线部分应用节点使得 Redis 的连接数先降下来。从而让绝大部分节点可以正常允许，再通过查找程序 bug 或调整 maxclients 进行问题的修复。
- 服务端：如果客户端无法处理，而当前 Redis 为高可用模式，可以考虑做故障转移。

---

### 客户端案例分析

#### Redis 内存陡增

**现象：**

服务端：Redis 主节点内存陡增，几乎用满 maxmemory，而从节点内存并没有变化。

客户端：客户端产生了 OOM 异常，也就是 Redis 主节点使用的内存已经超过了 maxmemory 的设置，无法写入新的数据。

**分析原因：**

① 确实有大量写入，但是主从复制出现问题。

② 如果主从复制正常，可以排查十分由客户端缓冲区造成主节点内存陡增，使用 info clinets 查询相关信息。如果客户端缓冲队列值很大，通过 client list 命令找到 omem 不正常的连接，一般来说为 0，因此不为 0 就是不正常的连接。有可能是因为客户端执行 monitor 造成的。

**处理方法：**

使用 client kil 杀掉这个连接，让其他客户端恢复正常即可。需要注意的有三点：

- 从运维层面禁止 monitor 命令，例如 rename-command 命令重置 monitor 为一个随机字符串。
- 禁止开发人员在生产中使用 monitor。
- 限制输出缓冲区的大小。

----

#### 客户端周期性超时

**现象：**

客户端：客户端出现大量超时，并且是周期性的。

服务端：没有明显的异常，只是有一些慢查询操作。

**分析原因：**

① 网络：服务端和客户端之间的网络出现周期性问题，网络正常。

② Redis 本身：观察 Redis 的日志统计，无异常。

③ 客户端：发现只要慢查询出现，就会连接超时，因此是慢查询导致了连接超时。

**处理方法：**

- 从运维层面，监控慢查询，超过阈值就发出警报。
- 从开发层面，避免不正确的使用。

---

### 总结

RESP 保证了客户端与服务端的正常通信，是各种编程语言开发客户端的基础。

要选择社区活跃客户端，在实际项目中使用稳定版本的客户端。

区分 Jedis 直连和连接池的区别，在生产环境应该使用连接池。

Jedis.close() 在直连下是关闭连接，在连接池则是归还连接。

客户端输入缓冲区不能配置，强制限制在 1G 以内，但是不会受到 maxmemory 限制。

客户端输出缓冲区支持普通客户端、发布订阅客户端、复制客户端配置，但不会受到 maxmemory 限制。

Redis 的 timeout 配置可以自动关闭闲置客户端，tcp-keepalive 参数可以周期性检查关闭无效 TCP 连接。

monitor 命令虽然好用，但是在高并发下存在输出缓冲区暴涨的可能性。

info clients 帮助开发和运维找到客户端可能存在的问题。

----

## 持久化

Redis 支持 RDB 和 AOF 两种持久化机制，持久化功能有效地避免因进程退出造成的数据丢失问题，当下次重启时利用之前持久化的文件即可实现数据恢复。

### RDB

RDB 持久化是把当前进程数据生成快照保存到硬盘的过程，触发 RDB 持久化过程分为手动触发和自动触发。

#### 触发机制

手动触发分别对应 save 和 bgsave 命令：

- save：阻塞当前 Redis 服务器，直到 RDB 过程完成为止，对于内存比较大的实例会造成长时间阻塞，线上环境不建议使用。
- bgasve：Redis 进程执行 fork 操作创建子进程，RDB 持久化过程由子进程负责，完成后自动结束。阻塞只发生在 fork 阶段，一般时间很短。

bgsave 是针对 save 阻塞问题做的优化，因此 Redis 内部所有涉及 RDB 的操作都采用 bgsave 的方式，而 save 方式已经废弃。除了手动触发外，Redis 内部还存在自动触发 RDB 的持久化机制，例如：

- 使用 save 相关配置，如 save m n，表示 m 秒内数据集存在 n 次修改时，自动触发 bgsave。
- 如果从节点执行全量复制操作，主节点自动执行 bgsave 生成 RDB 文件并发送给从节点。
- 执行 debug reload 命令重新加载 Redis 时也会自动触发 save 操作。
- 默认情况下执行 shutdown 命令时，如果没有开启 AOF 持久化功能则自动执行 bgsave。

---

#### 流程说明

bgsave 是主流的触发 RDB 持久化的方式，运作流程如下：

- 执行 bgsave 命令，Redis 父进程判断当前是否存在正在执行的子进程，如 RDB/AOF 子进程，如果存在 bgsave 命令直接返回。
- 父进程执行 fork 操作创建子进程，fork 操作过程中父进程会阻塞，通过 info stats 命令查看 latest_fork_usec 选项，可以获取最近一个 fork 操作的耗时，单位微秒。
- 父进程 fork 完成后，bgsave 命令返回"Background saving started"信息并不再阻塞父进程，可以继续响应其他命令。
- 子进程创建 RDB 文件，根据父进程内存生成临时快照文件，完成后对原有文件进行原子替换。执行 lastsave 命令可以获取最后一次生成 RDB 的时间，对应 info 统计的 rdb_last_save_time 选项。
- 进程发送信号给父进程表示完成，父进程更新统计信息，具体见 info Persistence 下的 rdb_* 相关信息。

----

#### RDB 文件的保存

**保存**

RDB 文件保存在 dir 配置指定的目录下，文件名通过 dbfilename 配置指定。可以通过执行 `config  set dir {newDir}` 和 `config set dbfilename {newFileName}` 运行期动态执行，当下次运行 RDB 文件会保存到新目录。

当遇到坏盘或磁盘写满情况时，可以通过 `config  set dir {newDir}`  在线修改文件路径到可用磁盘路径，之后执行 bgsave 进行磁盘切换，同样适用于 AOF 持久化文件。

**压缩**

Redis 默认采用 LZF 算法对生成的 RDB 文件做压缩处理，压缩后的文件远远小于内存大小，默认开启，可以通过参数 `config set rdbcompression {yes|no}` 动态修改。

虽然 RDB 会消耗 CPU，但可大幅降低文件的体积，方便保存到硬盘或通过网络发送给从节点，因此线上建议开启。

**校验**

如果 Redis 加载损坏的 RDB 文件时拒绝执行，可以使用 redis-check-dump 工具检测 RDB 文件并获取对应的错误报告。

---

#### RDB 的优缺点

**优点**

RDB 是一个紧凑压缩的二进制文件，代表 Redis 在某个时间点上的数据快照。非常适合于备份，全量复制等场景。例如每 6 个消时执行 bgsave 备份，并把 RDB 文件拷贝到远程机器或者文件系统中，用于灾难恢复。

Redis 加载 RDB 恢复数据远远快于 AOF 的方式。

**缺点**

RDB 方式数据无法做到实时持久化/秒级持久化，因为 bgsave 每次运行都要执行 fork 操作创建子进程，属于重量级操作，频繁执行成本过高。针对 RDB 不适合实时持久化的问题，Redis 提供了 AOF 持久化方式。

RDB 文件使用特定二进制格式保存，Redis 版本演进过程中有多个格式的 RDB 版本，存在老版本 Redis 服务无法兼容新版 RDB 格式的问题。

---

### AOF

AOF 持久化以独立日志的方式记录每次写命令，重启时再重新执行 AOF 文件中的命令达到恢复数据的目的。AOF 的主要作用是解决了数据持久化的实时性，目前是 Redis 持久化的主流方式。

#### 使用 AOF 

开启 AOF 功能需要设置：`appendonly yes`，默认不开启。AOF 文件名通过 appendfilename 配置，默认文件名 `appendonly.aof` 。保存路径同 RDB 方式一致，通过 dir 配置指定。

AOF 的工作流程操作：命令写入 append、文件同步 sync、文件重写 rewrite、重启加载 load：

- 所有的写入命令会追加到 aof_buf 缓冲区中。
- AOF 缓冲区根据对应的策略向硬盘做同步操作。
- 随着 AOF 文件越来越大，需要定期对 AOF 文件进行重写，达到压缩的目的。
- 当 Redis 服务器重启时，可以加载 AOF 文件进行数据恢复。

---

#### 命令写入

AOF 命令写入的内容直接是文本协议格式，采用文本协议格式的理由如下：

- 文本协议具有很好的兼容性。
- 开启 AOF 后，所有写入命令都包含追加操作，直接采用协议格式，避免了二次处理开销。
- 文本协议具有可读性，方便直接修改和处理。

AOF 把命令追加到缓冲区的原因：

Redis 使用单线程响应命令，如果每次写 AOF 文件命令都直接追加到硬盘，那么性能完全取决于当前硬盘负载。先写入缓冲区中还有另一个好处，Redis 可以提供多种缓冲区同步硬盘策略，在性能和安全性方面做出平衡。

---

#### 文件同步

Redis 提供了多种 AOF 缓冲区文件同步策略，由参数 appendfsync 控制，不同值的含义如下：

- always：命令写入缓冲区后调用系统 fsync 操作同步到 AOF 文件，fsync 完成后线程返回。

  配置为 always 时，每次写入都要同步 AOF，性能较低，不建议配置。

- everysec：命令写入缓冲区后调用系统 write 操作，write 完成后线程返回。fsync 同步文件操作由专门线程每秒调用一次。

  配置为 everysec 是建议的策略，也是默认配置，做到兼顾性能和数据安全。

- no：命令写入缓冲区后调用系统 write 操作，不对 AOF 文件做 fsync 同步，同步硬盘操作由操作系统负责，周期通常最长 30 秒。

  配置为 no 时，由于操作系统每次同步 AOF 文件的周期不可控，而且会加大每次同步硬盘的数据量，虽然提升了性能，但安全性无法保证。

write 会触发延迟写机制，Linux 内核提供页缓冲区来提高磁盘 IO 性能。write 操作在写入系统缓冲区后直接返回，同步硬盘操作依赖于系统调度机制，同步文件之前如果系统故障，缓冲区内数据将丢失。

fsync 针对单个文件操作，强制硬盘同步，将阻塞直到写入硬盘完成后返回，保证了数据持久化。

----

#### 重写机制

随着命令不断写入 AOF，文件会越来越大，为了解决这个问题，使用重写机制来压缩文件体积。AOF 文件重写是把 Redis 进程内的数据转化为写命令同步到新 AOF 文件的过程。

重写后 AOF 文件变小的原因：

- 进程内已经超时的数据不再写入文件。
- 旧的 AOF 文件含有无效命令，重写使用进程内数据直接生成，这样新的 AOF 文件只保留最终数据写入命令。
- 多条写命令可以合并为一个，为了防止单条命令过大造成客户端缓冲区溢出，对于 list、set、hash、zset 等类型操作，以 64 个元素为界拆分为多条。

AOF 重写降低了文件占用空间，另一个目的是：更小的 AOF 文件可以更快地被 Redis 加载。

AOF 重写分为手动触发和自动触发：

- 手动触发：直接调用 bgrewriteaof 命令。
- 自动触发：根据 auto-aof-rewrite-min-size 和  auto-aof-rewrite-percentage 参数确定自动触发时机。

**AOF 重写流程**

- 执行 AOF 重写请求，如果当前进程正在执行 AOF 重写，请求不执行并返回，如果当前进程正在执行 bgsave 操作，重写命令延迟到 bgsave 完成之后再执行。

- 父进程执行 fork 创建子进程，开销等同于 bgsave 过程。

- 主进程 fork 操作完成之后继续响应其他命令，所有修改命令依然写入 AOF 缓冲区并同步到硬盘，保证原有 AOF 机制正确性。

  由于 fork 操作运用写时复制技术，子进程只能共享 fork 操作时的内存数据。由于父进程依然响应命令，Redis 使用 AOF 重写缓冲区保存这部分新数据，防止新 AOF 文件生成期间丢失这部分数据。

- 子进程根据内存快照，按照命令合并规则写入到新的 AOF 文件。每次批量写入数据量默认为 32 MB，防止单次刷盘数据过多造成硬盘阻塞。

- 新 AOF 文件写入完成后，子进程发送信号给父进程，父进程更新统计信息。

  父进程把 AOF 重写缓冲区的数据写入到新的 AOF 文件，使用新的 AOF 文件替换旧文件，完成重写。

----

**重启加载**

AOF 和 RDB 文件都可以用于服务器重启时的数据恢复。Redis 持久化文件的加载流程：

- AOF 持久化开启且存在 AOF 文件时，优先加载 AOF 文件。
- AOF 关闭时且存在 RDB 文件时，记载 RDB 文件。
- 加载 AOF/RDB 文件成功后，Redis 启动成功。
- AOF/RDB 文件存在错误导致加载失败时，Redis 启动失败并打印错误信息。

----















