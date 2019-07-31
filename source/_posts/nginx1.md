---
title: 详解nginx配置
categories: 
- nginx
tags: 
- nginx
- 代理服务
---

## 前言
nginx配置文件由四部分组成：
1. main(全局变量)
2. server(主机设置)
3. upstream(上游服务器设置，主要为反向代理，负载均衡相关配置)
4. location(url匹配特定位置后的设置)


```
note：text/html是以网页形式发送，text/plain是以纯文本格式发送
```

nginx优势
1. 低内耗，占用内存少
2. 高负载，并发能力强
3. 负载均衡，节点健康检查
4. 反向代理，数据缓存


```
note：官方表示保持10000个没有活动的连接只需要2.5M
epoll模型：linux采用多路复用IO技术，是一种高性能的IO模型,unix和mac则是kqueue
```

特性：
1. 事件驱动：通信机制采用epoll模型，支持更大的并发连接
2. master/worker结构：一个master进程多个woker进程
3. 多进程 + 事件驱动

## 基础框架

master进程：
- 接收外部信号
- 传递信号给worker进程
- 监控各worker进程，出现异常后重新启动新的worker进程

worker进程：
- 相互独立（意味着无需加锁，一个worker进程异常不会影响其他worker进程）
- 一个请求只会在一个worker中处理
- 每个worker只有一个主线程，采用异步非阻塞方式处理请求，因此可以同时处理成千上万个请求；相对于appache，每一个请求占用一个线程的做法，一旦请求并发量大得到时候，内存占用大，线程上下文切换带来的CPU开销也大，性能就突然下降。

异步、同步（调用是否需要返回结果，发短信，打电话）
阻塞、非阻塞（等待返回结果时的状态，挂起当前线程，不影响当前线程）

## nginx配置
nginx.conf

```
{
    main
    events {}
    http {
        main
        server {
            main
            location {}
        }
        upstream name_xx {}
    }
}
```


nginx配置文件主要由四部分组成：main, upstream, server(http), location(server)
-  main:全局配置
-  upstream: 负载均衡
-  server: 虚拟主机，要挂载的服务器，可以多个
-  loaction: 匹配资源路径

nginx.conf

