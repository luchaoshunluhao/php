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

innodb表在使用统计的时候，直接使用count(字段名)就好了，各种说明都说的不适合使用\*。但是当测试数据量在200W的时候，使用\*比字段名要快一些！ 

在使用group by时，它会自动排序，可以使用order by null取消排序

> 非常重要的性能说明：大量数据的时候，分页的实现！ `limit N, 10;`
>
> 比如：`select * from myisam limit 300000, 10;`此时查询出结果说消耗的时间很长
>
> 优化：`select * from myisam where id > 300000 limit 10;`此时消耗的是就就很少了。
>
> 所以：id：请一定不要更新或者删除它。 

# 七、两种特殊的索引结构

## 1、全文索引（MyISAM）

其它索引：之前章节介绍的4种索引，都是把列的值作为索引。 

全文索引：是把列里面的值进行分词，然后把每一个分词作为索引。

文章：标题，作者，内容，发布时间

内容从几千到几万字不等，存储内容需要使用大文本字段。

搜索内容里面含有某一个词的文章，曾经学习的搜索方式唯有使用like ‘%词%’进行匹配。

改善搜索的查询速度，需要对文章内容里面的每一个词，进行索引。让查询的时候可以使用到索引，提升查询的性能。

myisam引擎支持的全文索引，就可以完成这样的功能。该引擎支持英文，不支持中文！

英文分词：空格进行分词。

添加全文索引：`alter table table_name add fulltext 取个名称(字段);`

删除全文索引：`alter table table_name drop index 取的名称;`

之前介绍的3种索引与全文索引删除时都可以使用index或者key

搜索：`select * from table_name where match('字段') against('搜索词') ;`

### （1）创建全文索引表

```MySQL
create table `fulltext`(
	id int(11) unsigned not null auto_increment comment 'id',
    content text comment 'hello world',
    primary key(id)
)engine=myisam charset=utf8;
```

### （2）创建全文索引

在插入一些数据后，创建全文索引：

```mysql
alter table `fulltex` tadd fulltext full(content);
eg:alter table `fulltext` add fulltext fullcon(content);
```

mysql里面，关键词。一定要加反引号！

### （3）进行搜索

搜索词：you，

```mysql
select * from `fulltext` where match(content) against('you');
eg:select * from `fulltext` where match(content) against('you');
查看表结果
show create table `fulltext`\G
如果显示FULLTEXT KEY `fullcon` (`content`)，则说明全文索引是添加成功的
```

### （4）进行搜索

搜索词：Dream

```mysql
select * from `fulltext` where match(content) against('Dream');
再搜索：
select * from `fulltext` where match(content) against('relationship');
总结：写文章的时候，you与is在英文文章中，是经常出现的，所以就没有搜索的必要。所以像这类的词，全文索引会过滤掉，这些词是全文索引定义的。
能够使用搜索的词，只有那些能够区分出内容的词，才能被搜索到。
```

### （5）删除全文索引

```### mysql
alter table `fulltext` drop index 索引名;
eg：alter table `fulltext` drop index fullcon;
```

### （6）总结

搜索不到的词，每篇文章中出现的机会都很多，搜索的时候就没有意义。分词就过滤掉了这些词，不对这些词建立索引。过滤词是全文索引定义的，可以不必在意这些。

中文文章内容搜索是可以实现的，需要使用中间件（第三方工具），来帮助mysql完成功能。

## 2、前缀索引

其它索引：之前介绍的4种索引，都是把列的值作为索引。

前缀索引：是把列前面的一部份值作为索引。

比如：张三的`张`；李四的`李`；王五的`王`

建立索引的数据是字段的前面一部分，查询返回值是需要一个完整的字段值。查询返回值是前缀索引时，为了返回一个完整的字段值，就必须要去数据区找到值。

前缀索引制作的要求：都是长字段。

字段值前面的一部分，能够很好的代表整个字段的时候，才有必要制作前缀索引。

> 索引的选择性
>
> 计算公式：字段不重复值的总数 / 字段值的总数 = 0 - 1
>
> ​         select count(distinct  字段值) / count(字段) = 0 -1
>
> 前缀索引的选择性 
>
> 计算公式：字段前面的几个值不重复的总数 / 字段值的总数 = 0 - 1 
>
>          select count(distinct left(字段值, number)) / 字段值的总数 = 0 - 1 
>
> number的确定：
>
>          前缀索引的选择性结果  === 索引的选择性结果 

left的示例：

`select left('abcd', 2);`打印出`ab`

`select left('abcd', 3);`打印出`abc`

添加前缀索引：前缀索引很多时候都有重复的值，使用普通索引即可！ 

`alter table table_name add key 取个索引名(字段(number))`

> number ：表示取字段值的前面多少个！是由前缀索引的选择性计算出来的值！ 

### （1）创建一个表

```mysql
create table prefix(
	id int(11) unsigned not null auto_increment comment 'id',
    name var varchar(20) not null comment 'name',
    passwd char(32) not null comment 'passwd',
    primary key(id)
)engine=innodb charset=utf8;
```

### （2）准备脚本

```php
$db = mysqli_connect('127.0.0.1', 'root', '123456', 'test');
mysqli_query($db, "set names utf8");
	
$sql = "insert into prefix (`name`, `passwd`) select concat('xiao', convert(round(rand() * 20000), char)) as name, 
			md5(
				substring('qwerop[]\|{PLKJH}asdfghjkl;:zxc567vbnm,./<>?`~1234890-=!@tyui#$%^&*()_+……%QWERTYUIOGFDSAZXCVBNM<>?:;[]F86892652jdmjwolbvxzm2', 
					ceiling( rand() * 128), 
					ceiling( rand() * 50 + rand() * 30 - 1)
				)
			) as passwd";

