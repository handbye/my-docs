## 题目描述

暂无

## 思路

打开题目

![20190830232649.png](https://raw.githubusercontent.com/handbye/images/master/20190830232649.png)

就这一句话，啥都没，看来得扫目录了。

![20190830232944.png](https://raw.githubusercontent.com/handbye/images/master/20190830232944.png)

扫出来三个目录，打开index.phps看下：

![20190830233045.png](https://raw.githubusercontent.com/handbye/images/master/20190830233045.png)

又学到一个新东西：

> phps文件就是php的源代码文件，通常用于提供给用户（访问者）查看php代码，因为用户无法直接通过Web浏览器看到php文件的内容，所以需要用phps文件代替。其实，只要不用php等已经在服> 务器中注册过的MIME类型为文件即可，但为了国际通用，所以才用了phps文件类型。
>它的MIME类型为：text/html, application/x-httpd-php-source, application/x-httpd-php3-source。

接下来就是代码审计了

需要传入一个id并且这个id进行url解码后的值为admin

这里需要注意的是当我们在浏览器输入admin时，浏览器会对admin进行一次url解码，所以需要对admin进行两次url编码才可

![20190830234614.png](https://raw.githubusercontent.com/handbye/images/master/20190830234614.png)

再进行一次编码：

![20190830234637.png](https://raw.githubusercontent.com/handbye/images/master/20190830234637.png)

最后结果为：%25%36%31%25%36%34%25%36%64%25%36%39%25%36%65

![20190830234756.png](https://raw.githubusercontent.com/handbye/images/master/20190830234756.png)

拿到flag
