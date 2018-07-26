## 一、MongoDB介绍

### 1、NoSQL：非关系型数据库

> 在php的世界里，常见的非关系型数据库有memcached、redis、mongodb

### 2、MongoDB介绍

mongodb是针对海量数据开发的持久化类型NoSQL，直接把数据存储到硬盘。mongodb主要是存储日志或者报表之类的数据。这类数据的量都是非常大的，并且缺失少许数据是无关紧要的。

> mongodb语法与js相同，并且可以在mongodb里面写js代码，以完成特定功能。 

mongodb 有库的概念，库的下面是集合（表）的概念，集合的里面是文档（数据）的概念。

数据以文档的方式来体现的，一行行的文档都是插入的数据。

> mongodb的id默认带主键索引。主键索引是不能修改、不能删除，并且是必须存在，值是不可重复的。

### 3、MongoDB的安装与配置 

下载地址：https://www.mongodb.com/ 

#### 安装

在linux中得到压缩包解压完成并进入目录，发现里面的内容，可以直接使用，所以直接把解压文件夹移动到`/working`目录下即可。

`mv mongodb-linux-i686-3.2.15/ /working/mongodb`

> 在mongodb文件夹里，mongod是服务器。
>
> 多数情况下，linux中的软件在取名时，文件名末尾有d的代表服务器，没有d的代表客户端

#### 查看帮助信息

`/working/mongodb/bin/mongod --help`

参数：

> 默认端口号：27017
>
> --logpath arg：这里的"arg"是文件，不能写目录
>
> --logappend：追加写日志
>
> --fork：后台运行
>
> --auth：运行的时候，要用户验证
>
> --storageEngline arg：默认引擎：wiredTiger；但是64位支持的。32位不支持；3.0之后的32位默认引擎要修改成mmapv1 
>
> --dbpath arg：指定一个目录

配置参数：

--port 27017 

--logpath 创建一个目录：logs目录

--dbpath 创建一个目录：data目录

#### 启动mongodb服务器

`/working/mongodb/bin/mongod --port 27017 --fork --logpath /working/mongodb/logs/mongod.log --logappend --storageEngine mmapv1 --dbpath /working/mongodb/data/`

启动之后检查一下有没有端口号和进程号

#### 启动客户端

`/working/mongodb/bin/mongo 192.168.xxx.xx:27017`

### 4、快速入门

#### （1）数据库(db)

查看库：`show databases;`或者`show dbs`

进入库：`use db_name`

> 说明：mongodb默认是可以进入任意一个库的。这个库存在不存在都可以。进入不存在的库，只有在库里面有数据了，就会自动创建这个库。

#### （2）集合（collection）

查看集合：`show tables;`

查看集合：`show collections`

> 默认情况下，当你的集合下面有数据的时候，就会自动创建这个集合 。

#### （3）文档（document）：插入数据 

插入数据： `db.collection_name.insert({});`

> db ：表示当前库
>
> collection_name：集合名称
>
> insert：操作动作（插入）
>
> {} ：文档，要写入的数据，数据格式是json。

eg：`db.book.insert({name:"xiaoming", age:18, sex:2});`

查看数据：`db.collection_name.find();`

eg：`db.book.find();`

> 插入数据的时候，没有指定id，会默认的生成一个id的字符串！

手动插入id：id的字段名必须是`_id` 值必须是不重复 ； `db.collection_name.insert({_id:1})`

eg：`db.book.insert({_id:1,name:"xiaohong",age:19,sex:1});`

插入多条数据：`db.collection_name.insert([{},{}]);` 。[{},{}] 。每一个文档，就是一条数据！

eg：`db.book.insert([{_id:2,name'xiao2',age:19,sex:1},{_id:3,name:'xiao3',age:19,sex:2}]);`

> 使用js自动生成数据：
>
> ```mongodb
> function autoinsert(){
>     for(var i = 10; i < 20; i++){
>         db.book.insert({_id:i, name:"xiao" + i, age: i + 10, sex:1});
>     }
> }
> //执行这个函数
> autoinsert();
> //查看数据
> db.book.find();
> ```

