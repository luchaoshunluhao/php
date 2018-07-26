> Redis与Memcache的区别：
>
> 1）redis是可以持久化（存储到硬盘），memcache只能在内存中
>
> 2）redis是单核运行的，memcache是多核运行的。
>
> 3）redis的写速度与memcache的写速度差不多。读速度memcache比redis快。
>
> 4）redis支持很多种数据结构，memcache只支持key=>value
>
> 5）redis的value最大支持512M，memcache的value最大支持:1M

## 安装Redis

英文：www.redis.io 

中文：www.redis.cn 

学习：doc.redisfans.com

将下载好的压缩包上传到linux中解压后进入目录`/redis-4.0.6`

### ①直接使用`meke`安装

### ②可以指定安装的位置：

`make PREFIX=/working/redis-4.0.6 install `

### ③安装完成后可以查看一下目录：

`/working/redis-4.0.4/bin`

在该目录下有一个redis-server，这个是服务端软件，可以使用--help参数查看帮助信息，确定启动方式：

`./redis-server /etc/redis/6379.conf `

### ④准备好配置文件，是解压目录里的`redis.conf`，复制到redis目录

`mkdir /working/redis-4.0.6/conf`

`cp redis.conf /working/redis-4.0.6/conf/`

### ⑤查看配置文件

`vim /working/redis-4.0.6/conf/redis.conf`

