Nginx优化

https://juejin.im/post/5adb45e96fb9a07ab773c767

---------------------------优化----------------------------------------------------

https://www.linuxidc.com/Linux/2016-09/134907.htm

---------------------------setup---------------------------------------------

1.yum install gcc-c++

2.yum install -y zlib zlib-devel

3.yum install -y openssl openssl-devel

4.wget -c https://nginx.org/download/nginx-1.14.0.tar.gz

5.tar -zxvf nginx-1.14.0.tar.gz

6.cd nginx-1.14.0

7.使用默认配置  ./configure

8.不推荐使用的配置./configure \--prefix=/usr/local/nginx \--conf-path=/usr/local/nginx/conf/nginx.conf \--pid-path=/usr/local/nginx/conf/nginx.pid \--lock-path=/var/lock/nginx.lock \--error-log-path=/var/log/nginx/error.log \--http-log-path=/var/log/nginx/access.log \--with-http_gzip_static_module \--http-client-body-temp-path=/var/temp/nginx/client \--http-proxy-temp-path=/var/temp/nginx/proxy \--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \--http-scgi-temp-path=/var/temp/nginx/scgi

9.make && make install

10.cd /usr/local/nginx/sbin/

​	./nginx 

​	./nginx -s stop

​	./nginx -s quit

​	./nginx -s reload

11.重新加载配置文件 ./nginx -s reload 

12.开机自启动

​	vi /etc/rc.local增加/usr/local/nginx/sbin/nginx (chmod 755 rc.local)



---------------Nginx-------------------------------------------------
启动：/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
重启：/usr/local/nginx/sbin/nginx -s  reload
停止：ps–ef | grepnginx（查看进程号）
kill -9 主进程号
kill -9 子进程号（可能有多个）

--------------------------------------------------------------------------------

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