#### （4）文档（document）：更新数据、删除数据 

更新数据：`db.collection_name.update({},{});`

> db ：表示当前库
>
> collection_name：集合名称
>
> update：操作动作（更新）
>
> {}：第一个文档，更新条件
>
> {}：第二个文档，更新的值

~~eg：`db.book.update({name:"xiaohong"}, {age:10});`~~

> 注：此用法，在更新之后，只保留更新的值与ID，其它的字段都不在了，因此使用下面的方法

>  选择器：
>
> $set：只修改更新的内容，其它值保持不变
>
> $inc：数值型值，指定自增多少，或者自减多少
>
> `db.collection_name.update({},{});`
>
> {}：第一个文档，更新条件
>
> {}：第二个文档，更新的值。第二个文档的内容写法：$set:{更新的内容}
>
> eg：`db.book.update({name:"xiaoming"},{$set:{age:10}});`

指定自增：$inc

`db.book.update({_id:2}, {$inc:{age:1}});`

指定直减：$inc

`db.book.update({_id:11}, {$inc:{age:-1}});`

修改内容的数量：$set

`db.book.update({age:20}, {$set:{sex:2}});`

> mongodb是一个海量的数据库，默认只是更新匹配到的第一个值！

更新数据的量，第三个文档：`db.collection_name.update({},{},{});`

> {}：第三个文档里面可以有二个值
>
> ​         multi：true 。true值，表示满足匹配条件的数据，全部更新；false（默认）值，表示更新第一条匹配到的数据。
>
> ​         upsert：true 。true值，表示没有匹配到任何数据，转成插入该数据；false（默认）值，表示没有匹配到任何数据，放弃操作。与MySQL里面的replace语法效果相同。
>
> 更新全部匹配的值：multi:true

eg：`db.book.update({age:20},{$set:{sex:3}},{multi:true});`

更新数据的时候，没有匹配的值，插入该数据：upsert:true 

eg：`db.book.update({age:18},{$set:{sex:2}},{upsert:true});`

> upsert与multi是可以同时使用的！
>
> `db.book.update({age:20}, {$set:{sex:2}},{upsert:true,multi:true});`

删除数据：`db.conllection_name.remove({});`

> db ：表示当前库
>
> collection_name：集合名称
>
> remove：操作动作（删除）
>
> {}：第一个文档，删除条件；空文档，匹配全部数据

eg：`db.book.remove({age:18});`

> 删除数据的时候，默认是匹配到多少，删除多少。

删除数据的量，第二个文档：`db.conllection_name.remove({},{});`

> {}：第二个文档里面可以有一个值
>
> ​         justOne：true 。true值，表示满足匹配条件的数据，删除一条；false（默认）值，表示匹配到的数据，全部删除。

eg：`db.book.remove({age:20},{justOne:true});`

删除空文档的效果：`db.book.remove({});`，此时的数据就全部删除了

#### （5）删除collection与删除db

删除集合：`db.collection_name.drop();`

eg：`db.book.drop();`

删除数据库：`db.dropDatabase();`

> 重点强调：要删除哪一库，必须在这个库里面操作！

#### （6）瞬间完成

> 上面的插入，删除和更新操作都是瞬间完成的，它们不需要等待数据库响应。这样的实现可以获取高性能，速度非常快，只会受客户端发送速度和网络速度的制约。但由于不会获取服务器状态，所以不能保证操作顺利完成。这对于付费系统，安全性较高的系统是不可行的，此时对这些操作需要使用它们的安全版本。安全版本会在操作执行后立即运行getLastError命令，来检查是否执行成功。如果失败一般会抛出可捕获的异常，然后我们可以在代码中处理。

### 5、MongoDB进阶 

#### （1）MongoDB帮助系统 

系统帮助：`help` 

> help后面不要加分号[;] 

库帮助：`db.help();`

集合帮助：`db.collection_name.help(); `

操作动作帮助：`db.collection_name.find().help();`

> mongodb的帮助信息，非常的方便，可以直接实现。

#### （2）查询

