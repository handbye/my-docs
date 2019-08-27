## 反射型XSS简介

接着[上篇文章](https://darkless.cn/2019/05/11/dvwa-xss-dom/)介绍下什么是反射性XSS。

在三种类型的XSS中，反射型XSS是利用最广泛的一种。它通过给别人发送带有恶意脚本代码参数的URL，当URL地址被打开时，特有的恶意代码参数被HTML解析、执行。它的特点是非持久化，必须用户点击带有特定参数的链接才能引起。

## Low等级

先尝试在输入框中随便输入一个字符。

![20190512092549.png](https://raw.githubusercontent.com/handbye/images/master/20190512092549.png)

可见，服务器是从输入框中获取用户输入，然后展示在前端页面上，那随便输入一段JS代码，看能否执行。

![20190512092833.png](https://raw.githubusercontent.com/handbye/images/master/20190512092833.png)

js代码被执行，说明存在xss攻击。

查看源码发现，并未对用户的输入做任何过滤：

![20190512092952.png](https://raw.githubusercontent.com/handbye/images/master/20190512092952.png)

## Medium等级

使用Low等级的方法，输入一下js代码

```js

<script>alert(1)</script>
```

![20190512093136.png](https://raw.githubusercontent.com/handbye/images/master/20190512093136.png)

发现前端直接显示了alert(1),说明将用户输入的`<acript>`标签做了过滤。 换一种构造方式，重新输入：

在输入框中输入:

```js

<img  src='1'  onerror=alert(1)>
```

![20190512093449.png](https://raw.githubusercontent.com/handbye/images/master/20190512093449.png)

利用成功，查看源码发现对用户输入的`<script>`关键字做了替换，但并未对其它可能造成的xss攻击代码关键字做过滤。

![20190512093623.png](https://raw.githubusercontent.com/handbye/images/master/20190512093623.png)

## High等级

使用Medium等级的方法输入发现也可以造成xss攻击：

![20190512093907.png](https://raw.githubusercontent.com/handbye/images/master/20190512093907.png)

查看源码，发现只是用正则表达式匹配`<script>`里面的字母来做替换，并不能从根本上预防xss。

![20190512094350.png](https://raw.githubusercontent.com/handbye/images/master/20190512094350.png)

## Impossible等级

随便输入一个字符查看结果：

![20190512094547.png](https://raw.githubusercontent.com/handbye/images/master/20190512094547.png)

发现此等级还使用了user_token。

使用High等级的方法进行输入：

![20190512094659.png](https://raw.githubusercontent.com/handbye/images/master/20190512094659.png)

发现直接将用户的输入原封不动的打印了出来，这样的话我们的任何构造输入都将无效。

查看源码：

![20190512095029.png](https://raw.githubusercontent.com/handbye/images/master/20190512095029.png)

发现使用了`htmlspecialchars()`函数，它会把预定义的字符 "<" （小于）和 ">" （大于）转换为 HTML 实体。
