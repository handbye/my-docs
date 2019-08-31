## 题目

云平台报表中心收集了设备管理基础服务的数据，但是数据被删除了，只有一处留下了入侵者的痕迹。

## 思路

打开题目发现是一个工控云管理系统

![20190829211656.png](https://raw.githubusercontent.com/handbye/images/master/20190829211656.png)

依次点击左侧的导航菜单，发现只有报表中心可以点击，其实题目中也有提示。

点开后发现是一个报表查询页面

![20190829211846.png](https://raw.githubusercontent.com/handbye/images/master/20190829211846.png)

选择日期范围什么也查不到，原本以为这里的日期查询可能存在注入什么的，尝试后证明我想多了，这里只写了各前端，根本没有后端。

观察url链接，发现了id参数，利用点可能在这里，使用burp爆破下试试

![20190829212848.png](https://raw.githubusercontent.com/handbye/images/master/20190829212848.png)

发现到2333时数据包长度和其他的不同，将id的值改为2333获得flag。

![20190829214644.png](https://raw.githubusercontent.com/handbye/images/master/20190829214644.png)
