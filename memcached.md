# Memcache技术概述

Memcached 是国外社区网站LiveJournal的开发团队开发的高性能的分布式内存缓存服务器。一般的使用目的是，通过缓存数据库查询结果，减少数据库访问次数，以提高动态 Web 应用的速度、提高可扩展性。

memcached  memory cache  = 内存缓存！

就是把数据存储在内存中的管理设备

数据库的数据：把数据库的数据使用PHP处理之后的数据存储进来；

静态化的数据：静态资源都是可以存储到这里的！

> memcached与mysql存储的数据区别：
>
> memcached里面的数据，如果没有，就可以到mysql存储的数据里面去查询。
>
> memcached里面的数据，是临时的，可以消失的，也是不重要的。
>
> mysql里面的数据，是非常重要的，不能遗失的！

> memcached特性：
>
> memcached的数据是不可持久化，只能保存在内存里面。
>
> 持久化：把数据存储到硬盘！
>
> memcached只支持key=>value的数据结构，所有只有字符型。
>
> memcached的key支持250字节；
>
> memcached的value支持1M大小；
>
> memcached的数据不可备份不可主从

我们memcached放弃了一些东西，得到了高速的响应速度。

memcached可以支持的并发数量上万。所有很多时候，memcached都不会成为网站的瓶颈。

在代码里使用它：

```php
//连接memcached
...
$data = $mem -> get('data');
if(empty($data))
{
    //连接MySQL获取新的数据
    ...
    $ret = mysqli_query($db, $sql);
    //处理ret
    ...
    $data  //处理完成之后，得到data数据
    //存储到memcached
    $mem -> set('data', $data);
}
```

#Memcache软件安装（linux下）

## 1、下载这个软件

http://memcached.org/ 

## 2、在linux上面查看

###（1）解压

解压软件和依赖软件：

`tar -zxf memcached-1.4.34.tar.gz`和`tar -zxf libevent-2.0.22-stable.tar.gz`

### （2）安装依赖包

进入到依赖包的文件夹里，查看帮助信息：`./configure --help`

> 确定使用的参数：--prefix：安装的位置

进行源码安装：

`./configure --prefix=/usr/local/libevent && make && make install`

### （3）安装memcached

进入memcached的目录，查看帮助信息：`./configure --help`

> 确定使用的参数：
>
> --prefix：安装的位置
>
> --with-libevent：依赖安装的位置

进行源码安装：

`./configure --prefix=/working/memcached \ --with-libevent=/usr/local/libevent/`

`make && make install`

### （4）查看帮助信息

`/working/memcached/bin/memcached -h`

> 需要知道的参数：
>
> -p：默认端口号是11211，可使用-p来修改
>
> -d：守护进程，后台运行
>
> -u：指定运行的用户，是root
>
> -m：管理的内存
>
> -c：同时连接数

## 3、启动Memcache服务器

`/working/memcached/bin/memcached -d -u root`

查看是否启动成功，查看进程：

`ps aux | grep memcached`

> PID号，就是主进程（父进程）的进程号。有一些程序直接写在文件里面的。 

查看端口号：

`netstat -tanp | grep memcached`

##4、连接Memcache服务器 

找到客户端：`telnet 192.168.xxx.xx 11211`

如果没有连接成功，可能是linux防火墙没有通过11211这个端口

> 关闭防火墙：
>
> （1）临时关闭：立即生效
>
> `service iptables stop`
>
> （2）永久关闭：重启之后生效
>
> ```linux
> # chkconfig --list iptables
> iptables	0：关闭  1：关闭  2：启用  3：启用  4：启用  5：启用
> 这个数字表示的是开机启动的模式；终端模式是3；图形化是5；
> 启用：表示使用该模式启动的时候，该程序会被启动。
> 关闭：表示使用该模式。程序不会随系统启动。
> ```
>
> `chkconfig --level 2345 iptables off`

在关闭防火墙后再使用telnet登录。

## 5、Memcache命令详解 

> 可以使用终端使用telnet命令，连接上我们的memcached服务器！请注意，我们使用的telnet命令，并不是我们memcached专用的客户端，有可能会出现小问题，这都是正常！
>
>  我们在telnet连接的命令窗口操作的命令，是我们窗口操作模式的实现，我们在PHP里面的操作，是完全不一样的。

