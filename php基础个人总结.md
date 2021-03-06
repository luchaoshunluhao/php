# php基础个人总结

标签： php

---

以下是我在学习php基础时，个人总结的一些零碎知识，本人文笔不好，可能会出现一些语句不通的情况，如有出现，欢迎各位对我严厉指出。我先发一些总结，后续部分以后会陆续更新，新的总结可能会在本篇继续更新，亦或新开一篇，具体的视篇幅而定。

### 1、注释：

php注释基本分为单行注释（//），和多行注释（/**/），这两个在平时工作和学习上使用较多，但是如果在编辑大段代码时，如果需要反复对某一部分进行注释和反注释（取消注释）的测试时，那么再使用这种常规的这些加注释的方法会变得很繁琐。

​下面我来介绍两种注释和反注释的方法，这种方法是我以前学习php基础时一位老师介绍的，个人感觉实用性很强，所以就分享给大家。在实际应用中只需要改一个数或一个符号就可以完成注释和反注释了。    

第一种方法：

```php
    //注释
    /*      
    //注意，在顶部写注释开头时不要在后面继续写代码，
    //否则在进行反注释后的本行代码还是会注释，造成代码显示不全的情况
    echo "<br />代码1";
    echo "<br />代码2";
    echo "<br />代码3";
    //*/

    //请注意看注释和反注释的区别，在进行反注释时，只需要在注释开头部分添加一个斜杠即可。

    //反注释
    //*
    echo "<br />代码1";
    echo "<br />代码2";
    echo "<br />代码3";
    //*/   
```

​    第二种方法（使用if语句）：

```php
    //注释
    if(1 == 0)
    {
        echo "<br />代码1";
        echo "<br />代码2";
        echo "<br />代码3";
    }
	//请注意看注释和反注释的区别，在进行反注释时，只需要在if条件判断部分将0改成1即可。   
	//反注释
    if(1 == 1)
    {
        echo "<br />代码1";
        echo "<br />代码2";
        echo "<br />代码3";
    }
```

以上就是注释与反注释的介绍，这样做可以节省在注释完后要取消注释时的操作时间，提高我们的编程效率。

### 2、一元运算符

一元运算符：只有一个操作数的运算符

5 + 6  两个操作数的运算符 二元运算符

++  自身加1

--   自身减1

- 前置++

  ```php
      $num1 = 5;
      ++$num1; 
  
      $num2 = 6;
      echo $num1 + ++$num2; //13
  ```

- 后置++

  ```php
      $num1 = 5;
      $num1++;    
      $num2 = 6; 
      echo $num1 + $num2 ++; //12
  ```

- 猜猜看
  ```php
      $a = 1; 
      $b = ++$a + ++$a; 
      echo $b; //5
  
      $a = 1; 
      $b = $a++ + ++$a; 
      echo $b;//4
  
      $a = 1; 
      $b = $a++ + $a++; 
      echo $b;// 3
  
      $a = 1; 
      $b = ++$a + $a++; 
      echo $b;//4
  ```

  **总结**
  **前置++：先加1，后参与运算**
  **后置++：先参与运算，后加1**；

  注意：后++ 有两个运行条件，满足其一则会执行。

  ⑴ 表达式没有结束但是又被使用了（如：$b = $a++ + $a++）；⑵表达式结束了；

  **上面两个理解后，下面两个自通**

  **前置--  ：先减1，后参与运算**
  **后置--  ：先参与运算，后减1**

### 3、错误处理

#### （1）常见的错误代号

含义：指在程序运行时，发生的错误，系统会针对每种错误，给出相应的错误代号，并进行提示（报错）。另外，程序如果在运行之前检查语法的时候就发现语法错误，也会报错，也有一个错误代号。

> 常见错误代号有：
>
> E_NOTICE：
>
> 提示性错误，轻微；
>
> 错误发生后，后面的程序继续执行。
>
> E_WARNING：
>
> 警告性错误，稍微严重；
>
> 错误发生后，后面的程序继续执行。
>
> E_ERROR：
>
> 严重错误/致命错误；
>
> 错误发生后，后面的程序不再执行！
>
> E_PARSE：
>
> 语法错误（语法解析错误）；
>
> 语法解释错误，是直接就不运行程序。
>
> E_USER_NOTICE：
>
> 用户自定义的提示错误
>
> E_USER_WARNING：
>
> 用户自定义的警告错误
>
> E_USER_ERROR：
>
> 用户自定义的严重错误
>
> E_ALL：
>
> 它是一个代表“所有”错误的代号。

