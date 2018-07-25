#  一、MySQL优化

MySQL优化从哪些方面入手：

### （1）存储层（数据）

构建良好的数据结构。可以大大的提升我们SQL语句的性能。 

###  （2）设计层(单台服务器) 

表结构的设计，表的引擎的选择，字段的索引的选择。

### （3）架构层(多台服务器)

多台服务器协同工作的时候的一种架构！主从服务器，一主多从的实现！

主服务器，负责我们的写操作。从服务器就可以实现我们的读操作！

### （4）SQL语句层 

写出一个符合我们业务的SQL语句是很重要。但是你能写出一个继符合业务的，又能在性能上有所要求的SQL就是更重要了 。

# 二、MyISAM与InnoDB的索引结构

> 数据结构：B+Tree
>
> 现在我们硬盘上的数据，基本上都是使用的这种数据结构来进行存储数据的。
>
> 操作系统上面的文件也是使用这种B+Tree的数据结构！

## 1、非聚簇（集）结构 

myisam主键索引：

> 主键索引有2个文件，有一个是索引文件，一个是数据文件。
>
> 通过索引查找数据的时候，是通过找到索引下面挂载的数据编号，通过这个编号找到我们的数据区，取出数据。

>  myisam的普通索引与主键索引在硬盘上面的存储方式，都是一样的。
>
> Mysql服务器实现的不同点是主键是唯一不能重复的，普通是可以重复的。

## 2、聚簇（集）结构 

innodb主键索引： 

主键与数据是在一起的，所有的Innodb引擎，表数据与表索引在一起，只有一个文件。

索引的作用：

索引是排过序数据，索引会让我们在遍历的时候，少遍历很多数据，所以会加速我们的查询。

> 使用索引是SQL语句的where条件，必须是有索引的字段！
>
> innodb如果使用非主键进行查询的时候，会出现我们常说的二次遍历！ 
>
> 第一次是遍历普通索引，找到普通索引上面的主键编号；
>
> 第二次是遍历主键索引，找到想要的数据，返回查询结果。

索引覆盖：查询的时候使用到了索引，并且想要获取的数据，都是建立索引的字段，就会出现索引覆盖现象。 

innodb主键索引的重要性：innodb引擎的表，必须有主键！

不创建主键索引时，它就会去找这个表里面某个字段来进行主键索引创建。找不到合适的字段，innodb会自动创建一个主键索引 。

无论如何Mysql服务器都会给innodb引擎的表创建主键！

> 我们在创建innodb引擎表的时候，必须给innodb创建主键。
>
> 一定要创建一个整型的主键，并且是自增长的，主键不能用于业务处理。

> 自增长的重要性：
>
> innodb引擎的表，插入操作的时候，一定要让主键字段是顺序插入的；如果非顺序插入，每次插入的时候，都会进行排序，数据就会整体移动。移动过程要消耗IO，数据量特别大时，占用的时间会特别的长。
>
> 使用自增长，将不会有这些问题！
>
> 创建表的时候，默认选择innodb就好，可以实现更多的业务需求。

# 三、MySQL存储引擎 

## 1、什么是存储引擎

存储引擎就是存储数据到硬件或者内存中的一种技术！

## 2、MyISAM与InnoDB引擎的区别 

> myisam是支持表锁，innodb是支持行锁（有条件的行锁，必须有索引字段）
>
> myisam是不支持事务，innodb是支持事务的
>
> myisam是支持地理空间索引，innodb是在5.6.2版本之上才支持
>
> myisam是支持压缩的，innodb是不支持压缩
>
> myisam是支持全文索引的，innodb是不支持全文索引

## 3、存储引擎插入数据的执行顺序 

> innodb的表会自动的排序；myisam的表不会自动排序。所以以后你在请求别人的接口的时候，一定要注意，不要相信别人的排序。自己排一下。

## 4、MyISAM引擎的压缩

### （1）对myisam引擎表数据进行压缩

