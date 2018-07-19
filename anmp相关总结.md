# AMP安装

## 源码包相关

> 源码包安装步骤：
>
> 第一步（configure）：我们需要检测环境；配置程序的安装内容；生成makefile文件！ 
>
> 第二步（make）：读取makefile文件，生成二进制文件！
>
> 第三步（make install）：把二进制文件安装到系统中！
>
> > 注：
> >
> > 第一步，如果失败，请检测你的软件，是不是可以安装到该系统的，不能就请找到合适的软件。
> >
> > 第二步，生成二进制文件失败，解决我们的问题；使用make clean清空生成的内容。从第一步再次执行！
> >
> > 第三步，安装软件到系统中，失败就请解决问题，并且使用make clean清空生成的内容。从第一步再次执行。

## Apache相关

###0、准备好Apache和相关依赖的软件

比如：

> apr-1.6.3.tar.gz、apr-util-1.6.1.tar.gz、expat-2.2.5.tar.bz2、http-2.4.29.tar.gz、openssl-1.1.0g.tar.gz、pcre-8.41.tar.gz

###1、将准备好的压缩包进行解压操作：

`tar -zxf 压缩包名`

###2、进入到httpd文件夹里：

`./configure --help` 可以查看安装的帮助说明

> --disable  ： 这类参数表示：你使用了这个参数，软件里面这个功能将不可用；反之，你不使用这个参数，软件里面的这个功能，就可以使用。
>
> --enable ：这类参数表示：你使用了这个参数，软件里面这个功能将可用；反之，你不使用这个参数。软件里面的这个功能，就不可以使用。
>
> --with ：这类一般情况要跟一个目录，就是对应的依赖软件的安装目录。如果你使用的是rpm安装的，就可以不使用这个参数，系统也可以找到。重点，如果系统的默认环境里面可以找到该依赖软件，就可以不使用这个参数。否则必须写。
>
> 看看目前需要什么参数：
>
> --prefix  ：源码安装，一般都会指定安装位置
>
> --enable-so ：这个开启功能；有了这个功能。PHP才可以以动态扩展的方式加入到HTTP
>
> --with-apr  ：apr软件的安装位置
>
> --with-apr-util ：apr-util软件的安装位置
>
> --with-pcre ：pcre软件的安装位置
>
> --with-z ：zlib压缩软件的安装位置
>
> --enable-deflate   减免传输编码支持
>
> --enable-rewrite    基于规则的URL操作

###3、解决prefix的参数

将Apache安装到/working目录下：`mkdir /working`

###4、解决with-apr的参数

进入到解压目录apr-1.6.3，查看帮助信息来确定使用参数：`./configure --help`

> 确定的参数：
>
> --prefix ：安装位置
>
> ​         把安装的程序放在/usr/local/下面，就可以了。默认这个是依赖软件。其它程序，有可能也会依赖这个软件。安装在这个默认位置。那其他管理员，需要的时候，就会看见它。就不必须安装重复了。

源码安装三步走：

第一步：`./configure --prefix=/user/local/apr`   参数后面的是目录名称，这个目录是不存在的，创建好程序后这个目录会自动生成；

第二步：`make` 

第三部：`make install` 

如果没有出现错误，说明Apache安装成功！

###5、解决with-apr-util的依赖软件expat参数：

进入expat-2.2.5目录，查看安装软件的帮助信息：`./configure --help`

> 这里也有--prefix参数，但是在这里是不能写的，写了之后会编译失败。所以不需要任何参数，直接执行。

源码安装三步走一起执行：

`./configure && make && make install `

> 编译的时候，警告直接忽略，错误才要解决

###6、解决with-apr-util的参数

进入到apr-util-1.6.1目录下，查看帮助信息：`./configure --help`

> 确定使用参数
>
> --prefix ：安装位置
>
> --with-apr ：这个是apr软件安装的位置

源码安装三步走：

`./configure --prefix=/usr/local/apr-util-1.6.1 --with-apr=/usr/local/apr-1.6.3/ && make && make install apr `

###7、解决with-pcre的问题

进入目录pcre-8.41，查看帮助信息：`./configure --help`

> --prefix ：安装位置
>
> ​         补充：依赖软件程序的时候，如果不是必要的，直接默认就可以了。

源码安装三步走：`./configure --prefix=/usr/local/pcre-8.41 && make && make install`

### 8、解决-with-z的问题

进入目录zlib-1.2.11，查看帮助信息：`./configure --help`

> 确定参数：--prefix

源码安装三步走：`./configure --prefix=/usr/local/zlib-1.2.11 && make && make install`

