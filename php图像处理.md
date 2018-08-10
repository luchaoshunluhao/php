# 一、图像处理概述

## 1、开启GD2图像扩展库

PHP不仅限于只产生HTML的输出，还可以创建与操作多种不同格式的图像文件。PHP提供了一些内置的图像处理函数，也可以使用GD函数库创建新图像或处理已有的图像。目前GD2库支持JPEG、PNG和WBMP格式。

GD扩展用于动态创建图片，使用C语言编写，开放源代码，现在的版本是2.0，所以称为GD2。

开启GD2扩展库：将php.ini中**extension=php_gd2.dll**选项前的分号去掉，重启。

查看图像扩展库GD2是否开启，使用函数：`phpinfo();`

## 2、创建图像的大致步骤

在PHP中创建一个图像，大致需要四个步骤：

- 创建画布：创建一个画布，以后的操作都基于此画布操作；
- 绘制图形：在画布上绘制图像轮廓或输入文本；

- 输出图像：也可以另存为；

- 释放资源：释放图像占用的内存资源。

## 3、画布坐标系说明

画布的坐标系，坐标原点位于画布左上角，以像素为单位，正右方为X轴正方向，正下方为Y轴正方向。

# 二、创建图像和销毁图像

## 1、创建基于已有图像的图像

- 描述：由文件或 URL 创建一个新图象
- 语法：**`resource imagecreatefrompng ( string $filename )`**
- 参数：$filename为图像的完整路径
- 返回：成功后返回图象资源,失败后返回 FALSE
- 提示：imagecreatefromjpeg()和imagecreatefromgif()语法与该函数一样

```php
//创建基于已有图像来创建画布
$filename = "./images/01.jpg";
//创建图像，并返回图像资源
$img = imagecreatefromjpeg($filename);
//输出图像到浏览器
header("Content-type:image/jpeg");
imagejpeg($img);
//释放资源
imagedestroy($img);
```

## 2、创建空画布图像

- 描述：新建一个真彩色图像，支持24位色，即RGB(256,256,256)
- 语法：**`resource imagecreatetruecolor ( int $width , int $height )`** 
- 参数：$width图像宽度；$height图像高度
- 返回：成功后返回**图象资源**,失败后返回 FALSE

```php
//创建一个空画布
$img = imagecreatetruecolor(400, 300);
//输出图像到浏览器
header("Content-type:image/jpeg");
imagejpeg($img);
//释放资源
imagedestroy($img);
```

## 3、销毁图像资源

- 描述：销毁一图像。释放与 image图像标识符关联的内存
- 语法：**`bool imagedestroy ( resource $image )`**
- 参数：$image为由imagecreatetruecolor()创建的图像标识符

```php
//创建一个空画布
$img = imagecreatetruecolor(400, 300);
//释放资源
imagedestroy($img);
//验证图像资源是否还存在
var_dump($img);
```

# 三、图像操作

## 1、为图像分配颜色

- 语法：**`int imagecolorallocate(resource $image,int $red,int $green,int $blue)`**
- 参数：$image图像资源标识符；颜色值可以用十进制(0-255)，16进制(0x00－0xFF)

```php
//创建一个空画布
$img = imagecreatetruecolor(400, 300);
//分配颜色
$bgcolor = imagecolorallocate($img, 0xFF, 0xFF, 0x00);
//给画布填充颜色
imagefill($img, 0, 0, $bgcolor);
//输出图像到浏览器
header("Content-type:image/jpeg");
imagejpeg($img);
//释放资源
imagedestroy($img);
```

## 2、输出图像到浏览器或保存文件

- 描述：以 JPG/GIF/PNG 格式将图像输出到浏览器或文件
- 语法：**`bool imagejpeg ( resource $image [, string $filename [, int $quality ]] )`** 
- 参数：
  - $filename，将创建的图像保存到文件；如果省略，则直接在浏览器输出
  - 如果要省略这个参数而提供 quality 参数，使用NULL
  - quality 为可选项，范围从 0（最差质量，文件更小）到 100（最佳质量，**文件最大**）。默认的质量值（大约 75）