for($i = 0; $i < 4444; $i++){
	mysqli_query($db, $sql);
}
```

### （3）运行脚本

将这个准备好的脚本放到linux站点目录下，并用浏览器访问该页面。此时浏览器刷新图标会一直在转，等到不转了表示请求成功了。

转的时候，等的很着急；你就会再次刷新页面。这个时候会出现什么情况呢？

刷新页面，就是再次发送请求。以当前页面来说这个问题。刚才我们刷新了二次，第一次没有等待结果返回，就再次刷新了。我们的数据结果是执行了完整的二次。

当你第一次刷新的时候，浏览器已经把请求发送给了服务器。服务器接收到任务，已经开始执行。你再次刷新的时候，浏览器发送了第二个任务给服务器，服务器接收到任务，然后开始执行。

服务器把第一次任务执行完成，准备返回的时候，找不到回家的路了，就没有办法返回。第二次任务执行完成，准备返回的时候，回家的路还在，因此就直接返回给浏览器了。 

将该PHP文件可以在php解析器直接执行一下：

`/working/php7/bin/php /站点目录/www/MySQL.php`

在查看一下数据库：`select count(*) from prefix;`。此时结果多了一个4444，证明运行成功了。

PHP程序在执行PHP文件的时候，连接数据的地址写的是：127.0.0.1。所以你的保证php连接的地址能够正常被访问到。127.0.0.1表示的是本地，所以php与mysql应该在同一台服务器上面。 

### （4）索引的选择性

`select count(distinct passwd) / count(*) from prefix;`

### （5）前缀索引的选择性 

`select count(distinct left(passwd, 6)) / count(*) from prefix;`

当取的字段前面6个字符的时候，计算出来的结果，就与索引选择性的结果是一样的。

### （6）添加前缀索引

`alter table prefix add key 取个名(passwd(6)); `

eg：`alter table prefix add key prepass(passwd(6)); `

### （7）确定搜索的值

`select * from prefix limit 1;`

查询出的结果，确定passwd的值为`4e1d9fdd24906a3256a0c2bafa3f532f`

### （8）使用值进行搜索  

`select * from prefix where passwd='4e1d9fdd24906a3256a0c2bafa3f532f';`

此时使用了索引，且索引的长度为：`key_len : 18`

### （9）删除前缀索引

`alter table prefix drop key 索引名; `

eg：`alter table prefix drop key prepass; `

### （10）创建普通索引

`alter table prefix add key passwd(passwd);`

### （11）使用普通索引进行搜索

`explain select * from prefix where passwd = "4e1d9fdd24906a3256a0c2bafa3f532f";`

此时的索引的长度：`key_len: 96`

### （12）结果总结

使用前缀索引的长度是：18 

使用普通索引的长度是：96 

前缀索引是普通索引的 5 倍速！

> 前缀索引制作的字段，一般情况是比较长的才使用它，工作中很少有这样的字段出现！

# 八、MySQL中的其他功能 

## 1、慢日志查询

> 慢[日志]查询：把sql语句执行慢的，都记录到日志文件里面。
>
> 定时检查日志文件内容，把sql语句执行慢进行优化！

慢[日志]查询中的“慢”指的是：人为设定的时间，超过了就是慢。

### （1）慢[日志]查询配置

`show variables like 'slow_query%';`

> 显示出的结果：
>
> slow_query_log  OFF：代表开关，OFF的时候是关闭的；ON的时候是开启的。 
>
> slow_query_log_file  /usr/local/mysql/data/localhost-slow.log；慢日志的存储位置，请不要修改它，因为修改就有权限的问题！

###  （2）慢[日志]查询时间配置 

`show variables like 'long_query%';`

查询出的结果：

> long_query_time    10.000000；中10.000000的小数点后面代表单位是秒

### （3）开启慢[日志]查询

`set global slow_query_log = 1;`

此时在进行第（1）步的操作，可以看到`slow_query_log`的值是`ON`，这就代表开启了。

> 这些内容，是mysql服务器管理人员设定的。一般情况都是默认设定在配置文件里面的。我们现在使用的临时有效的，重启就没有效果了。 

### （4）设定慢[日志]查询时间

`set long_query_time = 1.111111; `

此时经过第（2）步操作，可以看到`long_query_time`的值变为`1.100000`

这个就是设定的慢，SQL查询时间，超过这个的时候，就会记录到慢日志里面去。

### （5）查看慢[日志]查询日志文件位置 

日志文件在`/usr/local/mysql/data/`目录下的`localhost-slow.log`文件。

> 学习一个全新的查看方式：
>
> 参数 ： -f  是打开文件，占用当前窗口，只要文件有新内容从尾部插入，就立即展示出来。 
>
> eg：`tail -f /usr/local/mysql/data/localhost-slow.log`
>
> 此时打开之后，当前的窗口就会被占用了。
>
> 这种打开方式在工作中是经常使用的。

### （6）总结 

慢[日志]查询：开启的时候，可以把sql语句执行慢的记录下来。可以针对性的对sql语句优化。

-f参数非常的重要。工作中，集群服务器出现问题。访问的时候，有时候出bug，有时候没有出bug 。

此时，使用-f打开全部服务器日志文件。请求服务器内容时没有出现bug，可以立即查看哪台服务器日志文件有新消息，可以立即排除这台服务器了。

## 2、SQL语句缓存

执行sql语句的时候，直接把执行的结果，存储在服务器上面。下次再一次执行相同的SQL语句时，立即返回缓存结果。

### （1）确定服务器是否支持

`show variables like '%query_cache%'; `

得到的结果的参数解释：

> have_query_cache  ：值是yes的时候，就是支持sql语句缓存 。
>
> query_cache_limit  ：每条sql语句，可以占用（最大）的空间大小是1M。
>
> query_cache_min_res_unit ：最少占用的空间大小4K 。
>
> query_cache_size ：缓存大小 。（5.5版本时，是没有设定的，要自己设定！ ）
>
> query_cache_type ： ON是开启，OFF是关闭 。（5.5版本时，默认是开启！ ）

### （2）查看缓存状态

`show status like '%qcache%';`

得到参数解释：

> Qcache_free_memory ：可以使用的空间，即剩余空间
>
> Qcache_hits ：命中，使用了多少次缓存
>
> Qcache_inserts ：插入的缓存数量
>
> Qcache_lowmem_prunes ：内存不够，放弃缓存的数量
>
> Qcache_not_cached ：不缓存的数量
>
> Qcache_queries_in_cache ：已经缓存的数量

### （3）开启缓存

`set global query_cache_type = 1;`

如果是5.7版本的缓存开启，请进入配置文件：`vim /etc/my.cnf`

```my.cnf
[mysqld]
prot	= 3306
user 	= mysql
datadir	= /usr/local/mysql/data/
socket	= /tmp/mysql/sock
query_cache_type = 1   #将此行写在这个段落的这个位置
```

修改完配置文件后记得重启MySQL服务器！

### （4）设定缓存的大小

`set global query_cache_size = 1024 * 1024 * 80;`

执行完成后可以在`show variables like '%query_cache%';`中查询出`query_cache_size`的值。

设定好后，每次执行一次sql语句，都会在`show variables like '%query_cache%';`记录中Qcache_inserts`和`Qcache_queries_in_cache`的值加1。

### （5）重复执行sql语句

重复执行一条sql时，会在`show variables like '%query_cache%';`中的`Qcache_hits`值加1， 重复执行的，所以增加了命中。

### （6）修改sql语句

执行的sql语句中只要任何地方有变化，都会增加缓存的值，哪怕之前写的是`where`，之后是`WHERE`，这样也会增加缓存数。多写一个空格也会增加。

