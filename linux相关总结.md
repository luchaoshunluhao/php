## Shell指令

查看系统里面的shell类型：cat /etc/shells

type：不是shell程序自带的命令，使用type查看的时候就会告诉你，程序安装的位置。

​       eg：```type vim```        `type cd`

cd：参数，- 返回上次所在目录，eg：cd –；~ 表示家目录，eg：cd ~

shutdown：-h 关机；-r 重启；-k 通知；-c结束其它进程的关机或者重新操作。

halt：关机；reboot：重启

grep：文本内容搜索。grep 搜索的值 搜索的文本；eg：grep size anaconda-ks.cfg

grep是以行来进行搜索。只要行内有搜索的值，就会把当前行显示出来。

## Vim编辑

k：向上移动；j：向下移动；h：向左移动；l：向右移动

gg：光标移动到文档首；10gg：光标移动到第10行；

G：光标移动到文档末；10G：光标移动到第10行；

dd：删除；u：还原；yy：复制；p：粘贴；

2dd：删除2行，当前行和下一行；2yy：复制2行，当前行和下一行；

i：在光标的当前位置当前位置插入；

a：在光标的后面位置插入；

o：在光标所在行的最后面插入；

A：在光标所在行的最后面插入；

I：在光标所在行的最前面插入；

O：在光标的上一行插入；

复制：4co 6 把第4行复制到第6行的下一行；

移动：4m 1 把第4行移动到第1行；

4,6co 11：把第4行到第6行的内容复制到第11行的下面去

4,6m 1：把第4行到第6行的内容移动到第1行的下面去；

:/size ：查找关键词’size’；n：正向查找；N：方向查找；

取消高亮：set nohls；设置高亮：set hls；

C：删除光标所在位置到行末，进入插入模式；

D：删除光标所在位置到行末；

## rpm的使用

rpm –qa 查看系统上面安装的所有rpm包

-q 查询；-a 所有

统计命令：wc；-l统计行

wc –l 文本名   ：统计文本文件里面有多少行

rpm –qa | wc –l：把rpm查询出来的结果，统计多少行

rpm –qa | grep vim：把rpm查询出来的结果，搜索vim软件

使用rpm卸载软件

rpm –e 软件名

使用rpm安装软件

rpm –ivh rpm软件包

i 安装；v 显示安装过程；h 安装过程的进度条以#来表示

## 用户与组

用户文件所在路径：

`/etc/passwd 用户文件；/etc/shadow 密码文件`

组文件所在路径：

`/etc/group ；/etc/gshadow`

### 用户相关指令

添加用户：

useradd 用户名

修改用户：

usermod –c 注释 用户名

`usermod –c zhushi luhao`

usermod –l 新的用户名 用户名

`usermod –l luhaohao luhao`

usermod –u 编号 用户名

`usermod –u 666 luhao`

usermod –g

`usermod –g 502 luhao`

删除用户

userdel –r 删除时把创建的时候自动创建的东西(家目录)一并删除

`userdel –r luhao`

不带r参数是不删除家目录

### 组相关指令

groupadd：添加组

`groupadd –g 666 ming  //添加一个组编号为666 组名为ming的组`

groupmod：修改组

`groupmod –n newming ming //把组名为ming修改为newming`

`groupmod –g 520 new ming //把组名为newming的修改编号为520`

groupdel：删除组

`groupdel newming 把组名为newming的删除`

 

gpasswd 附属组

`gpasswd –a luhao root //把用户名为luhao的添加到root组中`

`gpasswd –d luhao root //把用户名为luhao的从root组中删除`

 

使用man查看帮助信息

`man ls    man userdel`

或者-h；--help也是可以查看帮助信息

 

设置用户口令：passwd

```linux
passwd –S root  //-S为查看状态，查看root用户的状态

passwd –l luhao   //-l为锁定密码，锁定用户luhao的密码

passwd –u luhao   //-u为解除锁定，解除用户luhao的密码

passwd –d luhao  //-d为删除密码，删除用户luhao的密码

```

### 显示用户的信息

`id root  //显示root用户的id`

`groups root  //显示root用户组信息`

### 查看用户资料信息

`chfn luhao`

## 文件操作

### 更改文件权限

`chmod 777 文件名`

字符表示法

属主：u；属组：g；其它：o；全部：a。

添加：+；减去：-。

`chmod u-rwx index.html`

-R:递归修改

`chmod o-rx abcd/   //把目录为abcd的权限及子文件减去可读可执行权限`

chown：修改属主

