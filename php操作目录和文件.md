# php操作目录

## 1、创建新目录

- 描述：尝试新建一个由 pathname 指定的目录。 
- 语法：**`bool mkdir ( string $pathname [, int $mode = 0777 [, bool $recursive = false ]] )`** 
- 参数：
  - $pathname：指定目录的路径；
  - $mode：默认的 mode 是 0777，意味着最大可能的访问权。$mode在windows下被忽略。 注意$mode的值为八进制。
  - $recursive：如果指定的路径的上级目录不存在，则也会递归创建。
- 返回值：成功时返回TRUE，失败时返回FALSE。 

```php
//创建一个新目录
$dirname = "./php";
$flag = mkdir($dirname);
var_dump($flag);

//递归创建新目录
$dirname = "./amp/php/mysql";
$flag = mkdir($dirname, 0777,true);
var_dump($flag);
```

**注意：如果目录已经存在，就报错了。**

## 2、判断是否是一个目录

- 描述：判断给定文件名是否是一个目录 。
- 语法：**`bool is_dir ( string $filename )`** 
- 返回值：如果文件名存在，并且是个目录，返回 TRUE，否则返回FALSE。

```php
//动态创建目录
$dirname = "./" . date("Ymd");
mkdir($dirname);
//判断是否是一个目录
if(is_dir($dirname))
{
    echo $dirname . "是一个目录";
}else
{
    echo $dirname . "不是一个目录";
}
```

## 3、判断目录或文件是否存在

- 语法：**`bool file_exists ( string $filename )`** 
- 提示：可以判断文件，也可以判断目录，是否存在
- 返回：如果有filename指定的文件或目录存在，则返回TURE，否则返回FALSE

```php
$dirname = "./" . date("YmdHi");
if(file_exists($dirname))
{
    echo $dirname . "目录已经存在，不能创建同名目录";
}else
{
    //不存在就创建它
    mkdir($dirname);
    echo $dirname . "目录创建成功";
}
```

## 4、删除目录

- 语法：**`bool rmdir ( string $dirname )`** 
- 参数：$dirname目录路径 
- 返回值：成功时返回 TRUE， 或者在失败时返回 FALSE
- 提示：尝试删除 dirname 所指定的目录。 该目录必须是空的，且要有相应的权限。

```php
$dirname = "./php";
if(@rmdir($dirname))
{
    echo $dirname . "目录已经删除";
}else
{
    echo $dirname . "不能删除，原因可能是非空目录，或者没有删除权限";
}
```

## 5、更改目录的访问权限

- 描述：改变文件的访问权限
- 语法：**`bool chmod ( string $filename , int $mode )`** 
- 参数：
  - $filename，指定文件的路径；
  - $mode，mode 参数包含**三个八进制数**按顺序分别指定了所有者、所有者所在的组以及所有人的访问限制。每一部分都可以通过加入所需的权限来计算出所要的权限。数字 1 表示使文件可执行，数字 2 表示使文件可写，数字 4 表示使文件可读。加入这些数字来制定所需要的权限。
  - 在window下文件的只读权限是 0444。文件夹只读权限0555。
- **此函数不能作用于远程文件**

```php
$dirname = "./php";
chmod($dirname, 0744);//给自己最大权限，所在组只读权限，其他用户只读权限
chmod($dirname, 0777);//自己、所在组、其他人都具有最高权限
```

## 6、取得目录或文件访问权限

- 语法：**`int fileperms ( string $filename )`**
- 返回值：以十进制数字返回文件的访问权限

```php
$dirname = "./php";
chmod($dirname, 0744);
//获取目录权限
$int = fileperms($dirname); //16895
//将十进制权限值，转成八进制
$int = decoct($int);//40777
//截取字符串
$int = substr($int, -4);
echo $int;//0777
```

## 7、重命名或移动文件或目录

- 描述：重命名或移动一个文件或目录。如果文件不存在同一目录下，就是移动。
- 语法：**`bool rename ( string $oldname , string $newname )`** 
- 返回值：成功时返回 TRUE， 或者在失败时返回 FALSE。

```php
//改名
$dirname = "./php";
$desname = "./mysql";
//两个子目录都位于网站根目录
rename($dirname, $desname);

//移动，如果源目录和目标目录不在同一根目录下
$dirname = "./php";
$desname = "./amp/php";
rename($dirname, $desname);
```

## 8、打开目录

