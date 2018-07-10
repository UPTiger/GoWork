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



----------------------------------------------------------------------------------------------------------------------------



```
wget http://repo.mysql.com/mysql57-community-release-el7-10.noarch.rpm
```

https://www.cnblogs.com/silentdoer/articles/7258232.html

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



------------------------------------------------------------------------------------------

1、创建新用户

　　通过root用户登录之后创建

　　>> grant all privileges on *.* to testuser@localhost identified by "123456" ;　　//　　创建新用户，用户名为testuser，密码为123456 ；

　　>> grant all privileges on *.* to testuser@localhost identified by "123456" ;　　//　　设置用户testuser，可以在本地访问mysql

　　>> grant all privileges on *.* to testuser@"%" identified by "123456" ;　　　//　　设置用户testuser，可以在远程访问mysql

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

​    5.修改mysql数据库目录权限以及配置文件

​    chown mysql:mysql -R /data/mysql/

​    vim /etc/my.cnf

​    port=3366

​    datadir=/data/mysql （制定为新的数据存放目录）

​    socket=/data/mysql.sock

​    log-error=/data/mysqld.log

​    vim /etc/init.d/mysqld

​    datadir=/data/mysql

-----------------------------------------------------