### （1）命令

> xshell中使用telnet连接的命令，是为了方便我们检查服务器里面的数据；假如你使用PHP写入数据在memcached服务器里面了，这个时候，你想要检查正确与否，就使用xshell使用telent连接上服务器，进行数据检查！

add ：添加数据。只能添加key是不存在的。如果key是存在的就报错

`add  key  0  0  4`

比如：`add name 0 0 5` 回车后输入：`luhao`即可保存

> key ：设置一个下标
>
> 0：压缩
>
> 0：时间
>
> 4：你要设置的值，是4个字节

set：添加数据。如果key是存在的，就直接覆盖。`set key 0 0 4`

比如：`set age 0 0 2` 回车后输入：`12`

get：获得值`get key` 

比如：`get age`

incr：指定增加多少。`incr key 10`

比如：`incr age 10` //年龄+10

decr：指定减少多少。

`decr age 10`

> decr与incr可以操作的最大范围是2^64无符号：0 – (2^64 – 1)
>
> 只能在这个范围里面去设置，过了这个范围，就会报错。
>
> 当incr到达最大值的时候，比如`set age 0 0 20` -> `18446744073709551610` -> `incr age 1`
>
> age的值会变成0。所以，使用incr到达最大值的时候，会直接从0开始，继续向前加。
>
> 而decr在到达最小值的时候，一直减下去，依然是0。

delete：删除一个key；`delete key`

比如：`delete age`

flush_all：清空；`flush_all`

> 清空时要注意，不要随便清空数据，因为在数据库里会有别人项目的数据。

stats：查看状态；`stats`

> 当我们使用get一次的时候，获取到了数据就是命中；没有获取到数据，就是未命中 。

> memcached是缓存设置，是为了解决mysql连接的问题。所以我们缓存的数据，应该保持比较高的命中，这样才能解决mysql连接的问题。
>
> 如果命中不高，就证明缓存的数据有问题了。应该重新设置一下缓存数据。让缓存有意义。

### （2）PHP中memcached扩展的对象方法对比

> 如果用PHP操作memcached，必须把PHP变成memcached服务器的客户端，就要安装memcached的扩展。安装好了，就变成客户端了。

> 安装客户端的时候，你安装的是memcache，查看文档的时候，就找memcache，你安装的是memcached，你看文档的时候，就是memcached。 
>
> memcache不支持PHP7；memcached支持PHP7。 

> `public bool Memcached::add(string $key, mixed $value [, int $expiration])`
>
> 该方法，如果key已经在服务端存在，此操作会失败。其中参数expiration，是到期时间，默认为0，如果该值设置为默认0时，则此元素永不过期（但是它可能由于服务器为了给其它新元素分配空间而被删除）。

> `public bool Memcached::set(string $key, mixed $value [, int $expiration])`
>
> 该方法，将value存储在memcached服务器上的key下，如果有expiration参数，则可以控制值的过期时间。值可以是任何有效的非资源型php类型，因为资源类型不能被序列化存储。如果Memcached::OPT_COMPRESSION选项开始，序列化的值会同样被压缩存储。

> 注意：资源类型的不能被序列化，所有不可以存储到memcached里面。 

## 6、PHP中使用Memcache

### （1）在linux中安装扩展memcached.so 

到扩展网站，下载对应文件包：

http://pecl.php.net/package-search.php?pkg_name=memcache&bool=AND&submit=Search 

上传到linux中，解压后开始操作。

#### ①安装依赖

进入到解压的依赖目录中`/libmemcached-1.0.18`：

`./configure --prefix=/usr/local/libmemcached && make && make install`

#### ②安装memcached扩展

进入到memcached扩展目录`memcached-3.0.4`

但是在该目录中没有找到configure文件！

> configure这个文件，必须由你为哪一个PHP版本生成扩展，就由这个版本的phpize程序执行完成来生成的。 

找到php安装目录开始操作

`/working/php7/bin/phpize`回车，即可生成memcached的configure文件

回到memcached安装目录，查看帮助信息：`./configure --help`确定参数。