找到相应的工具，linux为例，在目录`/usr/local/bin/`下，压缩工具：`myisampack`，解压工具：`myisamchk`

### （2）压缩工具介绍：

使用：`myisampack   表的绝对路径(没有表的后缀) `

eg：`/usr/local/mysql/bin/myisampack /usr/local/mysql/data/test/myisam`

> 如果数据表太小，则会提示不能压缩。

虽然可以压缩成功，但是索引会损坏，所以在压缩完成后应该重建索引。

### （3）重建索引

`myisamchk  -rq  表的绝对路径(没有表的后缀) `

eg：`/usr/local/mysql/bin/myisamchk -rq /usr/local/mysql/data/test/myisam`

执行结束后，索引会修复成功

### （4）刷新数据回硬盘【重点】

进入mysql中，执行`flush tables;`即可

> 说明：压缩的表，只能是只读的。不能修改，不能更新。 

### （5）解压缩

`myisamchk --unpack 表的绝对路径（没有表的后缀）`

eg：`/usr/local/mysql/bin/myisamchk --unpack /usr/local/mysql/data/test/myisam`

完成后可以查看表数据：在linux下执行`ll -h /usr/local/mysql/data/test/`

### （6）必须刷新回硬盘：

在MySQL下执行：`flush tables;`

##  5、MyISAM引擎与InnoDB引擎的备份与还原 

###  （1）mysql服务器的备份与还原： 

#### 备份

备份表：`mysqldump –u root –p  库 [表] > 备份的路径`

eg：`/usr/local/mysql/bin/mysqldump -u root -p test innodb > /`

备份库：`mysqldump  -u root –p  库 > 备份的路径`

eg：`/usr/local/mysql/bin/mysqldump -u root -p test > /tmp/test.sql`

备份：带库名：`mysqldump  -uroot –p –B 库 > 备份的路径 `

eg：`/usr/local/mysql/bin/mysqldump -u root -p -B test > /tmp/testb.sql`

#### 还原

还原表：`mysql –u root  -p  库名  <  备份sql的文件路径`

eg：`/usr/local/mysql/bin/mysql -u root -p test < /tmp/innodb.sql`

还原库：`mysql –uroot –p  < 备份SQL的库的路径`

> 备份文件里面是sql语句，是可以修改的。但是我们不应该修改它。备份就是数据的拷贝，修改了之后，一切都变味了。

## 6、其他存储引擎介绍 

###  （1）Memory 

数据置于内存的存储引擎，拥有极高的插入，更新和查询效率。但是会占用和数据量成正比的内存空间。并且其内容会在MySQL重新启动时丢失 。

### （2）Archive 

归档存储引擎，只支持数据的查询和写入。

经常用于存储日志等相关信息。

### （3）merge 

这个可以把很多myisam表连接在一起，我们查询数据的时候，就感觉在一张里面表里面使用。myisam表使用量大的时候，merge引擎是经常的出现。

> 查看MySQL引擎：`show engines;`
>
> 如果是merge，则会显示`mrg_myisam = merge `

# 四、列类型的选择

## 1、数值类型的选取 

tinyint占的字节数；int占的字节数；

有符号（-128，127）与无符号（0，255）的范围。

有一个字段，我们一定要判断它所在的范围，如果判断的不准，可以适当放大一点，保存数据能够全部存储进去。

选择类型的时候，尽量是选择最小的类型，就能够把字段所有的范围包含进去的。 

> 示例：年龄（人）：0 - 200
>
> 选择tinyint  无符号的
>
> 对ID选值的说明：目前来说ID默认选择int，无符号即可！
>
> float 是取近似值！
>
> double 四舍五入；很多商店都是选择的这种方式。
>
> 单位很重要：单位是元的时候，取2个小数点是没有问题的。

## 2、字符串类型的选取 

char(10)  varchar(10)  ：这个10是10个字符 

char ：0 – 255个字符！无论是utf8还是gbk！

varcahr ：0-65535个字节！