> 在69行：`bind 127.0.0.1`，这个绑定，是客户端要使用服务器的时候，连接的ip地址。客户端连接的时候，只能使用127.0.0.1连接服务器！
>
> 在92行有一个端口号：6379
>
> 在136行：`daemonize yse，这个参数是如果是后台运行是yes，前台是no
>
> 在158行：`pidfile /var/run/redis_6379.pid`，这个是pid号保存的位置。也就是父进程的pid号，保存在这个文件里面。 
>
> 在186行：`databases 16`，默认有16个库；库的顺序是从0开始的，一直到15。
>
> 在253行：`dbfilename fump.rdb`，持久化之后的文件名称
>
> 在263行：`dir ./`配置文件保存的路径

### ⑥启动redis服务器

`/working/redis-4.0.6/bin/redis-server /working/redis-4.0.6/conf/redis.conf`

## Redis快速入门 

### ①找到客户端：

`/working/redis-4.0.6/bin/redis-cli`

### ②查看帮助信息

`/working/redis-4.0.6/bin/redis-cli --help`

根据帮助信息，可以直接使用参数-h和-p

### ③开始连接

`/working/redis-4.0.6/bin/redis-cli -h 127.0.0.1 -p 6379`

> -h的参数是配置文件中bind的ip，如果绑定的是127.0.0.1的话，在登录客户端-p不是127.0.0.1时会连接失败

##Redis支持的数据类型 

> 常用的五种数据类型：字符串（string）、散列（hashes），列表（lists）、集合（sets）、有序集合（sorted sets）

### ①String（字符串） 

`set key value`：设置一个值；`set name lu`

`get key`：获得一个值；`get name`

`mset k v k v k v`：同时设置多个值；`mset age 19 sex 1`

`mget k k k`：同时获得多个值；`mget name age sex`

`del key`：删除；`del sex`

`incr age`：age自动加1

`decr age`：age自动减1

`incrby age 11`：age自动加11

`incrby age -11`：age自动加-11

`decrby age 11`：age自动减11

`decrby age -11`：age自动减-11

> redis是可以使用负数；memcached是不能使用负数。 
>
> 整型范围：redis是2^64 有符号；memcached是2^64无符号。 
>
> incrby不能超过最大范围，超过则报错；
>
> decrby不能超过最小范围，超过则报错

> memcached在最大值的时候，增加加会到0然后继续加。redis最大值到的时候，继续加就要报错。
>
> memcached在最小值的时候，继续减还是0；redis最小值的时候，继续减就要报错。

### ②Hash（哈希表）

> 数据存储与我们的数组是一样的：key  =>  array( key =>v1, key => v2, key => v3))

hset：设定值；`hset  key  field value  field value `；其中field不能重复！

`hset hash name lu age 12 sex 1`

hget：获得值；`hget key field`；

`hget hash name`。如果重复设置，设置的值会被覆盖

hmset：设定多个值；`hmset key field value field value `

`hmset xiao name ming age 19 sex 1`

hmget：获得多个值；

`hmget xiao name age sex`

hgetall：获得hash表的field与value；`hgetall key`

`hgetall xiao`

hlen：获得value值的个数

`hlen hash`

hdel：删除hash表里面的field值；`hdel key field `

`hdel hash name sex`

### ③List（列表）

lpush：给一个列表从左边添加内容；`lpush list lu zhang cheng`

lrange：查看list的内容；`lrange list 0 -1`。

> 列表的值，是可以重复的。
>
>  1   2   3   4   5
>
> 0是开始位置；-1是结束位置。如果是-1则是显示所有值

lpop：从左边弹出一个值；`lpop list`

llen：查看list长度；`llen list`

rpush：从右边插入数据到列表里面 ；`rpush list zhao qian sun li`

rpop：从右边弹出一个值 ；`rpop list`

> 队列：
>
> lpush  左边插入数据；rpop 右边弹出数据。
>
> rpush  右边插入数据；lpop 左边弹出数据。
>
> 堆栈：
>
> lpush  左边插入数据；lpop  左边弹出数据；
>
> rpush  右边插入数据；rpop  右边弹出数据。

### ④Set（集合）

sadd：添加数据；`sadd key v v`

`sadd set zhao qian sun li`

smembers：查看数据

`smembers set`  

> 查询出的是无序集合！

spop：随机弹出

`spop set` 或带个弹出数量：`spop set 2`

scard：查看长度

`scard set`

sinter：交集；`sinter set key`

sdiff：差集；redis里面的差集，是只显示第一个集合的内容。`sdiff set key`

sunion：并集；重复的内容，只显示一次。`sunion set key`

### ⑤SortedSet（有序集合）

zadd：添加数据；`zadd zset 1 name 3 lu 5 zhao`

zcard：查看长度；`zcard zset`

zrange：查看里面的值；`zrange zset 0 -1`

显示排序号：`zrange zset 0 -1 withscores`

zrevrange：倒序，最大值在前面。`zrevrange zset 0 -1`

zincrby：修改排序号；`zincrby zset 10 qian`

负号：`zincrby zset -10 qian`

### ⑥Key（键）

keys ：查看redis里面有多少个key 。`keys *`

> 匹配符：【*】

左边匹配：`keys *t`

右边匹配：`keys s*`

> 这个配置是非常的重要的，我们经常使用它！
>
> 因为使用redis的时候，肯定是数据量特别大的时候，这个时候，我们要去测试数据。有很多类型的测试数据，都是只能使用一次的，使用一次之后，就要删除的。假如这些数据都要存储在redis里面的。所有我们就要使用这种来删除我们不需要的数据！
>
>  
>
> 测试的时候，是要反反复复的测试的。不能只测试一次，所有我们反复测试。如果数据量是
>
> 1000W，测试个10次，就是1亿的数据。所有我们一定是先测试，完成之后，把测试数据删除之后，再来测试。

exists：判断key是否存在，存在显示1，不存在显示0；`exists name`

expire：设置过期时间；`expire age 20` 时间单位：秒

ttl：查看过期时间；`ttl age` 如果过期就获得不到了

type：key的类型；`type name`

select：选择库；默认有16个库，索引是从0开始，到15 。`select 8`

ping：在redis的客户端去ping我们redis的服务端；`ping hello`，发送什么就回复什么，所以会回复'hello'，如果只发送`ping`，如果连接成功则会回复'PONG'

flushdb：清空当前库；`flushdb`

flushall ：清空所有的库 ；`flushall`

>清空的操作，我们平时不要使用的。因为redis里面有其它项目的数据 。

### key命令规范

> 我们在给key进行设置的时候，一定要使用 : 来作为分割符！
>
> quan:6:redis

## 设置客户端登录密码

打开redis服务器配置文件：`vim /working/redis-4.0.6/conf/redis.conf`

在500行左右：`requirepass aabbcc` ，其中的'aabbcc'就是设置的密码，可以任意改

重启redis，需要杀死进程：

查看进程号（PID）：`ps aux | grep redis`

杀进程：kill

> kill的选项参数：-15（默认的），通知主（父）进程，关闭程序。主进程就会回收资源，然后把自己干掉。 
>
> 选项参数：-9（强制杀死），系统通知主进程，你死了然后就把它干掉。它的资源并没有及时回收。 
>
> 建议，默认情况使用-15

`kill -15 redis的进程号`

在启动redis：`/working/redis-4.0.6/bin/redis-server /working/redis-4.0.6/conf/redis.conf`

> kill补充说明：
>
> root启动的是主进程，非root的是子进程。主进程是管理员启动的；子进程是主进程启动的。 
>
> 主进程启动子进程的时候，使用的用户名，与启动多少个。都是配置好的。配置文件里面配置的。
>
> 如果杀死子进程，主进程就会回收，重启启动一下 。如果你把它杀死了。主进程就会重新启动一个新的子进程。 
>
> 如果强制杀死了主进程；子进程就变成了僵死（尸）进程 

> 有关主进程和子进程：
>
> 主进程是用户启动的。子进程就是主进程启动的。并且以副本的方式来运行的。
>
> 主进程就是比子进程多了管理子进程的功能的 。一般情况下主进程是管理子进程的，会接收请求把工作直接给子进程操作处理。 
>
> 子进程，主要就是完成各种工作的。可以接收httpd请求。httpd运行的时候，子进程使用的是配置的用户名来运行程序的。如果子进程死了，主进程就会重新启动一下。
>
> 子进程的运用用户名，运用用户组，就决定了它获得文件时候，能够获得到的权限。

## 在linux命令行操作

`/working/redis-4.0.6/bin/redis-cli key '*'`

执行del删除：`/working/redis-4.0.6/bin/redis-cli del xiao`

批量删除：`/working/redis-4.0.6/bin/redis-cli key '*t' | xargs /working/redis-4.0.6/bin/redis-cli del`

> xargs这个命令存在的意义：
>
> 通过管道的时候，只要有这个参数，它就是告诉左边的命令，你执行一条结果，就给我一条结果。
>
> 当左边在执行的时候，执行一个结果，就立即传给我们右边去执行。这个命令非常的重要。
>
> 使用了该参数：就是执行完一个结果，就传给右边，一个结果可以占10K的内存。传给右边也是占10K。

## 持久化功能

### ①snap shotting快照持久化

就是我们把内存中的内容，压缩之后，直接存储到硬盘中，这种方式在启动的时候，加载速度非常的快！ 

1、打开配置

`vim /working/redis-4.0.6/conf/redis.conf`

> 218-220行：
>
> save   900   1   	//900秒有1个keys改变，就会执行持久化
>
> save   300   10  	//300秒有10个keys改变，就会执行持久化
>
> save   60    10000	// 60秒有1w个keys改变，就会执行持久化
>
> 修改持久化方案：
>
> save 900 1
>
> save 120 5
>
> save 60 10000
>
> 
>
> 253行：
>
> dbfilename dump.rdb		//持久化之后保存的文件名
>
> 
>
> 263行：
>
> dir ./	//持久化之后，保存的文件路径，需要修改成绝对路径，创建保存的文件夹`mkdir /working/redis-4.0.6/data`
>
> 把路径修改成此路径：
>
> `dir /working/redis-4.0.6/data`

启动redis服务器后验证进程和端口号是否存在：

`ps aux | grep redis`

`netstat -tanp | grep redis`

使用客户端登录后写入点数据，查看持久化文件：

`vim /working/redis-4.0.6/data/dump.rdb`

通过这个文件，可以看到在redis里存储的数据，但是都是以二进制保存，所以不要动这个文件

> redis是一个内存服务器，我们要操作的数据都存储在内存里面的。访问速度是非常的快的，但是我们的数据想要存储到硬盘，就要持久化。
>
> 持久化的时候，就有持久化的机制，机制就有快照持久化；这种持久化是通过时间与key的改变来确定的。
>
> 
>
> 默认的配置是别人通过实践而配置好的，所以不用再修改其它配置。

> 被改变的值，在没有持久化的时候，是在内存里面的。这个时候如果服务器有异常，这些在内存里面的值，都会被清空！
>
> 如果服务器异常之后，再次启动，我们就会从持久化的文件里面读取数据到内存，之前没有持久化的数据，就在也找不到了。

### ②append only file （AOF持久化，精细持久化）

> 用户的每一次操作，我们都可以把操作的语句记录在文件里面。当我们开机的时候，就会读取这些语句，再一次的执行，把数据保存到内存里面，因此在开机的时候会比快照持久化慢。

打开配置文件：`vim /working/redis-4.0.6/conf/reids.conf`

> 672行：
>
> `appendonly yes：设置成yes，这个值是yes的时候，就是开启持久化；no代表关闭
>
> 676行：
>
> `appendfilename "appendonly.aof"`：数据保存的文件名
>
> 702行：
>
> `appendfsync everysec`：有三个值，只能选择一个值打开它，它的值介绍一下：
>
> always ：总是保存执行的命令到我们的文件里面。任何一个的改变，都要保存到我们的文件里面。时时的写文件，这个就会和IO相关，我们的速度就会变慢
>
> everysec ：每一秒中持久化一次。每一秒有多少改变都进行持久化！就是把我们一秒之内的写操作，修改操作，写进我们的文件里面
>
> no ：就是根据我们系统的IO来随机操作。
>
> 
>
> 持久化的数据保存在：`/working/redis-4.0.6/data/`