> \$eq（等于）；\$ne（不等于）； \$lt（小余）；\$lte（小余等于）；\$gt（大余）；\$gte（大余等于）；\$in（包含）；\$nin（不包含）；\$and（并且）；$or（或者） 

等于：`db.collection_name.find({name:{$eq:’xiao0’}});`

eg：`db.book.find({name:{$eq:"xiao0"}});`

不等于：`db.collection_name.find({name:{$ne:’xiao0’}}); `

eg：`db.book.find({name:{$ne:"xiao0"}});`

小于：`db.collection_name.find({age:{$lt:14}});`

eg：`db.book.find({age:{$lt:14}}); `

小于等于：`db.collection_name.find({age:{$lte:14}});`

eg：`db.book.find({age:{$lte:14}});`

大于：`db.collection_name.find({age:{$gt:14}});`

eg：`db.book.find({age:{$gt:14}}); ":`

大于等于：`db.collection_name.find({age:{$gte:14}}) ;`

eg：`db.book.find({age:{$gte:14}});`

> 总结：\$eq    \$ne    \$lt    \$lte    \$gt    \$gte
>
> db.collection_name.find({field:{$eq:value}})

包含：$in；

`db.collection_name.find({field:{$in:[value,value]}});`

eg：`db.book.find({age:{$in:[13,14,15]}});`

不包含：$nin；

`db.collection_name.find({field:{$nin:[value,value,value]}});`

eg：`db.book.find({age:{$nin:[13,14,15]}});`

> 总结：\$in    \$nin 
>
> db.collection_name.find({field:{$in:[v,v,v]}});

并且：$and

`db.collection_name.find({$and:[{age:1},{name:”xiao1”}]});`

eg1：`db.book.find({$and:[{name:"xiao0"},{age:10}]});`

eg2：`db.book.find({$and:[{name:"xiao0"}, {age:{$lt:11}}]});`

或者：$or

`db.collection_name.find({$or:[{field:value},{field:value}]});`

eg：`db.book.find({$or:[{name:"xiao0"},{age:{$gt:18}}]});`

> 总结：\$and   \$or
>
> db.collection_name.find({$and:[{field:value},{field:value}]});

> 查询条件说明：
>
> 操作动作find示例，演示了匹配条件。其它的操作动作只要有匹配条件的，都可以使用这些匹配条件。
>
> 例如，操作动作update（更新）的第一个文档，就是匹配条件，这些就可以完全被使用。
>
> 例如，操作动作remove（删除）的第一个文档，就是匹配条件，这些就可以完全被使用。

查询动作（find）的过滤动作介绍： 

内容跳过：skip 

`db.book.find().skip(2);`

> skip(n)  :number是一个整型。

内容显示：limit

`db.book.find().limit(2);`

> limit(n)：number是一个整型 。

内容统计：count 

`db.book.find().count();`

内容统计：size

`db.book.find().size();`

内容排序：sort

> field:value 
>
> value === 1 ：升序；`db.book.find().sort({name:1});`
>
> value === -1 ：降序；`db.book.find().sort({name:-1});`

实现分页效果：skip与limit

`db.book.find().skip(2).limit(2);`

> count默认是不认可skip与limit的操作的。

使用skip或者limit的时候：

`db.book.find().skip(8).limit(2).count(true);`

约束返回数据：

`db.collection_name.find({},{});`

> {}：第二个文档，表明返回哪些字段的数据。
>
> _id:value 。0值，不返回；1值，返回
>
> 设置注意：
>
> 同时设置\_id与其它字段时。\_id是要返回，其它字段必须是返回；\_id是不返回，其它字段可以返回或者不返回。
>
> 设置多个其它字段时，其它字段的设置状态必须保持一致。

`db.book.find({},{_id:1});`

> \_id如果返回，其它字段也必须返回；
>
> \_id如果不返回，其它字段可以返回或者不返回：
>
> `db.book.find({},{_id:0, name:1,age:1});`，此时就是返回的字段是：name，age

> 设置多个其它字段时，其它字段的设置状态必须保持一致：
>
> ``db.book.find({},{_id:0, name:0,age:0});``
>
> 下面是错误写法：
>
> ~~```db.book.find({},{_id:0, name:0,age:1});```~~

