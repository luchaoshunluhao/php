# 一、COOKIE

## 1、cookie概述

### （1）什么是cookie？

- COOKIE是一种**会话技术**
- 简单来说，Cookies就是服务器暂时存放在你的电脑里的资料（.txt格式的文本文件），好让服务器用来**辨认你的计算机**（通常经过加密）
- Cookie是由服务器端生成，发送给User-Agent（一般是浏览器），浏览器会将Cookie的key/value保存到某个目录下的文本文件内，下次请求同一网站时就发送该Cookie给服务器
- Cookie名称和值可以由服务器端开发者定义，这样服务器可以知道该用户是否是合法用户以及是否需要重新登录等，服务器可以设置或读取Cookies中包含信息，借此维护用户跟服务器会话中的状态

### （2）使用cookie的好处

- 用户登录时，记住登录账号和密码，为下一次登录时简化操作
- 另一个重要应用场合是“购物车”之类处理。用户可能会在一段时间内在同一家网站的不同页面中选择不同的商品，这些信息都会写入Cookies，以便在最后付款时提取信息
- 跟踪统计用户访问网站的习惯。什么时间访问，访问了哪些页面，页面停留时间等，为用户提供个性化服务，为网站营销提供参考

## 2、cookie操作

### （1）添加cookie数据

- 描述：向客户端发送一个 HTTP cookie
- 语法：**`bool setcookie ( string $name [, string $value [, int $expire = 0 [, string $path [, string $domain [, bool $secure = false [, bool $httponly = false ]]]]]] )`**
- 参数：
  - $name，cookie的名称
  - $value，cookie的值，会自动进行 URL 编码。接收时会进行 URL 解码。**COOKIE值的类型只能是标量数据类型，不可是数组、对象、资源**
  - $expire，cookie的有效期
  - $path，cookie的服务器路径
  - $domain，cookie的域名
  - $secure，规定是否通过安全的 HTTPS 连接来传输 cookie
  - $httponly设置哪些程序可以使用COOKIE
- 返回：设置成功返回TRUE，失败返回FALSE

```php
//添加cookie数据：告诉浏览器将cookie存储到本地电脑
//当前cookie的过期时间为关闭浏览器
setcookie("username", "admin");
setcookie("password", "123456");
```

### （2）读取cookie数据

- 描述：获取cookie数据是通过超全局数组$_COOKIE来实现的
- 语法：**`value = $_COOKIE[key]`**

```php
//打印$_COOKIE数组
//读取指定cookie数据
echo "用户名：" . $_COOKIE['username'];
echo "<br>密码：" . $_COOKIE['password'];
```

## 3、cookie设置

### （1）cookie过期时间

① 即时性cookie设置

- 描述：默认cookie的有效期是关闭浏览器时，自动失效。该cookie称为临时cookie
- 语法：**`bool setcookie ( string $name , string $value [, int $expire = 0 ])`**
- 参数：$expire可选，指定cookie保存的时间，默认为0，关闭浏览器失效

```php
//添加cookie数据：告诉浏览器将cookie存储到本地电脑
//当前cookie的过期时间为关闭浏览器
setcookie("username", "张三丰");
setcookie("password", "123456");
```

② 有效性cookie设置

- 语法： **`bool setcookie ( string $name , string $value [, int $expire = 0 ])`**
- 参数：$expire可选，指定cookie保存的时间，默认为0，关闭浏览器失效
- 说明：$expire为一个时间戳。一般用time()+N表示新的时间戳。在当前时间点，再加上N秒后，产生一个新的时间戳
- 提示：一旦指定了有效的过期时间，cookie失效跟关闭浏览器无关(持久cookie)

```php
//当前cookie的过期时间为1小时
setcookie("username", "admin", time() + 3600);
setcookie("password", "123456", time() + 3600);
```

③ 永久性cookie设置

- 描述：将PHP最大整数常量设置为COOKIE过期时间
- 语法：**`bool setcookie ( string $name [, string $value [, int $expire = 0]] )`**
- 参数：$expire指定为PHP最大整数常量PHP_INT_MAX的值

```php
//添加cookie数据，设置过期时间为php最大整数
setcookie("username", "admin", PHP_INT_MAX);
setcookie("password", "123456", PHP_INT_MAX);//2038年1月19日11：14：07
```

### （2）cookie路径有效性

