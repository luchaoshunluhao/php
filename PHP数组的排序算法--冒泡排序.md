# PHP数组的排序算法--冒泡排序

标签（）： php 冒泡排序

---

>原理：

>>遍历一个数组，在此过程中，将相邻的两个单元的值进行比较：如果前面的比后面的大，则将两个值交换位置。这个过程到最后，数组中的最大值一定放在最后位置了。

>>如果将上述过程再进行一遍，则又可以确定剩余数据中的最大值放在倒数第二的位置。

>>然后将上述过程继续进行一遍，则可以继续确定剩余数据中的最大值放在倒数第三的位置。

>>依次类推。。。。。。进行若干次，就排好了。

>>比如：
有数组：$arr = array(18,  22,  12,   15,  23,  9);
原始数组	18	22	12	15	23	9
第1趟之后	18	12	15	22	9	23
第2趟之后	12	15	18	9	22	23
第3趟之后	12	15	9	18	22	23
第4趟之后	12	9	15	18	22	23
第5趟之后	9	12	15	18	22	23

>规律总结：
1, 进行的趟数可以求出, 为\$n-1,  其中\$n = count(\$arr1);
2, 每一趟都需要将“剩余的数据”从前往后相邻的两个数据进行比较,
3, 如果比较的两个数据,前面的大于后面,就将他们交换位置!
4, 每一趟要比较的数据都比前一趟少一个, 其中第一趟要比较$n个数据。

冒泡排序的写法如下：

```
<?php 
header("content-type:text/html;charset=utf-8");

//使用冒泡排序对以下数组进行排序为正序
$arr1 = array(18,  22,  12,   15,  23,  9);

echo "<br>排序前:" . implode(', ', $arr1);
echo "<hr>";
function bubble(&$arr1){
    //开始排序:
    $n = count($arr1);
    for($i = 0; $i < $n-1; $i++)//控制进行的趟数
    {
    	//以下这个循环才是从前往后相邻数据进行比较的过程:
    	for($k = 0; $k < $n-$i-1; $k++)
    	{
    		if($arr1[$k] > $arr1[$k+1])
    		{
    			$temp = $arr1[$k];
    			$arr1[$k] = $arr1[$k+1];
    			$arr1[$k+1] = $temp;
    		}
    	}
    	echo "<br>排序中:" . implode(', ', $arr1);
    }
}
bubble($arr1);
echo "<hr>";
echo "<br>排序后:" . implode(', ', $arr1);
 ?>
```

当运行该代码时，显示如下：