- 提示：imagegif()、imagepng()，与imagejpeg()格式一样，但没有第3个参数

```php
//从已知图像上创建画布
$img = imagecreatefromjpeg("./images/01.jpg");
//将图像另存为文件，并不需要声明header()函数
//如果输出jpeg图像，必须要指定第3个质量参数
//imagepng()和imagegif()没有第3个参数
imagejpeg($img, './images/02.jpg', 100);
//释放资源
imagedestroy($img);
```

## 3、水平地画一行字符串

- 描述：水平地画一行字符串
- 语法：**`bool imagestring(resource $img,int $font,int $x,int $y,string $s,int $col)`** 
- 参数：
  - $img 图像资源
  - $font字体大小，取值1、2、3、4、5，使用内置字体
  - $x , $y 绘制字符串的开始坐标，一般在字符串左上角
  - $s 代表要绘制的一行字符串
  - $col 代表文本颜色

```php
//创建一个空画布
$img = imagecreatetruecolor(200, 50);
//分配颜色
$bgcolor = imagecolorallocate($img, 200, 200, 200);
$fgcolor = imagecolorallocate($img, 0xFF, 0x00, 0x00);
//给画布填充背景色
imagefill($img, 0, 0 ,$bgcolor);
//往图像上写一行普通字符串
$str1 = "Welcome to Beijing!";
$str2 = "Welcome to Beijing!";
$font = 5;
imagestring($img, $font, 25, 5, $str1, $fgcolor);
imagestring($img, $font, 25, 25, $str2, $fgcolor);
//输出并图像并销毁图像
header("Content-type:image/png");
imagepng($img);
imagedestroy($img);
```

## 4、获取画布的宽度和高度

- 宽度：**`int imagesx ( resource $image )`** 
- 高度：**`int imagesy ( resource $image )`** 

## 5、获取内置字体的宽度和高度

- 描述：返回指定字体一个字符宽度或高度的像素值
- 字体宽度：**`int imagefontwidth ( int $font )`**
- 字体高度：**`int imagefontheight ( int $font )`**
- 提示：$font为字体大小，取值1-5，最大为5

## 6、实例：在图像上绘制一行居中的字符串

> 应该知道的知识：
>
> - 图片在画布居中
> - 先计算画布起点坐标
> - 图像x轴坐标：（画布宽 - 图像宽） /   2
> - 图像y轴坐标：（画布高 - 图像高） /   2

```php
//1、从已知图像上创建画布
$img = imagecreatefromjpeg('./images/img02.jpg');
//2、分配颜色
$fontcolor = imagecolorallocate($img, 0xFF, 0x00, 0x00);
//3、计算字符串的起始坐标
$fontsize = 5;
$str = "Welcome";
$imgWidth = imagesx($img);
$imgHeight = imagesy($img);
$fontWidth = imagefontwidth($fontsize);
$fontHeight = imagefontheight($fontsize);
$x = ($imgWidth - $fontWidth * strlen($str)) / 2;
$y = ($imgHeight - $fontHeight) / 2;
//4、往画布上写入一行字符串
imagestring($img, $fontsize, $x, $y, $str, $fontcolor);
//输出图像，并销毁图像资源
header("Content-type:image/png");
imagepng($img);
imagedestroy($img);
```

## 7、画一个矩形并填充

- 描述：画一矩形并填充 
- 语法：**`bool imagefilledrectangle ( resource $image , int $x1 , int $y1 , int $x2 , int $y2 , int $color )`** 
- 参数：
  - $x1 , $y1 左上角坐标
  - $x2 , $y2 右上角坐标
  - $color 填充背景色

## 8、画一个单一像素