> 说明：
>
> - 这些错误代号，其实只是系统预先设定的一些常量，他们的值大约是：1， 2， 4， 8， 16.....
> - 这些错误代号，通常只是用于对错误控制时进行“设置”使用。
> - 他们是一系列的整数，并具有一定的规律：1,2,4,8,16,32,64， 。。。。
> - 可以在php.ini中使用（设置）他们

#### （2）错误触发

就是发生了一个错误的意思——即触发了错误。 

有两种情形会触发错误：

- 程序本身有错，则运行时就会触发错误（并提示）。

- 程序本身没错，但出现不符合预计的情形（比如数据不符合要求）。

  此时程序员可以主动触发一个错误，也可以说是由程序员“主动创建一个错误” 。

  ——这就是“用户错误”，包括：

  ​	E_USER_NOTICE：

  ​	E_USER_WARNING：

  ​	E_USER_ERROR：

  自定义错误触发语法：trigger_error(“自定义错误提示内容”,  自定义错误的代号); 

```php
//认为触发错误：
//假设年龄在0-60范围有效：
$age = 88;
if($age > 60 || $age < 0)
{
    trigger_error('年龄不符合本公司业务范围！', E_USER_ERROR);
}else
{
    echo "年龄复合要求，请点击下一步";
}
```

#### （3）错误显示设置：

如果有错误发生（触发了错误），默认情况下会被显示在页面（即输出的结果页面）。 

我们可以对此进行设置，以决定以下两点：

- 设置display_errors以决定是否显示错误： 

  ​	在php.ini中设置：      display_errors = On或Off ，这里设置，影响所有使用该php语言引擎的代码（网站页面）； 

  ​	在php文件中设置：    ini_set(‘display_errors’, 1或0);    //1表示显示，0不显示 。在这里设置，只影响当前网页代码本身。

