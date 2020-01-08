---------------MySQL-------------------------------------------------
#自动清理方法1：（修改配置文件和在mysql内设置参数可无需重启服务）
vim my.cnf
expire_logs_days = 7 // 表示日志保留7天，超过7天则设置为过期的 

mysql -u root -p > show binary logs; > show variables like '%log%'; > set global expire_logs_days = 7;

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
-----------删除已经安装的Mysql-------------------------------------


----------------------------------------------------------------

----------Centos安装Mysql启动多实例--------------------------------

安装mysql 5.7.24
 1.下载：wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
2.解压：tar -zxvf mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz -C /usr/local 
3. echo 'export PATH=$PATH:/usr/local/mysql/bin' >> /etc/profile
   source /etc/profile
3.1.为centos添加mysql用户组和mysql用户(-s /bin/false参数指定mysql用户仅拥有所有权，而没有登录权限):
groupadd mysql
useradd -r -g mysql -s /bin/false mysql
3.2.进入安装mysql软件的目录，命令如下：
3.3 cd /usr/local/mysql
3.4.修改当前目录拥有者为新建的mysql用户，命令如下：
chown -R mysql:mysql ./
3.5.安装mysql，命令如下：
./bin/mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --initialize
[root@r730-105 8610]# 
mysqld --initialize-insecure --user=mysql --basedir=/usr/local/mysql --datadir=/home/mysql/8610/mysql/
bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/home/mysql/8610/mysql/
bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/home/mysql/8610/mysql/
[root@r730-105 mysql]# ./support-fi
4.修改my.cnf 

vim /etc/my.conf
[client]
port=3306
socket=/tmp/mysql.sock

[mysqld_multi] 
mysqld = /usr/local/mysql/bin/mysqld_safe 
mysqladmin = /usr/local/mysql/bin/mysqladmin 
log = /home/mysql/mysqld_multi.log 

[mysqld] 
user=mysql 
basedir = /usr/local/mysql 
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES 

[mysqld3306] 
mysqld=mysqld 
mysqladmin=mysqladmin 
datadir=/home/mysql/mysql-3306/mysql 
port=3306 
server_id=3306 
socket=/tmp/mysql-3306.sock 
log-output=file 
slow_query_log = 1 
long_query_time = 1 
slow_query_log_file = /home/mysql/mysql-3306/log/slow.log 
log-error = /home/mysql/mysql-3306/log/error.log 
log-bin = /home/mysql/mysql-3306/log/mysql3306-bin
binlog-ignore-db = mysql 

[mysqld8610] 
mysqld=mysqld 
mysqladmin=mysqladmin
datadir=/home/mysql/mysql-8610/mysql 
port=8610 
server_id=8610 
socket=/tmp/mysql-8610.sock 
log-output=file 
slow_query_log = 1 
long_query_time = 1 
slow_query_log_file = /home/mysql/mysql-8610/log/slow.log 
log-error = /home/mysql/mysql-8610/log/error.log 
log-bin = /home/mysql/mysql-8610/log/mysql8610-bin

replicate-ignore-db=mysql
relay-log = slave-relay-bin
relay-log-index = slave-relay-bin.index
read_only