- 描述：画一个单一像素
- 语法：**`bool imagesetpixel ( resource $image , int $x , int $y , int $color )`**
- 说明：imagesetpixel() 在 image图像中用 color颜色在 x，y 坐标（图像左上角为 0，0）上画一个点 

```php
//创建一个空画布
$img = imagecreatetruecolor(400, 300);
//分配颜色
$bgcolor = imagecolorallocate($img, 0xCC, 0xCC, 0xCC);
//绘制矩形并填充颜色
imagefilledrectangle($img, 0, 0, 400, 300, $bgcolor);
//绘制随机像素点
for($i = 0; $i <= 1000; $i++)
{
    $pixcolor = imagecolorallocate($img, mt_rand(0, 255), mt_rand(0, 100), mt_rand(0, 100));
    imagesetpixel($img, mt_rand(0, 400), mt_rand(0, 300), $pixcolor);
}
//输出图像，并销毁图像资源
header("Content-type:image/png");
imagepng($img);
imagedestroy($img);
```

# 四、实例：图像验证码

## 1、绘制图像验证码

```php
//1、随机的四位字符串验证码
//2、把随机字符串写入图片

//一.产生4位随机字符串
//a.产生一个随机字符串数组
$arr = array_merge( range('a', 'z'), range(0, 9), range('A', 'Z') );
//b.打乱数组的顺序(打乱两次)
shuffle($arr);
shuffle($arr);
//c.从原数组中随机取四个下标
$arr2 = array_rand($arr, 4);
//d.根据随机的下标取出对应数组元素的值
$str = '';
foreach($arr2 as $index)
{
  $str .= $arr[$index];
}

//二.将四位随机字符串写入图片
//a.创建一个空画布
$img = imagecreatefromjpeg('./images/captcha_bg2.jpg');
//b.分配颜色
$fontcolor = imagecolorallocate($img, mt_rand(200, 255),  mt_rand(200, 255), mt_rand(200, 255));
//c.计算字符串的起始坐标
$imgWidth = imagesx($img);
$imgHeight = imagesy($img);
$fontsize = 5;
$fontWidth = imagefontwidth($fontsize);
$fontHeight = imagefontheight($fontsize);
$x = ($imgWidth - $fontWidth * strlen($str)) / 2;
$y = ($imgHeight - $fontHeight) / 2;
//d.往图片上写一行字符串
imagestring($img, $fontsize, $x, $y, $str, $fontcolor);
//输出图像并销毁图像
header('content-type:image/png');
imagepng($img);
imagedestroy($img);
```

## 2、产生一个指定范围的数组

- 描述：建立一个包含指定范围单元的数组
- 语法：**`array range ( mixed $start , mixed $limit [, number $step = 1 ] )`**
- 参数：
  - $start 指定范围第1个值
  - $limit 指定范围最后1个值
  - $step 指定步长值，必须为正整数，默认为1

## 3、合并数组

- 描述：合并一个或多个数组
- 语法：**`array array_merge ( array $array1 [, array $... ] )`**
- 说明：如果输入的数组中有相同的字符串键名，则该键名后面的值将覆盖前一个值。然而，如果数组包含数字键名，后面的值将不会覆盖原来的值，而是附加到后面

## 4、从数组中随机取出一个或多个单元

- 描述：从数组中随机取出一个或多个单元
- 语法：**`mixed array_rand ( array $input [, int $num_req = 1 ] )`**
- 参数：$input代表当前数组 , $num_req指明了你想取出多少个单元

## 5、生成更好的随机数

- 描述：生成更好的随机数
- 语法：**`int mt_rand ( int $min , int $max )`**
- 参数：min可选的、返回的最小值（默认：0）；max可选的、返回的最大值

# 五、实例：往图像上写入一行汉字