> 总结：意味着SQL的缓存，是区分大小写的。
>
> 缓存是必须SQL一模一样的，才会认定是同一条SQL。不然就会是新的缓存。
>
> 因为我们的SQL的查询结果进行缓存的时候，存储的数据格式是key=>value。这个key就是SQL语句HASH之后的值；value就是SQL的查询结果。sql的HASH结果，只要SQL有任何一点的变化，这个HASH结果，都会不一样的 。

### （7）修改数据或者增加数据

更新一条sql语句时，执行完成完成时，再查看缓存状态，可以看到`Qchche_queries_in_cache`的值变少了。

> 说明：当表的SQL语句，被缓存的时候。然后这个表有任何的修改（更新，插入，表结构）都会清空这个表的缓存。 

### （8）强制不缓存

`select sql_no_cache * from table_name`，其中`sql_no_cache `：告诉服务器不缓存，查询结果。

在执行完成后，缓存状态里的`Qcache_not_cached`的值增加了。

### （9）清空缓存

`reset query cache;`

### （10）总结

sql缓存使用的地方特别少，现在很多数据都要更新，或者添加数据。交互的内容越来越多。

数据经常发生变化的时候，不适合使用sql缓存。

开启sql缓存，每次查询请求，都会把sql语句作hash取值操作。去缓存中查询有没有缓存。数据经常变化，缓存常被清空，查询的命中就低。整个去缓存的过程，就经常变成无意义的操作，浪费了服务器资源！ 

# 九、MySQL分区与分表 

当表数据达到一定的数量级时。SQL语句的查询会非常的慢。 解决这个问题，就要把数据拆分到不同的表里面去。这个时候，就要使用分区或者是分表的技术。

## 1、分区与分表的区别

分区：mysql服务器帮助实现的数据分表功能，就是分区。

​	mysql把数据存储在拆分的不同表里面，对用户是无感知的，还是像一张表一样操作。

分表：是程序员，自己实现的拆表。把相同的数据，存储到不同的表里面。就是分表。

​	要分表的非常好，需要一定的算法功底。

## 2、分区的类型

类型说明：

key ：指定id作为hash值，来进行划分。内部会有一定算法实现的。 key可以不是整型 。

hash ：指定整型作为hash值，来进行划分。

range ：指定整型，作为范围，来进行划分。

list：指定整型，作为固定的区域，来进行划分。

## 3、key分区操作

### （1）创建表

```mysql
create table goods_key(
    id int  unsigned not null auto_increment comment '主键id',
    name  varchar(32) not null comment '名称',
    price  decimal(10,2) not null default 0 comment '价格',
    pubdate datetime not null comment '出厂时间',
    primary key (id)
)engine=myisam charset=utf8
partition by key (id) partitions 4;
```

执行完成后在查看表文件时，可以看到文件`goods_key.par`为分区结构。

### （2）合并分区

`alter table table_name coalesce partition 2; `

当执行表操作的时候，一定要关注数据。

表文件已经只有2个了，但是数据还是存在的，这就证明了，我们的表数据已经移动了。合并的时候，表数据会移动，移动数据，就会有IO的操作。所以大家在数据量大的时候，一定一定不要随便的操作合并。在夜深人静的时没什么人访问网站的时候进行这种操作。 

### （3）增加分区

`alter table table_name add partition partitions 2;`

说明：新增加的2个表文件都有数据的，就证明添加的时候，数据也会被移动的。会重新的进行数据的分区操作。这样的话，数据就会占用IO资源。谨慎操作。 

### （4）移除分区

`alter table table_name remove partitioning;`

移除分区后，数据还在，但是分区的表文件都被移除了，只剩下原来普通的模式了。

### （5）总结

添加与合并：都会进行数据的重新移动，这个时候，就会占用IO资源。所以操作的时候，一定要谨慎操作。

移除的时候，会把分区删除掉。也会进行数据的移动的操作。

这三个操作，数据还会存在。

## 4、hash分区操作  

### （1）创建表

```mysql
create table goods_hash(
    id int  unsigned not null auto_increment comment '主键id',
    name  varchar(32) not null comment '名称',
    price  decimal(10,2) not null default 0 comment '价格',
    pubdate datetime not null comment '出厂时间',
    primary key (id,pubdate)
)engine=myisam charset=utf8
partition by hash (month(pubdate)) partitions 4;
```

### （2）合并分区

`alter table table_name coalesce partition 2;`

说明：合并的时候，数据会进行移动。也就是io操作。合并操作，并不会影响数据库里面的数据 。

### （3）添加分区

`alter table table_name add partition partitions 2;`

说明：新分区的文件里面也是包含了数据的。也就是说当有新分区的时候。数据会重新进行分区操作。会占用IO资源。

### （4）移除分区

`alter table table_name remove partitioning;`

### （5）总结

合并与添加都会进行数据重新分区的操作。这种操作会占用IO资源。数据量大了就会非常的危险。所以操作的时候，一定要注意。

移除的时候也会进行数据移动操作。

上面三个操作，都会保证数据的正常。

> 小结：
>
> key | hash 分区：
>
> 添加分区：
>
> ​         alter table table_name add partition partitions number（添加表的个数）
>
> 合并分区：
>
> ​         alter table table_name coalesce partition number（合并表的个数）
>
> 移除分区：
>
> ​         alter table table_name remove partitioning;
>
> 使用分区的字段，必须是主键！
>
> 分区的时候，字段的选择？
>
>  一般情况下，要尽量让分区的表，每一张表的数量尽可能的保持一致！100W数据的表，分成2张表，尽量保持是50W一张表！
>
> 在表结构里面，什么样的字段，能够满足这样的需求，你就可以去选择这个字段，来制作分区表！

## 5、range分区操作

### （1）创建表

```mysql
create table goods_range(
    id int unsigned not null auto_increment comment '主键id',
    name  varchar(32) not null comment '名称',
    price  decimal(10,2) not null default 0 comment '价格',
    pubdate datetime not null comment '出厂时间',
    primary key (id,pubdate)
)engine=myisam charset=utf8
partition by range (year(pubdate))(
    partition year60 values less than (1970),		//注意，从这行开始往下必须从小到大
    partition year70 values less than (1980),
    partition year80 values less than (1990),
    partition year90 values less than (2000)
);
```

创建完成后，再进行插入数据。`insert into goods_range values (1,'htc',  3451.3,'1975-02-14 12:30:12');`插入总共几条条类似这种的语句。

此时如果插入一个比分区范围更大的值。比如`insert into goods_range values (6,'nokia',  3451.3,'2018-07-24 12:30:12');`此时会报错，因为2018不在分区数据里面。