###9、配置好httpd的参数

进入到Apache安装目录httpd-2.4.29

① 检查环境，生成makefile文件

> ./configure  \
>
> --prefix=/working/httpd-2.4 \
>
> --enable-so \
>
> --with-apr=/usr/local/apr-1.6.3 \
>
> --with-apr-util=/usr/local/apr-util-1.6.1 \
>
> --with-pcre=/usr/local/pcre-8.41 \
>
> --with-z=/usr/local/zlib-1.2.11 \
>
> --enable-deflate   \
>
> --enable-rewrite   \

②make读取makefile文件，生成二进制文件：`make`

③`make install` 安装到系统

### 10、启动Apache

`/working/httpd-2.4/bin/apachectl start`

如果是安装完第一次启动出现报错的信息时没有域名时，需要进入配置文件修改配置：

`vim /working httpd-2.4/conf/httpd.conf`

在ServerName处添加：`ServerName localhost`，保存退出

重启Apache：``/working/httpd-2.4/bin/apachectl restart`

### 11、配置快捷启动及开机自启

在/bin目录下把Apache生成软连接后放进去即可：

`ln -s /working/httpd-2.4/bin/apachectl /bin/apache`

设置开启自启：

`echo "/working/httpd-2.4/bin/apachectl start" >> /etc/rc.d/rc.local`

###12、Apache虚拟主机配置

> ServerName ：设置域名
>
> DirectoryIndex ：设置默认访问首页
>
> DocumentRoot ：设置web站点目录
>
> Options  ：这个是选项参数。只是这个，不可以修改的。
>
> +Indexes  ：2.4版本大家必须写；[+ | -] 
>
> ​         + 找不到默认访问首页的时候，就显示目录；
>
> ​	  - 找不到默认访问首页的时候，就返回403。 
>
> +FollowSymLinks ：链接支持；最好是写+号；个人测试这个参数的时候，写成-号对有一些图片格式有影响；图片不会显示出来。
>
> +ExecCGI ：cgi脚本的支持
>
> AllowOverride All ：ALL值是对分布式配置的支持。就是可以把apache的配置文件，单独的写在别一个文件里面，这个文件的修改，可以对这个网站生效。但是不需要重启apache。所以这个是很多网站都在使用的功能；none，关闭分布式的支持。
>
> 
>
> Order allow,deny
>
> Allow from all
>
> ​         2.2版本的访问控制
>
> Require all granted
>
> ​         2.4版本的访问控制

虚拟主机配置示例：

```Apache虚拟主机
<VirtualHost *:80>
    DocumentRoot "web站点目录"
    ServerName www.xxx.com
  <Directory />
      Options -Indexes +FollowSymLinks +ExecCGI
      AllowOverride All
      Order allow,deny
      Allow from all
     Require all granted
  </Directory>