- 描述：打开目录句柄，可用于之后的 closedir()，readdir() 和 rewinddir() 调用中。 
- 语法：**`resource opendir ( string $path )`** 
- 返回值：如果成功则返回**目录句柄**的 **resource**，失败则返回 FALSE。

```php
//打开目录
$dirname = "./php";
$handle = opendir($dirname);
//打开目录成功。返回目录句柄资源，后续操作都是针对该目录句柄的
var_dump($handle);
```

## 9、读取目录中条目

- 描述：从目录句柄中读取条目
- 语法：**`string readdir ([ resource $dir_handle ] )`** 
- 说明：返回目录中下一个文件的文件名。文件名以在文件系统中的排序返回
- 返回：成功则返回文件名 或者在失败时返回 FALSE

```php
//读取目录中所有条目，以phpMyAdmin
$dirname = "./phpMyAdmin";
//判断目录是否存在
if(!file_exists($dirname))
{
    die("{$dirname}目录不存在！");
}
//判断是否为一个目录
if(!is_dir($dirname))
{
    die("{$dirname}不是一个目录");
}
//打开目录，返回目录句柄资源
$handle = opendir($dirname);
//循环取出目录中所有条目
while($line = readdir($handle))
{
    echo $line . "<br>";
}
//关闭目录
closedir();
```

##  10、显示中文目录或文

- 描述：字符串按要求的字符编码来转换
- 语法：**`string iconv ( string $in_charset , string $out_charset , string $str )`**
- 参数：
  - $in_charset，输入的字符集
  - $out_charset，输出的字符集
  - $str，要转换的字符串
- 返回：返回转换后的字符串， 或者在失败时返回 FALSE

```php
//读取目录中所有条目
$dirname = './';
//判断目录是否存在
if (!file_exists($dirname))
{
    die("{$dirname}目录不存在");
}
//判断是不是目录
if (!is_dir($dirname))
{
    die("{$dirname}不是一个目录");
}
//打开目录，并返回目录句柄资源
$handle = opendir($dirname);
//循环取出目录中所有条目
while($line = readdir($handle))
{
    //操作系统字符集gbk，转换成utf-8
    echo iconv('gbk', 'utf-8', $line) . $line . "<br>";
}
//关闭目录
closedir();
```

## 11、关闭目录句柄

- 描述：关闭由 dir_handle 指定的目录流。流必须之前被 opendir() 所打开
- 语法：**`void closedir ( [resource $dir_handle] )`** 
- 提示：如果省略$dir_handle，则默认为最后由opendir()打开的目录句柄

## 12、综合实例

递归遍历phpMyAdmin下的所有条目 

```php
//递归遍历phpmyadmin目录下所有条件，包括子目录中的内容
function recursive($dirname)
{
    //打开目录。返回目录句柄资源
    $handle = opendir($dirname);
    echo '<ul>';
    //循环读取目录中的条目
    while ($line = readdir($handle))
    {
        //如果是"."或".."，则继续
        if ($line == "." || $line == "..") continue;
        echo '<li>' . iconv('gbk', 'utf-8', $line) . '</li>';
        //如果是目录，则递归调用
        if (is_dir($dirname . '/' . $line)) {
            //递归到下一层的某个文件夹
            recursive($dirname . '/' . $line);
        }
    }
    echo '</ul>';
    //关闭目录
    closedir($handle);
}
$dirname = './phpmyadmin';
//判断是不是已经存在的目录
if (!file_exists($dirname))
{
    die("{$dirname}目录不存在！");
}

//判断是不是目录
if(!is_dir($dirname))
{
    die("{$dirname}不是一个目录");
}
//调用函数，显示以下目录
recursive("./phpmyadmin");
```

# php操作文件

## 1、打开文件

- 描述：打开文件或者 URL
- 语法：**`resource fopen ( string $filename , string $mode )`**
- 参数：
  - $filename，指定要打开的目标文件或URL
  - $mode，指定文件的打开方式
- 提示：为移植性考虑，在用 fopen()打开文件时总是使用 'b' 标记
- 返回值：**成功时返回文件指针资源**，如果打开失败，本函数返回 FALSE

```php
//打开test.txt文件
$filename = "./test.txt";
//判断文件是否存在
if(!$file_exists($filename))
{
    die("{$filename}不存在");
}
//只读方式打开文件
$handle = fopen($filename, 'rb');
var_dump($handle);//如果文件打开成功，会打印出返回文件句柄资源

//打开php.txt文件
$filename = "./php.txt";
//写入（覆盖）方式打开文件，如果文件不存在就创建它
//如果文件存在，则覆盖它
$handle = fopen($filename, 'wb');
var_dump($handle);

//打开php.txt文件
$filename = "./php.txt";
//追加写入方式打开文件，如果文件不存在就创建它
//如果文件存在，则追加新内容
$handle = fopen($filename, 'ab');
var_dump($handle);

//打开百度首页文件
$filename = "http://www.baidu.com";
var_dump($handle);
```