- 设置error_reporting以决定显示哪些类型的错误： 

  ​	在php.ini中设置：      error_reporting = 错误代号1  | 错误代号2  |  .....  //(要显示的就写出来，或者可以写E_ALL, 表示显示所有）。

  ​	在代php文件中，道理类似：ini_set(‘error_reporting’, 错误代号1  | 错误代号2  |  .....） 

#### （4）错误日志设置

如果有错误发生（触发了错误），默认情况下不会将错误信息记录（保存）下来。 

我们可以对此进行设置，以决定以下两点：

- 设置log_errors以决定是否记录错误： 

  ​	php.ini中设置： `log_errors = On 或 Off `，代码文件中设置： `ini_set (‘log_errors’, 1或0) `

- 设置error_log以决定记录到哪里：

  ​	通常，就设置为一个文件名，php系统会在网站的每个文件夹下都建立该文件，并记录错误。php.ini中： `error_log = error.txt;`       //它是纯文本的 。代码中：` ini_set(“error_log”,  ‘error.txt’);`

```php
//界面不显示错误，但是希望记录记录下来
ini_set("display_errors", 0);//不显示错误
ini_set("log_errors", 1);//记录日志
ini_set("error_log", "myError.txt");//记录到该文件中
echo $i;//E_NOTICE错误
func1();//E_ERROR错误
```

#### （5）自定义错误处理

分两步：

- 声明错误发生时，由我们自己来处理——设定一个错误处理的函数名。
- 定义该函数，在函数中详细设定错误的处理情况：怎么显示，显示什么，怎么记录，记录什么。

> 特别注意：
>
> 如果发生系统的E_ERROR级别错误，则还是不会去调用我们的自定义错误处理机制，还是按系统的处理方式来处理。

```php
//第1步：声明自定义错误处理函数：
//一旦发生错误，请找函数myErrorControl去处理
set_error_handler("myErrorControl");

//第2步：定义该函数：
function myErrorControl($errCode, $errMsg, $errFile, $errLine){
	//此函数中就可以任意发挥：想怎么处理就怎么处理！
	echo "<h1>发生错误！</h1>";
	//这里，可以来决定（通过if或其他逻辑判断）来显示或记录那些错误信息
	//下面是演示"显示"这个功能：
	echo "<br>错误代号：" . $errCode;
	echo "<br>错误消息：" . $errMsg;
	echo "<br>错误文件：" . $errFile;
	echo "<br>错误行号：" . $errLine;
	//还可以去“记录错误”：记录到文件，或记录到数据库。。。
}
echo $i;	//E_NOTICE错误，会调用自定义错误处理函数
echo $j;	//同上。
func1();	//E_ERROR错误，不会调用自定义错误处理函数
```

### 4、系统常用函数

#### （1）跟函数有关的函数

`function_exists(“函数名”)`：判断一个函数是否已经存在；

`func_get_arg( $n )`：在函数内部可用，用于获得第n个实参（n从0开始算起） ；

`func_get_args()`： 在函数内部可用，用于获得所有实参，结果是一个数组；

`func_num_args()`： 在函数内部可用，用于获得实参的个数。

上面3个函数，可以让我们在自定义的函数内部，直接访问（使用）实参数据，而不依赖于形参变量。这种特性，可以给我们定义某种“不确定有几个数据需要计算的”场合。

#### （2）字符串有关常用函数

输出与格式化：

- echo，输出字符，可以输出多个
- print，输出字符 
- printf()，输出格式化字符
- print_r()，输出简化性“数据信息”
- var_dump()，输出完整性“数据信息”

字符串去除与填充：

- trim()，去除两端字符

- ltrim()，去除左端字符

- rtrim()，去除右端字符

- str_pad()，将一个字符填充到指定的长度

- implode()，将一个数组的每一项使用某个字符“串起来”成为一个“长的字符串”，别名： join()

  `$arr = array(“12”, “345”, “abc”); `

  `$str = implode( $arr, “|”);`，结果：”12|345|abc” 

- explode()，将一个“长的字符串”，使用某个字符进行“分割”得到一个数组

  `$v1 = '12, 345, abc';`

  `$re = explode($v1, ",");  ` 结果为： array(“12”, “345”, “abc”); 

- str_split()：按给定的长度分割字符串

字符串截取： 

- substr()，截取字符串的前多少个字符 
- strchr()， 查找首次出现的字符，截取出该字符往后的所有字符
- strrchr()， 反向查找

字符串替换：str_replace()， substr_replace()

字符串长度与位置： 

- strlen()， 取得字符串的长度
- strpos()， 取得某个字符在某个字符串中的首次出现的位置
- strrpos()，取得某个字符在某个字符串中的最后一次出现的位置
- mb_strlen()，求该字符串的“字符个数” 

字符转换：

- strtolower()，  将字符串转化为小写
- strtoupper()，  将字符串转化为大写
- lcfirst()， 第一个字母转换小写
- ucfirst()，第一个字母转换大写
- ucwords()， 第一个单词转换大写

特殊字符处理：

- nl2br()，将换行符替换为”\<br\>”标签 
- addslashes()， 将一些特殊字符(‘,  “,  \ )转换成“转义形式”（\’,  \”,  \\\\)
- htmlspecialchars()， 将html中的字符转换为“实体字符”，比如：”<”转换为`&lt;`
-  htmlspecialchars_decode()，跟上面的相反过程

#### （3）常用数学函数

- max()，取得若干个数据的最大值
- min()，取得若干个数据中的最小值
- round()，对某个数据进行四舍五入（可以设定保留几位小数）
- ceil()，对某个数“向上取整”：将一个数据往上找出其小的一个整数（含其本身）。 

​        `$n1 = ceil(4.1);`      //5

- floor()，对某个数“向下取整”：将一个数据往下找出其大的一个整数（含其本身）。

​        `$n1 = floor(4.1);`    //4 

- abs()，取得某个数据的绝对值

- sqrt()，计算某个数的开方值