utf8 ：一个汉字占3个字节； gbk ：一个汉字占2个字节。

varchar 最大长度是：

utf8：(65535 – 1 - 2) / 3  = 21844能够存储的字符

gbk： (65535 – 1 - 2) / 2  = 32766能够存储的字符

> varchar可以使用的宽度是动态变化的 。

blob的是存储二进制的：如果MySQL要存储图片，选择longblob即可。

> 创建char字段的表，存储的字符数超过255时会报错，但是5.5版本的时候，超过的长度会自动截取；5.7版本的时候，会报错。 
>
> 至于是自动截取还是报错，都是可以配置的。

> 对于varchar对宽度的影响：
>
> 创建表时：`CREATE TABLE varc(name varchar(21844),age int)ENGINE=MYISAM CHARSET=utf8;`
>
> 这时因为多加了个age int这个字段，就创建失败了。
>
> 把多加的字段的空间留出来，就可以创建成功了：`CREATE TABLE varc(name varchar(21842),age int)ENGINE=MYISAM CHARSET=utf8;`

## 3、时间类型的选取 

时间戳

PHP：time();

MYSQL：unix_timestamp();

如果存储时间的时候，不显示要求的时候，就使用时间戳存储，选择int就好 。

## 4、枚举类型与集合类型

set与enum底层是使用整型来实现的。只是我们传输的时候，是传的相应的值过来。但是这种设置不够灵活。 

tinyint实现：1 = > man    2 => woman    3 => ...

php配置文件： array(1=>man, 2=>woman)

php代码使用的时候，直接拿来替换就可以。

## 5、IP与整型数据的转换 

mysql ：inet_aton  ；IP地址转成整型

`select inet_aton('192.168.111.110');`此时这个IP会转成`3232264046`

mysql ：inet_ntoa  ；整型转成IP地址 

`select inet_ntoa(3232264046);`此时这个整型会转成`192.168.111.110`这个IP

php：ip2long  ；ip转成整型 

`echo ip2long('192.168.111.110');`会打印出`-1062703250`

php ：long2ip  ；整型转成IP 

`echo long2ip(-1062703250);`会打印出`192.168.111.110`

> 使用php转成的整型，就要使用PHP的函数转成IP地址。 
>
> 不同的语言转出来的值可以不一样。所有一定要使用什么语言转的，就要使用什么转回去。

# 六、MySQL中的执行计划 

## 1、什么是MySQL执行计划 

就是把SQL语句的执行效果显示出来。

## 2、执行计划的作用

让我们清楚的知道SQL语句干了什么，满足不满足我们的理想！

## 3、执行计划的基本语法 

`explain 查询SQL语句`

eg：`explain select * from innodb limit 1;`

## 4、explain详解 

上节的查询语句会查询出的信息，解释如下：

> type ：const | ref | range | index | all
>
> ​              小                                              大
>
> ​	const ：基本上使用等于的时候才会出现这个值
>
> ​	ref ：小范围结果的时候，就会这个值
>
> ​	range ：大范围结果的时候，就会这个值
>
> ​	index ：整个索引查询，遍历索引
>
> ​	all ：全表查询，使用索引了。
>
> Extra：
>
> Using filesort  ：文件排序，这个就很慢了。可以优化
>
> Using temporary  ：使用了临时表，这个就可以优化
>
> Using index  ：使用了索引覆盖
>
> Using where  ：使用了数据过滤
>
> 索引长度：创建表的时候，只是通知数据库帮助创建了索引。索引是怎么创建的，如何创建的，都是mysql服务器自己做的事情。所有这个长度，你只需要拿来对性能优化的时候，对比即可。

## 5、数据库中索引的设计 

### （1）什么是索引

存储引擎以一种数据结构进行存储的数据。

这种数据结构的数据让我们查询数据时非常的高效。

### （2）非常重要的索引类型 

#### ① 主键索引（primary key）

删除主键：`alter table table_name drop primary key`