整型与字符：

`db.book.insert({_id:10,name:"xiao10",age:"19",sex:1});`

查看age是19的：`db.book.find({age:19});`

> 说明：mongodb里面，是区分字符数字与整型的。

集合下的统计：count

`db.book.count({_id:{$gt:15}});`

> 在Mongodb里面有很多方法实现相同的效果，掌握一种就可以了 。

#### （3）聚合

`db.collection_name.aggregate({});`

> {}：文档里面可以有三个值
>
> $match:{}  
>
> ​	{} 这个文档里面的值，同find的查找内容别无二致。
>
> $group:{}
>
> ​	 {} 这个文档里面的值，非常重要。
>
> ​	_id ：这个是必须写的，强制要求的，文档有强调。

> 例：
>
> {_id:'123'}; 
>
> 给每一行文档一个固定的值是123，对文档的123值进行分组。分组结果：_id是字段，值是123。
>
> {_id:"$sex"};
>
> 对每一行文档里面的sex字段值进行分组。分组结果：_id是字段，值是sex字段不重复的值。 
>
> {_id:"$sex",  fieldname:{$sum:1}};
>
> 给每一行文档一个固定的值是1。对每一行文档里面的sex字段值进行分组，统计每一个分组里面固定值1的和（sum），并且把和的结果给字段fieldname。分组结果：_id是字段，值是sex不重复的值；fieldname是字段，值是统计固定值1的结果。 
>
> {_id:"$sex",  fieldname {$sum: "$age"}} 
>
> 对每一行文档里面的age字段值进行分组，统计每一个分组里面sex值的和（sum），并且把和的结果给字段fieldname。分组结果：_id是字段，值是age不重复的值；fieldname是字段，值是统计sex值的结果。
>
> \$sum ：求和；\$avg ：求平均值；\$max ：求最大值；$min：求最小值。
>
> $sort:{}
>
> {}这个文档里面的值，同find的排序动作（sort）别无二致。 age:1 。值1，升序；值-1，降序 

示例：$match；

`db.quan6.aggregate({$match:{_id:{$lt:5}}});`

eg：`db.book.aggregate({$match:{_id:{$lt:5}}});`

示例：$group

eg1：`db.book.aggregate({$group:{_id:123}});`

eg2：`db.book.aggregate({$group:{_id:"$sex",persions:{$sum:1}}});`

eg3：`db.book.aggregate({$group:{_id:"$sex",persions:{$sum:"$age"}}});`

> 统计所有人年龄的平均值：`db.school.aggregate({$group:{_id:1,avg:{$avg:"$age"}}});`
>
> 统计不同性别的年龄的平均值：`db.school.aggregate({$group:{_id:"$sex",avg:{$avg:"$age"}}});`
>
> 统计所有人的年龄的最大值：`ab.school.aggregate({$group:{_id:1,man:{$min:"$age"}}});`

示例：$sort

升序：`db.school.aggregate({$sort:{sex:1}});`

降序：`db.school.aggregate({$sort:{sex:-1}});`

> 三大值都单词操作完成了：组合使用！
>
> `db.collection_name.aggregate([{},{},{}]);`
>
> 三个大的类型，可以写一个，写二个，或者写三个。

> \$match与\$group
>
> `db.school.aggregate({$match:{_id:{$lt:8}}},{$group:{_id:"$sex",avg:{$avg:"$_id"}}});`

> \$match与\$sort
>
> `db.school.aggregate({$match:{_id:{$lt:8}}},{$sort:{age:-1}});`

> \$group与\$sort
>
> `db.school.aggregate({$group:{_id:"$sex", agemax:{$max:"$age"}}},{$sort:{agemax:1}});`
>
> 发现：2个一起使用时，第一个结果就是第二个的参数

> 三个连用：
>
> `db.school.aggregate([{$match:{_id:{$ne:10}}},{$group:{_id:"$sex",agesum:{$sum:"$age"}}},{$sort:{_id:1}}]);`
>
> 最好的使用顺序就是match然后是group再是sort 