- 描述：用 TrueType 字体向图像写入文本
- 语法：**`array imagettftext ( resource $image , float $size , float $angle , int $x , int $y , int $color , string $fontfile , string $text )`**
- 参数：
  - $size，字号大小，自定义，同word字号一样
  - $angle，旋转角度(0~360)
  - $x和$y，定义了第一个字符的基本点（大概是字符的左下角）
  - $fontfile，是想要使用的 TrueType 字体的路径
  - $text，UTF-8 编码的文本字符串(其它编码要进行转换) 

```php
//网图像上写入一行TTF文本，该文本可以调大小，改字体
//1、创建一个空画布
$img = imagecreatetruecolor(400, 200);
//2、分配颜色
$bgcolor = imagecolorallocate($img, 200, 200, 200);
$fgcolor = imagecolorallocate($img, 255, 0, 0);
//3、给画布填充背景色
imagefill($img, 0, 0, $bgcolor);
//4、写入文本的参数
$fontsize = 24;
$fontfile = './images/msyh.ttf';
$str1 = 'HelloWorld';
$str2 = '北京欢迎您';

//5、往图像上写入TTF字符串
imagettftext($img, $fontsize, 0, 50, 100, $fgcolor, $fontfile, $str1);
imagettftext($img, 18, 0, 150, 150, $fgcolor, $fontfile, $str2);
//6、输出图像，并销毁图像
header('content-type:image/png');
imagepng($img);
imagedestroy($img);
```

# 六、实例：制作图像水印效果

- 描述：为图像分配透明颜色imagecolorallocatealpha()
- 语法：**`int imagecolorallocatealpha ( resource $image , int $red , int $green , int $blue , int $alpha )`**
- 说明：imagecolorallocatealpha() 的行为和 imagecolorallocate() 相同，但多了一个额外的透明度参数 alpha，其值从 0 到 127。0表示完全不透明，127 表示完全透明

```php
//1、从已知图像上创建画布
$img = imagecreatefromjpeg('./images/01.jpg');
//2、分配半透明的颜色
$fontcolor = imagecolorallocatealpha($img, 0xdd, 0xdd, 0xdd, 100);
//3、往图像上写一行TTF字符串
$fontsize = 24;
$fontfile = './images/msyhbd.ttf';
$str = '北京欢迎您';
imagettftext($img, $fontsize, 15, 45, 300, $fontcolor, $fontfile, $str);
//4、输出图像
header('content-type:image/png');
imagepng($img);
imagedestroy($img);
```

# 七、实例：生成图像缩略图

- 描述：将一幅图像中的一块正方形区域拷贝到另一个图像中，平滑地插入像素值，因此，减小了图像的大小而仍然保持了极大的清晰度
- 语法：**`bool imagecopyresampled ( resource $dst_image , resource $src_image , int $dst_x , int $dst_y , int $src_x , int $src_y , int $dst_w , int $dst_h , int $src_w , int $src_h )`**
- 参数：
  - $dst_image，目标图像
  - $src_image，源像图
  - $dst_x和$dst_y，目标图像x、y坐标
  - $src_x和$src_y，源图像x、y坐标
  - $dst_w和$dst_h，目标图像的宽度和高度
  - $src_w和$src_h，源图像的宽度和高度
- 提示：如果源和目标的宽度和高度不同，则会进行相应的图像收缩和拉伸

```php
//1、创建基于原图的画布
$src_img = imagecreatefromjpeg('./images/03.jpg');
$src_w = imagesx($src_img);
$src_h = imagesy($src_img);
//2、创建目标画布，画布的尺寸是原图的一半
$dst_w = $src_w * 0.3;
$dst_h = $src_h * 0.3;
$dst_img = imagecreatetruecolor($dst_w, $dst_h);
//3、复制原图并调整大小
imagecopyresampled($dst_img, $src_img, 0, 0, 0, 0, $dst_w, $dst_h, $src_w, $src_h);
//4、保存缩略图文件
imagejpeg($dst_img, './images/s_03.jpg', 100);
//5、释放资源
imagedestroy($src_img);
imagedestroy($dst_img);
```