5.初始化数据库
cd /usr/local/mysql
[root@r430-102 mysql]# mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/home/mysql/mysql-3306/mysql/
2018-11-30T03:30:38.923963Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-11-30T03:30:38.924081Z 0 [Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
2018-11-30T03:30:38.924091Z 0 [Warning] 'NO_AUTO_CREATE_USER' sql mode was not set.
2018-11-30T03:30:39.869086Z 0 [Warning] InnoDB: New log files created, LSN=45790
2018-11-30T03:30:40.149698Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2018-11-30T03:30:40.287949Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 4f7bdf5d-f450-11e8-a1dc-44a8422ba11a.
2018-11-30T03:30:40.310881Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-11-30T03:30:40.311744Z 1 [Note] A temporary password is generated for root@localhost: B4pt)XjlHTv4
[root@r430-102 mysql]# bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/home/mysql/mysql-8610/mysql/
2018-11-30T03:32:07.319996Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-11-30T03:32:07.320123Z 0 [Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
2018-11-30T03:32:07.320133Z 0 [Warning] 'NO_AUTO_CREATE_USER' sql mode was not set.
2018-11-30T03:32:08.230757Z 0 [Warning] InnoDB: New log files created, LSN=45790
2018-11-30T03:32:08.502609Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2018-11-30T03:32:08.657999Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 84281278-f450-11e8-a949-44a8422ba11a.
2018-11-30T03:32:08.689189Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-11-30T03:32:08.690166Z 1 [Note] A temporary password is generated for root@localhost: nbOnR/qv=6Uw
2019-01-23T07:49:55.564828Z 1 [Note] A temporary password is generated for root@localhost: x-oqrmdty5wX
如果没有看到随机密码，请查看是否在日志文件里面

6.启动集群
/usr/local/mysql/bin/mysqld_multi start

7.查看集群
[root@r430-102 mysql]# mysqld_multi report
/usr/local/mysql/bin/mysqld_multi report
Reporting MySQL servers
MySQL server from group: mysqld3306 is running
MySQL server from group: mysqld8610 is running
[root@r430-102 mysql]# ss -tupln | grep mysqld
tcp    LISTEN     0      80       :::8610                 :::*                   users:(("mysqld",pid=93909,fd=23))
tcp    LISTEN     0      80       :::3306                 :::*                   users:(("mysqld",pid=93906,fd=23))

启动单个实例：
/usr/local/mysql/bin/mysqld_multi start 3306
mysqld_multi start 3619
停止单实例：
/usr/local/mysql/bin/mysqld_multi stop 3306
mysql_multi stop 3306 -password=root
mysql_multi stop 3619 -password=root
  or
mysqladmin -S /tmp/mysql-3306.sock -u root -p shutdown
mysqladmin -S /home/mysql/3619/mysql-3619.sock -u gpsadmin -p shutdown

1qaz&619   fhxt&clw715#

查看单实例：
  /usr/local/mysql/bin/mysqld_multi report 3306
查看监听端口：
  ss -tulpn | grep mysqld
修改密码：
 mysql -S /tmp/mysql-3306.sock -p
 set password=password('123456');

 set password=password('fhxt&clw715#');
使修改生效；或者重启服务
 flush privileges;
设置远程连接mysql:
  GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
  FLUSH PRIVILEGES;
  其中，root是用户名，%表示所有人都可以访问；password是密码，尽量不用使用root，安全很重要。
  

访问单个Mysql
mysql -S /home/mysql/3619/mysql-3619.sock -p
fhxt&clw715#

----error:1267-Illegal mix of collations (gbk_chinese_ci,IMPLICIT) and (latin1_swedish_ci,IMPLICIT) for operation '='  -------------------
mysql> SHOW VARIABLES LIKE 'collation_%'; 
+----------------------+-------------------+
| Variable_name        | Value             |
+----------------------+-------------------+
| collation_connection | utf8_general_ci   |
| collation_database   | latin1_swedish_ci |
| collation_server     | latin1_swedish_ci |
+----------------------+-------------------+
[client]
default-character-set=gbk
[mysql]
default-character-set=gbk

mysql> SHOW VARIABLES LIKE 'collation_%'; 
+----------------------+----------------+
| Variable_name        | Value          |
+----------------------+----------------+
| collation_connection | gbk_chinese_ci |
| collation_database   | gbk_chinese_ci |
| collation_server     | gbk_chinese_ci |
+----------------------+----------------+
3 rows in set (0.00 sec)
!!!!!如果还是没有解决这个问题则需要通过Navicat for MySQL 将原数据库和现在数据库的
连接设置为自动(实际看到是UTF8)，1.删除所有存储过程，重新进行复制 
2.检查对应数据表，如果出现乱码，也需要做删除重新复制操作。

----------------mysql 1055 sql_mode=only_full_group_by--------------------

->mysql> select @@GLOBAL.sql_mode;

->set sql_mode=(select replace(@@GLOBAL.sql_mode,'ONLY_FULL_GROUP_BY',''));

----------------------------------------------------------------------------------------------------------------------------

```
wget http://repo.mysql.com/mysql57-community-release-el7-10.noarch.rpm
```

https://www.cnblogs.com/silentdoer/articles/7258232.html

-------------MySql查看集群--------------------------------------------
查询数据库中的存储过程
方法一：select `name` from mysql.proc where db = 'your_db_name' and `type` = 'PROCEDURE'
方法二：show procedure status;
查看存储过程或函数的创建代码
show create procedure proc_name;
show create function func_name;
------------------------------------------------------------------------------

mysql主从
https://www.cnblogs.com/hankguo/p/8204868.html
-------------------------------------------------------------------------------------------------------------------------------------------
Mysql登录：mysql -u root -p
创建用户：CREATE USER 'gpsadmin'@'%' IDENTIFIED BY '1qaz&619';
默认配置文件路径：  
配置文件：/etc/my.cnf  
日志文件：/var/log/var/log/mysqld.log  
服务启动脚本：/usr/lib/systemd/system/mysqld.service  
socket文件：/var/run/mysqld/mysqld.pid 
->mysql> select @@GLOBAL.sql_mode;
->set sql_mode=(select replace(@@GLOBAL.sql_mode,'ONLY_FULL_GROUP_BY',''));
　　通过root用户登录之后创建
　　>> grant all privileges on *.* to testuser@localhost identified by "123456" ;　　//　
　　>> grant all privileges on *.* to gpsadmin@localhost identified by "1qaz&619" ;
　创建新用户，用户名为testuser，密码为123456 ；
　　>> grant all privileges on *.* to testuser@localhost identified by "123456" ;　　//　　设置用户testuser，可以在本地访问mysql
　　>> grant all privileges on *.* to testuser@"%" identified by "123456" ;　　　//　　设置用户testuser，可以在远程访问mysql
grant all privileges on *.* to gpsadmin@"%" identified by "1qaz&619" ;
　　>> flush privileges ;　　//　　mysql 新设置用户或更改密码后需用flush privileges刷新MySQL的系统权限相关表，否则会出现拒绝访问，还有一种方法，就是重新启动mysql服务器，来使新设置生效

　　2、设置用户访问数据库权限
　　>> grant all privileges on test_db.* to testuser@localhost identified by "123456" ;　　//　　设置用户testuser，只能访问数据库test_db，其他数据库均不能访问 ；
　　>> grant all privileges on *.* to testuser@localhost identified by "123456" ;　　//　　设置用户testuser，可以访问mysql上的所有数据库 ；
　　>> grant all privileges on test_db.user_infor to testuser@localhost identified by "123456" ;　　//　　设置用户testuser，只能访问数据库test_db的表user_infor，数据库中的其他表均不能访问 ；

　　3、设置用户操作权限
　　>> grant all privileges on *.* to testuser@localhost identified by "123456" WITH GRANT OPTION ;　　//设置用户testuser，拥有所有的操作权限，也就是管理员 ；
　　>> grant select on *.* to testuser@localhost identified by "123456" WITH GRANT OPTION ;　　//设置用户testuser，只拥有【查询】操作权限 ；
　　>> grant select,insert on *.* to testuser@localhost identified by "123456"  ;　　//设置用户testuser，只拥有【查询\插入】操作权限 ；
　　>> grant select,insert,update,delete on *.* to testuser@localhost identified by "123456"  ;　　//设置用户testuser，只拥有【查询\插入】操作权限 ；
　　>> REVOKE select,insert ON what FROM testuser　　//取消用户testuser的【查询\插入】操作权限 ；
　　4、设置用户远程访问权限
　　>> grant all privileges on *.* to testuser@“192.168.1.100” identified by "123456" ;　　//设置用户testuser，只能在客户端IP为192.168.1.100上才能远程访问mysql ；

　　5、关于root用户的访问设置

　　设置所有用户可以远程访问mysql，修改my.cnf配置文件，将bind-address = 127.0.0.1前面加“#”注释掉，这样就可以允许其他机器远程访问本机mysql了；
　　>> grant all privileges on *.* to root@"%" identified by "123456" ;　　　//　　设置用户root，可以在远程访问mysql
　　>> select host,user from user; 　　//查询mysql中所有用户权限
　　关闭root用户远程访问权限
　　>> delete from user where user="root" and host="%" ;　　//禁止root用户在远程机器上访问mysql
　　>> flush privileges ;　　//修改权限之后，刷新MySQL的系统权限相关表方可生效　

-------------------------------------------------------------------------------

4.移动/复制之前存放数据库目录文件，到新的数据库存放目录位置
​    cp -R /var/lib/mysql/* /data/mysql/    #或mv /var/lib/mysql/* /data/mysql

​5.修改mysql数据库目录权限以及配置文件
​    chown mysql:mysql -R /data/mysql/
​    vim /etc/my.cnf
​    port=3366
​    datadir=/data/mysql （制定为新的数据存放目录）
​    socket=/data/mysql.sock
​    log-error=/data/mysqld.log

​    vim /etc/init.d/mysqld
​    datadir=/data/mysql

-----------------------------------------------------


[mysqld_multi] 
mysqld = /usr/local/mysql/bin/mysqld_safe 
mysqladmin = /usr/local/mysql/bin/mysqladmin 
log = /home/mysql/mysqld_multi.log 

[mysqld] 
user=mysql 
basedir = /usr/local/mysql 
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES 

[mysqld3619] 
mysqld=mysqld 
mysqladmin=mysqladmin 
datadir=/home/mysql/3619/mysql 
port=3619 
server_id=3619 
socket=/tmp/mysql-3619.sock 
log-output=file 
slow_query_log = 1 
long_query_time = 10 
slow_query_log_file = /home/mysql/3619/log/slow.log 
log-error = /home/mysql/3619/log/error.log 
log-bin = /home/mysql/3619/log/mysql3619-bin
binlog-ignore-db = mysql 

[mysqld8610] 
mysqld=mysqld 
mysqladmin=mysqladmin
datadir=/home/mysql/8610/mysql 
port=8610 
server_id=8610 
socket=/tmp/mysql-8610.sock 
log-output=file 
slow_query_log = 1 
long_query_time = 10 
slow_query_log_file = /home/mysql/8610/log/slow.log 
log-error = /home/mysql/8610/log/error.log 
log-bin = /home/mysql/8610/log/mysql8610-bin



[mysqld3306] 
mysqld=mysqld 
mysqladmin=mysqladmin 
datadir=/home/mysql/3306/mysql 
port=3306 
server_id=3306 
socket=/tmp/mysql-3306.sock 
log-output=file 
slow_query_log = 1 
long_query_time = 10 
slow_query_log_file = /home/mysql/3306/log/slow.log 
log-error = /home/mysql/3306/log/error.log 
log-bin = /home/mysql/3306/log/mysql3306-bin

replicate-ignore-db=mysql
relay-log = slave-relay-bin
relay-log-index = slave-relay-bin.index
read_only

------------单机版---------------------------


#-------my.cnf-----------#
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
#datadir=/var/lib/mysql
user=mysql
datadir=/mnt/data
port=3366
#socket=/var/lib/mysql/mysql.sock
socket=/mnt/data/mysql.sock

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

#log-error=/var/log/mysqld.log
log-error=/mnt/data/mysqld.log

sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION 

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/mnt/data/mysql.sock
#--------end my.cnf----------------#


bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/home/mysql/8610/mysql/
mysql -u root -p

select host,user from user; 　　//查询mysql中所有用户权限
grant all privileges on *.* to gpsadmin@localhost identified by "1qaz&619" ;
grant all privileges on *.* to gpsadmin@"%" identified by "1qaz&619" ;

10.开启mysql服务，命令如下：
./support-files/mysql.server start
11.将mysql进程放入系统进程中，命令如下：
cp support-files/mysql.server /etc/init.d/mysqld
12.重新启动mysql服务，命令如下：
service mysqld restart
13.使用随机密码登录mysql数据库，命令如下：
mysql -u root -p
等待系统提示，输入随机密码，即可登录
14.进入mysql操作行，为root用户设置新密码（小编设为rootroot）：
alter user 'root'@'localhost' identified by 'rootroot';
alter user 'root'@'localhost' identified by 'fhxt&clw715#';
15.设置允许远程连接数据库，命令如下：
update user set user.Host='%' where user.User='root';
16.刷新权限，命令如下：
flush privileges;
---------------------------------------------
[root@r730-105 mysql]# service mysqld restart
 ERROR! MySQL server PID file could not be found!
Starting MySQL.2019-01-23T09:40:38.182471Z mysqld_safe error: log-error set to '/home/mysql/8610/log/mysqld.log', however file don't exists. Create writable for user 'mysql'.
 ERROR! The server quit without updating PID file (/home/mysql/8610/mysqld.pid).


[root@r730-105 8610]# touch mysqld.pid
[root@r730-105 8610]# chmod 777 mysql.pid
[root@r730-105 8610]# chmod 777 log
[root@r730-105 log]# touch mysqld.log
[root@r730-105 log]# chmod 777 mysqld.log

[Err] 1067 - Invalid default value for 'gpstime'
将数据文件中的0000-00-00 00:00:00 替换为 2018-01-01 00:00:01

firewall-cmd --zone=public --add-port=8610/tcp --permanent
firewall-cmd --reload

firewall-cmd --zone=public --add-port=443/tcp --permanent && firewall-cmd --reload
---------------------------------------
查看mysql最大连接数：
show full processlist; 

set global max_connections=1000 ##重新设置
show variables like '%max_connections%'; ##查询数据库当前设置的最大连接数