- pow()，对某个数进行“幂运算”（就是获得某个数的若干次方） 

  `$n1 = pow(3, 2);` //3的2次方的结果为9 

- rand()，获得某两个数之间的随机整数（含该两个数）

- mt_rand()，获得某两个数之间的随机整数（含该两个数）

  `$n1 = mt_rand(0, 10);` //随机数在0-10之间

#### （4）常用数组函数

- max()，获取一个数组中的最大值
- min()，获取一个数组中的最小值
- count()，获取一个数组的元素个数
- in_array()，判断一个数据是否在指定数组中。语法形式：$b = in_array( $数组， 数据); //结果true或false
- range()，生成某个范围的连续值的数组，比如range(3, 9)会得到数组：array(3, 4, 5, 6, 7, 8, 9, );
- array_keys()，取出一个数组中的所有“键”并放入一个索引数组中
- array_values()，取出一个数组中的所有“值”并放入一个索引数组中
- array_push()，将一个或多个数据放入一个数组的“末端”
- array_pop()，将一个数组的最后一个单元删除，并返回该单元的值
- array_reverse()，将一个数组的所有单元的顺序进行反转（最前的放最后，最后的放最前） 

常用的排序数组

- sort($arr1)，按值排序,正序,不保留原始键
- rsort($arr1)，按值排序,倒序,不保留原始键
- asort()，按值排序,正序,保留原始键
- arsort()，按值排序,倒序,保留原始键
- ksort()，按键排序,正序,不保留原始键

### 5、文件上传

#### （1）文件上传原理

- 上传文件，就是将文件从浏览器端传到服务器端
- 上传文件，必须使用\<form>标记来向服务器端发数据
- 上传文件，\<form>标记的method属性值必须是POST
- 上传文件，\<form>标记的enctype属性值必须是multipart/form-data
- 上传文件，必须使用\<input type = ‘file’ name=’upload’>标记实现

```html
	<form action="./upload.php" method="post" name='form1' enctype="multipart/form-data">
        上传图片：<input type="file" name="uploadFile">
        <input type="submit" value="上传">
        <input type="hidden" value="upload" name='ac'>
	</form>
```

#### （2）超全局变量数组 $_FILES

使用$_FILES数组，来获取上传文件的信息

```php
//获取上传文件数据
print_r($_FILES);
//可以打印出上传文件的信息，如下：
/**
 *Array
 *(
 *	[uploadFile] => Array
 *		(
 *			[name] => 上传的文件名
 *			[type] => 文件的MIME类型
 *			[tmp_name] => 文件在服务器临时位置
 *			[error] => 错误编号
 *			[size] => 文件大小
 *		)
 *)
```

#### （3）上传文件错误代码

| 错误代码 | 含义                                                     |
| -------- | -------------------------------------------------------- |
| 0        | 没有错误发生，文件上传成功                               |
| 1        | 上传的文件超过了php.ini中upload_max_filesize选项限制的值 |
| 2        | 上传文件的大小超过了HTML表单中MAX_FILE_SIZE选项指定的值  |
| 3        | 文件只有部分被上传                                       |
| 4        | 没有文件被上传                                           |
| 6        | 招不到临时文件夹                                         |
| 7        | 文件写入失败                                             |

#### （4）查看上传的临时文件位置

- 上传文件的默认临时目录，如果没有指定php.ini配置项upload_tmp_dir的值，则使用操作系统临时目录(c:\windows\temp)。通过phpinfo()函数查看
- 为什么在 c:\windows\temp 中看不到上专文件呢？因为临时文件是短暂存在的，也就是在脚本执行完毕后，就消失了

```php
//延迟后再执行一下脚本，用于观察生成的临时文件
sleep(10);
//获取上传文件数据
print_r($_FILES);
//上传的临时文件，临时文件会在网页执行完毕后删除，因此，需要在临时文件删除前，移动到永久目录下
```

#### （5）将上传文件移动到新位置

- 描述：本函数检查并确保由 filename 指定的文件是合法的上传文件（即通过 PHP 的 HTTP POST 上传机制所上传的）。如果文件合法，则将其移动为由 destination 指定的文件。最好在临时文件没有消失前移动
- 语法：**`bool move_uploaded_file ( string $filename , string $destination )`**
- 参数：$filename，指定上传的临时文件名；$destination，指定新的文件名路径
- 注意：如果文件已经存在，则会覆盖操作