## 2、打开文件的方式

- r：只读方式打开；
- r+：读写方式打开；
- w：写入方式打开，如果文件不存在，则自动创建；
- w+：读写方式打开，如果文件不存在，则自动创建；
- a：追加写入方式打开，如果文件不存在，则自动创建；
- a+：追加读写方式打开，如果文件不存在，则自动创建；
- x：只写，并创建文件，如果文件存在，则fopen()调用失败，返回false；
- x+：读写，并创建文件，如果文件存在，则fopen()调用失败，返回false；
- b：二进制，要结合以上参数一起使用

## 3、关闭文件

- 描述：关闭一个已打开的文件指针
- 语法：**`bool fclose ( resource $handle )`**
- 参数：$handle必须是由fopen()打开的文件指针

```php
//打开本地文件
$filename = "./php.txt";
//以只读方式打开
$handle = fopen($filename, 'rb');
var_dump($handle);
//关闭文件
fclose($handle);
var_dump($handle);
```

## 4、读取指定大小文件内容

- 描述：读取文件（可安全用于二进制文件）
- 语法：**`string fread ( resource $handle , int $length )`**
-  参数：$handle是由fopen()创建的handle；$length最多读取的字节数
- 提示：在区分二进制文件和文本文件的系统上（如 Windows）打开文件时，fopen() 函数的 mode 参数要加上`b`
- 返回值：返回所读取的字符串， 或者在失败时返回 FALSE

```php
//读取指定大小的文件内容
$filename = "./php.txt";
//以只读方式打开
$handle = fopen($filename, 'rb');
//读取指定大小的文件内容
$str = fread($handle, 1024);
echo iconv('gbk', 'utf-8', $str);
//关闭文件
fclose($handle);
```

读取图片内容：

```php
header("content-type:image/jpeg");
$filename = "./01.jpg";
//以只读方式打开
$handle = fopen($filename, 'rb');
//读取指定大小的文件内容
$str = fread($handle, filesize($filename));
echo $str;
//关闭文件
fclose($handle);
```

## 5、读取一行内容

- 描述：从文件指针中读取一行
- 语法：**`string fgets ( resource $handle [, int $length ] )`**
- 参数：
  - $handle：文件指针必须是有效的
  - $length：从 handle指向的文件中读取一行并返回长度最多为 length - 1 字节的字符串。碰到换行符（包括在返回值中）、EOF 或者已经读取了 length - 1 字节后停止（看先碰到那一种情况）。如果没有指定 length，则默认为 1K，或者说 1024 字节 
- 返回值：从指针 handle 指向的文件中读取了 length - 1 字节后返回字符串。 如果文件指针中没有更多的数据了则返回 FALSE

```php
//一行一行读取文件
$filename = "./php.txt";
//以只读方式打开
$handle = fopen($filename, 'rb');
//循环读取所有行数据
while($str = fgets($handle))
{
    echo iconv('gbk', 'utf-8', $str);
}
//关闭文件
fclose($handle);
```

## 6、读取文件内容到数组中

- 描述：把整个文件读入一个数组中
- 语法：**`array file ( string $filename [, int $flags = 0 ] )`**
- 参数：
  - $filename，文件的路径 ring $filename
  - $flags，附加选项
  - FILE_USE_INCLUDE_PATH(1)：在include_path 中查找文件
  - FILE_IGNORE_NEW_LINES(2)：在数组每个元素末尾不添加**换行符**
  - FILE_SKIP_EMPTY_LINES(4)：跳过空行
- 不需要打开文件和关闭文件

```php
//把新浪首页读入到一个数组中
$filename = "http://www.sina.com.cn";
//读取文件内容到数组中，并去除空行和换行符
$arr = file($filename, 2 | 4);
print_r($arr);
```

## 7、读取文件内容到字符串中

- 描述：将整个文件读入一个字符串
- 语法：**`string file_get_contents ( string $filename [, bool $use_include_path = false]] )`**
- 参数：
  - $filename指定文件路径
  - $use_include_path，是否读取include中的文件内容，默认值为FALSE
- 返回：返回读取的数据，或在失败时返回false
- **不需要打开和关闭文件**

