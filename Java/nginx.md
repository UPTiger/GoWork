--------------------多--------------------------------
https://www.cnblogs.com/aaron-agu/p/8003831.html
user root root;
worker_processes  8;
worker_cpu_affinity 00000001 00000010 00000100 00001000 00010000 00100000 01000000 1000000; 

worker_processes 2;
worker_cpu_affinity 0101 1010;
0101也就是第1、3个逻辑CPU上，1010就是第2、4个逻辑CPU上。 

----------------------------------------------------
Nginx优化
https://juejin.im/post/5adb45e96fb9a07ab773c767
---------------------------优化----------------------------------------------
https://www.linuxidc.com/Linux/2016-09/134907.htm
---------------------------setup---------------------------------------------
1.yum install gcc-c++
2.yum install -y zlib zlib-devel
3.yum install -y openssl openssl-devel
4.wget -c https://nginx.org/download/nginx-1.14.1.tar.gz
5.tar -zxvf nginx-1.14.0.tar.gz
6.cd nginx-1.14.0
7.使用默认配置  ./configure
8.不推荐使用的配置./configure \--prefix=/usr/local/nginx \--conf-path=/usr/local/nginx/conf/nginx.conf \--pid-path=/usr/local/nginx/conf/nginx.pid \--lock-path=/var/lock/nginx.lock \--error-log-path=/var/log/nginx/error.log \--http-log-path=/var/log/nginx/access.log \--with-http_gzip_static_module \--http-client-body-temp-path=/var/temp/nginx/client \--http-proxy-temp-path=/var/temp/nginx/proxy \--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \--http-scgi-temp-path=/var/temp/nginx/scgi
9.make && make install
10.cd /usr/local/nginx/sbin/
	./nginx 
	./nginx -s stop
	./nginx -s quit
	./nginx -s reload
11.重新加载配置文件 ./nginx -s reload 
12.开机自启动
	vi /etc/rc.local增加/usr/local/nginx/sbin/nginx (chmod 755 rc.local)
用root用户进入....nginx/sbin
然后chown root nginx
chmod u+s nginx
然后通过普通用户就可以启动了。
增加普通用户修改配置文件权限。
cd /usr/local/nginx/conf/
chmod 666 nginx.conf
---------------Nginx-------------------------------------------------
启动：/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
重启：/usr/local/nginx/sbin/nginx -s  reload
停止：ps–ef | grepnginx（查看进程号）
kill -9 主进程号
kill -9 子进程号（可能有多个）
----Nginx增加SSL模块-----------------------------------------------------------
Nginx增加SSL模块
nginx: [emerg] unknown directive "ssl" in /usr/local/nginx/conf/nginx.conf:102 
到解压的nginx目录下
./configure --with-http_ssl_module
当执行上面语句，出现./configure: error: SSL modules require the OpenSSL library.
用 yum -y install openssl openssl-devel
再执行./configure
重新执行./configure --with-http_ssl_module
make ，切记不能make install 会覆盖。
把原来nginx备份
cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak
把新的nginx覆盖旧的
cp objs/nginx /usr/local/nginx/sbin/nginx
出现错误时cp: cannot create regular file ‘/usr/local/nginx/sbin/nginx’: Text file busy
用cp -rfp objs/nginx /usr/local/nginx/sbin/nginx解决
测试nginx是否正确
/usr/local/nginx/sbin/nginx -t
（nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful）

重启nginx
/usr/local/nginx/sbin/nginx -s reload
## https访问不通也有可能是433端口被禁##
--------------------------------------------------------------------------------------------------------
# /usr/local/nginx/sbin/nginx -s reload 时报invalid PID number报错
/usr/local/nginx/sbin/nginx -s reload
nginx: [error] invalid PID number "" in "/usr/local/nginx/logs/nginx.pid"

解决方法：
 /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf

使用nginx -c的参数指定nginx.conf文件的位置 
[root@localhost nginx]# cd logs/
[root@localhost logs]# ll
总用量 12
-rw-r--r-- 1 root root 1246 12月  9 18:10 access.log
-rw-r--r-- 1 root root  516 12月 10 15:39 error.log
-rw-r--r-- 1 root root    5 12月 10 15:38 nginx.pid

看nginx.pid文件已经有了。
-----------------------Nginx TCP----------------------------------
单次图片加载一半或几分之一
日志提示：
2019/01/24 10:36:02 [crit] 78585#0: *111515 open() "/usr/local/nginx/proxy_temp/9/80/0000000809" failed (13: Permission denied) while reading upstream, client: 218.17.179.250, server: bs.ifengstar.com, request: "GET /statics/js/echarts/echarts.js HTTP/1.1", upstream: "http://113.106.93.247:80/gps/statics/js/echarts/echarts.js", host: "bs.ifengstar.com", referrer: "http://bs.ifengstar.com/home.html"
2019/01/24 10:37:27 [notice] 78615#0: signal process started
问题指向proxy_temp权限问题
[root@r730-105 nginx]# chmode 777 -R proxy_temp/

-----------------------Nginx TCP----------------------------------
http://nginx.org/en/docs/stream/ngx_stream_core_module.html
安装：
（1）配置Nginx编译文件参数
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-stream
（2）编译、安装，make,make install .
（3）配置nginx.conf文件
stream {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  logs/access.log  main;

    upstream io {
        hash $remote_addr consistent;
        server 172.16.18.105:8102 weight=5 max_fails=1 fail_timeout=30s;
    }
 
    server {
        listen 8000;
        proxy_connect_timeout 10s;
        proxy_timeout 15s;
        proxy_pass io;
    }
 
}
-----------------------Nginx 安装参数----------------------------------
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-stream
make
