+++
date = "2015-08-23T18:05:50+08:00"
description = "sql note"
title = "sql"

+++

# 范式：
	
名词定义：主属性（在主键中的属性），其余的都是非主属性

* 第一范式：数据项不可分隔。

* 第二范式：主键到每一个非主属性都是完全函数依赖

* 第三范式：消除第二范式基础上的非主属性间的传递依赖

* BCNF 范式：满足第三范式的情况下，作为决定因素的属性一定要在码当中。

* 第四范式：在BCNF下，消除多值依赖

<br />

# 日志

1.二进制日志

<br />

`log_bin = /var/log/...mysql-bin.log`  

<br />

2、错误日志

<br />

`log_error=\"...errorlog.log\"`

<br />

3、更新日志

<br />

`log_update=\"...updatelog.log\"`

<br />

4、慢查询日志

```
log_show_queries = \"...\"    #保存日志的路径和文件名，确保权限可写
long_query_time = 2           #超过多少秒则保存查询数据
log-queries-not-using-indexs  #记录不使用索引的
```

<br />

# 常用语句

<br />

* 返回MySQL服务器状态信息 `show status`

* 显示所有可用show语句 `help show`

* 查看表结构 `describe table_name`

* 显示建表(库)信息 `show create table(database) XXX` 返回的AUTO_INCREMENT为当前最大值+1

* 复制表结构 `create table t3 like t1`

* 复制表数据 `insert into t3 select * from t1` 索引不会复制

* 刷新二进制日志 `FLUSH LOGS`

* 刷新权限 `FLUSH PRIVILEGES`

* 刷新所有表 `flush tables` 主从同步时 `flush tables with read lock`

* 快速重建表 `repair table table_name quick`

* 修改表属性 `ALTER (IGNORE)TABLE table_name ...`

* 存储IP地址 `select inet_aton(ip) from table_name` 转回用 `ntoa`

# Join

基本语法：

    `a LEFT JOIN b USING (c1,c2,c3)`

等价于

    `a LEFT JOIN b ON a.c1=b.c1 AND a.c2=b.c2 AND a.c3=b.c3 where ...`

几种 JOIN 方式

* `INNER JOIN` 
    * `Select A.Name,B.Hobby from A, B where A.id = B.id`

    * `Select A.Name,B.Hobby from A INNER JOIN B ON A.id = B.id`

    * `LEFT or RIGHT JOIN`

    * `Select A.Name,B.Hobby from A Left JOIN B ON A.id = B.id`

    * `Select A.Name,B.Hobby from B Right JOIN A ON A.id = B.id`

    * 保留所有 A 表中联结字段的记录，若无与其相对应的 B 表中的字段记录则留空

    * `NATURAL JOIN` 默认是两个表中同名字段完全匹配（a.id=b.id,a.name=b.name….）的 `INNER JOIN`

* `FULL OUTER JOIN` 

    并集，两方互相没有匹配到的都用Null替代，MySQL 并不支持 `FULL OUTER JOIN`，但是我们可以使用LEFT JOIN 和 RIGHT JOIN 两者的结果 UNION 来实现这一功能。
