---------------MySQL-------------------------------------------------
#自动清理方法1：（修改配置文件和在mysql内设置参数可无需重启服务）
vim my.cnf
expire_logs_days = 7 // 表示日志保留7天，超过7天则设置为过期的 
# mysql -u root -p > show binary logs; > show variables like '%log%'; > set global expire_logs_days = 7;

#手动清理方法2：（推荐）
如果没有主从复制，可以通过下面的命令重置数据库日志，清除之前的日志文件：
reset master

但是如果存在复制关系，应当通过 PURGE 的名来清理 bin-log 日志，语法如下：

# mysql -u root -p
> purge master logs to 'mysql-bin.010’; //清除mysql-bin.010日志
> purge master logs before '2016-02-28 13:00:00'; //清除2016-02-28 13:00:00前的日志
> purge master logs before date_sub(now(), interval 3 day); //清除3天前的bin日志

注意，不要轻易手动去删除binlog，会导致binlog.index和真实存在的binlog不匹配，而导致expire_logs_day失效
	-------
#PURGE MASTER LOGS BEFORE DATE_SUB(CURRENT_DATE,INTERVAL 7 DAY);

show slave status
hknavi-relay-bin

---------------Nginx-------------------------------------------------
启动：/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
重启：/usr/local/nginx/sbin/nginx -s  reload
停止：ps–ef | grepnginx（查看进程号）
kill -9 主进程号
kill -9 子进程号（可能有多个）



----------------------------------------------------------------

安装mysql 5.7.11
 1.下载：wget http://cdn.mysql.com/Downloads/MySQL-5.7/mysql-server_5.7.11-1ubuntu12.04_amd64.deb-bundle.tar
 2.解压：tar -xvf mysql-server_5.7.11-1ubuntu12.04_amd64.deb-bundle.tar 
3.依次执行：
sudo apt-get install libaio1
sudo dpkg-preconfigure mysql-community-server_*.deb
sudo dpkg -i mysql-{common,community-client,client,community-server,server}_*.deb
这期间如果遇到任何依赖问题，请执行： 
sudo apt-get -f install
4.修改my.cnf 





Mysql登录：mysql -u root p

创建用户：CREATE USER 'gpsadmin'@'%' IDENTIFIED BY '1qaz&619';

默认配置文件路径：  

配置文件：/etc/my.cnf  

日志文件：/var/log/var/log/mysqld.log  

服务启动脚本：/usr/lib/systemd/system/mysqld.service  

socket文件：/var/run/mysqld/mysqld.pid 

