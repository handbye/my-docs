XCTF第八题xff_refer  write up

## 题目

X老师告诉小宁其实xff和referer是可以伪造的。

## 思路

打开网页,提示：

![20190827184008.png](https://i.loli.net/2019/08/27/1w3rKHGSZi2mDCI.png)

伪造HTTP请求头中的x-forward-for

![20190827184105.png](https://i.loli.net/2019/08/27/ZcgM5lPWKSxIXUr.png)

刷新页面提示：

![20190827184142.png](https://i.loli.net/2019/08/27/7GmOTQD9krtY3d1.png)

继续伪造HTTP请求头中的refer

![20190827184229.png](https://i.loli.net/2019/08/27/zAZ95aPdgQWK1y4.png)

刷新页面即可得到Flag

![20190827184258.png](https://i.loli.net/2019/08/27/UDZqjLzExG8XvTp.png)
