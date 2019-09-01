## 题目描述

解密

## 思路

打开题目

![20190901160634.png](https://raw.githubusercontent.com/handbye/images/master/20190901160634.png)

很明显题目已经提示了，需要解密#miwen，直接写代码吧：

```php
<?php
$a=" a1zLbgQsCESEIqRLwuQAyMwLyq2L5VwBxqGA3RQAyumZ0tmMvSGM2ZwB4tws";
$a=str_rot13($a);
$a=strrev($a);
$a=base64_decode($a);
function decode233($str)
{
    $b="";
    for($test=0;$test<strlen($str);$test++)
    {
        $b=$b.(chr(ord($str[$test])-1));
    }
    return $b;
}

echo strrev(decode233($a));
```

运行代码得到Flag

![20190901165435.png](https://raw.githubusercontent.com/handbye/images/master/20190901165435.png)