![BubbleSort_01](https://raw.githubusercontent.com/luchaoshunluhao/images/master/BubbleSort_01.png)

当然，正常来讲，冒泡排序到此为止就已经结束了，这个代码看上去已经能很好的完成我们正常的排序需求了。

但是，当一个数组中的排序正好是从小到大或者从大到小时，再去使用冒泡排序法,这时的程序还是会从头开始一步一步的运行着，但是却没有数据来进行交换，那么这样的代码就会造成资源浪费。

下面我们来优化一下这个代码，使得即使这个数组是有序的排列，也不会造成资源的浪费。

优化后的代码如下：

```
<?php 
header("content-type:text/html;charset=utf-8");

//使用冒泡排序对以下数组进行排序为正序
$arr1 = array(18,  22,  12,   15,  23,  9);

echo "<br>排序前:" . implode(', ', $arr1);
echo "<hr>";
function bubble(&$arr1){
    $flag = 0;//设置一个比较位
    //开始排序:
    $n = count($arr1);
    for($i = 0; $i < $n-1; $i++)//控制进行的趟数
    {
    	//以下这个循环才是从前往后相邻数据进行比较的过程:
    	for($k = 0; $k < $n-$i-1; $k++)
    	{
    		if($arr1[$k] > $arr1[$k+1])
    		{
    			$temp = $arr1[$k];
    			$arr1[$k] = $arr1[$k+1];
    			$arr1[$k+1] = $temp;
    			$flag = 1;
    		}
    	}
    	if ($flag == 0)
    	{
        	//如果$flag == 0,说明数组没有进行一次交换,
        	//则该数组已经是一个从小到大的顺序了,因此结束排序
        	break;
        }
        else
        {
            //如果$flag == 1,说明数组进行过一次交换,
            //则该数组还不是一个从小到大的顺序,因此重新设置为0
            $flag = 0;
        }
    	echo "<br>排序中:" . implode(', ', $arr1);
    }
}
bubble($arr1);
echo "<hr>";
echo "<br>排序后:" . implode(', ', $arr1);
 ?>
```

就这个代码而言，其实影响不大，但是如果是一个数组是有序排列的话，那么就会在运行到27行时直接结束排序。

为了让这个优化的代码更有说服力，下面我们来测试一下更大的数据，我们设置一个数组在1-10000之间随机排列，先测试的是优化前的代码，如下所示：

```
<?php 
header("content-type:text/html;charset=utf-8");

//使用冒泡排序对以下数组进行排序为正序
//$arr1 = array(18,  22,  12,   15,  23,  9);
$arr1 = range(1, 10000);
function bubble(&$arr1){
    //开始排序:
    $n = count($arr1);
    for($i = 0; $i < $n-1; $i++)//控制进行的趟数
    {
    	//以下这个循环才是从前往后相邻数据进行比较的过程:
    	for($k = 0; $k < $n-$i-1; $k++)
    	{
    		if($arr1[$k] > $arr1[$k+1])
    		{
    			$temp = $arr1[$k];
    			$arr1[$k] = $arr1[$k+1];
    			$arr1[$k+1] = $temp;
    		}
    	}
    }
}
//在排序前打印时间
date_default_timezone_set('PRC');
echo '<br>' . date('H:i:s');
bubble($arr1);
echo '<br>' . date('H:i:s');
echo "<br>排序后:" . implode(', ', $arr1);
 ?>
```

![BubbleSort_02](https://raw.githubusercontent.com/luchaoshunluhao/images/master/BubbleSort_02.png)

由上图可以看到，在1-10000的无序数组使用冒泡法排好后需要十秒左右，下面我们再对比一下优化后的代码:

```
<?php 
header("content-type:text/html;charset=utf-8");

//使用冒泡排序对以下数组进行排序为正序
//$arr1 = array(18,  22,  12,   15,  23,  9);
$arr1 = range(1, 10000);
function bubble(&$arr1){
    $flag = 0;//设置一个比较位
    //开始排序:
    $n = count($arr1);
    for($i = 0; $i < $n-1; $i++)//控制进行的趟数
    {
    	//以下这个循环才是从前往后相邻数据进行比较的过程:
    	for($k = 0; $k < $n-$i-1; $k++)
    	{
    		if($arr1[$k] > $arr1[$k+1])
    		{
    			$temp = $arr1[$k];
    			$arr1[$k] = $arr1[$k+1];
    			$arr1[$k+1] = $temp;
                $flag = 1;
    		}
    	}
    	if ($flag == 0) 
        {
            //如果$flag == 0,说明数组没有进行一次交换,
            //则该数组已经是一个从小到大的顺序了,因此结束排序
            break;   
        }
        else
        {
            //如果$flag == 1,说明数组进行过一次交换,
            //则该数组还不是一个从小到大的顺序,因此重新设置为0
            $flag = 0;
        }
    }
}
//在排序前打印时间
date_default_timezone_set('PRC');
echo '<br>' . date('H:i:s');
bubble($arr1);
echo '<br>' . date('H:i:s');
echo "<br>排序后:" . implode(', ', $arr1);
 ?>
```

![BubbleSort_03](https://raw.githubusercontent.com/luchaoshunluhao/images/master/BubbleSort_03.png)

显然,优化后的代码，排序1-10000的无序数组时在完成排序后所需要的时间仅仅是一秒不到，这就是优化的好处。

非常感谢您能抽出时间来阅读本次的文章，以上就是本篇的php数组冒泡排序的介绍，由于笔者也是处于学习阶段，如果出现错误的地方，欢迎各位朋友进行指正。



#