改完配置文件后重启redis，再登录客户端进行数据的修改，然后查看`/working/redis-4.0.6/data/`下面的数据有改变了说明配置成功了。

> 说明：aof持久化与快照持久化；工作中没有绝对的，一切都是以项目实际情况出发的。快照持久化使用的比较多。 

## Redis的主从模式 

### （1）Redis Replication的特点和优势 

> 1）同一个Master可以同步多个Slaves。
>
> 2）Slave同样可以接受其它Slaves的连接和同步请求，这样可以有效的分载Master的同步压力。因此我们可以将Redis的Replication架构视为图结构。
>
> 3）Master Server是以非阻塞的方式为Slaves提供服务。所以在Master-Slave同步期间，客户端仍然可以提交查询或修改请求。
>
> 4）Slave Server同样是以非阻塞的方式完成数据同步。在同步期间，如果有客户端提交查询请求，Redis则返回同步之前的数据。
>
> 5）为了分载Master的读操作压力，Slave服务器可以为客户端提供只读操作的服务，写服务仍然必须由Master来完成。即便如此，系统的伸缩性还是得到了很大的提高。
>
> 6）Master可以将数据保存操作交给Slaves完成，从而避免了在Master中要有独立的进程来完成此操作。

> 主从实现了；实现写读分离；写读分离必须是PHP代码层面上要体现出来。