- 描述：设置COOKIE只能在指定的目录及其子目录下有效
- 语法：**`bool setcookie ( string $name [, string $value [, int $expire = 0 [, string $path]]] )`**
- 参数：$path默认为"/"，代表**当前网站根目录**，在当前目录及其所有子目录都可以使用
- 说明：由于浏览器每次请求时，携带到服务器端很多的COOKIE数据，会导致请求数据变得庞大；将COOKIE划分到各个路径中，减少每个路径下COOKIE的数量
- 假设：京东网站往我们电脑写入了1万个COOKIE数据，但是，每个页面的COOKIE数量很少。如果每次请求，都把1万个COOKIE带过去，可能当前页面只需要5个COOKIE，则有9995个COOKIE无用。造成带宽极大浪费 

```php
//添加cookie数据：设置路径有效性
//设置cookie的有效性路径，只有访问该路径文件，cookie才带过去
setcookie("username", "admin", time() + 3600, "/php");
setcookie("password", "123456", time() + 3600, "/php");
```

### （3）cookie域名有效性

- 描述：给一个COOKIE指定访问的域名
- 语法：**`bool setcookie ( string $name [, string $value [, int $expire = 0 [, string $path [, string $domain]]]] )`**
- 提示：默认情况下，cookie只能在当前域名下有效
- 域名划分（以自己本地站点为例）：
  - 主域名：`2018.cn`
  - 一级域名：`www.2018.cn`
  - 二级域名：`news.2018.cn`
  - 三级域名：`php.news.2018.cn`
- 说明：如果COOKIE域名有效性，设置为主域名，则在所有域名下都可以使用。如果设置为子域名，则只能在子域名下使用该COOKIE

```php
//添加cookie数据：设置域名有效性
//如果设置为主域名，则在所有子域名下都可以用
//如果设置子域名，则只能在当前域名下使用
setcookie("username", "admin", time() + 3600, "/", "2018.cn");
setcookie("password", "123456", time() + 3600, "/", "news.2018.cn");
```

### （4）是否仅限https才能发送cookie呢？

- 描述：是否仅限https来发送COOKIE数据，默认情况下，为http协议发送
- 语法：**`bool setcookie ( string $name [, string $value [, int $expire = 0 [, string $path [, string $domain [, bool $secure = false]]]]] )`**
- https：是以安全为目标的HTTP通道，简单讲是HTTP的安全版。即HTTP下加入SSL层，HTTPS的安全基础是SSL，因此加密的详细内容就需要SSL

```php
//添加cookie数据：是否仅能使用https来发送cookie数据
//如果值为true，则只能通过https来发送cookie数据
//如果值为false，则在http和https都能发送cookie数据
setcookie("username", "admin", time() + 3600, "/", "2018.cn", true);
setcookie("password", "123456", time() + 3600, "/", "2018.cn", false);
```

### （5）是否只能通过http协议来使用COOKIE呢？

- 描述：客户端的COOKIE除了可以通过HTTP协议来使用外，还可以使用JS来使用COOKIE
- 语法：**`bool setcookie ( string $name [, string $value [, int $expire = 0 [, string $path [, string $domain [, bool $secure = false [, bool $httponly = false ]]]]]] )`**

```php
//添加cookie数据：是否仅能使用http来发送cookie数据
//如果值为true，则只能通过http来发送cookie数据
//如果值为false，则通过其它程序（如JS）发送cookie数据
setcookie("username", "admin", time() + 3600, "/", "2018.cn", false, true);
setcookie("password", "123456", time() + 3600, "/", "2018.cn", false, flase);
```

## 4、删除cookie数据

### （1）设置COOKIE有效性为过去的某一个时间

- 描述：设置有效期为过去的某一个时间
- 语法：**`bool setcookie ( string $name [, string $value [, int $expire = 0 ]] )`**

```php
//删除cookie数据：设置为过去的某个时间
setcookie("username", "admin", time() - 1);
setcookie("password", "123456", time() - 1);
```

### （2）设置COOKIE的值为false或空字符串

```php
//删除cookie数据
setcookie("username", false);
setcookie("password", false);
```

### （3）不设置cookie的值

```php
//删除cookie数据
setcookie("username");
setcookie("password");
```

### （4）清理浏览器缓存

清理浏览器缓存，所有的缓存数据、COOKIE数据都会被清理掉。

# 二、SESSION

## 1、session概述

### （1）什么是session？

- Session对象存储特定用户的会话数据
- Session将会话数据存储在服务器端
- Session是基于Cookie技术的，没有Cookie就没有Session
- Session在整个用户会话中，一直存在下去
- 一个用户会话时效：从用户登录开始，到用户登录结束
- Session存储的数据量要比cookie大的多
- Session存储的内容类型，不限于字符串
- Session数据存储在服务器端，更安全，可靠

### （2）开启session会话功能