#### （4）mapReduce：批量操作 

`db.collection_name.mapReduce(map, reduce, {}); `

> map：函数名称。
>
> 扫描集合中的所有文档，把每一行文档的数据拿来处理。每一行文档数据，都执行一次map函数。
>
> 执行map函数，this表示正在执行的这行文档对象。使用某一个字段，就使用this.field。
>
> 例如：获取年龄（this.age）
>
> 函数执行完成，想要存储结果，必须调用内置emit函数。
>
> 该函数必须有二个参数：emit(v1, v2)。
>
> function map(){ emit(this.sex, this.age) }
>
> 假如：文档数量有4个！map实现是emit(this.sex, this.age)
>
> 扫描第一行文档的时候，产生的结果：array(sex => array(age))
>
> 扫描第二行文档的时候，产生的结果：array(sex => array(age, age))
>
> 扫描第三行文档的时候，产生的结果：array(sex => array(age, age, age))
>
> 扫描第四行文档的时候，sex和上面的三个不一样了，产生的结果：
>
> ​         array(sex => array(age, age, age), sex => array(age))

> 数值：（age : 10; sex :1）; （age:11; sex:1）;  age: 12; sex:1）; （age:13; sex:0）
>
> emit(this.sex, this.age)
>
> 扫描第一行文档的时候，产生的结果：array(1 => array(10)) 
>
> 扫描第二行文档的时候，产生的结果：array(1 => array(10, 11))
>
> 扫描第三行文档的时候，产生的结果：array(1 => array(10, 11, 12))
>
> 扫描第四行文档的时候，产生的结果：array(1 => array(10, 11, 12), 0 => array(13))
>
> reduce：函数名称。该函数有二个参数(v1, v2)
>
> ​         第一个参数，是emit函数的key值
>
> ​         第二个参数，是emit函数的value值（数组）
>
> reduce函数调用的次数，由emit函数存储的key数量决定。emit函数有二个key，reduce函数就调用二次。
>
> reduce函数，就是去循环emit函数的结果
>
> {}：文档。设置不同作用的值一个
>
> ​         out:{inline:1} 。打印到屏幕
>
> ​         out:{replace:”collection_name”} 。结果存储到collection_name集合里面
>
> collection_name集合存在就覆盖，不存在就创建！

实现map函数：

获得sex与age，存储起来

```mongodb
function map(){
    var age = this.age;
    var sex = this.sex;
    emit(sex,age);
}
```

实现reduce函数：

求的不同性别年龄的和！

```mongodb
function reduce(sex, ages){
    var agesum = Array.sum(ages);
    var res = sex + '-' + agesum;
    return res;
}
```

> 实践：
>
> `db.quan6.mapReduce(map,reduce,{out:{inline:1}});`

#### （5）索引

查看索引：`db.collection_name.getIndexes(); `

eg：`db.book.getIndexes();`

创建索引：`db.collection_name.createIndex({field:1});`

> field:1 。1值是升序索引；-1值是降序索引。

eg：`db.book.createIndex({age:1});`

> 之所以写索引名称，就是方便管理

删除索引：`db.collection_name.dropIndex(索引名); `

eg：`db.book.dropIndex('age_1');`

删除主键索引：`db.book.dropIndex('_id_');`

创建唯一索引：`db.collection_name.createIndex({field:1},{unique:true}); `

> unique:true ：是true的时候，才是唯一索引，强调在创建的时候，值必须是没有重复的 

eg：`db.book.createIndex({name:1},{unique:true});`

测试唯一索引：`db.collection_name.insert({});`

eg：`db.book.insert({_id:10,name:"xiao9",age:19,sex:1});`

不使用重复的值：`db.book.insert({_id:10,name:"xiao10",age:19,sex:1});`

查看索引被使用了：`db.collection_name.find({''}).explain('executionStats'); `

eg：`db.book.find({name:"xiao9"}).explain('executionStats');`

创建一个降序索引：`db.collection_name.createIndex({field:-1}); `

eg：`db.book.createIndex({age:-1});`

删除全部索引，不包含主键索引：`db.collection_name.dropIndexes();`

