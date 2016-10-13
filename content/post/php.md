+++
date = "2015-01-12T19:32:35+08:00"
description = "php notes"
title = "php"

+++

# Session

设置个30分钟的session:

<br />

1.设置php.ini的session.gc_maxlifetime 

<br />

但是PHP是用一定的概率来运行session的gc，不能保证一定运行，而且假设5分钟前设置了一个a=1, 5分钟后又设置了一个b=2, 那么这个Session文件的修改时间为添加b时刻的时间, 那么a就不能在30分钟的时候被清除。还有，如果有俩个应用都没有指定自己独立的save_path（两个都存在默认路径）, 一个设置了过期时间为2分钟(假设为A), 一个设置为30分钟(假设为B), 那么每次当A的Session gc运行的时候, 就会同时删除属于应用B的Session files.

<br />

2.设置session载体cookie，session.cookie_lifetime

<br />

但可以被抓包后修改发送，不严谨.

<br />
    
3.自己为每一个Session值增加Time stamp.每次访问之前, 判断时间戳.

<br />

# 二进制安全

「binary safe」 的函数会把它的输入字符串原封不动的进行处理；

<br />

而非 「binary safe」 的函数是在底层直接调用C的字符串相关的函数，而这些函数处理字符串会把NULL后边的内容忽略掉。

<br />

如果函数strlen是binary safe的话，我们将得到7；如果函数是非binary safe的话，我们将得到3

```
==================================
$str = \"abcx00abc\"; //x00为NULL
echo strlen($str);  //7
==================================
```

<br />

# Require & Include

首次判断是否绝对路径， 如果是则跳过include_path进行解析

<br />

再次判断是否相对路径 （./file, ../dir/file），基点永远是当前工作目录（运行脚本的目录)

<br />

最后判断include_path列表

<br />

# 正则表达式

`#(?<=c)d(?=e)#` &nbsp;&nbsp;d前面紧跟c, d 后面紧跟e，满足的string匹配出d

<br />

# SSO

每个站点, 设置用户验证中心（`center.php`）

<br />
	
在A站登录成功后，跳转到A站的 `center.php` 设置 `token`(cookie为载体），并将信息入库

<br />

在 `center.php` 的 html 部分，向其他站（如B站，C站...）的对应处理中心（`client.php`）发送 `get` 请求（iframe 为载体），附带 `token` 参数

<br />

其他站的`client.php` 接收到 `token` 后（可经数据库）验证，成功后在 iframe 内设置该站的 cookie

<br />

PS：IE下要加P3P头

<br />

# 生成Base64

```
function data_uri($file, $mime) {
  $contents=file_get_contents($file);
  $base64=base64_encode($contents);
  echo \"data:$mime;base64,$base64\";
}
```