> 重点说明：我们的这个范围分区设置的时候，一定要把所有可能的值，全部包含。这样插入数据的时候，才不会报错。

### （2）设置一个最大的分区

包含所有的可能性：`alter table table_name add partition(partition 分区名称 values less than MAXVALUE);`

eg：`alter table goods_range add partition(partition yearmax values less than MAXVALUE);`

再查看数据时，数据都存在。

再重新插入`insert into goods_range values (6,'nokia',  3451.3,'2018-07-24 12:30:12');`此时就可以成功插入了。

注意：添加分区的时候，只能添加越来越大的。 

### （3）删除分区

`alter table table_name drop partition 分区名称 ;`

eg：`alter table goods_range drop partition year 70;`

> 强调：删除分区的时候，数据也会一并被删除，所以请注意了。

### （4）移除分区

`alter table table_name remove partitioning; `

eg：`alter table goods_range remove partitioning;`

此时，分区文件已经被删除了；只有普通的样子了。 

### （5）总结

删除分区，会直接删除掉数据。所以请操作之前，三思。

添加分区，只能添加越来越大的分区。

移除分区，数据是还存在的，就会存在IO资源的使用。

合并分区也是有的 。

## 6、list分区操作 

### （1）创建分区

```mysql
create table goods_list(
    id int unsigned not null auto_increment comment '主键id',
    name  varchar(32) not null comment '名称',
    price  decimal(10,2) not null default 0 comment '价格',
    pubdate datetime not null comment '出厂时间',
    primary key (id,pubdate)
)engine=myisam charset=utf8
partition by list (month(pubdate))(
    partition spring values in (3,4,5),
    partition summer values in (6,7,8),
    partition autumn values in (9,10,11),
    partition winter values in (12,1,2)
);
```

再进行插入数据。

### （2）删除分区

`alter table table_name drop partition 分区名称;`

eg：`alter table goods_list droppartition winter;`

> 删除分区的时候，数据会一并被删除的。

### （3）修复分区

修复一下刚刚删除的"winter"分区，其实就是重新添加一下

`alter table table_name add partition(partition winter values in (12,1,2));`

此时，虽然有重新创建了分区，但是在删除了数据，删除了分区的话，数据是不会回来的，现在只是相同的名称的分区，已经和曾经的分区不一样了。

### （4）移除分区

`alter table table_name remove partitioning; `

此时，表文件恢复到了没有分区时的样子了。

### （5）总结

删除分区：就是把分区表删除，表删除了，里面的数据也就一起删除了。

添加分区：添加的分区是一个全新的，里面是没有任何数据的。

移除分区，会进行数据的移动，会占用IO资源。

> 小结：
>
> range 与 list 分区
>
> 添加分区：
>
> alter table table_name add partition (
>
> ​         partition 分区名称 values less than (整型)
>
> ​         或者
>
> ​         partition 分区名称 values in (整型,整型,整型)
>
> )
>
> 删除分区：
>
> ​         alter table table_name drop partition 分区名
>
> 移除分区：
>
> ​         alter table table_name remove partitioning;
>
>  
>
> 注意：移除分区是会进行数据的移动的；所有请注意，一定要在夜深人静的时候操作。
>
>  
>
> 删除分区与添加分区，都不会进行数据操作，所有请操作的时候三思而行！
>
>  
>
> range与list也有合并分区；合并的时候，都是会进行数据移动的！

## 7、水平分表

数据有很多的时候，存储在多张表里面；这些表的表结构肯定是一模一样的。

表结构一模一样的表，在存储数据的时候，我们就可以理解成水平分表！

数据有1000W的时候，我们进行水平分表；分成2张表；

策略一：前500W数据，分在表1里面；后500W数据，分在表2里面；

策略二：id是单数的，分在表1里面；id是双数的，分在表2里面。



完成策略一：首先1000W的数据；新增数据。 

​	读取曾经的数据，跑前500W在表1里面；跑后500W在表2里面； 

​	新增数据：需要一直维护一个id值，通过id值去判断，我们的数据应该存储在那张表里面了。假如1001W，这个时候就存储在表3里面；到达1501W，这个时候就存储在表4里面。 

查询数据的时候：

​	查询数据的，必须给数据维护好id值，查询的时候，尽量使用id查询，通过id值，就能区分它在哪一个表里面了！ 

​	merge引擎；它可以把myisam引擎的多张表连接起来，你查询的时候，直接查询这张表，它就会把所有的myisam引擎的表，都遍历一次，这样就获得了数据。

​	非myisam引擎的表，就要使用中件间（第三方工具）实现同merge引擎一样的效果！



完成策略二：首先1000W的数据；新增数据。

​	读取曾经的数据，跑数据id，id是单数的就存储在表1里面；跑数据id，id是双数的就存储在表2里面。

​	新增数据：需要一直维护一个id值，通过id值去判断， 只要id是单数就存储在表1里面；id是双数就存储在表2里面。

面试常考：有一个用户信息表，数据量有点大了，我们要进行分表，应该怎么划分?

​         id ，姓名，年龄，性别，登录时间，籍贯，time

用户信息表：

​	1）新用户注册的时候，注册信息 

​	2）用户登录的时候，登录获得信息 

用户信息表，经常都是回答使用用户名进行划分的。

​	用户名是一个字符串，可以通过hash转成整型，整型在进行取余就可以了。 

要拿的值，一定是尽量是唯一的，不要重复的，不可修改的。



完成一下策略二：

分表的数据先定义好：2张表；

分表的算法要定义好：id进行取余；

通过算法，就会发现表名可以设计的有点意思：

id = 3；

  3 % 2 = 1；

id = 4；

  4 % 2 = 0；

它的值，只有0或者是1；

确定一个表前缀，然后加上这个取余的结果，就可以组装成表名！

表前缀是:goods_   ; 取余结果：0 ;组装：goods_0（表名）

### （1）创建好2个表

```mysql
create table goods_0(
    id int unsigned not null auto_increment comment '主键id',
    name  varchar(32) not null comment '名称',
    price  decimal(10,2) not null default 0 comment '价格',
    pubdate datetime not null default '0000-00-00 00:00:00'  comment '出厂时间',
    primary key (id)
)engine=myisam charset=utf8;

create table goods_1(
    id int unsigned not null auto_increment comment '主键id',
    name  varchar(32) not null comment '名称',
    price  decimal(10,2) not null default 0 comment '价格',
    pubdate datetime not null default '0000-00-00 00:00:00'  comment '出厂时间',
    primary key (id)
)engine=myisam charset=utf8;
```

### （2）创建维护id的表

这个id一定要是自增长的！