```php
//将上传的临时文件，移动到网站根目录下
$tmp_name = $_FILES['uploadFile']['tmp_name'];
$dst_name = "./upload/" . $_FILES['uploadFile']['name'];
move_upload_file($tmp_name, $dst_name);
```

#### （6）上传文件的相关配置(php.ini)

- upload_tmp_dir配置：修改上传文件的临时目录
- upload_max_filesize配置：上传单个文件的大小限制，默认为2MB
- post_max_size配置：规定上传多个文件的总大小，默认为8MB
- max_file_uploads配置：规定最多上传的文件个数，默认为20个
- file_uploads配置：上传功能是否开启，默认为on

```php.ini
file_uploads = On   是否开启文件上传功能
upload_tmp_dir = 	指定上传临时文件位置，默认为c:\windows\temp
upload_max_filesize = 2M 	上传单个文件的大小
max_file_uploads = 20	一次性上传文件总数
```

#### （7）获取文件路径信息

- 描述：返回文件路径的信息
- 语法：**`mixed pathinfo ( string $path [, int $options = PATHINFO_DIRNAME | PATHINFO_BASENAME | PATHINFO_EXTENSION | PATHINFO_FILENAME ] )`**
- 参数：
  - $path，要解析的路径
  - $options，如果省略，返回全部单元，取值有：PATHINFO_DIRNAME(目录名称)、PATHINFO_BASENAME(文件名称)、PATHINFO_EXTENSION(扩展名)、PATH_FILENAME(文件名)

#### （8）检查数组中是否存在某个值

- 描述：检查数组中是否存在某个值
- 语法：**bool in_array ( mixed $needle , array $arr )**
- 参数：$needle检索的值，$arr原数组

#### （9）生成唯一ID

- 描述：生成一个唯一ID
- 语法：**`string uniqid ([ string $prefix = "" [, bool $more_entropy = false ]] )`**
- 参数：
  - $prefix前缀字符串，如果省略，返回字符串长度为13
  - $more_entropy后缀字符集，如果省略，返回字符串长度为23

#### （10）实例：单个文件上传

```php
    //判断表单是否提交
    if(isset($_POST['ac']) && $_POST['ac'] == 'upload')
    {
        //1、判断上传有没有错误发生
        if($_FILES['uploadFile']['error'] != 0)
        {
            echo '上传文件有错误发生！';
            die();
        }
        //2、上传文件大小不能超过1MB
        if($_FILES['uploadFile']['size'] > 1024 * 1024)
        {
            echo '上传文件超过了1MB的最大限额！';
            die();
        }
        //3、判断上传的文件是不是图片
        $arr = array('jpg', 'gif', 'png');
        //4、取出上传文件的扩展名
        $ext = pathinfo($_FILES['uploadFile']['name'], PATHINFO_EXTENSION);
        if(!in_array($ext, $arr))
        {
            echo '上传的类型不是图片！';
            die();
        }
        //5、移动文件
        $tmp_name = $_FILES['uploadFile']['tmp_name'];
        $dst_name = './upload/' . uniqid('s_', true) . '.' . $ext;
        move_uploaded_file($tmp_name, $dst_name);
        echo '<br>文件：' . $_FILES['uploadFile']['name'] . '上传成功！';
    }else
    {
        echo '非法操作！';
    }
```

#### （11）实例：多个文件上传

```html
	<form action="./upload3.php" method="post" name='form1' enctype="multipart/form-data">
        上传图片1：
        <input type="file" name="uploadFile[]"><br> 上传图片2：
        <input type="file" name="uploadFile[]"><br> 上传图片3：
        <input type="file" name="uploadFile[]"><br>
        <input type="submit" value="上传">
        <input type="hidden" value="upload" name='ac'>
    </form>
```