eg：`db.book.reopIndexes();`

> 索引是为了海量数据准备的，所有有很多不一样的特性。都是因为数据不一样而产生的。mongodb还有很多种索引的类型。 

#### （6）固定集合

> 就是提前创建好集合，并且设定它有大小。里面的数据，不能删除。数据存储满了之后，使用最近最少原则删除内容。
>
> 作用，当项目有一些数据，是前面的数据无用的。保持一定的数据量，就可以使用它。

`db.createCollection('collection_name', {});`

> 文档的说明：
>
> ​         capped:true 。true值的时候，表示是创建的固定集合。必须的值
>
> ​         size:50 。设置集合的存储空间大小。必须的值
>
> max:3 。设置集合最大的文档数量。可选的值 
>
> 强调：max与size这二个限制大小的操作，任何一个先到达，就开始执行最近最少使用原则删除数据。 

创建一个：`db.createCollection('python',{capped:true,size:50,max:3});`

查看集合：`show tables;`

插入数据：`db.python.insert({_id:1,name:"xiao1",age:12});`

查看数据：`db.python.find();`

更新数据：`db.python.update({_id:2},{$set:{age:22}});`

删除数据：`db.python.remove({_id:5});`

删除集合：`db/python.drop();`

> 固定集合，是可以删除的。但是里面的数据是不能删除的。只能插入与更新。 

#### （7）GridFS功能 

> 就是把文件以二进制的形式，上传到mongodb里面存储的。获取出来的时候，直接转换成了曾经的模式。 

①找到工具：在目录`/working/mongodb/bin`下的`mongofiles`文件。

> 它可以把任何文件上传到mongodb里面。无论是图片，还是压缩文件，还是文本文件。都可以以二进制的形式，存储到mongodb的数据库里面。

> mongodb会以2G的空间大小，划分出区域来存储这些内容。这个时候，2G来划分，就很难产生硬盘的碎片！对搜索数据就会有性能的提升。

②查看帮助信息并确定参数：`/wordking/mongodb/bin/mongofiles --help`

> 确定参数：
>
> list：显示；search：查询；put：上传；get：下载；delete：删除
>
> 上传与下载的时候，指定文件路径。

③上传文件：`/working/mongodb/bin/mongofiles --db=data put install  --local=/root/install.log.syslog `

显示一下：`/working/mongodb/bin/mongofiles list --db=data`

④下载文件：`/working/mongodb/bin/mongofiles get install --local=/home/install.log --db=data`

⑤搜索：`/working/mongodb/bin/mongofiles search a --db=data`  自带模糊匹配

删除：`/working/mongodb/bin/mongofiles delete apr --db=data`

### 6、MongoDB的用户管理

#### （1）MongoDB中的权限角色

角色：

收银员（虚拟）：收钱，发钱。

​         权限是挂在角色上面。

李白：拥有收银员的角色

​         权限：收钱，发钱。

​         用户是要有角色的，通过角色就有相应的权限。

> mongodb里面是使用的这种权限。还是有其它权限管理的。 

mongodb里面默认的角色：

库的角色：

read ：读的权限

readWrite ：读写的权限

userAdmin ：管理库的权限



集群的角色：

readAnyDatabase ：集群的读的权限

readWriteAnyDatabase ：集群的读写的权限

userAdminAnyDatabase ：集群的管理库的权限



管理员：root

mongodb里面有很多已经设定好的角色，太多了，需要的在看

#### （2）开启MongoDB中的权限控制

启动mongodb服务器的时候，加上参--auth就可以了。开启之后，登录mongodb服务器，就必须要使用账户与密码。 

> 注意：在开启账户与密码的时候，一定一定要提前设置好有管理员的权限（至少）。

提前设置好：集群的管理库的权限

```MongoDB
db.createUser({
user:’username’,
pwd:’password’,
roles:[{role:'rolename', db:'管理的库'}]  
})
```

> 重点介绍：mongodb里面用户是属于某一个库的。必须在这个库里面才能使用这个用户。 

创建管理员：