```mysql
create table goods_id_incr(
    id int unsigned not null auto_increment comment '主键id',
    primary key (id)
)engine=myisam charset=utf8;
```

插入数据：`insert into goods_id_incr values (null);`

### （3）代码实现

```php
//连接数据库
$db = mysqli_connect('127.0.0.1', 'root', '123456', 'test');
//表前缀
$table_pre = 'goods_';
//表的数量
$table_num = 2;
//有数据来了
$data = ['name' => 'chuizi', 'price' => 3299, 'pubdate' => '2018-05-15 19:00:00'];
//数量时没有ID的，要给它准备一个id值
$i_sql = "insert into goods_id_incr values (null)";
mysqli_query($db, $i_sql);
//获得生成的id值
$auto_id = mysqli_insert_id($id);
//取余计算表明
$table_last = $auto_id % $table_num;
//拼接表名
$table_name = $table_pre . $table_last;
//写入数据的sql
$sql = "insert into $table_name values ('{$auto_id}', '{$data['name']}', '{$data['price']}', '{$data['pubdate']}')";
mysqli_query($db, $sql);
```

### （4）发现一个问题：`goods_id_incr`

查询一下`good_id_incr`表，得知有5条数据，那么以后会有多少条呢？

​	这些数据，只是在组装id的时候，才会有用。之后都将没有作用。所以我们应该清空它！

> delete from goods_id_incr;  ：清空表数据，不清空自动增长！
>
> truncate goods_id_incr : 清空表数据，并且把自动增长清空，所以不能使用它。

## 8、垂直分表

表里面有很多字段，有一些是常用的，有一些是不常用的。

把常用的分成一张表，把不常用的分成一张表。

假如：用户表

id  name  age  sex  phone email  wechat  address  content  .....

不常用的：

id  fid  address  content  .....

补充说明：垂直分表的真实内涵

​         我们的数据是存储到硬盘里面的，那么一行数据的内容都是存储在一起的。而IO一次性读取的数据，也是整块读取的。当io读取一次的时候，里面有很多数据，是不需要的。这次读取就浪费太多资源。         

​	进行垂直分表。那一行数据里面，都是需要的。现在IO一次性读取出来的内容，都是需要的。

# 十、锁

## 1、什么是锁

就是对数据的访问控制的一种技术。

## 2、mysql里面的锁的几种形式

共享锁（读锁）：加了共享锁的，大家都可以读它。

排它锁（写锁）：加了排它锁的，只有加锁的这个人可以去读写它。

锁的范围：

​	表锁：就是把一张表给锁住，就是表锁。myisam引擎，实现的就是表锁。表锁的颗粒大，加锁快，并发访问低。

​	行锁：就是把一张表里面的一行锁住，就是行锁。innodb引擎，实现的就是行锁，颗粒小，加锁慢，并发访问高。

加锁的时机：

​	悲观锁：当我想使用数据的时候，我就认为别人有可能会使用，我就赶紧加锁。

​	乐观锁：当我想使用数据的时候，认为别人不会使用。那我就修改的时候才加锁。

当我们使用乐观锁的时候，想要去修改它。这个时候，我们要读取数据出来。改变这些数据。

改变之后，就要更新。但是在更新之前检查一下version号是否一致。一致就更新。不一致，就先重新获取一下数据。然后修改数据在更新。

锁冲突：

当前用户进行加锁的时候，其它用户加锁会出现锁等待的情况。

并发访问的情况：

A ：对ID是1的加锁，执行访问ID是2的，出现锁等待

B ：对ID是2的加锁，执行访问ID是1的，出现锁等待

这个就是锁冲突。

## 3、表锁myisam引擎（表锁）

读锁：

​	开始加锁：lock tables table_name read

​	解锁：unlock tables

写锁：

​	开始加锁：lock tables table_name write

​	解锁：unlock tables

开始实践：准备2个用户！

> 说明：用户是以session来区分的，不是以用户名来分区的。

读锁：

加锁：`lock tables myisam read;`

当前用户：读操作！

当前用户再操作其它表时，会报错。你给谁加了锁，你就要操作哪张表，直到解锁。

其他用户：读操作时是可以进行读的。

但是进行写操作时，会出现锁等待。

当前用户：写操作。此时会报错，写操作是不可以执行的，读的锁，不能写。

解锁：`unlock tables;`

> 总结：myisam表的读锁，满足所有用户可读；所有用户不可写。



写锁：

加锁：`lock tables myisam write;`

当前用户进行读操作时可以进行，但是其他用户在此时进行读、写操作时会进行锁等待。

说明：其他用户是不能再写锁的时候操作读与写的。

当前用户：写操作时可以执行。

解锁：`unlock tables;`

> 总结：写锁是当前用户可以读与写的。其他用户是不可以读不可以写的。

## 4、行锁innodb引擎（行锁） 

事务（Transaction）及其ACID属性： 

​	原子性（Atomicity）：事务是一个原子操作单元，其对数据的修改，要么全都执行，要么全都不执行。 

​	一致性（Consistent）：事务开始和完成，数据都必须保持一致状态。这意味着所有相关的数据规则都必须应用于事务的修改，以保持数据的完整性；事务结束时，所有的内部数据结构也都必须是正确的。 

​	隔离性（Isolation）：数据库系统提供一定的隔离机制，保证事务在不受外部并发操作影响的“独立”环境执行。这意味着事务处理过程中的中间状态对外部是不可见的，反之亦然。 

​	持久性（Durable）：事务完成之后，它对于数据的修改是永久性的，即使出现系统故障也能够保持。

事务级别有所不同：4个级别！

事务里面操作：

开始事务：

​         begin：

结束事务：

​         commit：

读锁：sql + lock in share mode

写锁：sql + for update

#### 当前用户加个读锁

开始事务：`begin;`

当前用户：读锁`select * from innodb where id = 1 lock in share mode'`

其他用户：读锁`select * from innodb where id = 1 lock in share mode;`

其他用户：写操作`update innodb set name = '6666' where id = 1;`此时出现了锁等待。

其他用户：写操作非加锁的行`update innodb set name where id = 2;`，执行成功，当前用户确实是加的行锁。

当前用户：写操作：`update innodb set name = '5555' where id = 1;`

在innodb引擎的时候，在加了读锁的时候，依赖能够写操作成功。因为当你写操作的时候，自动就转成了写锁了。 

写锁是排它的。所以使用其他用户，查询一下：`select * from innodb where id = 1 lock in share mode;`，此时出现锁等待。

提交一下事务：`commit;`

总结：当Innodb引擎加读锁的时候，是共享锁。大家都可以读。其他用户不能写。如果当前用户写数据了。锁就会修改成写锁，具体排它性。 

#### 当前用户加个写锁

开始事务：`begin;`