> --prefix ：严重警告！！给PHP安装扩展的时候，不要指定安装的位置！！
>
> --with-php-config  ：这个就是PHP版本里面的php-config的绝对路径。确定你找的这个php-config的绝对路径，是与上面生成configure的phpize程序是同一个版本下面的。
>
> php-config  phpize  ：这二个程序，必须是同一个PHP版本下面的。
>
> --with-libmemcached-dir  ：安装的依赖路径。

检查环境，生成makefile文件

`./configure --with-libmemcached-dir=/usr/local/libmemcached/ --with-php-config=/working/php7/bin/php-config`

如果出现报错是缺少一个`sasl.h`这个依赖，可以选择不安装，这个依赖不是必须存在的。

关闭来操作：

`./configure --with-libmemcached-dir=/usr/local/libmemcached/ --with-php-config=/working/php7/bin/php-config --disable-memcached-sasl `

安装到系统：`make && make insatll`

安装完成后可以到目录`/working/php7/lib/php/extensions/no-debug-zts-20160303`下，可以看到`memcached.so`，这个就是生成的扩展文件。

> 注：linux上的php扩展，都是以.so结尾！

打开php.ini，大概在900+行的位置，配置添加：`extension=memcached.so`

重启Apache！

#### ③检查扩展

方式一：`php -m`

方式二：用php程序来执行phpinfo.php页面

`/working/php7/bin/php /站点目录/phpinfo.php | grep memcached`

### （2）php连接memcache服务器

```php
//连接memcached
$mem = new memcached();
$mem -> addServer('192.168.xxx.xx', 11211);
$res = $mem -> getStats();
var_dump($res);
```

## 7、探究Memcache能存储的数据类型

### （1）使用PHP存储标量类型

#### 字符类型：

```php
$mem = new memcached();
$mem -> addServer('127.0.0.1', 11211);
$str = 'qwerty';
$mem -> set('str', $str);
```

执行页面后，进入memcached服务器查看：

`get str`

#### 布尔类型

```php
$mem = new memcached();
$mem -> addServer('127.0.0.1', 11211);

$true = true;
$mem -> set('true', $true);
$false = false;
$mem -> set('false', $false);
```

执行页面后，进入memcached服务器查看：

`get true`

`get false`

> 说明：当我们存储数据的时候，memcached扩展帮助我们实现了隐式转换，然后在存储到memcached服务器。 

#### 整型类型

```php
$mem = new memcached();
$mem -> addServer('127.0.0.1', 11211);
$int = 123;
mem -> set('int', $int);
```

执行页面后，进入memcached服务器查看：

`get int`

#### 浮点类型

```php
$mem = new memcached();
$mem -> addServer('127.0.0.1', 11211);
$f = 123.123;
mem -> set('f', $f);
```

执行页面后，进入memcached服务器查看：

`get f`

> 通过这四种类型，都可以正常存储

### （2）使用PHP存储复合类型

#### 数组

```php
$mem = new memcached();
$mem -> addServer('127.0.0.1', 11211);
$arr = [1, 2, 3, 4];
mem -> set('arr', $arr);
```

执行页面后，进入memcached服务器查看：

`get arr`

> 这个数据，就是memcached扩展帮助我们序列化之后，存储到里面的。获得的时候，memcached扩展也会帮助我们反序列化回来。

#### 对象

```php
$mem = new memcached();
$mem -> addServer('127.0.0.1', 11211);
$obj = new A;
$mem -> set('obj', $obj);
$res = $mem -> get('obj');
var_dump($res -> getName());

class A
{
    public $name = 'zhangsan';
    public function getName()
    {
        return $this -> name;
    }
}
```

执行页面后，由于在代码中有打印代码，所以会在浏览器中显示出“zhangsan”。

进入服务器查看：`get obj`

> 服务器存储的数据，就是序列化之后的结果。

###（3）使用PHP存储特殊类型

####null

```php
$mem = new memcached();
$mem -> addServer('127.0.0.1', 11211);
$null = NULL;
$mem -> ('null', $null);
$res = $mem -> get('null');
var_dump($res);
```

查看服务器：`get null`

结果：这个也是序列化之后的数据。

#### 资源

```php
$mem = new memcached();
$mem -> addServer('127.0.0.1', 11211);
$re = $mem;
$mem -> set('re', $re);

$res = $mem -> get('re');
var_dump($res -> getStats());
```

访问浏览器时出现报错！

> 原因：资源类型不能被序列化存储。