> 注意，必须先进入admin这个库。这个库曾经是管理员的库，然后现在默认是自己创建用户的时候，才生成。以前是自带的。所有现在创建第一个管理员，保持和曾经一样，先进入这个库。在这里创建一个管理员。 

1）进入admin库

`use admin`

2）创建一个管理员

```MongoDB
db.createUser({
    user:"zhang",
    pwd:"123456",
    roles:[{role:"userAdnubAnyDatabase", db:"admin"}] //权限是集群的管理库的，属于admin库
})
```

3）查看当前库

`show collections`

查看数据：`db.system.indexes.find();`，这个就是索引。

查看创建用户名信息：`db.system.users.find();`

4）客户端退出

`exit`

5）停止服务器

先查看进程，通过进程来关闭！`ps aux | grep mongod`

> 使用kill 的时候，注意，一定不要使用-9来关闭。因为mongodb里面的官方文档说的，不要使用。
>
> 如果你使用了-9，有可能就启动不起来了。如果你启动不起来了。就查看一下目录：`/working/mongodb/data`，删除data目录下面的mongod.lock即可。

6）启动服务器，加上—auth参数 

`/working/mongodb/bin/mongod --port 27017 --fork --logpath /working/mongodb/logs/mongod.log --logappend --storageEngine mmapv1 --dbpath /working/mongodb/data/ --auth `

7）使用客户端登录：

`/working/mongodb/bin.mongo 192.168.xxx.xx:27017`

此时登录成功后如果要`show dbs`时，如果发现报错了，这是因为没有权限。

用户是属于库的，所以我们要进入用户所在的库，进行登录。 

选择`use admin`库后，再进行登录：`db.auth('zhang','123456');`，显示1，则是登录成功。再查看当前库：`show collections;`，此时也会报错，'zhang'是管理员，没有查看的权限。

8）使用管理员，创建一个用户。

```mongodb
db.createUser({
    user:"adminr",
    pwd:"123456",
    roles:[{role:"read",db:"admin"}]  //权限为读，用户属于admin这个库
})
```

使用这个新的用户：`use admin`选择库后，进行登录：`db.auth("adminr","123456");`，查看库：`show tables;` ，是可以查看的，但是继续查看`db.system.users.find();`时又会报错，还是没有权限。

9）进入到管理员

`use admin`，并且登录`db.auth('zhang','123456');`

10）给quan库里面撞见一个readWrite的用户

`use quan`

```mongodb
db.createUser({
    user:"quanrw",
    pwd:'123456',
    roles:[{roles:"readWrite", db:"quan"}]
})
```

使用这个账户：`use quan`，登录一下：`db.auth('quanrw', '123456');`，查看库：`show tables;`，这个库里没有东西，但是可以往里面写内容：`db.quan.insert({_id:1, name:"123"});`，使用一下读操作：`db.quan.find();`即可读出数据。

### 7、使用PHP操作MongoDB 

#### （1）安装php_mongo扩展

给php安装mongodb的扩展，让php成为可以连接mongodb服务器的客户端！ 

http://pecl.php.net/package-search.php?pkg_name=mongo&bool=AND&submit=Search

> mongo有2个扩展，任意选择一个都可以。但是mongo扩展只能是php7以下的，mongodb扩展支持7以上的。 

安装linux扩展： 

得到扩展的压缩包，放到linux下，解压：`tar -zxf /存放路径/mongodb-1.3.4.tgz`

进入到该目录下，再确定好PHP版本后安装mongodb扩展， 我们要给这个安。就要使用里面的phpize与php-config这2个程序。只能是这个版本的，不能混用 。

编译安装：`./configure --with-php-config=/working/php7/bin/php-config && make && make install `

安装完成后查看扩展，在目录：`/working/php7/lib/php/extensions/no-debug-zts-20160303/`下，会有一个`mongodb.so`文件。

配置php.ini信息：`vim /working/php7/etc/php.ini`

> 打开的这个php.ini配置文件，肯定是和phpize与php-config同一个版本下面的。 

进入文件后，在大概929行左右，添加：`extension=mongodb.so`

验证一下：`/working/php7/bin/php -m`，如果出现mongodb时说明安装成功。

