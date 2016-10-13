+++
date = "2014-11-03T15:18:40+08:00"
description = "nginx notes"
title = "nginx"

+++

# 指令

* 选择配置文件启动 `.../sbin/nginx -c xx.conf`

* 平滑停止 `kill - QUIT Nginx_master_pid`

* 平滑重启 `kill - HUB Nginx_master_pid`

* 快速停止 `kill - TERM Nginx-master_pid`

* 强制停止 `pkill -9 nginx`

其他控制信号：

* USR1 - 重新打开日志文件，用于日志切割
* USR2 - 平滑升级可执行程序
* WINCH - 平滑关闭工作进程

<br />

# 虚拟主机

一个虚拟主机的一个server段

```bash
http
{
    server
    {
        listen 192.168.8.43:80   //区分IP
        server_name a.com //区分域名
        access_log ...
        location /
        {
            index index.html index.htm
            root /...
        }
    }
    server
    {
        listen 192.168.8.44:80
        server_name b.com
        access_log ...
        location /
        {
            index index.html index.htm
            root /...
        }
    }
}
```

<br />

# 负载均衡

```bash
upstream php_pool {
    //如果使用了ip_hash指令(根据ip定位服务器)
    //如果要突然去掉某个服务器要在最后加 'down' , 否则会影响之前 hash 运算结果，对session造成问题
    server 192.168.8.1:3128 weight=5 max_fails=2 fail_timeout=30s;
    ...
}

upstream bbs_pool {
    ...
}

server{
    proxy_next_upstream
    proxy_pass
    proxy_set_header X-Forwarded-For $remote_addr;
    client_max_body_size 300m    #允许客户端请求的最大单个文件字节数
    client_body_buffer_size 128k #代理请求的最大字节数
    proxy_connect_timeout 600    #跟后端服务器连接超时时间
    proxy_read_timeout 600       #后端排队超时时间
    proxy_send_timeout 600       #后端回传数据超时时间
    proxy_buffer_size 16k        #代理请求缓存区，缓存头信息
    proxy_buffers 4 32k
    proxy_busy_buffers_size 64k
    proxy_temp_file_write_size 64k #proxy缓存临时文件大小
}
```

<br />

# 静态文件缓存

```bash
location ~ .*\\.(gif|jpg...)$
{
    proxy_cache cache_one;
    proxy_cache_valid 200 10m; //m -> minute
    proxy_cache_valid 304 1m;
    proxy_cache_valid 301 302 1h;
    proxy_cache_valid any 1m;
    proxy_cache_key $host$uri$is_args$args;
    proxy_set_header
    proxy_pass
}
```

<br />

# 重定向

`rewrite ^/html/tagindex/.*$ http://$host permanent`

<br />

Nginx和Apache的rewrite规则对比：

```bash
[L]换成last
RewriteCond 对应 if
[R]换成redirect
[P]换成last
[R,L] 换成 redirect （302）//[r=302]
[P,L] 换成 last
[PT,L] 换成 last
```

Nginx文件目录匹配：

```bash
if (!-e $request_filename)
* -f和!-f用来判断是否存在文件
* -d和!-d用来判断是否存在目录
* -e和!-e用来判断是否存在文件或目录
* -x和!-x用来判断文件是否可执行
```

<br />

# 访问控制

与Apache不同，跟书写顺序有关，只要IP匹配成功一条，后面的就失效

```
location / {
    //这里将永远输出403错误。
    deny all;
    //这些指令不会被启用，因为到达的连接在第一条已经被拒绝
    deny 192.168.1.1;
    allow 192.168.1.0/24;
    allow 10.1.1.0/1
}
```

---

<br />

# 一份配置实例

<br />

注意`cgi.fix_pathinfo=0`,如果pathinfo必须设为1的话，可配合`try_files $fastcgi_script_name =404;`