`chown luhao 文件名`

也可以递归修改

chown 属主:属组 文件名

`chown root:root abcd/`

chown :属组 文件名

`chown :root index.php`

chgrp：修改属组

chgrp 属组名 文件名

### 目录操作

mkdir

-p：递归创建；

`mkdir –p a/b/c/d`

-m 创建的时候，指定权限。

`mkdir –m 615 目录`

rmdir 删除目录

-p：递归删除

`rmdir –p a/b/ `  注：递归删除时，必须写到最里层

rmdir这个命令是不使用的。

### 文件操作

touch：创建文本文件

`touch index.php`

rm：删除文件

-f：强制删除；-r：删除目录。删除目录一般配合强制删除

`rm –rf abcd`

cp：复制

`cp index.php /home  //复制文件到home目录下`

-R：递归复制。 注：如果不用-R参数时，是没有办法直接复制目录

`cp –R /home/luhao ./`

-p：复制的时候，不修改属性。在备份的时候，必须使用！

`cp –p install.log /home/`

mv：剪切、移动

移动文件

`mv install.log.syslog /home/`

可以同时移动多个文件。移动的位置必须是目录，移动的位置，就是最后一个参数。

`mv /home/index.html /home/index.php /home/index.xml /root`

移动目录

`mv /luhao/ /tmp/`

当使用移动时，移动的目标目录还是文件当前目录，且更换名称时，则会实现重命名。

### wc文件统计

wc：统计

-l：```wc -l index.php```  //统计在index.php中有多少行

-c：```wc -c index.php```  //统计在本文件中有多少字符

-w：```wc -w index.php```  //统计在本文件中有多少词

注：分词是以空格为区分的。这个是英文分词。

### grep搜索匹配行

grep：文本搜索

```grep echo index.html```  

-n：显示行号

```grep -n echo index.php```

-c：统计搜索的次数

```grep -c h index.php```

-i：不区分大小写

`grep -i H index.html`

### 显示文件内容

cat：打印内容到屏幕

`cat index.php`

tac：以行倒着打印到屏幕

`tac index.html`

head：显示前10行内容

`head /etc/passwd`

-n：指定显示前几行的内容

`head -n 3 /etc/passwd`  //显示文件passwd的前3行内容

tail：显示文本内容的后面10行

`tail /etc/passwd`

-n：指定显示后几行的内容

`tail -n 3 /etc/passwd`  //显示文件passwd的后3行内容

### 创建链接

#### 硬链接：

是在硬盘上面实现的。数据区与属性都是相同的。修改一个，其它的都要一起变。硬链接数，就是innode编号的连接数量。

ln：创建链接

`ln 创建对象 新的对象`

eg：``ln index.html index.php``

删除硬链接：

`rm -rf index.html`  

在删除之后，数据还在，连接数减一。

#### 软连接

-s：创建软连接

`ln -s 创建对象 新的对象`

eg：`ln -s index.php index.html`

删除软连接：

`rm -rf index.php`

总结：软连接在工作中是经常使用的，它可以跨硬盘；但是硬链接不能跨硬盘

### 文件查找

find：文件进行搜索，搜索文件属性信**（经常使用）**

`find 目录参数 选项参数 搜索值`

-name：文件名

`find /root/ -name install.log`

如果需要模糊匹配，则需要使用匹配符：[*]

`find /root -name '*stall.log'`

`find /root -name 'install*'`

-group：属组

`find /root/ -group root`

搜索到的内容包含隐藏文件。隐藏文件就是文件名前面有一个 '**.**'。

-user：属主

`find /root -user root`

对比：

find：搜索的是文件属性

grep：搜索的是文本内容

文本内容搜索三剑客：grep -> sed -> awk

### 显示磁盘信息

`df`：查看硬盘空间，看看有没有占满

`df -h`：以1024进制显示**（掌握）**

`df -H`：以1000进制显示

`df -T`：显示文件系统类型

df -t：需要一个文件系统类型参数；显示指定的文件系统类型

`df -t ext4`

df -x：需要一个文件系统类型参数；不显示指定的文件系统类型  

### 文件统计

du：显示文件目录的大小

-k：指定kb大小来显示

`du -k`  注：默认的单位就是k。如果没有指定目录，则默认当前目录

-b：使用b大小来显示

`du -b`

-m：指定mb大小来显示

`du -m`

-h：以人类容易识别的方式来显示

`du -h`

-s：显示指定目录的总大小

`du -s /boot`

经常使用的是：-sh，用于查询目录大小

