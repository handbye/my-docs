XCTF第六题disable_buttom  write up

## 题目

X老师今天上课讲了前端知识，然后给了大家一个不能按的按钮，小宁惊奇地发现这个按钮按不下去，到底怎么才能按下去呢？

## 思路

打开页面，发现按钮无法点击。

![20190827125357.png](https://i.loli.net/2019/08/27/ktUKYqQO98L5He6.png)

F12查看源码

![20190827125538.png](https://i.loli.net/2019/08/27/ywVTMsa4UzrjfeB.png)

发现input标签有个disable属性

> disabled 属性规定应该禁用的 `<input>`元素。被禁用的 input 元素是无法使用和无法点击的。

删除源码中的disable属性，按钮即可点击，然后得到Flag。

![20190827125908.png](https://i.loli.net/2019/08/27/lHt6GMmXSz8DWVx.png)
