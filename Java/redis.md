 find /(路径) -name *ssl*.h

redis-cli keys "GT10_123456*" | xargs redis-cli del
如果不行则先执行：
redis-cli keys "GT10_123456*" | xargs
http://download.redis.io/releases/redis-3.2.9.tar.gz
service reidsd start
设置Bind值以那个IP对外提供服务


-------------Redis集群20181116--------------------------------------------------------------------
一、下载Redis
可以只下载解压后进行make不执行make install。这样拷贝redis的可执行文件即可。

cd /usr/local/src && wget http://download.redis.io/releases/redis-4.0.11.tar.gz
cd /usr/local && mkdir redis4
#cd /usr/local/src && tar -zxvf redis-4.0.11.tar.gz -C /usr/local/redis4
cd /usr/local/src && tar -zxvf redis-4.0.11.tar.gz
#make 
make PREFIX=/usr/local/redis4/ install
cd /usr/local/redis4
二、进行测试
yum install gcc tcl
make test

三、安装系统自带Ruby，一般来说般本比较低。
yum -y install ruby ruby-devel rubygems rpm-build
ruby -v低于2.2是不满足使用的。

gem install redis
提出Ruby版本不够，升级Ruby,Ruby gem依赖zlib 和 OPENSSL最好保证有
yum -y install wget
wget http://www.zlib.net/zlib-1.2.11.tar.gz
tar zxf zlib-1.2.11.tar.gz
./configure    --prefix=/usr/local/zlib
make
make install 
更新动态链接库数据
echo "/usr/local/zlib/lib" >> /etc/ld.so.conf 
ldconfig -v

安装zlib-devel库
yum install zlib-devel       

安装OPENSSL
wget https://www.openssl.org/source/openssl-1.0.2q.tar.gz
tar -zxf openssl-1.0.2.q.tar.gz
cd openssl-1.0.2q
./config -fPIC --prefix=/usr/local/openssl enable-shared
./config -t
make && make install

[root@r730-104 ruby-2.5.3]# ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl
[root@r730-104 ruby-2.5.3]# ln -s /usr/local/ssl/include/openssl /usr/include/openssl
[root@r730-104 ruby-2.5.3]# echo "/usr/local/openssl/lib" >> /etc/ld.so.conf
[root@r730-104 ruby-2.5.3]# openssl version
OpenSSL 1.0.2q  20 Nov 2018
[root@r730-104 ruby-2.5.3]# ldconfig -v
[root@r730-104 ruby-2.5.3]# openssl version

----------------------------------------------------------------
#升级所有包，改变软件设置和系统设置,系统版本内核都升级
yum -y update 
#升级所有包，不改变软件设置和系统设置，系统版本升级，内核不改变
yum -y upgrade
1.安装开发工具集：yum groupinstall "Development Tools"
#卸载ruby2.0.0
$ yum remove ruby
rpm -qa | grep ruby
yum erase ruby

2.下载编译安装Ruby
wget https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.3.tar.gz
因为用wget下载ruby-2.5.3.tar.gz太慢了。所以用迅雷下载好，直接拷贝到centos的/tmp目录下
2.1解压：
tar -zxvf ruby-2.5.3.tar.gz
$ mkdir -p /usr/local/ruby
2.2编译安装
./configure --prefix=/usr/local/ruby
make && make install
默认情况下，Ruby安装到/usr/local目录。如果想使用其它目录，可以把--prefix=DIR选项给./configure脚本
##[root@r730-105 bin]# ln -s /usr/local/ruby/bin/ruby /usr/bin/ruby
##[root@r730-105 bin]# ln -s /usr/local/ruby/bin/gem /usr/bin/gem

这里选择了直接将ruby加入到环境变量
vim /etc/profile
RUBY_HOME=/usr/local/ruby
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$RUBY_HOME/bin
source /etc/profile

2.3查看版本
ruby -v
如果卸载：在安装目录下(/tmp/ruby-2.5.3)make uninstall

安装自带的openssl
cd /home/deve/soft/ruby-2.3.0/ext/openssl
ruby extconf.rb --with-openssl-include=/usr/local/openssl/include/ --with-openssl-lib=/usr/local/openssl/lib
ln -s /usr/local/src/ruby-2.2.3/include /
make 
make install 