```
#定义Nginx运行的用户为appadmin
user  appadmin;

#nginx worker进程数，建议设置等于CUP内核数量
worker_processes 4;

#全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
error_log  logs/nginx_error.log  notice;

#进程文件
pid        /Data/nginx/sbin/nginx.pid;

#限制最大可打开文件数据限制，不设置时默认为65535
worker_rlimit_nofile 65535;

events
{
    #事件模型 linux默认epoll事件模型
    use epoll;
    
    #每个worker进程处理的最大并发数量
    worker_connections 2000;
}

http
{
    #文件类型与文件扩展名的映射表
    include       mime.types;
    
    #默认文件类型
    default_type  application/octet-stream;
    
    #默认编码
    charset  gb2312;
    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    #允许客户端请求的最大单文件字节数
    client_max_body_size 8m;
    
    #开启高效文件传输模式
    sendfile on;
    
    #防止网络阻塞
    tcp_nopush     on;
    
    #长连接超时时间，单位是秒，默认75s
    keepalive_timeout 90s;
    tcp_nodelay on;
    
    #指定连接到后端FastCGI 的超时时间, 默认为60s
    fastcgi_connect_timeout 300s;
    #向FastCGI 传送请求的超时时间
    fastcgi_send_timeout 300s;
    #接收FastCGI 应答的超时时间
    fastcgi_read_timeout 300s;
    #指定读取FastCGI 应答第一部分需要用多大的缓冲区
    fastcgi_buffer_size 64k;
    #指定本地需要用多少和多大的缓冲区来缓冲FastCGI 的应答
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    #在写入fastcgi_temp_path 时将用多大的数据块
    fastcgi_temp_file_write_size 128k;
    
    #开启gzip压缩输出
    gzip on;
    
    #需要压缩文件的最小限制
    gzip_min_length  1k;
    
    #缓存压缩数据流，以原始数据大小并以16k为单位的4倍申请内存
    gzip_buffers     4 16k;
    
    #http协议的版本
    gzip_http_version 1.0;
    
    #gzip压缩比（1-9），数值越大压缩效果越好
    gzip_comp_level 2;
   
    #匹配mime.types类型，压缩对应的文件类型
    gzip_types       text/plain application/x-javascript text/css application/xml application/javascript;
    
    #根据客户端的HTTP头来判断，是否需要压缩
    gzip_vary on;
  
    server {
        # 监听端口
        listen       80;
        # 域名 可以有多个，用空格隔开
        server_name  xx.xx.com;
        
        #默认编码
        charset utf-8;
        
        #定义本虚拟主机的访问日志
        #access_log  ar/loginx/log/host.access.log  main;
	
	    # 因为所有的地址都以 / 开头，所以这条规则将匹配到所有请求,但是正则和最长字符串会优先匹配
        location / {
            proxy_pass  http://127.0.0.1:8080;
            
            # 设置后端服务器“Location”响应头和“Refresh”响应头的替换文本
            proxy_redirect     off;
            
            proxy_set_header   Host             $host;
            # 获取用户的真实 IP 地址
            proxy_set_header   X-Real-IP        $remote_addr;
            #后端的Web服务器可以通过 X-Forwarded-For 获取用户真实IP，多个 nginx 反代的情况下，例如 CDN
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            
            # 限制上传文件大小，默认为1m
            client_max_body_size 10m
            
            #nginx跟后端服务器连接超时时间(代理连接超时),默认为60s
            proxy_connect_timeout      90s;
            #后端服务器数据回传时间(代理发送超时)
            proxy_send_timeout         90s;
            #连接成功后，后端服务器响应时间(代理接收超时)
            proxy_read_timeout         90s;
            
            #后端服务器的相应头大小
            proxy_buffer_size          4k;
            #后端响应内容的缓冲区大小
            proxy_buffers              4 32k;
            #高负荷下缓冲大小（proxy_buffers*2）
            proxy_busy_buffers_size    64k;
            #设定缓存文件夹大小，大于这个值，将从upstream服务器传
            proxy_temp_file_write_size 64k;
            #临时文件大小，0代表关闭硬盘缓冲
            proxy_max_temp_file_size 0;
        }
        
        # 精确匹配 /
        location = / {
            root /Data/client/test_build/;
            index index.html;
        }	
        
        # 匹配所有以 html,js,css,map 结尾的请求
        location ~* \.(html|js|css|map)$ {
            root /Data/client/test_build/;
        }
        
        # 匹配任何以/assets/开头的地址，匹配符合以后，停止往下搜索正则
        location ^~ /assets/ {
            alias /Data/client/assets/;
        }
        
        # 匹配任何以/download/开头的地址，匹配符合以后，停止往下搜索
        location ^~ /download/ {
            alias /Data/deploy/download/;
        }
        
        # 匹配任何以/Data/source/images/开头的地址，匹配符合以后，停止往下搜索
        location ^~ /Data/source/images/ {
            alias /Data/source/images/;
        }
        
        # 匹配任何以/dev/开头的地址
        location ^~ /dev/ {
            deny 192.168.1.1; #设置禁止IP
            allow 10.45.25.1; #设置允许访问IP
            deny all;# 设置其他IP禁止访问
            alias /Data/client/test_build/;
        }
        
        # 屏蔽以/api/v1/issue/number开头的接口
        location ^~ /api/v1/issue/number {
            return 200; # 为不影响前端用户，直接返回200，亦可自定义去处理
        }
        
        # 配置查看nginx并发连接数,打开浏览器如http://10.10.10.10/connection即可访问，10.10.10.10是服务器IP
        location /connection {
            stub_status on;
            access_log /Data/nginx/logs/connection.log;
            auth_basic "NginxStatus";
        }

    }
    
    server {
        listen       90;
        server_name  xx.xx.com;
        
        location / {
            proxy_pass  http://h1backup;
        }
        
        location = / {
            root /Data/client/test_build/;
            index index.html;
        }
    }
    
    upstream h1backup {
        ip_hash; #使用访问IP算法
        server 10.45.25.11:8080 down; #当前server不参与负载
        server 10.45.25.12:8080 weight=2; #权值为2
        server 10.45.25.13:8080;
        server 10.45.25.14:8080 backup; #其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻
        
    }
	
}

```

- 已=开头表示精确匹配
- 如 A 中只匹配根目录结尾的请求，后面不能带任何字符串。
- ^~ 开头表示uri以某个常规字符串开头，不是正则匹配
- ~ 开头表示区分大小写的正则匹配;
- ~* 开头表示不区分大小写的正则匹配
- / 通用匹配, 如果没有其它匹配,任何请求都会匹配到

优先级：
(location =) > (location 完整路径) > (location ^~ 路径) > (location ~,~* 正则顺序) > (location 部分起始路径) > (/)

root和alias：都是指定文件路径，区别是root处理结果是root+loaction路径，alias处理结果是alias替换location路径，并且alias后面要加“/”



## nginx命令
（以nginx安装在/Data/目录下为例）
#查看nginx进程
$ps -ef | grep nginx

#启动
$ sudo /Data/nginx/sbin/nginx

#快速停止
$ sudo /Data/nginx/sbin/nginx -s stop
$ kill -term 主进程号

#从容停止
$ sudo /Data/nginx/sbin/nginx -s quit
$ kill -quit 主进程号

#重启
$ sudo /Data/nginx/sbin/nginx -s reload

#查看nginx版本
$ sudo /Data/nginx/sbin/nginx -v

#查看nginx版本，配置
$ sudo /Data/nginx/sbin/nginx -V


## 注意事项

> 1）gzip压缩

> 2）proxy_temp_file_write_size缓存文件大小

> 3）worker_rlimit_nofile并发数量限制

> 4）端口号小于1024需要root权限

> 5）**nginx只有一个server时，不会去匹配server_name，只有多个server才会去匹配server_name，倘若多个server都匹配不到，会默认匹配到第一个server中去**

