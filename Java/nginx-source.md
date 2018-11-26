001.开启调试日志http://nginx.org/en/docs/debugging_log.html

./configure --prefix=/usr/local/nginx --with-debug --with-http_stub_status_module --with-stream --with-http_ssl_module

1./configure --with-debug ...
2 在nginx.conf中增加：
daemon off;#关闭nginx在后台启动(守护进程模式)
master_process off;#关闭创建子进程


#user  nobody;
worker_processes  1;
 
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
~~~~~~~~~~~~~~~~~~
#“stderr”, “emerg”, “alert”, “crit”, “error”,”warn”, “notice”, “info”, “debug”
如果我们指定第一个级别为”debug”，那么nginx还允许我们指定第二级别：
“debug_core”, “debug_alloc”, “debug_mutex”, “debug_event”,
“debug_http”, “debug_mail”, “debug_mysql”
第二级别的指定是多选的，因此可以有多条关于第二级别的配置项目：
error_log  logs/error.log debug_http;
error_log  logs/error.log debug_core;
~~~~~~~~~~~~~~~~~~
#pid        logs/nginx.pid;
daemon off;
master_process off;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    #access_log  logs/access.log  main;
    sendfile        on;
    #tcp_nopush     on;
    #keepalive_timeout  0;
    keepalive_timeout  65;
    #gzip  on;
    #post server
    server {
        listen       8018;
        server_name  localhost;
        #charset koi8-r;
        #access_log  logs/host.access.log  main;
        access_log  logs/post.log  main;
        location / {
            root   html;
            index  index.html index.htm;
        }
        location /v1 
        {
            if ($request_method = GET ) 
            {
                    return 403;
            }
            mytest;
        }
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html 
        {
            root   html;
        }
    }
    #get service
    server {
        listen       8017;
        server_name  localhost;
        access_log  logs/get.log  main;
        #access_log off;
        location / 
        {
            root   html;
            index  index.html index.htm;
        }
        location /hello 
        {
            if ($request_method = POST )
            {
                return 403;
            }
            mytest;
        }
       error_page   500 502 503 504  /50x.html;
       location = /50x.html 
       {
            root   html;
       }
    }
 ｝
--------------------- 

