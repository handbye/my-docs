XCTF第十一题command_execution  write up

## 题目

小宁写了个ping功能,但没有写waf,X老师告诉她这是非常危险的，你知道为什么吗。

## 思路

打开页面，如下，输入127.0.0.1,查看命令执行结果

![20190827193144.png](https://i.loli.net/2019/08/27/tdVmbsDTirvJnCk.png)

联想到能否使用命令拼接的方式来执行其它命令：

![20190827193331.png](https://i.loli.net/2019/08/27/N6blMpzOYQXgmRd.png)

执行成功，得到flag.txt存放的地址

![20190827193406.png](https://i.loli.net/2019/08/27/A1wVkr2YDGxFgmq.png)