当前用户可以正常读操作，但是其他用户进行读操作和写操作时出现锁等待。

当前用户可以正常写操作。

提交事务：`commit;`

总结：写锁是当前用户可以写可以读；其它用户是排它性的，不能读不能写。

> 事务：就是拷贝了一份，单独的拿出来操作。在事务没有提交的时候，事务与源数据，是拷贝关系。 

innodb总结：innodb是有条件的行锁，当使用索引的字段值的时候，才能加上行锁，非索引字段加的是表锁。 

表锁是mysql服务器实现的，不是innodb引擎实现的。所有当有表锁冲突的时候，我们的innodb引擎没有办法解决这个问题。

行锁冲突的时候，innodb引擎会自动帮助我们解决这个问题！



查看表结构：有一个复合索引`KEY name_fu (name, age, sex)`

删除复合索引：`alter table innodb drop key name_fu;`

开始事务：`begin;`

当前用户：写锁。`select * from innodb where name = '8888' for update;`

name字段现在没有索引，所以加的是表锁

可以测试一下：其他用户进行读操作，非name=8888的字段查询，`select * from innodb where name = 'xiao15' lock in share mode;`，查询非id=1的出现了锁等待，就证明了加了表锁。

提交事务：`commit;`



给name字段创造一个普通索引：`alter table innodb add key name(name);`

开始事务：`begin;`

当前用户：写锁。`select * from innodb where id = 1 for update;`

说明：现在的name字段是有普通索引的，所以加的是行锁。

再测试一下：其他用户进行非加锁行操作：`select * from innodb where name = 'xiao15' lock in share mode;`

数据是可以查出。

说明：已经证明了，非加锁行，都可以正常操作。

其他用户：对加锁行操作 时，出现了锁等待

> 总结：只有加锁的行，其他用户操作的时候，才出现了锁等待。就证明了。innodb的表锁，是在使用的时候，没有索引的情况下产生的。 

提交事务：`commit;`

当使用范围的时候。只要范围的都会被加锁！

id > 10 ;  锁不是绝对的。这个和事务的级别有关系。

## 5、php实现锁 

需求：把访问人数，统计到我们表里面！

### （1）创建一张表，记录人数

```mysql
create table lock_num(
	id tinyint(1) unsigned not null comment 'id',
    num int unsigned not null
)engine=myisam charset=utf8;
```

num字段就是把我们有多少人访问，把统计的结果，存储在这里！ 

### （2）提前数据预热 

`insert into lock_num values(1,0);`

很多时候，我们都需要把数据提前预热；因为我们的访问一但来了，这个时候，我们没有准备好数据，就只能让mysql服务器硬抗，抗不住的。

### （3）代码实现

```php
//文件名：lock.php
//配置mysql服务器
$db = mysqli_connect('127.0.0.1', 'root', '123456', 'test');
mysqli_query($db, "set names utf8");
//等待一个人的到来
$person = 1;
//把曾经的访问数据取出来
$s_sql = "select num from lock_num where id = 1";
$res = mysqli_qyery($db, $s_sql);
$data = mysqli_fetch_assoc($res);
$num = $data['num']; //曾经的访问总人数
//曾经的人与现在访问人相加
$num = $num + $person;
//更新到数据库里面
//update自带写锁，select自带读锁，但是它们的作用范围都是一行语句
//所以有多行语句，或者要处理sql语句的记过，再操作就需要手动的加锁
$u_sql = "update lock_num set num = $num where id = 1";
mysqli_query($db, $u_sql);
```

### （4）模拟多人并发访问

在linux中执行`/working/httpd-2.4/bin/ab -n 500 -c 50 http://127.0.0.1/lock.php`

执行完后查看数据库，发现：并发问题，当同时有多个人要操作同一个地方的数据的时候。如果不加锁，直接操作。有的就会被抹掉。

解决这个问题，就只能**加锁**！

### （5）实现加锁功能 

让请求实现我们的效果： 

```php
//文件名：lock.php
//配置mysql服务器
$db = mysqli_connect('127.0.0.1', 'root', '123456', 'test');
mysqli_query($db, "set names utf8");
//等待一个人的到来
$person = 1;
//加好写锁
mysqli_query($db, 'lock tables lock_num write');
//把曾经的访问数据取出来
$s_sql = "select num from lock_num where id = 1";
$res = mysqli_qyery($db, $s_sql);
$data = mysqli_fetch_assoc($res);
$num = $data['num']; //曾经的访问总人数
//曾经的人与现在访问人相加
$num = $num + $person;
//更新到数据库里面
//update自带写锁，select自带读锁，但是它们的作用范围都是一行语句
//所以有多行语句，或者要处理sql语句的记过，再操作就需要手动的加锁
$u_sql = "update lock_num set num = $num where id = 1";
mysqli_query($db, $u_sql);
//操作完成，解锁
mysqli_query($db, 'unlock tables');
```

再用ab来测试一下：`/working/httpd-2.4/bin/ab -n 500 -c 50 http://127.0.0.1/lock.php`

> 结果：加了写锁的，都是串行的。速度就会变慢的。为了数据的完整性，变慢也必须这样做。 

## 6、文件锁

手册内容：

> 参数：
>
> handle
> 	文件系统指针，是典型地由 fopen() 创建的 resource(资源)。
> operation
> 	operation 可以是以下值之一： 
> 	◦ LOCK_SH取得共享锁定（读取的程序）。  
> 	◦ LOCK_EX 取得独占锁定（写入的程序。  
> 	◦ LOCK_UN 释放锁定（无论共享或独占）。  
>
>
> ​	如果不希望 flock() 在锁定时堵塞，则是 LOCK_NB（Windows 上还不支持）。 

```php
//文件名：lock.php
//配置mysql服务器
$db = mysqli_connect('127.0.0.1', 'root', '123456', 'test');
mysqli_query($db, "set names utf8");
//等待一个人的到来
$person = 1;
//加好写锁
$fp = fopen('./lock.txt', 'w');
if(flock($fp, LOCK_EX))
{
    //把曾经的访问数据取出来
    $s_sql = "select num from lock_num where id = 1";
    $res = mysqli_qyery($db, $s_sql);
    $data = mysqli_fetch_assoc($res);
    $num = $data['num']; //曾经的访问总人数
    //曾经的人与现在访问人相加
    $num = $num + $person;
    //更新到数据库里面
    //update自带写锁，select自带读锁，但是它们的作用范围都是一行语句
    //所以有多行语句，或者要处理sql语句的记过，再操作就需要手动的加锁
    $u_sql = "update lock_num set num = $num where id = 1";
    mysqli_query($db, $u_sql);
    flock($fp, LOCK_UN);
}
fclose($fp);
```

