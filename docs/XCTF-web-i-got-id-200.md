## 题目描述

嗯。。我刚建好了一个网站

## 思路

打开题目，有三个链接可以点击

![20190907165220.png](https://raw.githubusercontent.com/handbye/images/master/20190907165220.png)

点击Files有个可以上传文件的地方，随便上传一个文件

页面上将文件内容显示了出来

![20190907165442.png](https://raw.githubusercontent.com/handbye/images/master/20190907165442.png)

简单学习了下perl的上传，猜测可能后台为：

```perl
my $cgi= CGI->new;
if ( $cgi->upload( 'file' ) )
{
my $file= $cgi->param( 'file' );
while ( <$file> ) { print "$_"; } }
```

param()函数会返回一个列表的文件但是只有第一个文件会被放入到下面的file变量中。而对于下面的读文件逻辑来说，如果我们传入一个ARGV的文件，那么Perl会将传入的参数作为文件名读出来。这样，我们的利用方法就出现了：在正常的上传文件前面加上一个文件上传项ARGV，然后在URL中传入文件路径参数，这样就可以读取任意文件了。

> 意ARGV是PERL默认用来接收参数的数组,不管脚本里有没有把它写出来,它始终是存在的。

所以尝试构造:

![20190907165738.png](https://raw.githubusercontent.com/handbye/images/master/20190907165738.png)

然后使用IFS作为命令分割，查看根目录有哪些文件

![20190907170022.png](https://raw.githubusercontent.com/handbye/images/master/20190907170022.png)

可以看到有Flag文件，接下来读取这个文件即可拿到Flag.

![20190907175035.png](https://raw.githubusercontent.com/handbye/images/master/20190907175035.png)

> IFS为shell的字段分隔符，可将字符串按照特性的格式进行分割

参考：

- [https://lengjibo.github.io/%E8%B5%9B%E5%AE%81%E5%B9%B3%E5%8F%B0web%E9%A2%98%E8%A7%A3/](https://lengjibo.github.io/%E8%B5%9B%E5%AE%81%E5%B9%B3%E5%8F%B0web%E9%A2%98%E8%A7%A3/)
- [https://tsublogs.wordpress.com/2016/09/18/606/](https://tsublogs.wordpress.com/2016/09/18/606/)
- [https://blog.csdn.net/u013155359/article/details/93638783](https://blog.csdn.net/u013155359/article/details/93638783)