```php
//把新浪首页读入到一个字符串中
$filename = "http://www.sina.com.cn";
//读取文件内容到字符串中
$str = file_get_contents($filename);
echo $str;
```

## 8、实例：读取记事本中的内容，并用表格展示出来

```php
$filename = "./php.txt";
//只读方式打开文件
$handle = fopen($filename, 'rb');
//构建表格字符串
$str = "";
$str .= "<table border='1'>";
//循环取出一行一行数据
while($arr = fgets($handle))
{
    //将每个数组元素的值，分割成新数组
    $arr2 = explode(', ', $arr);
    $str .= "<tr>";
    //循环第二个数组
    for($i = 0; $i < count($arr2); $i++)
    {
        $str .= "<td>" . iconv("gbk", "utf-8", $arr2[$i]) . "</td>";
    }
    $str .= "</tr>";
}
$str .= "</table>";
fclose($handle);
//输出结果
echo $str;
```

## 9、写入文件

- 描述：写入文件（可安全用于二进制文件）
- 语法：**`int fwrite ( resource $handle , string $string )`**
- 返回：返回写入的字符数，出现错误时则返回 FALSE

```php
//转存图片数据
$filename = "./01.jpg";
//打开文件
$handle = fopen($filename, 'rb');
//读取二进制数据
$str = fread($handle, filesize($filename));
//关闭文件
fclose($handle);
//以写入方式创建新文件
$filename = "./a.jpg";
//打开文件
$handle = fopen($filename, 'wb');
//写入二进制数组
fwrite($handle, $str);
//关闭文件
fclose($handle);
```

## 10、将一个字符串写入文件

- 描述：和依次调用 fopen()，fwrite()以及 fclose() 功能一样
- 语法：**`int file_put_contents ( string $filename , mixed $data [, int $flags = 0] )`**
- 参数：
  - $filename要被写入数据的文件名
  - $data要写入的数据。类型可以是 string，array(一维数组)
  - $flags附加选项
  - FILE_USE_INCLUDE_PATH(1) 在 include 目录里搜索 filename
  - FILE_APPEND(8)如果文件 filename已经存在，追加数据而不是覆盖
  - LOCK_EX(2)在写入时获得一个独占锁
- **如果文件不存在，则会自动创建**。如果文件过大，不能一次性处理
- 返回：该函数将返回写入到文件内数据的字节数，失败时返回FALSE

```php
//将新浪首页另存到本地
$filename = "http://www.sina.com.cn";
//直接获取新浪首页源代码
$str = file_get_content($filename);
//把新浪源代码，另存到本地网页
$filename = "./sina.html";
file_put_contents($filename, $str, 8 | 2);
```

## 11、拷贝文件

- 描述：将文件从 source 拷贝到 dest
- 语法：**`bool copy ( string $source , string $dest )`** 
- 如果目标文件存在，则会覆盖

```php
$source = "./a.jpg";
$dest = "./b.jpg";//目标文件
copy($source, $dest);
```

## 12、删除文件

- 语法：**`bool unlink ( string $filename )`**
- 返回：成功时返回 TRUE， 或者在失败时返回 FALSE
- 提示：可以删除虚拟空间之外的文件，但必须使用相对路径(../)，但必须有权限 
- **注意：删除的文件不会进入回收站**

```php
$filename = "./b.jpg";
unlink($filename);
```

## 13、其它文件的操作函数

- filesize()，可以获取文件大小，单位为字节
- is_writable()，判断文件是否可写
- is_readable()，判断文件是否可读
- feof()，判断文件指针是否到达文件结尾
- filectime()，获取创建文件的时间
- fileatime()，获取文件最新访问时间
- filemtime()，获取文件最后修改时间

## 14、综合实例

递归删除phpMyAdmin目录

```php
//(1)空目录才能删除
//(2)打开目录，最后必须关闭目录
function delAll($dirname)
{
    //打开目录
    $headle = opendir($dirname);
    //循环读取目录中所有文件
    while ($line = readdir($headle))
    {
        //如果是"."或".."，则跳过
        if ($line == '.' || $line == '..') continue;
        //判断是不是目录
        if (is_dir($dirname . '/' . $line))
        {
            //递归调用
            delAll($dirname . '/' . $line);
        }
        else
        {
            //如果是文件，使用unlink()删除
            unlink($dirname . '/' . $line);
        }
    }
    //关闭目录
    closedir($headle);
    //目录关闭才能删除目录
    rmdir($dirname);
}

//调用函数
delAll('./phpmyadmin');
```