### （2）Redis Replication的工作原理 

在Slave启动并连接到Master之后，它将主动发送一个SYNC命令。此后Master将启动后台存盘进程，同时收集所有接收到的用于修改数据集的命令，在后台进程执行完毕后，Master将传送整个数据库文件到Slave，以完成一次完全同步。而Slave服务器在接收到数据库文件数据之后将其存盘并加载到内存中。此后，Master继续将所有已经收集到的修改命令，和新的修改命令依次传送给Slaves，Slave将在本次执行这些数据修改命令，从而达到最终的数据同步。

如果Master和Slave之间的链接出现断连现象，Slave可以自动重连Master，但是在连接成功之后，一次完全同步将被自动执行。

> 总结：当从服务器连接上主服务器时候，我们的主服务器就会把数据发送给从服务器，这些数据都是二进制，第一次连接的时候，是全量拷贝。
>
> 之后主服务器有任何数据的修改，都会发送给从服务器，现在发送都是修改的数据，就叫增量拷贝。

只要主从之间断开过，再次连接，就会实现一次全量拷贝。

### （3）如何配置Replication

> 首先得有2台redis：同一台电脑上启动二台。
>
> 端口号不能重复；
>
> pid文件不能重复；
>
> 数据不能放在一起。

准备从服务器的配置文件：

`cp /working/redis-4.0.6/conf/redis.conf /working/redis-4.0.6/conf/slave.conf`

编辑一下配置文件：

> 69行：`bind 127.0.0.1`注释掉。关闭之后，任何ip地址都可以连接，但是必须要有密码
>
> 92行：`port 6380`端口号6379 -> 6380
>
> 158行：`pidfile /var/run/redis_6380.pid`修改pid文件名成"redis_6380.pid"
>
> 263行：`dir /working/redis-4.0.6/sdata`修改数据目录，没有就新建一个`mkdir /working/redis-4.0.6/sdata`
>
> 保存退出！

启动从服务器：

`/working/redis-4.0.6/binredis-server /working/redis-4.0.6/conf/slave.conf`

配置主从同步：

主服务器：端口为6379

从服务器：端口为6380

①进入主服务器配置文件

> 281行：#slaveof <masterip><masterport>，将本行修改一下：
>
> slaveof 127.0.0.1 6379
>
> 288行：#masterauth <master-password>，将本行修改一下：
>
> masterauth aabbcc	//aabbcc是设置的登录密码
>
> 保存退出，重启redis

②登录主服务器和从服务器

观察主服务器和从服务器的数据是否一致，如果一致说明已经同步。

再在主服务器上添加几条测试数据，再观察从服务器是否同步。

此时的从服务器只有读操作，没有写操作，若执行写操作则会报错。

## PHP操作Redis（基本操作） 

### 1、linux上安装redis扩展

得到扩展的压缩包后解压完成后，先生成configure文件，必须进入到解压文件中执行：

`/working/php7/bin/phpize`

通过查看帮助信息`./configure --help`可以有如下参数：`--with-php-config=/working/php7/bin/php-config`

检查环境，生存makefile文件：

`./configure --with-php-config=/working/php7/bin/php-config`

生成二进制文件并安装：`make && make install`

配置php.ini文件：

> 928行左右添加：extension=redis.so

重启Apache后查看扩展安装是否成功

### 2、使用PHP操作Redis 

```php
//连接redis
$redis = new redis();
$redis -> connect('127.0.0.1', 6379);
$redis -> auth('aabbcc'); //登录密码
$redis -> set('test', 'Are you OK?');
```

运行代码后可以到redis客户端里查看是否存入成功。



> PHP实现读写分离：
>
> ```php
> $redis_one = new redis();//主服务器
> 
> $redis_two = new redis();//从服务器
> 
> $redis_one -> set();//在主服务器写数据
> 
> $redis_two -> get();//在从服务器读数据
> ```