eg：`alter table innodb drop primary key;`但是此时会报错，因为主键是自动增长的，因此不能删除。

删除自动增长：`alter table table_name modify id int(11) unsigned not null;`

eg：`alter table innodb modify id int(11) unsigned not null;`此时自动增长就删除了，再进行删除主键就能执行成功。

添加主键：`alter table table_name add primary key (id)`

eg：`alter table innodb add primary key (id);`

#### ② 唯一索引（unique key）

> 主键：一张表里面只能有一个；主键值是不能够重复的。可以使用自动增长。
>
> 唯一：一张表里面可以有多个；主键值是不能够重复的。可以使用自动增长。

添加唯一索引：`alter table table_name add unique key 取个索引名(字段) ;`

eg：`alter table innodb add unique key myname(name);`

删除索引：`alter table table_name drop key 索引名;`

eg：`alter table innodb drop myname;`

#### ③ 普通索引（key）

普通：普通索引在值是可以重复的，一张表可以创建多个，没有限制的。

添加普通索引：`alter table table_name add key 取个名称(字段);`

eg：`alter table innodb add key yourage(age;)`

删除普通索引：`alter table table_name drop key 索引名称;`

eg：`alter table innodb drop key yourage;`

> 重点说明：普通索引才是使用的非常广泛，而且经常需要我们操作的。

####  ④ 复合（联合）索引 

创建索引的，以多个字段共同创建就是复合索引。复合索引的类型根据创建的关键字来决定 。这些关键字，就是上面的三种索引。所以你创建的是哪一个索引，就有哪一种索引的特性。 

复合索引：很多时候，都是普通形式存在的。

添加：`alter table table_name add key 取个索引名(字段1,字段2,……) ;`

eg：`alter table innodb add key name_age(name, age);`

删除符合索引：`alter table table_name drop key 索引名;`

eg：`alter table innodb drop key name_age;`

#### ⑤ 总结

添加索引的时候：

> 主键索引：表里面唯一的，值不能重复
>
> ​         `alter table table_name add primary key (id)`
>
> 唯一索引：表里面可以有多个，值不能重复
>
> ​        ` alter table table_name add unique key 索引名(字段)`
>
> 普通索引：表里面可以有多个，值可以重复
>
> ​         `alter table table_name add key 索引名(字段)`
>
> 普通复合索引：
>
> ​         `alter table table_name add key 索引名(字段,字段,……)`
>
> 删除索引的时候：
>
> 主键索引：
>
> ​         `alter table table_name drop primary key`
>
> 唯一索引|普通索引|普通复合索引
>
> ​         `alter table table_name drop key 索引名`
>
> 复合索引 === 联合索引， 这2个名称是一个意思！ 

### （3）索引字段的使用原则

1）等于匹配

验证只有使用id的时候，才会使用到主键索引

`explain select * from innodb where name = 'xiao1'\G`

上面的语句中，name字段是没有索引的，索引没有办法使用到索引。

`explain select * from innodb where id = 1\G`

使用索引字段的时候，使用到了索引，并且范围还是很小。

> 能不能使用索引，根据where后面的条件的来决定的。

2）大于，小于匹配

说明：在实际工作，大于小于尽量不使用，能够使用in替代的，就使用in代替。

大于：`explain select * from innodb where id > 0\G`

小于：`explain select * from innodb where id < 0\G`

> 大于小于在目前看来都使用了索引；但是如果数据量变化了，这个结果会改变的。 

3）最左原则

like查询：`explain select * from innodb where name like 'xiao1%'\G`

结果：数据会影响索引的使用。

如果将`%`写在左边：`explain select * from innodb where name like %xiao1'\G`

结果：没有使用索引。

> 说明：在使用索引的时候，值的左边一定要保持是定值。定值就是不会改变的。

4）列独立

`explain select * from innodb where id + 1 = 2\G`此时，这个`id + 1`不是一个定值，因此不能使用索引。