</VirtualHost>
```

## Nginx相关

###1、Nginx特点

> 1）热部署 ：修改配置了之后，不用去重启就可以加载修改的内容
>
> 2）可以高并发连接 ：相比于apache来说，支持的并发连接数比apache高
>
> 3）低的内存消耗
>
> 4）处理响应请求很快
>
> 5）具有很高的可靠性

###2、Apache与Nginx服务器区别

> 1）nginx和apache的软件底层架构不一样。
>
> ①：Nginx的并发性要比apache好很多；
>
> ②：nginx属于轻量级服务器软件，apache属于重量级软件；
>
> ③：nginx在处理静态页的效率要比apache好很多，apache在处理动态页面上的效率要比nginx高
>
> ④：apache在安全性要比nginx要好。
>
> 因此有一种不常用的组合：lnamp。
>
> 2）运行模式不同的
>
> ①：apache运行PHP是通过加载php模块运行。由于是apache去加载php模块，所以每次修改了php.ini配置文件需要重启apache。
>
> ②：nginx运行php是通过网络连接php-fpm（fastCGI）方式运行。 php-fpm是一个独立的软件（默认端口：9000）。因此在nginx下修改了php.ini配置文件需要重启php-fpm。

### 3、Nginx安装

#### 准备好nginx的压缩包

例如：nginx-1.12.2.tar.gz

#### 解压文件

`tar -zxf nginx-1.12.2.tar.gz`

#### 进入到该目录，并查看帮助信息

`./configure --help`

确定使用参数：

> ./configure  --prefix=/working/nginx  \
>
> --with-zlib=  压缩的支持
>
> --with-pcre=  正则的支持\
>
> --with-http_ssl_module  https的支持 \
>
> --with-openssl= ：openssl的支持
>
> --user=www  --group=www

> 注：在帮助信息中有这个参数--with-openssl=DIR，这里要指定的是源码路径，而不是安装好的路径

#### 需要的依赖

版本例如：openssl-1.1.0g.tar.gz、pcre-8.41.tar.gz、zlib-1.2.11.tar.gz

将上面三个依赖的压缩包解压后，进入nginx的解压目录。

####开始编译Nginx

```nginx编译
./configure  --prefix=/working/nginx-1.12.2  \
--with-zlib=/root/zlib-1.2.11  \
--with-pcre=/root/pcre-8.41  \
--with-http_ssl_module  \
--with-openssl=/root/openssl-1.1.0g  \
--user=www  --group=www  \
```

> --user 子进程运行的用户名；--group 子进程运行的组名。
>
> www：这个是必须要存在的。

检查用户名与组名是否存在：`grep www /etc/passwd`  `grep www /etc/group`

如果不存在则创建：`useradd -r www`  //-r ：创建一个系统用户；就是用户编号在500以内的。 

####读取makefile文件，生成二进制文件 

`make`

####安装到系统

`make install`

###4、Nginx配置文件详解

打开配置文件：`vim /working/nginx-1-12-2/conf/nginx.conf`

> user  username  [groupname] ：设置程序运行时候的用户
>
> worker_processes :配置的子进程数量；配置成cpu的核心数
>
> 错误日志  日志位置    错误级别
>
> error_log  logs/error.log  notice
>
> pid文件存储的位置 ：主程序的进程号
>
> pid        logs/nginx.pid
>
> events ：事件模型
>
> ​         可以设置几个事件模型。
>
> ​         worker_connections ：连接数，并发连接数。
>
> ​                   1024秒来计算：
>
> ​		   设置这个值，是按最高峰来设置的。某一时间最高峰来设置。
>
> include 引入一个文件！
>
> 默认设置     二进制数据流
>
> default_type  application/octet-stream;
>
> 设置日志   给日志取个名称      获得服务器的值
>
> log_format   main               ‘’;
>
> 访问日志   日志位置      日志名
>
> access_log  logs/access.log  main
>
> sendfile    ：开启linux内核的支持；
>
> tcp_nopush  ：默认是对数据流的设定。让多次数据流满足一定大小之后，一起发送。
>
> ​         tcp_nopush这个要开户；就必须开启sendfile支持，开启之后是0.2秒发送一次。
>
> keepalive_timeout ：连接时长，单位是秒
>
> gzip ：压缩
>
> server ：这个里面设置的，就是虚拟主机。
>
> listen 监听端口
>
> server_name ：设定域名
>
> charset ：设置字符集
>
> access_log ：访问日志
>
> ​         访问日志只可以在虚拟主机里面，也可以在虚拟主机外面。
>
> location ：设定一个段 
>
> ​         root ：设置web站点目录
>
> ​         index ：设置默认访问首页
>
> error_page ：设定错误码  指定要访问的页面
>
> ​         location = /50.html  访问50.html，在有错误的时候。
>
> ​                   root ：web站点目录
>
> 代理的设置
>
> ​         proxy_pass 代理的设置。
>
> ~ ：表示使用正则  \.php$ 匹配.php结尾的文件 
>
> root ：指定web站点目录
>
> fastcgi_pass ：fcgi的连接地址
>
> fastcgi_index ：访问的首页
>
> fastcgi_param：定义一个常量
>
> include 引入常量

### 5、设置工作进程的数量

`worker_processes  1;`

###6、单个进程的连接数（并发量）

```nginx配置文件
events {
    worker_connections  1024;
}
```

### 7、虚拟主机的配置

```nginx
root /working/nginx-1.12.2/html
location / {
    index  index.html index.php index.htm;
}