再用ab来测试一下：`/working/httpd-2.4/bin/ab -n 500 -c 50 http://127.0.0.1/lock.php`

确实添加进数据了。

再查看项目目录：确实出现了`lock.txt`这个文件

> 文件锁只能在单台服务器使用。
>
> mysql锁可以在多台服务器使用。
<<<<<<< HEAD

# 十一、主从服务器

## 1、什么是主从服务器

主从服务器：一台服务器设置成为主服务器，一台服务器设置成为从服务器。 

mysql服务器的抗压能力有限。

假设一台服务器最多能够接受的并发请求数是500个。当并发超过500的时候，很显然一台服务器就不能正常工作了，就需要更多的服务器来支持。

保证服务器之间的数据及时同步问题，需要使用主从的技术解决方案实现数据同步。

众多业务之中，查询请求为主，修改与写请求相对来说非常少。

应对项目中的实际情况。设置一台服务器专门接受修改与写的请求，这台服务器就会被设置成主服务器；设置一台服务器专门接受查询的请求，这台服务器就会被设置成从服务器。

主服务器的数据变更时，快速的同步到从服务器，保证主从服务器的数据是相同的。这样的架构就是主从架构，同时也实现了读写分离。

## 2、主从服务器的实现原理

> 主服务器：
>
> 1）开启二进制日志，让修改与写操作的SQL语句，记录到bin日志（二进制日志）。
>
> 2）设置集群的唯一id。
>
> 3）创建一个从服务器登录主服务器的账户。

> 从服务器：
>
> 1）设置集群的唯一id。
>
> 2）使用主服务器提供的账户与其它信息连接上主服务器。
>
> 3）从服务器的I/O进程，去主服务器的bin日志里面读取内容，然后保存到从服务器的中继日志里面。
>
> 4）从服务器的SQL进程，去中继日志里面读取内容，把读取到的 SQL语句在服务器上面执行一次。

注意：主从服务器开启同步的时候，一定要让主从服务器在同步之前，所有数据保持一致。

例如：主服务器的写SQL语句，同步保存到从服务器。从服务器的SQL进程执行该SQL语句，发现没有这张表，就会报错，同步操作就会异常中断！

> 注意事项：
>
> 1）主从服务器的网络是能够连通的
>
> 2）主服务器开启端口允许访问或者关闭防火墙

## 3、MySQL服务器添加（授权）账号 

### 添加与删除账户

启用新的账户，并且设定密码与权限！ 

`grant 权限 on  库.表  to  账号@IP地址  identified by 密码;`

> 权限：select ：查询权限；all ：所有权限
>
> 库： 指定某一个库，使用*代表所有的库
>
> 表： 指定某一个表，使用*代表所有的表
>
> 账号：设置数据库里面没有的账户名 
>
> IP地址：限制账户在指定IP地址上面，才能使用账户登录系统 
>
> ​	192.168.182. 53 ：只能是拥有这个IP地址的电脑，才能使用它对应的账户登录系统 。
>
> ​	% :代表所有的IP地址，有的版本不包含127.0.0.1 

删除账户：`drop user '用户名'@'ip地址';`

> 如果删除账户之前该账户已经登录MySQL了，在删除之后如果没有登出就还是可以继续使用，一旦登出就不能再使用了。

### bin-log日志

#### 开启bin-log日志功能

找到MySQL的配置文件：`/etc/my.cnf`

打开它并修改内容：

```mysql
[mysqld]
log-bin			= filename	#这个是文件名
server-id		= 1			#一定要设置一个serverid
port		    = 3306
user		    = mysql
datadir			= /usr/local/mysql/data	#bin日志是保存在这个目录下面，不能有重名
socket			= /tmp/mysql.sock
query_cache_type = 1
```

修改完后重启MySQL，每一次重启都会生成一个新的bin日志文件 

### 和bin-log日志相关的一些函数

#### 命令生成新的日志文件

在MySQL下输入：`flush logs;`

说明：每一个bin日志文件，都会因为长时间存储，文件足够大了。文件大了，就应该生成一个新的文件，把曾经的旧文件，备份起来，存储好。以备以后使用 。

#### 删除全部日志文件，生成新日志文件

`reset master`

> 使用这个命令。是删除原来的所有二进制日志文件。生成一个全新的二进制日志文件。

#### 查看日志状态 

`show master status;`

## 4、可以使用log-bin日志完成数据恢复  

bin日志里面，记录的都是sql语句。如果数据丢失，再把里面的sql语句拿出来执行，就实现数据恢复。 

> 说明：开启二进制日志之后，修改或者写的SQL语句，才会记录到bin日志里面。曾经的修改或者写的SQL语句，都已经是过眼云烟！ 

### 产生二进制日志数据

执行修改或者写的SQL语句 

写SQL：`insert into innodb values (null, 'xiao12', 18, 1, 'fjsd');`

更新SQL：`update innodb set name = '123456654321' where id = 1;`

### 查看二进制日志内容：工具应用

找到查看工具：`/usr/local/mysql/bin/`目录下的`mysqlbinlog`文件。

使用一下：`/usr/local/mysql/bin/mysqlbinlog --base64-output=decode-rows -v /usr/local/mysql/data/bin-log.000001`

### 查看二进制日志内容：命令应用

`show binlog events;`用于查看当前正在使用的二进制日志文件。

查看指定的二进制文件：`show binlog events in '二进制文件文件名';`

> 这个是5.7版本升级之后的效果，5.5版本的效果不是这个样子！5.7版本的查看这些就已经非常的难了。

###  数据消失应采取的措施

误操作：删除表。

恢复手段：使用innodb备份文件：`/tmp/innodb.sql`，可以直接恢复。

### 查看二进制日志与show binlog events确定位置

时间恢复 | 起始位置与结束位置来进行恢复！

时间的问题：1秒可以插入10条数据；数据掉了5条，想恢复只是这5条。以时间来进行恢复，就会把10条数据都恢复。有重复的5条数据，就会报错。

恢复工具：`/usr/local/mysql/bin/mysqlbinlog --help`

> --start-datetime ：时间
>
> --start-position  ：开始位置 
>
> 确定以位置来进行恢复！
>
> --stop-position ：结束位置

`mysqlbinlog –start-position=开始位置  --stop-position=结束位置  二进制日志文件路径  | mysql –u root –p [库名] `

### 恢复表数据

第一步：还原一下：`/usr/local/mysql/bin/mysql/ -u root -p test < /tmp/innodb/sql`

第二步：使用二进制日志进行恢复

在日志中可以找到update开始位置和结束的位置。

按照我的日志显示是开始位置：219；结束位置：762