修改一下：`explain select * from innodb where id = 2 - 1\G`，字段id这边不要有其它值时，就会使用到索引

5）复合索引

创建复合索引：`alter table innodb add key name_fu(name,age,sex);`

使用索引进行查询：`explain select * from innodb where name = 'xiao1' and age = 18 and sex = 1\G`

此时使用到了索引，此时索引的长度`key_len: 64`

只使用name和age索引查询：`explain select * from innodb where name = 'xiao1' and age = 18\G`

此时的索引的长度`key_len: 63`

只使用name字段索引查询：`explain select * from innodb where name = 'xiao1'\G`

此时的索引的长度`key_len: 62`

> 创建复合索引的时候：name字段的索引长度是62；age与sex分别是1

只使用name和sex字段索引查询：`explain select * from innodb where name = 'xiao1' and sex = 1\G`

说明：只使用了name字段长度的索引。sex是没有被使用的。 

只使用age和sex字段索引查询：`explain select * from innodb where age= 18 and sex = 1\G`

此时没有使用到索引。

只使用age字段的索引查询：`explain select * from innodb where age= 18\G`

只使用sex字段的索引查询：`explain select * from innodb where sex= 1\G`

此时这两种查询也都没有使用到索引。

> 总结：复合索引在创建的时候的顺序，影响了使用的顺序。
>
> 创建的时候是什么顺序，使用的时候就是什么顺序。
>
> 创建的时候是最左边是name  然后是age ; 最后是sex；所以你使用的时候，要保持最左原则。从左边开始使用。
>
> sql发送给mysql服务器的时候，mysql服务器会解析，然后在优化。优化的时候，就会重新拼接sql语句。 

6）OR表达式：or最好不要使用，性能特别低。

7）mysql对索引的自动判断介绍 

mysql服务器才是管理索引的使用者。我们只能告诉mysql服务器，去创建一个什么样的索引。你在使用索引的时候，一切的规则都是按MYSQL服务器，内部的规则去运行的。所以你只需要记住这些规则就可以了。通过explain看出来效果。选择出最佳的就可以了 。

比如：当你的字段有索引的时候。但是你查询的数据，有很多的时候。MYSQL服务器就会自动的判断。使用索引与直接在硬盘上面查数据，那个更快。经过它自己的判断 。会决定最终使用那一个。如果它认为直接扫描硬盘更快。就会不经过索引。直接使用全表扫描。

## 4、适合使用索引的应用场景  

### （1）索引的选择性：

字段里面不重复的值的总数  /  字段值的总数  = 0 - 1 

这个值应该大于0.4制作索引的效果就可以了。 

统计总数：

count：`select count(age) from innodb;`

显示统计字段里不重复的值：`select distinct sex from innodb;`

统计不重复的总数：`select count(distinct sex) from innodb;`

计算：`select count(distinct sex) / count(sex) from innodb;`

> 这个值匹配度太低了，所有不适合做索引。所有性别是不能做索引。做索引就太浪费资源。

### （2）数据的量：

数据表的数据量，在1000以上的就可以制作索引 。重复值太多的字段不适合做索引。

## 5、SQL语句优化

myisam表在使用统计的时候，直接使用count(\*)就好了。因为我们myisam表的最后面有一个隐藏的值就是我们表的数量。你使用count(*)其实就是把这个值给取出来。 

innodb表在使用统计的时候，直接使用count(字段名)就好了，各种说明都说的不适合使用\*。但是个人测试数据量在200W的时候，使用*比字段名要快一些！ 

在使用group by时，它会自动排序，可以使用order by null取消排序

> 非常重要的性能说明：大量数据的时候，分页的实现！ `limit N, 10;`
>
> 比如：`select * from myisam limit 300000, 10;`此时查询出结果说消耗的时间很长
>
> 优化：`select * from myisam where id > 300000 limit 10;`此时消耗的是就就很少了。
>
> 所以：id：请一定不要更新或者删除它。 



