## 题目描述

无

## 思路

下载提供的附件，解压后用编辑器打开发现是js代码，但是有一部分是乱码。

![20190829235522.png](https://raw.githubusercontent.com/handbye/images/master/20190829235522.png)

仔细查看这段js代码后，发现最后有个`eval()`函数执行行了前面的`_`函数。

eval()函数：

![20190829235717.png](https://raw.githubusercontent.com/handbye/images/master/20190829235717.png)

猜测是前面代码中的一些字符被eval计算了，所以乱码。

将eval函数改为alert()即可将原函数弹出来

![20190829235915.png](https://raw.githubusercontent.com/handbye/images/master/20190829235915.png)

```js
function $() {
    var e = document.getElementById("c").value;
    if (e.length == 16) if (e.match(/^be0f23/) != null) if (e.match(/233ac/) != null) if (e.match(/e98aa$/) != null) if (e.match(/c7be9/) != null) {
        var t = ["fl", "s_a", "i", "e}"];
        var n = ["a", "_h0l", "n"];
        var r = ["g{", "e", "_0"];
        var i = ["it'", "_", "n"];
        var s = [t, n, r, i];
        for (var o = 0; o < 13; ++o) {
            document.write(s[o % 4][0]);
            s[o % 4].splice(0, 1)
        }
    }
}
document.write('<input id="c"><button onclick=$()>Ok</button>');
delete _
```

这就是原本的js代码，继续审计代码。

只要e的规则符合长度为16并且以be0f23开头以e98aa结尾并且需要匹配233ac和c7be9即可.

这样的字符串就是`be0f23ac7be98aa`

![20190830000957.png](https://raw.githubusercontent.com/handbye/images/master/20190830000957.png)

输入至输入框中即可得到Flag

![20190830001038.png](https://raw.githubusercontent.com/handbye/images/master/20190830001038.png)

其实运行下面这段js代码也能得到Flag

```js
var t = ["fl", "s_a", "i", "e}"];
        var n = ["a", "_h0l", "n"];
        var r = ["g{", "e", "_0"];
        var i = ["it'", "_", "n"];
        var s = [t, n, r, i];
        for (var o = 0; o < 13; ++o) {
            document.write(s[o % 4][0]);
            s[o % 4].splice(0, 1)
        }
```