`du -sh /boot/`

### 挂载磁盘（应用U盘、移动设备）

光盘目录：`/dev/cdrom`

实现挂载磁盘：

`mount /dev/cdrom /media`

umount：卸载

`umount /media`

说明：

挂载的时候，有的时候出错，就直接卸载，弹出，多操作几次。然后再挂载。

卸载的时候，光盘里面的数据，不能被使用，用户所在的位置，不能在光盘。光盘是完全没有被使用的，才能实现卸载。

eject：弹出

`eject`

## 配置Linux网络

### 网络配置文件解说（/etc/sysconfig/network-scripts/ifcfg-eth0）

网络的配置非常重要，在配置之前先做好备份

`cp -p /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth0.bak`

打开配置文件

`vim  /etc/sysconfig/network-scripts/ifcfg-eth0`

文件内容解释：

>DEVICE：设备名称
>
>ONBOOT：yes：启动网卡（必需设定成这个值）；no：不启动网卡
>
>NM_CONTROLLED：yes是启动我们的网络管理软件；但是请设定成no，关闭这个网络管理软件。因为这个软件的设计初衷是好的，但是实现效果是不理想！使用它之后，问题更多。
>
>BOOTPROTO：dhcp是自动获取ip地址；none手动设置ip地址
>
>IP地址是由dhcp服务器发给用户的，dhcp服务器一般我们认识的是在路由器里面。ip地址在整个局域网内，必需是唯一的。不可重复！
>
>HWADDR：mac（物理）地址
>
>IPADDR：IP地址（在手工设置IP地址的时候才使用）
>
>NETMASK：子网掩码（在手工设置IP地址的时候才使用）
>
>GATEWAY：默认网关（在手工设置IP地址的时候才使用）
>
>手工设置ip地址的时候，是我们设定一个Ip地址，通过默认网关注册到dhcp服务器里面。请注意，一定要保存你设定的ip地址，在整个局域网内是唯一的，不可重复的。
>
> 
>
>如果没有网络基础知识，请一定使用dhcp模式，它可以自动获得ip地址！



### 网络服务操作

service：服务的意思

网络服务的名称是：network

启动网络服务：`service network start`

停止网络服务：`service network stop`

重启网络服务：`service network restart`

查看网络的状态：`service netword status`

> 如果重启不成功，就停止再启动。

### 网卡操作命令

ifdown：关闭指定的网卡

`ifdown eth0`

ifup：开启指定的网卡

`ifup eth0`

> 总结：
>
> service network 可以管理网络服务；ifconfig也可以管理网卡
>
> ifconfig是管理的指定的某一个网卡；
>
> service network是管理的整个网络服务；
>
> > 推荐使用service network来进行网络操作。

### ping命令

参数：

-c：指定只发多少个包

`ping -c 4 192.168.182.1`

> 总结：同样的命令，在不同的操作系统上面，实现方式是有所差异的。
>
> 知道软件的版本是有差异，同样的版本在不同的系统上面是有差异的。

## FTP软件



查看ftp服务状态：`service vsftpd status`

> 查看到的状态是已经停止的。停止的概念，就相当于端口已经没有被占用，已经取消了。
>
> 客户端要连接上服务器，必须是服务器已经启动，并且是监听状态。

启动|停止|重启

`service vsftpd start  |  stop   |  restart `

> 当使用远程连接工具连接服务器时如果连接成功但是有错误，是文件权限不够。
>
> 需要关闭selinux，方法如下：
>
> 立即关闭：`setenforce 0` ；数值：0，关闭；1，开启
>
> 永久关闭：修改配置文件，重启后生效。
>
> 1、`vim /etc/selinux/config`
>
> 2、编辑文件：`SELINUX=disabled`

## SSH服务

默认是启动状态，默认端口号：22

查看状态：`service sshd status`

重启|开始|停止

`service sshd restart | start | stop `

> 这个软件，一定要保持在线状态，开始状态。不能让它停上来。
>
> 这个ssh协议支持的软件，是为很多软件，提供安全保证的。

## 问题说明

如果使用软件远程连接服务器时，出现问题时排查方案：

> 1、确定二个电脑之间，是可以互通的。也可以ping的通。 
>
> 2、服务端的防火墙是允许客户端访问的 。
>
> 3、验证客户端连接的IP地址，是对的 。
>
> 4、服务端要启动 。
>
> 5、确定服务端的端口号是多少。客户端连接的端口号是没有问题的。
>
> 6、确定账户与密码是与正确 。









