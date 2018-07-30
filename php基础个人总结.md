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

### 3、多态

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