#### （2）使用PHP连接MongoDB 

> mongodb扩展与mongo扩展，他们的操作方式是完全不一样的。
>
> 你使用哪一个扩展，就使用哪一个扩展的说明文档，进行操作。

```php
$manager = new MongoDB\Driver\Manager("mongodb://quanrw:123456@192.168.xxx.xx:27017/quan");
var_dump($manager);
```

如果配置信息没有错误，可以打印出相关的对象信息。

#### （3）使用PHP+MongoDB实现CURD操作 

```php
$insert = new MongoDB\Driver\BulkWrite;
$insert -> insert(['_id' => 1, 'name' => 'xiao1', 'age' => 10, 'sex' => 1]);
$insert -> insert(['_id' => 2, 'name' => 'xiao2', 'age' => 10, 'sex' => 1]);
$insert -> insert(['_id' => 3, 'name' => 'xiao3', 'age' => 10, 'sex' => 2]);
$insert -> insert(['_id' => 4, 'name' => 'xiao4', 'age' => 10, 'sex' => 2]);

$manager = new MongoDB\Driver\Manager("mongodb://quanrw:123456@192.168.xxx.xx:27017/quan");
$manager -> executeBulkWrite('quan.quan6', $insert);
var_dump($manager);
```

访问页面后可以看到打印出的数据信息，可以再进入数据库验证数据：

```mongodb
show tables;
db.quan6.find();
```

更新数据：

```php
$update = new MongoDB\Driver\BulkWrite;
//mongodb写的时候，一定要写单引号
$update -> update(['sex' => ['$eq' => 1]], ['$set' => ['age' => 11]], ['multi' => true, 'upsert' => true]);
//连接mongodb数据库
$manager = new MongoDB\Driver\Manager("mongodb://quanrw:123456@192.168.xxx.xx:27017/quan");
//执行构建
$manager -> executeBulkWrite('quan.quan6', $update);
var_dump($manager);
```

删除数据：

```php
$delete = new MongoDB\Driver\BulkWrite;
//在php里面limit是真的时候，表示删除一条数据
$delete -> delete(['age' => 10], ['limit' => 1]);
//连接mongodb数据库
$manager = new MongoDB\Driver\Manager("mongodb://quanrw:123456@192.168.xxx.xx:27017/quan");
//执行构建
$res = $manager -> executeBulkWrite('quan.quan6', $delete);
var_dump($res);
```

查询数据：

```php
//查询条件
$filter = ['_id' => ['$in' => [1, 2, 4]]];
//过滤选择
$options = [
    //查询想要的字段：字段值如果对应的是0，不显示。对应的值是1，就是显示
    'projection' => ['_id' => 0, 'name' => 1, 'age' => 1],
    'sort' 		 => ['age' => 1],
    'skip' 		 => 1,
    'limit'		 => 1,
];
$query = new MongoDB\Driver\Query($filter, $options);
//连接MongoDB服务器
$manager = new MongoDB\Driver\Manager("mongodb://quanrw:123456@192.168.xxx.xx:27017/quan");
$cursor = $manager -> executeBulkWrite('quan.quan6', $query);
foreach($cursor as $value)
{
    var_dump($value);
    echo '<br>';
}
```

聚合：

```php
//聚合使用
$options = [
    //aggregate表示聚合，对应的是聚合名称
    'aggregate' => 'quan6',
    'pipeline' => [
        ['$match' => ['_id' => ['$in' => [1, 2, 4]]]],
        ['$group' => ['_id' => '$sex', 'agesum' => ['$sum' => '$age']]],
        ['$sort'  => ['agesum' => 1]],
    ],
    'cursor' => new stdClass,
];
//构架聚合
$command = new MongoDB\Driver\Command($options);
//连接MongoDB服务器
$manager = new MongoDB\Driver\Manager("mongodb://quanrw:123456@192.168.xxx.xx:27017/quan");
//执行构建
$cursor = $manager -> executeBulkWrite('quan', $command);
foreach($cursor as $val)
{
    var_dump($val);
    echo '<br>';
}
```









