location ~ \.php$ {
    fastcgi_pass    127.0.0.1:9000;
    fastcgi_index   index.php;
    fastcgi_param   SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include		    fastcgi_params;
}
```

检查一下：配置有没有问题！`/working/nginx-1.12.2/sbin/nginx -t`

启动：`/working/nginx-1.12.2/sbin/nginx 回车`

停止：强行停止  `/working/nginx-1.12.2/sbin/nginx -s stop` 不建议使用

停止：通知停止 `/working/nginx-1.12.2/sbin/nginx -s quit` 建议使用

热加载：`/working/nginx-1.12.2/sbin/nginx -s reload`

## PHP安装相关

> Apache的PHP是专门配置的，Nginx的PHP也是单独为Nginx配置的，这两个PHP不能混在一起！为了更好区分两种PHP，为Apache安装PHP7，为Nginx安装PHP5.6

### （1）、为Apache安装配置PHP7

####①解压安装包

在得到PHP7安装包后解压：`tar -xf php-7.1.13.tar.gz`

####②进入解压目录

查看帮助信息：`./configure --help`

确认参数：

> ./configure \
>
> --prefix=/working/php7 \
>
> --with-config-file-path=/working/php7/etc/  \
>
> --with-apxs2=/working/httpd-2.4/bin/apxs \
>
> --with-mysqli --with-pdo-mysql --enable-mysqlnd \
>
> --enable-mbstring --enable-opcache \
>
> --enable-bcmath --enable-zip \
>
> --enable-pcntl --enable-shmop --enable-soap \
>
> --enable-sockets --enable-sysvsem \
>
> --with-gettext --with-iconv-dir \
>
> --with-pcre-regex --with-zlib \
>
> --with-pear --with-xmlrpc \
>
> --with-freetype-dir\
>
>  --with-jpeg-dir\
>
>  --with-png-dir \

> 其中的参数：--with-config-file-path 指定php.ini在哪个位置。
>
> --with-apxs2 指定httpd里面的apxs软件的位置。写错了。PHP与HTTPD的模式就没有办法生成。然后httpd就没有办法解释PHP文件。 

#### ③源码安装第一步

将上面确认好的参数粘贴到PHP目录下回车开始进行编译。

出现报错时：缺少哪个就上网找哪个依赖的安装方法。

#### ④源码安装第二步

make 读取makefile文件，生成二进制文件：`make`

#### ⑤源码安装第三步

make install 读取二进制文件，安装到系统 ：`make install`

#### ⑥查看安装目录

进入PHP目录下的etc下，发现php.ini文件不存在。

​	找到配置文件，将php.ini复制到安装目录中：

`cp php.ini-development /working/php7/etc/php.ini `

打开php.ini文件修改时区

`date.timezone = Asia/Shanghai  `

> 如果配置完成后访问PHP文件时发现没有解析时：
>
> `vim /working/httpd-2.4/conf/httpd.conf`
>
> 添加：`AddType application/x-httpd-php  .php`
>
> 保存退出，重启Apache。

### （2）为Nginx安装配置PHP5.6

> Nginx服务器下，使用PHP时需要php-fpm，php-fpm是独立的程序，必须要单独安装。nginx要通过网络来访问这个。有的时候php-fpm没有反应，nginx会报502错误；502网关错误。你访问的服务器的上由服务器有问题。
>
> php-fpm是在php的源码包里面就自带的。只要我们在配置参数的时候，启动这个这个参数，就可以了。

下载openssl,解压并编译安装

`wget http://www.openssl.org/source/openssl-1.0.0s.tar.gz`

`tar -zxvf openssl-1.0.0s.tar.gz`

`cd openssl-1.0.0s`

`./configure && make  && make install`

#### ①下载PHP5.6并解压

`wget http://cn2.php.net/distributions/php-5.6.14.tar.gz`

`tar -zxvf php-5.6.14.tar.gz `

#### ②进入解压目录

查看帮助信息：`./configure --help`

确认参数：

> ./configure  --prefix=/working/php5.6 \
>
> --with-config-file-path=/working/php5.6/etc/  \
>
> --enable-fpm  --with-fpm-user=www  --with-fpm-group=www  \
>
> --with-mysqli  --with-pdo-mysql  --enable-mysqlnd  \
>
> --enable-mbstring  --enable-opcache  \
>
> --enable-bcmath  --enable-zip \
>
> --enable-pcntl  --enable-shmop  --enable-soap \
>
> --enable-sockets  --enable-sysvsem \
>
> --with-gettext  --with-iconv-dir \
>
> --with-pcre-regex  --with-zlib \
>
> --with-pear  --with-xmlrpc \
>
> --with-openssl  --with-curl  --with-gd  --with-xsl  \
>
> --with-freetype-dir\
>
>  --with-jpeg-dir\
>
>  --with-png-dir \

> 注：--with-fpm-user=www --with-fpm-group=www  这二个配置一定要和nginx的运行用户名与组名一样。nginx可以直接访问静态资源，PHP和静态资源是放在一起的吧。权限设置就保持一致。使用相同的用户名与组名。权限就容易设置。 

#### ③源码安装第一步

检查环境，生成makefile文件。将上面准备好的参数粘贴到PHP目录下，回车编译。

出现报错时，缺少什么就百度相关的安装方法。

#### ④源码安装第二步

读取makefile文件，生成二进制文件： `make`

#### ⑤源码安装第三步