//yum install -y rubygems
----------------------------------------------------------------
四、安装Redis插件

gem install redis

ERROR:  Loading command: install (LoadError)
    no such file to load -- zlib
ERROR:  While executing gem ... (NameError)
    uninitialized constant Gem::Commands::InstallCommand
进入ruby源码文件夹安装ruby自身提供的zlib包
#cd ext/zlib
#ruby ./extconf.rb
#make
#make install
make: *** No rule to make target `/include/ruby.h', needed by `zlib.o'. Stop. 
vim /tools/ruby-2.5.1/ext/zlib/Makefile
:/ruby.h
#zlib.o: $(top_srcdir)/include/ruby.h #把这一行替换成下面一行
zlib.o: ../../include/ruby.h

[root@r730-104 openssl]# gem install redis
ERROR:  While executing gem ... (Gem::Exception)
Unable to require openssl, install OpenSSL and rebuild Ruby (preferred) or use non-HTTPS sources
[root@r730-104 openssl]# make
compiling openssl_missing.c
make: *** No rule to make target `/include/ruby.h', needed by `ossl.o'.  Stop.

***提示找不到/include/ruby.h 需要将ruby目录下的include做软连接到/***
如果出现openssl错误 ，先查看 openssl version
如果没有安装则下载进行安装；如果已经是安装的，那么可能是Ruby安装不正确

gem install redis 成功后开始配置Redis单机文件
五、配置Redis
新建6个文件夹用来存放每一个节点数据。为了方便移植将Redis可执行文件拷贝到每一个文件夹。
----redis cluster------------------------------------------------------------------
daemonize    yes                          //redis后台运行
pidfile  /var/run/redis_2003.pid          //pidfile文件对应7000,7002,7003
port  2003                                //端口7000,7002,7003
cluster-enabled  yes                      //开启集群  把注释#去掉
cluster-config-file  nodes_2003.conf      //集群的配置  配置文件首次启动自动生成 7000,7001,7002
cluster-node-timeout  5000                //请求超时  设置5秒够了
一般来说不需要开启appendonly aof日志文件太大，GPS应用对Redis数据，并没有非常高要求
appendonly  yes                           //aof日志开启  有需要就开启，它会每次写操作都记录一条日志

dir /home/developer/redis-cluster/2003/
logfile "/home/developer/redis-cluster/2003/redis-2003.log"
-----------------------------------------------------------------------------------
cp -r 2001/ 2000
建立脚本依次启动：
redis-cluster/ vim start-all.sh

cd 2000
./redis-server redis.conf
cd ../2001
./redis-server redis.conf
cd ../2002
./redis-server redis.conf
cd ../2003
./redis-server redis.conf
cd ../2004
./redis-server redis.conf
cd ../2005
./redis-server redis.conf
~
chmod 777 start-all.sh
./start-all.sh
ps -ef | grep redis
观察子目录下的redis-XXXX.log

关闭脚本：stop-all.sh
2000/redis-cli -p 2000 shutdown
2001/redis-cli -p 2001 shutdown
2002/redis-cli -p 2002 shutdown
2003/redis-cli -p 2003 shutdown
2004/redis-cli -p 2004 shutdown
2005/redis-cli -p 2005 shutdown

六、创建集群
./redis-trib.rb  create --replicas  1  127.0.0.1:2000 127.0.0.1:2001  127.0.0.1:2002  127.0.0.1:2003  127.0.0.1:2004  127.0.0.1:2005
[developer@r730-104 redis-cluster]$ ./redis-trib.rb  create --replicas  1  172.16.18.104:2000 172.16.18.104:2001  172.16.18.104:2002  172.16.18.104:2003  172.16.18.104:2004  172.16.18.104:2005
>>> Creating cluster
>>> Performing hash slots allocation on 6 nodes...
Using 3 masters:
127.0.0.1:2000
127.0.0.1:2001
127.0.0.1:2002
Adding replica 127.0.0.1:2004 to 127.0.0.1:2000
Adding replica 127.0.0.1:2005 to 127.0.0.1:2001
Adding replica 127.0.0.1:2003 to 127.0.0.1:2002
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: d268ffb7f284ff22ad3d9d9877d89489f4f082a4 127.0.0.1:2000
   slots:0-5460 (5461 slots) master
M: c576b1175b4035787cd087e8efa2ec8c33fe3352 127.0.0.1:2001
   slots:5461-10922 (5462 slots) master
M: 9a0ef38514b5e9114687501e3fe84f7ac183c021 127.0.0.1:2002
   slots:10923-16383 (5461 slots) master
S: 4bbee759b49d68ce7dc104a61bdbb05776c718b2 127.0.0.1:2003
   replicates d268ffb7f284ff22ad3d9d9877d89489f4f082a4
S: a7fa12ff5dbeb8cabedfda8621b3a4708e119557 127.0.0.1:2004
   replicates c576b1175b4035787cd087e8efa2ec8c33fe3352
S: 38300c40240cd80e9eb5e2f1d6cc556a6722f324 127.0.0.1:2005
   replicates 9a0ef38514b5e9114687501e3fe84f7ac183c021
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join....
>>> Performing Cluster Check (using node 127.0.0.1:2000)
M: d268ffb7f284ff22ad3d9d9877d89489f4f082a4 127.0.0.1:2000
   slots:0-5460 (5461 slots) master
   1 additional replica(s)
S: a7fa12ff5dbeb8cabedfda8621b3a4708e119557 127.0.0.1:2004
   slots: (0 slots) slave
   replicates c576b1175b4035787cd087e8efa2ec8c33fe3352
S: 38300c40240cd80e9eb5e2f1d6cc556a6722f324 127.0.0.1:2005
   slots: (0 slots) slave
   replicates 9a0ef38514b5e9114687501e3fe84f7ac183c021
S: 4bbee759b49d68ce7dc104a61bdbb05776c718b2 127.0.0.1:2003
   slots: (0 slots) slave
   replicates d268ffb7f284ff22ad3d9d9877d89489f4f082a4
M: c576b1175b4035787cd087e8efa2ec8c33fe3352 127.0.0.1:2001
   slots:5461-10922 (5462 slots) master
   1 additional replica(s)
M: 9a0ef38514b5e9114687501e3fe84f7ac183c021 127.0.0.1:2002
   slots:10923-16383 (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
[developer@r730-104 redis-cluster]$ 

七、给集群设置密码
1、密码设置(推荐)
方式一：修改所有Redis集群中的redis.conf文件加入： 
masterauth hkredispassword1test 
requirepass hkredispassword1test 
说明：这种方式需要重新启动各节点

方式二：进入各个实例进行设置：
./redis-cli -c -p 7000 
config set masterauth hkredispassword1test 
config set requirepass hkredispassword1test 
config rewrite 
注意：client.rb路径可以通过find命令查找：find / -name 'client.rb'

2、设置密码之后如果需要使用redis-trib.rb的各种命令 
如：./redis-trib.rb check 127.0.0.1:2000，则会报错ERR] Sorry, can’t connect to node 127.0.0.1:2000 
解决办法：/usr/local/ruby/lib/ruby/gems/2.5.0/gems/redis-4.0.3/lib/redis/client.rb，然后修改passord
find / -name client.rb
vim /usr/local/ruby/lib/ruby/gems/2.5.0/gems/redis-4.0.3/lib/redis/client.rb
class Client
    DEFAULTS = {
      :url => lambda { ENV["REDIS_URL"] },
      :scheme => "redis",
      :host => "127.0.0.1",
      :port => 6379,
      :path => nil,
      :timeout => 5.0,
      :password => "hkredispassword1test",
      :db => 0,
      :driver => nil,
      :id => nil,
      :tcp_keepalive => 0,
      :reconnect_attempts => 1,
      :inherit_socket => false
    }
带密码访问集群
./redis-cli -h 127.0.0.1 -c -p 2000 
auth hkredispassword1test

./redis-cli -h 127.0.0.1 -c -p 2000 -a hkredispassword1test

./redis-cli -h 172.16.18.104 -c -p 2000 
auth hkredispassword1test
去除密码后出现：
[developer@r730-104 redis-cluster]$ ./redis-trib.rb create --replicas 1 172.16.18.104:2000 172.16.18.104:2001 172.16.18.104:2002 172.16.18.104:2003 172.16.18.104:2004 172.16.18.104:2005
>>> Creating cluster
[ERR] Sorry, can't connect to node 172.16.18.104:2000
vim /usr/local/ruby/lib/ruby/gems/2.5.0/gems/redis-4.0.3/lib/redis/client.rb
class Client
    DEFAULTS = {
      :url => lambda { ENV["REDIS_URL"] },
      :scheme => "redis",
      :host => "127.16.18.104",
      :port => 6379,
      :path => nil,
      :timeout => 5.0,
      :password => nil,
      #:password => "hkredispassword1test",
      :db => 0,
      :driver => nil,
      :id => nil,
      :tcp_keepalive => 0,
      :reconnect_attempts => 1,
      :inherit_socket => false
    }
屏蔽password

**https://www.imooc.com/article/44472
https://www.cnblogs.com/huangxincheng/p/5615037.html
http://www.cnblogs.com/zhoujie/p/redis3.html
https://www.cnblogs.com/lihaoyang/p/6906444.html

redis状态监控可视化工具RedisLive使用
https://www.cnblogs.com/dyfblog/p/8329918.html

./redis-cli -h 127.0.0.1 -c -p 2000

127.0.0.1:2000> cluster nodes
d268ffb7f284ff22ad3d9d9877d89489f4f082a4 127.0.0.1:2000@12000 myself,master - 0 1542696402000 1 connected 0-5460
38300c40240cd80e9eb5e2f1d6cc556a6722f324 127.0.0.1:2005@12005 slave 9a0ef38514b5e9114687501e3fe84f7ac183c021 0 1542696402483 6 connected
a7fa12ff5dbeb8cabedfda8621b3a4708e119557 127.0.0.1:2004@12004 slave c576b1175b4035787cd087e8efa2ec8c33fe3352 0 1542696401481 5 connected
c576b1175b4035787cd087e8efa2ec8c33fe3352 127.0.0.1:2001@12001 master - 0 1542696403085 2 connected 5461-10922
9a0ef38514b5e9114687501e3fe84f7ac183c021 127.0.0.1:2002@12002 master - 0 1542696401000 3 connected 10923-16383
4bbee759b49d68ce7dc104a61bdbb05776c718b2 127.0.0.1:2003@12003 slave d268ffb7f284ff22ad3d9d9877d89489f4f082a4 0 1542696401000 4 connected
127.0.0.1:2000> 


-----------------------------------------------------------------------------------
# 安装redis

下载地址：<http://redis.io/download>
1、将下载好的redis复制到：/opt/software/redis-4.0.11.tar.gz
2、在/opt/software/目录下执行命令：tar -zxvf redis-4.0.11.tar.gz -C /opt/local/
3、进入/opt/local/redis-4.0.9/目录：cd /opt/local/redis-4.0.11
4、执行make install命令；
以上步骤可以将redis安装完成。
# 将redis设置成开机启动
1、进入redis目录：/opt/local/redis-4.0.11
2、执行命令：./utils/install_server.sh 
3、一直默认即可：

[root@localhost redis-4.0.11]# ./utils/install_server.sh 
Welcome to the redis service installer
This script will help you easily set up a running redis server

Please select the redis port for this instance: [6379] 
Selecting default: 6379
Please select the redis config file name [/etc/redis/6379.conf] 
Selected default - /etc/redis/6379.conf
Please select the redis log file name [/var/log/redis_6379.log] 
Selected default - /var/log/redis_6379.log
Please select the data directory for this instance [/var/lib/redis/6379] 
Selected default - /var/lib/redis/6379
Please select the redis executable path [/usr/local/bin/redis-server] 
Selected config:
Port           : 6379
Config file    : /etc/redis/6379.conf
Log file       : /var/log/redis_6379.log
Data dir       : /var/lib/redis/6379
Executable     : /usr/local/bin/redis-server
Cli Executable : /usr/local/bin/redis-cli
Is this ok? Then press ENTER to go on or Ctrl-C to abort.
Copied /tmp/6379.conf => /etc/init.d/redis_6379
Installing service...
Successfully added to chkconfig!
Successfully added to runlevels 345!
Starting Redis server...
Installation successful!
```

以上操作可以将redis设置为开机启动。

PS：通过这种方式这是redis开机启动，redis的配置文件路径：/etc/redis/6379.conf
​        所以如果修改redis的配置文件，修改路径应该是：/etc/redis/6379.conf
​        redis安装目录下的配置文件/opt/local/redis-4.0.9/redis.conf修改会不生效；
​        如果想要使用/opt/local/redis-4.0.9/redis.conf，修改/etc/init.d/redis_6379文件即可。

------------------------------------------------------------------------------------------------------------------------------------

redis 学院
http://www.runoob.com/redis/redis-install.html
---------------------------------------------------------
https://blog.csdn.net/liulihui1988/article/details/78087495?utm_source=debugrun&utm_medium=referral

亲测可以执行：https://www.cnblogs.com/onephp/p/6245902.html

redis 下载 https://redis.io/download

wget http://download.redis.io/releases/redis-3.2.6.tar.gz```
解压缩```
tar xzf redis-3.2.6.tar.gz```
进入解压后的文件目录```
cd redis-3.2.6```
redis安装相对简单 直接编译即可```
make
创建存储redis文件目录```
mkdir -p /usr/local/redis
复制src目录下的redis-server redis-cli到新建立的文件夹```
cd src
cp redis-server /usr/local/redis/
cp redis-cli /usr/local/redis/
复制redis的配置文件
cd ..
cp redis.conf /usr/local/redis/
编辑配置文件
cd /usr/local/redis/
vim redis.conf
![img](https://images2015.cnblogs.com/blog/931262/201701/931262-20170103182144284-577583439.png)改为yes 后台运行 
添加开机启动服务
vim /etc/systemd/system/redis-server.service
[Unit]
Description=The redis-server Process Manager
After=syslog.target network.target
[Service]
Type=simple
PIDFile=/var/run/redis_6379.pid
ExecStart=/usr/local/redis/redis-server /usr/local/redis/redis.conf         
ExecReload=/bin/kill -USR2 $MAINPID
ExecStop=/bin/kill -SIGINT $MAINPID
[Install]
WantedBy=multi-user.target
 设置开机启动
systemctl daemon-reload 
systemctl start redis-server.service 
systemctl enable redis-server.service
检查是否安装成功
![img](https://images2015.cnblogs.com/blog/931262/201701/931262-20170103182358378-909440994.png)
创建redis命令软连接
ln -s /usr/local/redis/redis-cli /usr/bin/redis
测试redis
![img](https://images2015.cnblogs.com/blog/931262/201701/931262-20170103182546597-499368577.png)
2.第二种方式 （永久方式）
需要永久配置密码的话就去redis.conf的配置文件中找到requirepass这个参数，如下配置：
修改redis.conf配置文件　　
\# requirepass foobared
requirepass 123   指定密码123
保存后重启redis就可以了
---------------------------------------------------------------------------------------------------------
Redis的配置
　　daemonize：如需要在后台运行，把该项的值改为yes
　　pdifile：把pid文件放在/var/run/redis.pid，可以配置到其他地址
　　**bind**：指定redis只接收来自该IP的请求，如果不设置，那么将处理所有请求，在生产环节中最好设置该项
　　port：监听端口，默认为6379
　　timeout：设置客户端连接时的超时时间，单位为秒
　　loglevel：等级分为4级，debug，revbose，notice和warning。生产环境下一般开启notice
　　logfile：配置log文件地址，默认使用标准输出，即打印在命令行终端的端口上
　　database：设置数据库的个数，默认使用的数据库是0
　　save：设置redis进行数据库镜像的频率
　　rdbcompression：在进行镜像备份时，是否进行压缩
　　dbfilename：镜像备份文件的文件名
　　dir：数据库镜像备份的文件放置的路径
　　slaveof：设置该数据库为其他数据库的从数据库
　　masterauth：当主数据库连接需要密码验证时，在这里设定
　　requirepass：设置客户端连接后进行任何其他指定前需要使用的密码
　　maxclients：限制同时连接的客户端数量
　　maxmemory：设置redis能够使用的最大内存
　　appendonly：开启appendonly模式后，redis会把每一次所接收到的写操作都追加到appendonly.aof文件中，当redis重新启动时，会从该文件恢复出之前的状态
　　appendfsync：设置appendonly.aof文件进行同步的频率
　　vm_enabled：是否开启虚拟内存支持
　　vm_swap_file：设置虚拟内存的交换文件的路径
　　vm_max_momery：设置开启虚拟内存后，redis将使用的最大物理内存的大小，默认为0
　　vm_page_size：设置虚拟内存页的大小
　　vm_pages：设置交换文件的总的page数量
##### 　　vm_max_thrrads：设置vm IO同时使用的线程数量
wget http://download.redis.io/releases/redis-3.2.9.tar.gz
tar -zxvf redis-3.0.7.tar.gz
编译：
make  
运行：
/src/redis-server
运行成功如下图：
将redis做成一个服务：

4. Redis的配置
4.1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
    daemonize no
4.2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
    pidfile /var/run/redis.pid
4.3. 指定Redis监听端口，默认端口为6379，作者在自己的一篇博文中解释了为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
    port 6379
4.4. 绑定的主机地址
    bind 127.0.0.1
4.5.当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
    timeout 300
4.6. 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
    loglevel verbose
4.7. 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
    logfile stdout
4.8. 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
    databases 16
4.9. 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
    save <seconds> <changes>
    Redis默认配置文件中提供了三个条件：
    save 900 1
    save 300 10
    save 60 10000
    分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。
4.10. 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
    rdbcompression yes
4.11. 指定本地数据库文件名，默认值为dump.rdb
    dbfilename dump.rdb
4.12. 指定本地数据库存放目录
    dir ./
4.13. 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
    slaveof <masterip> <masterport>
4.14. 当master服务设置了密码保护时，slav服务连接master的密码
    masterauth <master-password>
4.15. 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
    requirepass foobared
4.16. 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
    maxclients 128
4.17. 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
    maxmemory <bytes>
4.18. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
    appendonly no
4.19. 指定更新日志文件名，默认为appendonly.aof
     appendfilename appendonly.aof
4.20. 指定更新日志条件，共有3个可选值： 
    no：表示等操作系统进行数据缓存同步到磁盘（快） 
    always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
    everysec：表示每秒同步一次（折衷，默认值）
    appendfsync everysec
4.21. 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
     vm-enabled no
4.22. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
     vm-swap-file /tmp/redis.swap
4.23. 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
     vm-max-memory 0
4.24. Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
     vm-page-size 32
4.25. 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
     vm-pages 134217728
4.26. 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
     vm-max-threads 4
4.27. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
    glueoutputbuf yes
4.28. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
    hash-max-zipmap-entries 64
    hash-max-zipmap-value 512
4.29. 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
    activerehashing yes
4.30. 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
    include /path/to/local.conf
-------------------------------------------------------------------------------------
http://blog.csdn.net/niucsd/article/details/50966733
详解 Redis 应用场景及应用实例
codis集群部署实战 重量级！！！！
http://navyaijm.blog.51cto.com/4647068/1637688
-------------------------------------------------------------------------------------





# redis的启动方式

1.直接启动
  进入redis根目录，执行命令:
  #加上‘&’号使redis以后台程序方式运行

 2.通过指定配置文件启动
  可以为redis服务启动指定配置文件，例如配置为/etc/redis/6379.conf
  进入redis根目录，输入命令：

  #如果更改了端口，使用`redis-cli`客户端连接时，也需要指定端口，例如：

3.使用redis启动脚本设置开机自启动
  启动脚本 redis_init_script 位于位于Redis的 /utils/ 目录下，redis_init_script脚本代码如下：

 根据启动脚本，将修改好的配置文件复制到指定目录下，用root用户进行操作：

 将启动脚本复制到/etc/init.d目录下，本例将启动脚本命名为redisd（通常都以d结尾表示是后台自启动服务）。

设置为开机自启动，直接配置开启自启动 chkconfig redisd on 发现错误： service redisd does not support chkconfig

解决办法，在启动脚本开头添加如下注释来修改运行级别：

 再设置即可