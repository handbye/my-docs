XCTF第十二题simple_php  write up

## 题目

小宁听说php是最好的语言,于是她简单学习之后写了几行php代码

## 思路

打开页面发现是一段PHP代码

![20190828101851.png](https://i.loli.net/2019/08/28/iu5UlNGTaWcXQfK.png)

简单审计下代码，发现需要以get的方式传入两个参数a和b。

a参数的要求 a必须等于0且a为真

b参数的要求 b不能为数字且b大于1234

这道题的核心问题是理解PHP语言的弱类型，具体文章请参考：[https://www.cnblogs.com/Mrsm1th/p/6745532.html](https://www.cnblogs.com/Mrsm1th/p/6745532.html)

解法：

![20190828103137.png](https://i.loli.net/2019/08/28/lQ8y1wsHmMquUIX.png)