make install 读取二进制文件，安装到系统 ：`make install`

#### ⑥查看安装目录

将php.ini复制到配置目录下：`cp php.ini-development /working/php5.6/etc/php.ini `

> 找到php-fpm独立的程序： `/working/php5.6/sbin/`
>
> 找到它的配置文件： `/working/php5.6/etc/`
>
> 其中`php-fpm.conf.default`是主配置文件，`php-fpm.d`是副配置文件。
>
> 让主配置文件生效：`cp /working/php5.6/etc/php-fpm.conf.default /working/php5.6/etc/php-fpm.conf`
>
> 让副配置文件生效：`cp /working/php5.6/etc/php-fpm.d/www/conf.default /working/php5.6/etc/php-fpm.d/www.conf`
>
> 启动php-fpm：`php-fpm   回车就是启动。 `
>
> > 如果要重启php-fpm，需要杀死该进程再启动：`pkill php-fpm`或者`kill -USR2 fpm的进程号`

打开php.ini文件修改时区

`date.timezone = Asia/Shanghai  `

## MySQL安装

MySQL安装步骤：

> 第1步：找到安装包，一定是已经编译好的
>
> 第2步：移动到/usr/loca/mysql里面
>
> 第3步：初始化数据，得到root账户密码
>
> 第4步：属主属组的修改
>
> 第5步：启动服务器
>
> 第6步：使用客户端登录服务器，修改密码！！

### 1、解压压缩包

将准备好的二进制压缩包：mysql-5.7.21-linux-glibc2.12-i686.tar.gz解压

`tar -zxf mysql-5.7.21-linux-glibc2.12-i686.tar.gz`

### 2、移动解压文件夹

把解压文件夹移动到/usr/local/mysql目录下：

`mv mysql-5.7.21-linux-glibc2.12-i686/ /usr/local/mysql`

> 原本在local文件夹内没有mysql目录，是把二进制文件包移动过去并更名成mysql目录

> 强调：mysql必须放在这个目录里面，因为这个是别人配置好的（配置在/usr/local/mysql/support-files/下的mysql.server中）。如果你不放在这个目录里面，就会出问题的。 

###3、初始化数据

> 这是MySQL5.7版本才会初始化数据，在5.5版本不使用这个

在目录/usr/local/mysql/bin/中的mysqld

> 初始化参数：
>
> --defaults-file=指定配置文件的路径。
>
> --initialize 初始化的意思
>
> --user=指定子进程运行的用户

要配置这些参数，先把my.cnf文件，移动到/etc目录下：

`mv /最开始的解压目录/my.cnf /etc/`

移动完成后，--defaults-file的参数就确定完毕：

`--defaults-file=/etc/my.cnf`

打开一下my.cnf，有如下内容：

```my.cnf配置内容
[clienr]
port	= 3306
socket	= /tmp/mysql.sock

[mysqld]
port	= 3306
user	= mysql
datadir	= /usr/local/mysql/data/
socket	= /tmp/mysql.sock

[mysqld_safe]
log-error = /var/log/mysqld.log
pid-file  = /var/run/mysqld.pid
```

> 在上面参数--user：指定的运用用户，必须使用mysql。使用的这个用户，要与配置文件里面的保持一致。一个地方修改，另一个地方就要修改。 

检查MySQL用户是否存在：`grep mysql /etc/passwd`

如果在passwd中存在mysql用户就可以直接使用，如果不存在就使用useradd创建它

### 5、实践初始化

`/usr/local/mysql/bin/mysqld --defaults-file=/etc/my.cnf --initialize --user=mysql`

在初始化结束后会显示出一个root账户的密码，保存好，后面登录要用！

> 如果把密码搞忘记了，就要去日志文件里面查看。如果还找不到，就删除数据目录下面的所有数据，重新初始化。

### 6、修改文件的属主和数组

属主：root

属组：mysql

因为有目录存在的，所以要递归修改！

`chown –R root:mysql /usr/local/mysql`

单独修改数据目录：属主:mysql; 属组：mysql：

`chown –R mysql /usr/local/mysql/data`

###7、启动MySQL

查看/etc/my.cnf文件，如果文件权限是777，这需要改成644：

`chmod 644 /etc/my.cnf`

对MySQL服务进行操作：

命令：`mysql.server  start | stop | restart`

eg：`/usr/local/mysql/support-files/mysql.server start`

###8、登录MySQL

`/usr/local/mysql/bin/mysql -u root -p 回车`

提示输入密码后，将刚才初始化得到的密码复制到这里即可登录。

成功登录后首先修改密码：`set password=password('设置的密码');`

完成！