```php
    //判断表单是否提交
    if(isset($_POST['ac']) && $_POST['ac'] == 'upload')
    {
        //把三维数组变成两维数组
        $arr = $_FILES['uploadFile'];
        //循环数组
        foreach($arr['name'] as $key => $name)
        {
            //判断文件名是否为空，如果不空，则移动文件
            if($name != '')
            {
                $ext = pathinfo($name, PATHINFO_EXTENSION);
                $tmp_name = $arr['tmp_name'][$key];
                $dst_name = './upload/' . uniqid() . '.' . $ext;
                move_uploaded_file($tmp_name, $dst_name);
            }
        }
        echo '上传文件成功！';
    }
```

### 6、多态

多态通俗的说：就是多种状态，就是指在面向对象中，对象（类）在不同情况下的多种状态（根据使用的上下文）。可以通过继承父类或者实现接口而来体现多态。

> 特点：
>
> - PHP可以根据传入的对象类型不同，调用对应的该对象的方法；
> - PHP天生就是多态语言。因为你给它什么，它就是什么。

#### ① 多态的表现

先看一个简单的案例来说明一下多态：

有一个Food食物类，Food下有两个子类：Fish鱼肉类和Bone骨头类

有一个Animal动物类，Animal下有两个子类：Cat猫类和Dog狗类

还有一个Master主人类，主人类可以用于类型约束。

实现下主人给动物喂食：

```php
    header("content-type:text/html;charset=utf-8");
    //多态
    class Animal
    {
        public $name;
        public function __construct($name)
        {
            $this->name = $name;
        }
    }
    class Dog extends Animal
    {
        public function showInfo()
        {
            echo $this -> name;
        }
    }
    class Cat extends Animal
    {
        public function showInfo()
        {
            echo $this -> name;
        }
    }

    class Food
    {
        public $name;
        public function __construct($name)
        {
            $this -> name = $name;
        }
    }
    class Fish extends Food
    {
        public function showInfo()
        {
            echo $this -> name;
        }
    }
    class Bone extends Food
    {
        public function showInfo()
        {
            echo $this -> name;
        }
    }
    $dog = new Dog('德国黑背');
    $cat = new Cat('波斯猫');
    $fish = new Fish('鲤鱼');
    $bone = new Bone('大棒骨');

    class Master
    {
        public $name;
        public function __construct($name)
        {
            $this -> name = $name;
        }
        //喂食
        public function feed($animal, $food)
        {
            echo '<br>';
            $animal -> showInfo();
            echo '喜欢吃';
            $food -> showInfo();
        }
    }
    //创建一个主人
    $master = new Master('小明');
    $master -> feed($dog, $bone);
    $master -> feed($cat, $fish);
```

这种代码，就是体现的是PHP的多态。但是有人提出质疑：这也太灵活了，这PHP的语法过于松散，很容易造成类型没有约束。

传进去一个字符串如果调不起来，不就报错了么？虽然传进去可以提示报错，但是为什么一定要等人家报错你再告诉别人错了呢。应该在传进的形参的地方，入口这个地方约束一下，这是更好的一种多态。这样也是对我们的程序是一种保护。

如果不加约束，完全可以在传参的时候反着传一下比如`$master -> feed($fish, $cat);`

这样运行起来也很流畅，但是这个逻辑就不太对了。你这鲤鱼怎么能吃一只猫呢？    

所以说在传参的地方加上一个约束条件，这样可以限制传入参数的类型，也是更好的体现出多态

比如说`public function feed(Animal $animal, Food $food){} `

有一点要注意，就是在传参的地方Animal不能写Dog，如果你这么写了，那么$cat就传不进来了。但是如果写的条件是Animal ，那么它的子类就都可以传进来。这就是很有效的防护机制。 

下面就把优化部分的代码来再写一下：

```php
    class Master
    {
	public $name;
	public function __construct($name)
	{
	    $this -> name = $name;
        }
	//喂食
	public function feed(Animal $animal, Food $food)
	{
	    echo '<br>';
	    $animal -> showInfo();
            echo '喜欢吃';
	    $food -> showInfo();
	}
    }
    //创建一个主人
    $master = new Master('小明');
    $master -> feed($dog, $bone);
    $master -> feed($cat, $fish);
```