进行恢复：`/usr/local/mysql/bin/mysqlbinlog --start-position=219 --stop-position=762 /usr/local/mysql/data/filename.000001 | /usr/local/mysql/bin/mysql -u root -p -test`

# 十二、主从复制的配置

## 1、配置主服务器

设定主从服务器：一定是主服务器版本低于或者相同于从服务器。最佳的情况是主从服务器的版本一致。

说明：linux上面的mysql是5.7版本的；win上面的mysql是5.5版本的。

确定主服务器是win上面的。

### （1）开启二进制日志

打开在windows上mysql.ini配置文件，设置集群的唯一id：

```mysql
[mysqld]
log-bin = bin-log
server-id = 8		#集群里面，不要有重复的
......
```

重启服务器。

### （2）创建从服务器登录主服务器的授权账户

权限：`replication slave`

`grant replication slave on *.* to 'slave'@'%' identified by '123456';`

关闭防火墙。

## 2、配置从服务器

### （1）设置集群的唯一id

在linux中打开mysql配置文件：`vim /etc/my.cnf`

```mysql
[mysqld]
log-bin = bin-log
server-id = 1		#集群里面，不要有重复的
......
```

保存重启mysql服务器

### （2）连接主服务器设置

需要主服务器提供如下信息：

> change master to 
>
> master_host=’192.168.182.52’
>
> master_port=3306
>
> master_user=’slave’
>
> master_password=’123456’
>
> master_log_file=’log-bin.000001’   #这个的值是主服务器的二进制文件的文件名 
>
> master_log_pos=250		#这个值是主服务器的二进制文件最新的位置 

> 重点说明：开始进行主服务器与从服务器的数据同步！把所有的主服务器的内容都复制到从服务器上面。 

因为数据的同步，就是在从服务器上面再次执行SQL语句，没有库没有表，就会执行报错。 

假装同步：

配置从服务器：

```mysql
change master to master_host='192.168.182.52',
master_port=3306,
master_user='slave',
master_password='123456',
master_log_file='log-bin.000001',
master_log_pos=250;
```

## 3、从服务器管理

查看从服务器状态：`show salve status\G;`

启动从服务器：`start slave;`

停止从服务器：`stop slave;`

清空从服务器配置：`reset slave all ;`

> 在查看从服务器状态时，`Slave_IO_Running`和`Slave_SQL_Running`的值都是yes时，才是启动成功
>
> 出现错误信息时，记录在`Last_Errno`和`Last_Error`中

> 从服务器已经停止了在主服务器上面把拿数据过来，此时不会再同步了 。
>
> 每一次从服务器去主服务器取数据的时候，都会记录最后一次取数据的位置。然后下一次去取的时候，就是从这个位置开始向下取的。 
>
> 从服务器的修改与写入操作，都不会记录到主服务器上面的。所以修改或者写操作，必须由主服务器完成。

## 4、读写分离的配置

PHP代码层面实现： 

主服务器：192.168.182.52; 

从服务器：192.168.182.53;

$db1 = mysqli_connect(); 主服务器

$db2 = mysqli_connect(); 从服务器

\$sql = “select”  这条sql是你写的。你知道它是查询，请使用\$db2操作mysqli_query(\$db2, $sql);

\$sql = “insert”  这条sql是你写的。你知道它是写的，请使用\$db1操作mysqli_query(\$*db1*,\ $sql);

# 十三、定时任务

查看程序所在路径：`whereis vim`

也可以使用find，但是这种方式搜文件名比较慢。

date指令：

%M是分钟数；%S是秒数：`date +%M----%S`显示出的结果`41----55`

可以使用转义符：\

`date +\%M----\%S`

创建一个文件：名称由当前的分与秒组成

`touch ./file-$(date +\%M-\%S).php`创建后的文件名：`file-44-09.php`

\$()：在这里面可以写Linux的命令，会解析LINUX的命令。 

## 1、什么是定时任务

指定时间自动运行的程序，就是定时任务。

## 2、为什么需要定时任务

很多工作需要定时定点完成，这个时候就需要定时任务！

例：按天分析用户的访问数据。

​	用户的一天时间是0 – 23 点，当一天结束之后，项目立即需要对用户访问数据进行分析，然后处理入库。

​	提早把数据分析，处理入库的代码写好，测试通过；设定一个定时任务，0晨执行该代码，就可以完成数据处理。

定时任务充斥着整个项目。完整的一个项目，有许多的定时任务。他们在指定的时间，运行这些程序，完成项目中各种各样的需求。 

## 3、定时任务语法介绍

语法：

时间参数    执行命令（绝对路径）    命令参数     [ &>  /dev/null ]

语法分解：

/dev/null  ：这个设备叫数据黑洞，无论写入多少内容，直接吞掉，无法恢复。

&>  ：成功或者失败的消息，全部写入设备 /dev/null 

命令参数  ：根据执行命令决定参数内容 

执行命令  ：根据需求，选择合适的命令 

时间参数  ： 

​	分    时    天    月  星期  ：对应的5个位置，必须有值！ 

​	*	 \*	 *	*	*	：重点【*】使用【每】来代表。 

`1   *   *   *   *     ：每小时的第1分钟，执行命令 `

`2   *   1   *   *     ：每月第1天每小时的第2分钟，执行命令`

`1   *   *   *   1     ：每周一的每月每天每小时第1分钟，执行命令 `

`*/2   *   *   *   *     ：每小时的每2分钟，执行命令`

`*/4   *   *   *   2     ：每周二的每小时的每4分钟，执行命令 `

`1   */3   *   *   *     ：每天的每三个小时的第1分钟，执行命令`

`1   1-5   *   *   *     ：每天的第1小时到第5小时的第1分钟，执行命令`

`1   1   *   *   1,3     ：星期一和星期三的每天第1小时第1分钟，执行命令`

`1   1,4   *   *   *     ：每天的第1小时和第4小时的第1分钟，执行命令 `

## 4、定时任务命令介绍  

> crontab  选项参数 
>
> -l : 	查看已经设定好的定时任务
>
> -e: 	编辑定时任务：使用方式和vim的操作一致 
>
> ​	添加一个定时任务：添加定时任务语法即可！
>
> ​	删除一个定时任务：删除该行任务内容即可！
>
> -r：	清除所有的定时任务 

确定一个定时任务： 

​	每2分钟创建一个文件，并且文件名包含当前分与秒！ 

`crontab -e`

`*/2  *  *  *  *  /bin/touch  /root/file-$(date +\%M-\%S).txt  &>  /dev/null`

等待2分钟后可以查看结果。

清空创建任务

`crontab -r`

`crontab -l`

=======
>>>>>>> de8a937b6509e825ceb5aad8df1c535f69584b05
