XCTF第七题simple_js  write up

## 题目

小宁发现了一个网页，但却一直输不对密码。(Flag格式为 Cyberpeace{xxxxxxxxx} )

## 思路

打开页面在输入框中输入任何内容都显示一个结果

![20190827183050.png](https://i.loli.net/2019/08/27/jHGWzPTnNqxp9Ed.png)

查看网页源码

![20190827183130.png](https://i.loli.net/2019/08/27/HLvxS2CyVD9XKJU.png)

发现这段JS代码表示完全无论我们输入的什么内容，结果都是一样的，所以判断这段代码无用。

有用的是：

```js
 String["fromCharCode"](dechiffre("\x35\x35\x2c\x35\x36\x2c\x35\x34\x2c\x37\x39\x2c\x31\x31\x35\x2c\x36\x39\x2c\x31\x31\x34\x2c\x31\x31\x36\x2c\x31\x30\x37\x2c\x34\x39\x2c\x35\x30"));
```

写python脚本进行转码即可：

```python
string = "\x35\x35\x2c\x35\x36\x2c\x35\x34\x2c\x37\x39\x2c\x31\x31\x35\x2c\x36\x39\x2c\x31\x31\x34\x2c\x31\x31\x36\x2c\x31\x30\x37\x2c\x34\x39\x2c\x35\x30"
s = string.split(",")
c = ""
for i in s:
    i = chr(int(i))
    c = c+i
print(c)

```

结果：

786OsErtk12