这样写的代码的优越性，还可以体现出一点就是，你在Animal的子类再添加羊类，老虎类，在Food的子类添加草类和兔子类，那么羊吃草，老虎吃兔子，Master这个主人类都不用动，代码照样可以跑起来。这样非常容易进行代码的维护。 

所以别人问到你多态有什么好处时，回答：有利于代码的维护和扩展。这就OK了。

#### ② 重载也是多态的一种表现

说到重载，php的重载和Java的重载是两个不一样的概念，php的重载是指动态地"创建"类属性和方法，我们是通过魔术方法来实现的 。虽然像Java那样的重载在php中是不支持的，但是php使用魔术方法`__call()`时也是可以实现出来，这种重载也可以展现出多态的效果。 

走一下代码来说明一下：

```php
    class A 
    {
        public function test1($p)
        {
            echo 'test1 <br>';
            print_r($p);
            echo '<hr>';
        }
        public function test2($p)
        {
            echo 'test2 <br>';
            print_r($p);
            echo '<hr>';
        }
        public function __call($method, $p)
        {
            if(count($p) == 1)
            {
                $this -> test1($p);
            }elseif (count($p) == 2) 
            {
                $this -> test2($p);
            }
        }
    }
    $a = new A();
    $a -> f1(11, 45);
    echo '<br>';
    $a -> f1(45);
```

这样一来，php就实现了出现同一方法的不同参数时，可以走不一样的方法了。

> 再来个题外话：
>
> 当通过子类去访问父类的属性时，如果这个属性被子类继承，则输出子类的值，如果没有继承，则输出父类的值。 

### 7、面向对象设计模式—单例模式

- 描述：一个类只能创建一个实例对象，不管用什么办法都无法创建第2个对象。
- 要求（三私一公）
  - 一私：私有的静态的保存对象的属性
  - 一私：私有的构造方法，阻止类外new对象
  - 一私：私有的克隆方法，阻止类外clone对象
  - 一公：公共的静态的创建对象的方法

```php
class Db
{
    //私有的静态的保存对象的属性
    private static $obj = NULL;
    //私有的构造方法：阻止类外new对象
    private function __construct(){}
    //私有的克隆方法：阻止类外clone对象
    private function __clone(){}
    //公共的静态的创建对象的方法
    public static function getInstace()
    {
        //判断当前类的对象是否存在
        if(!self::$obj instanceof self)
        {
            //如果当前类的对象不存在，则创建并保存它
            self::$obj = new self;
        }
        //返回当前类的对象
        return self::$obj;
    }
}
//创建数据库类的对象
$db1 = Db::getInstance();
$db2 = Db::getInstance();
var_dump($db1, $db2);//通过打印信息，可以看到两个对象是同一个对象
```

> 单例设计模式，不管调用多少次，总是一个对象！

### 8、面向对象设计模式—工厂设计模式

#### （1）什么是工厂设计模式

- 根据传递不同的类名参数，返回不同类的对象
- 工厂模式，就是生产各种的不同类的对象
- 工厂模式，改变了在类外使用new关键字创建对象的方式，改成了在工厂类中创建类的对象
- 在类的外部我们无法控制类的行为，但在类内部自己可以控制类的行为

#### （2）工厂设计模式的要求

- 工厂类中的方法，应该是公共的静态的方法
- 工厂类中有一个私有的静态的保存对象的属性(单例工厂模式)
- 该方法功能：就是根据传递的不同参数，去创建不同的类实例
- 提示：工厂设计模式，一般会与单例设计模式搭配使用

#### （3）工厂设计模式的简单应用

./app.php

```php
header("Content-type:text/html;charset=utf-8");
//类的自动加载
spl_autoload_register(function($className){
    //构建类文件的真实路径
    $filename = "./libs/$className.class.php";
    //如果类文件存在，则包含进来
    if(file_exists($filename))	require_once($filename);
});
//创建狗狗类对象
$dogObj = Factory::getInstance("Dog");
$dogObj -> showInfo();
echo "<br>";
//创建猫猫类对象
$catObj = Factory::getInstance("Cat");
$catObj -> showInfo();
```

./libs/Factory.class.php