```
# 使用的用户和组
user www www;

# 指定工作衍生进程数（一般等于cpu的总核数或总核数的两倍）
worker_processes 8;

# 定义错误日志存放的路径，错误日志的级别可选为:[debug | info | notice | warn | error | crit]
error_log /data1/logs/nginx_error.log crit;

# 指定pid存放路径
pid /usr/local/nginx/nginx.pid

# 指定文件描述符数量
worker_rlimit_nofile 100000;

events
{
  # 使用的网络I/O模型，Linux系统推荐使用epoll模型，FreeBSD推荐kqueue模型
  Use epoll;
  #允许使用的链接数
  Worker_connections 2048;
  # multi_accept on;
  }

http
{
  inlcude mime.types;
  #设定mime类型,类型由mime.type文件定义 include /etc/nginx/mime.types;
  
  default_type application/octet-stream;
  
  # 设置使用的字符集，推荐不随便设置，程序员在html代码中meta标签设置
  #charset utf-8;
  
  server_names_hash_bucket_size 128;
  client_header_buffer_size 32k;
  large_client_header_buffers 4 32k;
  
  #设置客户端能够上传的文件大小
  client_max_body_size 8m;
  
  sendfile on;
  # sendfile 如果用来进行下载等应用磁盘IO重负载应用，可设置为 off
  tcp_nopush on;
  
  keepalive_timeout 60;
  tcp_nodelay on;
  
  server_tokens off;
  
  tcp_nopush on;
  # 告诉nginx在一个数据包里发送所有头文件，而不一个接一个的发送
 
  #开启gzip压缩
  #gzip on;
  #gzip_min_length 1k;
  #gzip_buffers 4 16k;
  #gzip_http_version 1.1;
  #gzip_comp_level 4;
  #gzip_types text/plain application/x-javascript text/css application/xml;
  #gzip_vary on; # 给代理服务器用，根据需要决定
  
  #设定请求缓冲
  client_header_buffer_size    1k;
  large_client_header_buffers  4 4k;
  
  include /etc/nginx/conf.d/*.conf;
  include /etc/nginx/sites-enabled/*;
  
  #设定负载均衡的服务器列表
  upstream mysvr {
    #weigth参数表示权值，权值越高被分配到的几率越大
    #本机上的Squid开启3128端口
    server 192.168.8.1:3128 weight=5;
    server 192.168.8.2:80  weight=1;
    server 192.168.8.3:80  weight=6;
  }
  
  upstream mysvr2 {
    #weigth参数表示权值，权值越高被分配到的几率越大
    server 192.168.8.x:80  weight=1;
    server 192.168.8.x:80  weight=6;
  }
  
  server
  {
  
    listen 80;
    server_name www.test.com test.com;
    index index.html index.htm;
    root /usr/htdocs;
    
    location / {
        try_files $uri $uri/ /index.php; 
        # 检查文件是否存在 try_files path1 [path2] (url|=code)
        # try_files $uri $uri/ /index.php?$args;
    }
    
    # 定义错误提示页面？
    error_page   500 502 503 504 /50x.html;  
    location = /50x.html {
        root   /root;
    }
    
    location ~ \\.php$ {
        if ( $fastcgi_script_name ~ ..*/.*php ) {
            return 403;
        }
        
        fastcgi_pass 127.0.0.1:9000;
        
        #fastcgi_index index.php;
        #fastcgi_param SCRIPT_FILENAME /var/www/nginx-default$fastcgi_script_name;
        #include /etc/nginx/fastcgi_params;
        
        try_files $uri =404;
        include fastcgi.conf;  
        # fastcgi.conf 里面已定义了 fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;      
    }
    
    location ~ .*\\.(gif|jpg|jpeg|png|bmp|swf)$
    {
        expires 30d;
    }
    
    location ~ .*\\.(js|css)$
    {
        expires 1h;
    }
    
    #设定查看Nginx状态的地址
    location /NginxStatus {
        stub_status           on;
        access_log            on;
        auth_basic            \"NginxStatus\";
        auth_basic_user_file  conf/htpasswd;
    }
    #禁止访问 .htxxx 文件
    location ~ /\\.ht {
        deny all;
    }
    
    #对aspx后缀的进行负载均衡请求
    location ~ .*\\.aspx$ {
    
          root   /usr/htdocs;                     #定义服务器的默认网站根目录位置
          index index.php index.html index.htm;   #定义首页索引文件的名称
          
          proxy_pass  http://mysvr;      #请求转向 mysvr|php|bbs 定义的服务器列表
          
          #proxy_next_upstream http_502 http_504 error timeout invalid header; 出现这些情况转到池中下一台
          #proxy_redirect http://a.com:9080/ /  把所有\"http://a.com:9080/\"的内容替换成\"/\",消除端口号对代理的影响
          proxy_redirect off;
          
          #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          client_max_body_size 10m;      #允许客户端请求的最大单文件字节数
          client_body_buffer_size 128k;  #缓冲区代理缓冲用户端请求的最大字节数，
          proxy_connect_timeout 90;      #nginx跟后端服务器连接超时时间(代理连接超时)
          proxy_send_timeout 90;         #后端服务器数据回传时间(代理发送超时)
          proxy_read_timeout 90;         #连接成功后，后端服务器响应时间(代理接收超时)
          proxy_buffer_size 4k;          #设置代理服务器（nginx）保存用户头信息的缓冲区大小
          proxy_buffers 4 32k;           #proxy_buffers缓冲区，网页平均在32k以下的话，这样设置
          proxy_busy_buffers_size 64k;   #高负荷下缓冲大小（proxy_buffers*2）
          proxy_temp_file_write_size 64k;#设定缓存文件夹大小，大于这个值，将从upstream服务器传
    }
    
    access_log /data1/logs/access.log access; #设定本虚拟主机(server)的访问日志
    
  }
  
}
```

