XCF第四题backup write up

## 题目

X老师忘记删除备份文件，他派小宁同学去把备份文件找出来,一起来帮小宁同学吧！

## 思路

打开靶场提示：

![20190827111243.png](https://i.loli.net/2019/08/27/ehBSzw7UYMI4tWZ.png)

寻找index.php的备份文件，手工输入index.php.backup无此页面，在输入index.php.bak成功访问下载到备份文件。

![20190827111422.png](https://i.loli.net/2019/08/27/ZoF1q8f2hpELx6l.png)

即可拿到flag。
