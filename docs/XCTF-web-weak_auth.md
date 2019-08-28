XCTF第九题weak_auth  write up

## 题目

小宁写了一个登陆验证页面，随手就设了一个密码。

## 思路

从题目得知此题的解法应该是爆破弱口令

打开页面是个登录页，随手输入用户名和密码，提示必须用admin登录

![20190827190808.png](https://i.loli.net/2019/08/27/sn3AyJpY2gdtuBP.png)

接下来使用burp抓包爆破即可，过程胜率，直接看结果：

![20190827190913.png](https://i.loli.net/2019/08/27/MijNyHwYE465rzu.png)
