## 题目描述

无

## 思路

打开题目是一个注册页面，先注册登录

![20190901111909.png](https://raw.githubusercontent.com/handbye/images/master/20190901111909.png)

登陆后是一个这个页面

![20190901111948.png](https://raw.githubusercontent.com/handbye/images/master/20190901111948.png)

单击 Manage发现只有管理员（admin）才有权限。

![20190901112047.png](https://raw.githubusercontent.com/handbye/images/master/20190901112047.png)

看看有没有办法越权至admin

在登陆页有个找回密码的操作

![20190901112502.png](https://raw.githubusercontent.com/handbye/images/master/20190901112502.png)

抓包查看正常找回密码的流程，发现输入正确信息到第二部后直接输入新密码即可改密

![20190901114524.png](https://raw.githubusercontent.com/handbye/images/master/20190901114524.png)

继续抓包

![20190901114601.png](https://raw.githubusercontent.com/handbye/images/master/20190901114601.png)

这一步只校验了用户名，修改username为admin即可重置admin的密码。

![20190901114741.png](https://raw.githubusercontent.com/handbye/images/master/20190901114741.png)

然后使用admin登录

![20190901114821.png](https://raw.githubusercontent.com/handbye/images/master/20190901114821.png)

登录成功，点击Manage,提示IP不允许

![20190901114846.png](https://raw.githubusercontent.com/handbye/images/master/20190901114846.png)

修改X-Forwarded-for为127.0.0.1即可绕过

![20190901115006.png](https://raw.githubusercontent.com/handbye/images/master/20190901115006.png)

![20190901115050.png](https://raw.githubusercontent.com/handbye/images/master/20190901115050.png)

查看网页源代码，发现了这么一个注释

![20190901115117.png](https://raw.githubusercontent.com/handbye/images/master/20190901115117.png)

这是一个文件管理页面，将do后面的参数改为upload试试

![20190901115329.png](https://raw.githubusercontent.com/handbye/images/master/20190901115329.png)

进入了一个上传页面，看能否上传php🐎

![20190901115430.png](https://raw.githubusercontent.com/handbye/images/master/20190901115430.png)

提示是php文件不能上传，上传个图片试试呢

![20190901115517.png](https://raw.githubusercontent.com/handbye/images/master/20190901115517.png)

可以上传，但是不能拿到Flag,经过测试这里不仅对后缀进行了黑名单过滤，同时会检查文件的开头内容，所以不能以`<?php`开头，可以用`<script language="php"> ... php code... </script>`来进行绕过，先以jpg后缀上传，抓包改后缀为php5或php4，即可看到flag。

![20190901130937.png](https://raw.githubusercontent.com/handbye/images/master/20190901130937.png)