- 描述：启动新会话或者重用现有会话
- 语法：**`bool session_start (void )`**
- 提示：\$\_SESSION变量默认是不存在的，与$\_POST、$\_GET、$\_FILES不一样。因此，必须先开启SESSION，才能使用$\_SESSION变量
- 提示：每个需要SESSION数据的页面，都要开始SESSION功能
- 注意：**SESSION功能，不能重复开启(同一个页面不能开2次)**
- 返回：成功开始会话返回 TRUE ，反之返回 FALSE

开启session：`session_start();`

## 2、session操作

### （1）添加session数据

- 对Session的管理(增删改查)，都是通过超全局数组$_SESSION进行的
- 语法：**`$_SESSION[key] = value`**
- 提示：一个网站不管添加多少个SESSION数据，最终在浏览器端只存储一个SESSION的ID值，该ID值是经过加密的，并且永不重复。Key只能是String类型的数据

```php
//开启session会话
//如果当前sessID不存在，则创建新的
//如果当前sessID已存在。则启用旧的
//session不能重复开启，一个页面只需要开启一次
session_start();
//添加session数据：添加一个数组元素
//元素的值，不能是资源型，其它都可以
$_SESSION['username'] = 'admin';
$_SESSION['password'] = '123456';
```

### （2）读取session数据

- 语法：**`$value = $_SESSION[key]`**
- 提示：每次SESSION操作，都要先开SESSION功能

```php
//开启session会话
session_start();
//读取指定session数据
echo "用户名：" . $_SESSION['username'];
echo "<br>密码：" . $_SESSION['password'];
```

### （3）删除session数据

- 使用 unset() 函数，删除一个SESSION数据
- 将空数组赋给$\_SESSION：$\_SESSION = array()

```php
//开启session会话
session_start();
//删除session数据
unset($_SESSION['username']);
unset($_SESSION['password']);
//将一个空数组赋给$_SESSION
$_SESSION = array();
```

### （4）销毁session文件

- 描述：删除当前的SESSION文件，不影响其它的SESSION文件
- 语法：**`bool session_destroy ( void )`**

```php
//删除自己的session文件
session_destroy();
```

### （5）获取当前sessionID和session名称

```php
//开启session会话
session_start();
//读取指定session数据
echo "用户名：" . $_SESSION['username'];
echo "<br>密码：" . $_SESSION['password'];
echo "<br>Sess名称：" . session_name();
echo "<br>SessID：" . session_id();
```

## 3、session的配置(php.ini)

### （1）修改session文件的保存位置：session.save_path

php.ini中：

```php.ini
;session.save_oath = "/tmp"     #在配置文件中这个一般不建议修改
```

### （2）修改session对应的cookie名称：session.name

```php.ini
session.name = PHPSESSID
```

### （3）修改session对应的cookie配置

① session对应cookie过期时间设置：session.cookie_lifetime

```php.ini
session.cookie_lifetime = 0
#session对应的cookie数据的过期时间，指定一个秒值即可，即应该在N秒后过期。例如：1小时秒值为3600，不使用time()+N格式
```

② session对应cookie有效路径设置：session.cookie_path

```php.ini
session.cookie_path = /
#指定cookie数据的目录有效性
```

③ session对应cookie域名有效性设置：session.cookie_domain

```php.ini
session.cookie_domain = 指定域名有效性
```

④ 是否仅限https来发送session对应的cookie数据：session.cookie_secure

```php.ini
session.cookie_secure = 是否仅限https来发送cookie数据
```

⑤ 是否仅限http来使用session对应的cookie数据：session.cookie_httponly

```php.ini
session.cookie_httponly = 是不是只能是服务器才能使用该cookie
```

## 4、session垃圾回收机制

### （1）什么是session的垃圾回收机制？

- SESSION垃圾回收， 就是将过期的SESSION服务器数据删除的机制
- SESSION会自动删除那些过期的服务器端session数据区文件
- 当我们登录后，没有单击“退出”按钮时，该用户对应的SESSION文件就变成了垃圾文件

### （2）垃圾回收的周期：session.gc_maxlifetime

```php.ini
session.gc_maxlifetime = 1440  #session垃圾文件回收的最大间隔为24分钟
```

### （3）垃圾回收的概率：session.gc_divisor

```php.ini
session.gc_probability = 1   #概率的分子
session.gc_divisor = 1000	#概率的分母
#如果有1000人访问了这个网站，那么，垃圾回收就会完整的清理一遍
```

> 说明：首先以24分钟为间隔，如果24分钟内，登录了100个用户，即1/10，24分钟一到，就清理前1/10的垃圾文件。