```php
//定义最终的工厂类：生产不同类对象的工厂
final class Factory
{
    //公共的静态的不同类对象：类名可以是变量名
    public static function getInstance($className)
    {
        //返回创建的不同类对象，类名称可以使变量名
        return new $className();
    }
}
```

./libs/Dog.class.php

```php
//定义最终的狗狗类
final class Dog
{
    private $name = '哮天犬';
    private $food = '大棒骨';
    public function showInfo()
    {
        echo "{$this -> name}喜欢吃{$this -> food}";
    }
}
```

./libs/Cat.class.php

```php
//定义最终的猫猫类
final class Cat
{
    private $name = '加菲猫';
    private $food = '小金鱼';
    public function showInfo()
    {
        echo "{$this -> name}喜欢吃{$this -> food}";
    }
}
```

#### （4）单例工厂类的实现

将（3）中的Factory.class.php代码改成一下内容：

```php
//定义最终的工厂类：生产不同类对象的工厂
//工厂类本身不创建对象
final class Factory
{
    //私有的静态的保存对象的数组
    private static $arrObj = [];
    //公共的静态的创建不同类对象的方法
    public static function getInstace($className)
    {
        //判断当前类的对象是否存在
        if(empty(self::$arrObj[$className]))
        {
            self::$arrObj[$className] = new $className;
        }
        //返回创建的不同类对象，类名可以是变量名
        return self::$arrObj[$className];
    }
}
```

在Cat.class.php和Dog.class.php代码的类中增加两个私有方法：

`private function __construct() {}`

`private function __clone() {}`

就可以完成单例工厂。

### 9、无限级分类

#### （1）无限级分类原理（文章表为例）

① 读取原始的文章分类数据到内存中

② 将原始的文章分类数据，转成无限级分类数据（有层级关系）

③ 转换的原则：

​	从根菜单开始，一层一层往下查找，把找到的复合条件的菜单，放到临时数组$categorys中。

​	菜单的缩进空格数：菜单层级 * 空格数

​	菜单层级：根据函数递归调用的次数

#### （2）案例（文章表为例）

只写主要代码了，其余的像命名空间什么的偷懒不写了哈。

① 文章分类控制器

```php
final class CategoryController
{
    public function index()
    {
        //获取原始的分类数据
        $categorys = ...
        //获取无限级分类数据
        $categorys = CategoryModel::getInstance() -> categoryList($categorys);
        //向视图赋值
        ...
    }
}
```

② 文章分类模型

```php
final class CategoryModel
{
    //受保护的数据表名称
	protected $table = "category";
    
    //静态变量：方法或函数调用完毕，该变量不会消失；
    //注意事项：静态变量只在函数或方法第1次调用初始化，以后不再初始化；
    //静态变量只能定义在函数或方法内。
	/**
	 * [categoryList description]
	 * @param   $arrs  [原始的分类数据]
	 * @param   $level [菜单层级,初始值为0]
	 * @param   $pid   [上次递归传递过来的id值]
	 * @return  $categorys      [description]
	 */
    //获取无限级分类的方法
	public function categoryList($arrs, $level = 0, $pid = 0)
	{
		//定义静态变量,用于存储每次递归找到的数据
		static $categorys = [];
		//循环原始的分类数组
		foreach ($arrs as $arr) 
		{
			//如果本次pid和传递过来的id相等的话,就找到了下层菜单
			if($arr['pid'] == $pid)
			{
				$arr['level'] = $level;//给数组添加level元素
				$categorys[] = $arr;
				//递归调用
				$this->categoryList($arrs, $level + 1, $arr['id']);
			}
		}
		//返回无限级分类的数组
		return $categorys;
	}
}
```

③ 文章分类视图

```html
<!-- 使用smarty模板 -->
<{foreach $categorys as $category}>
<tr>
    <td>...</td>
    <td>
        <!-- str_repeat()字符串重复的函数 -->
        <!-- level：菜单层级 -->
        <{str_repeat('----', $category.level * 2)}>
        <!-- classname：分类名称 -->
        <{$categoty.classname}>
    </td>
    <td>...</td>
</tr>
<{/foreach}>
```