+++
date = "2014-10-13T16:21:34+08:00"
description = "apache notes"
title = "apache"

+++

# Ubuntu 下的命令：
	
<br /> 

切换模块
<br />

    `sudo a2enmod rewrite`  开启 apache 的 rewrite 功能
    `sudo a2dismod rewrite` 禁用模块
    
切换站点

    `a2ensite sites-available里的文件名`
    `a2dissite sites-available里的文件名`
    
控制命令 apachectl or httpd

    `apachectl -k graceful` – 平滑重启
    `apachectl -k graceful-stop` – 平滑停止
    `httpd -v` 显示当前服务器版本
    `httpd -V` 显示编译apache时的编译设定
    `httpd -l` 显示已编译进apache内核的模块
    `httpd -f ./config.conf` 以指定的配置文件启动
    `httpd -L` 显示有效的配置文件**指令**清单
    `httpd -D` 附加参数 附带附加参数启动 `<IfDefine></IfDefine>`
    `apachectl configtest` 测试配置文件
    
<br />

# 指令处理顺序

1. `<Directory>`(除了正则表达式)和`.htaccess`
2. `<DirectoryMatch>`(和`<Directory ~>`)
3. `<Files>`和`<FilesMatch>`
4. `<Location>`和`<LocationMatch>`

数字大的覆盖数字小的的相同设置

<br />

除了 `<Directory>`，每个组都按它们在配置文件中出现的顺序被依次处理，而 `<Directory>` 会按字典顺序由短到长被依次处理。

<br />

例如：`<Directory /var/web/dir>` 会先于 `<Directory /var/web/dir/subdir>` 被处理。

<br />

如果有多个指向同一个目录的 `<Directory>` 段，则按它们在配置文件中的顺序被依次处理。

<br />

用 Include 指令包含进来的配置被视为按原样插入到 Include 指令的位置。

<br />

位于 `<VirtualHost>` 容器中的配置段在外部对应的段处理完毕以后再处理，这样就允许虚拟主机覆盖主服务器的设置。

<br />

当请求是由 `mod_proxy` 处理的时候，`<Proxy>` 容器将会在处理顺序中取代 `<Directory>` 容器的位置。

<br />

# 禁止特定的请求方式

```
<Directory \"/apache/www\">
<Limit DELETE TRACE OPTIONS>
    #禁止DELETE TRACE OPTIONS请求
    Order allow,deny
    Deny from all
    </Limit>
</Directory>

<Directory \"/apache/www\">
<LimitExcept GET POST HEAD>
    #禁止除GET POST HEAD以外的请求
    Order allow,deny
    Deny from all
    </Limit>
</Directory>
```

<br />

# 日志

`LogFormat \"%h %l...\" log_nick_name`

`CustomLog log_filename log_nick_name` 应用刚才设置的LogFormat到log_filename

<br />

日志轮替, Apache 自带的 logrotate

`CustomLog \"|a/b/c/rotate_bin_name log_filename 86400 log_nick_name`

<br />

86400(24h)日志文件清空重记录

<br />

# 访问控制

`Order Deny,Allow`

<br />

先检查 Deny，后检查 Allow。后覆盖前的

* Deny 为空，若 Allow 非空，则除明确允许的，其他拒绝。

* Allow 为空，若 Deny 非空，则除明确禁止的，其他允许。

```
Order Deny,Allow
Allow from All  
```

先 Deny ,Deny 为空，允许全部。后 Allow，仍然是允许全部

```
Order Allow,Deny
Deny from All  
```

先 Allow ,Allow 为空。后 Deny，Deny 非空并禁止全部，所以没有允许的。

```
Order Deny,Allow
Deny from ip1 ip2  
```

先 Deny，禁掉了 ip1，ip2。Allow 时 Deny 非空，除被 Deny 的，其他允许。

```bash
Order Allow,Deny  
Allow from all    
Deny from ip1 ip2
```

先 Allow 了全部，后禁止了个别几个

<br />

以下是错误案例

```bash
Order Deny,Allow  
Allow from all
Deny from domain.org  
```

想禁止来自domain.org的访问。先 Deny 了 domain.org。

<br />

Allow 此时要是为空，则除了 domain.org 其他允许

<br />

但 Allow 此时非空，允许全部覆盖了之前的禁止，所以失效。

<br />

# 2.2 与 2.4 的区别

认证：

<br />

原本使用 `Order Deny / Allow` 的方式，改用 Require

```bash
Require all denied
Require all granted
Require host xxx.com
Require ip 192.168.1 192.168.2
Require local
```

