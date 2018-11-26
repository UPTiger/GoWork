# [Linux命令扫盲 之 sar](https://www.cnblogs.com/xiekeli/archive/2012/04/29/2476197.html)



今天在读《大规模Web服务开发技术》一书的时候，书中提到了sar这个命令，感觉很有用，有必要整理学习一下。（对于一位Linux初学者，不能放过任何一个学习机会 ：P）

打开自己的CentOS，敲入“sar”，表示很失望：

[root@localhost ~]# sar 
bash: sar: command not found

竟然没有安装，不过还好linux下安装还是非常方便的。

[root@localhost ~]# yum install sysstat   
Loaded plugins: fastestmirror 
Loading mirror speeds from cached hostfile 
\* base: mirrors.grandcloud.cn 
\* extras: mirrors.grandcloud.cn 
\* updates: mirrors.grandcloud.cn 
addons                                                   | 1.9 kB     00:00     
base                                                     | 1.1 kB     00:00     
extras                                                   | 2.1 kB     00:00     
updates                                                  | 1.9 kB     00:00     
updates/primary_db                                       | 255 kB     00:01     
Setting up Install Process 
Resolving Dependencies 
--> Running transaction check 
---> Package sysstat.i386 0:7.0.2-11.el5 set to be updated 
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================ 
Package           Arch           Version                  Repository      Size 
================================================================================ 
Installing: 
sysstat           i386           7.0.2-11.el5             base           182 k

Transaction Summary 
================================================================================ 
Install       1 Package(s) 
Upgrade       0 Package(s)

Total download size: 182 k 
Is this ok [y/N]: y 
Downloading Packages: 
sysstat-7.0.2-11.el5.i386.rpm                            | 182 kB     00:01     
Running rpm_check_debug 
Running Transaction Test 
Finished Transaction Test 
Transaction Test Succeeded 
Running Transaction 
  Installing     : sysstat                                                  1/1

Installed: 
  sysstat.i386 0:7.0.2-11.el5                                                  

Complete!

注：Sar是后台进程sadc的前端显示工具，安装名为“sysstat”的包后，sadc就会自动从内核收集报告并保存。

下面对sar的一般用法进行总结，以备忘之。

 

**sar –u  查看CPU使用率**

[root@localhost ~]# sar -u 
Linux 2.6.18-194.26.1.el5 (localhost)   2012年04月29日

09时39分42秒       LINUX RESTART

09时40分01秒       CPU     %user     %nice   %system   %iowait    %steal     %idle 
09时50分01秒       all         0.14      0.00          0.58          0.12         0.00       99.15 
10时00分01秒       all         0.06      0.00          0.50          0.16         0.00       99.27 
10时10分01秒       all         0.11      0.06          0.95          2.58         0.00       96.30 
10时20分01秒       all         0.12      0.19          0.82          1.41         0.00       97.46 
10时30分01秒       all         0.14      0.00          0.54          0.12         0.00       99.20 
10时40分01秒       all         0.15      0.00          0.54          0.16         0.00       99.15 
Average:              all         0.12      0.04          0.65          0.76         0.00       98.43

这里：

%user ： 用户模式下消耗的CPU时间的比例；

%nice：通过nice改变了进程调度优先级的进程，在用户模式下消耗的CPU时间的比例；

%system：系统模式下消耗的CPU时间的比例；

%iowait：CPU等待磁盘I/O而导致空闲状态消耗时间的比例；

%steal：利用Xen等操作系统虚拟化技术时，等待其他虚拟CPU计算占用的时间比例；

%idle：CPU没有等待磁盘I/O等的空闲状态消耗的时间比例；

注：

如果 %iowait 的值过高，表示硬盘存在I/O瓶颈 
如果 %idle 的值高但系统响应慢时，有可能是 CPU 等待分配内存，此时应加大内存容量 
如果 %idle 的值持续低于 10，则系统的 CPU 处理能力相对较低，表明系统中最需要解决的资源是 CPU。

 

**sar –q 查看平均负荷**

[root@localhost ~]# sar -q 
Linux 2.6.18-194.26.1.el5 (localhost)   2012年04月29日

09时39分42秒       LINUX RESTART

09时40分01秒   runq-sz  plist-sz   ldavg-1   ldavg-5  ldavg-15 
09时50分01秒         0       152          0.00      0.02      0.05 
10时00分01秒         0       152          0.00      0.00      0.00 
10时10分01秒         0       156          0.39      0.09      0.03 
10时20分01秒         0       151          0.00      0.03      0.01 
10时30分01秒         0       151          0.00      0.00      0.00 
10时40分01秒         0       151          0.00      0.00      0.00 
10时50分01秒         0       151          0.00      0.00      0.00 
Average:                0       152          0.06      0.02      0.01

runq-sz：   运行队列的长度（等待运行的进程数）                                      
plist-sz：   进程列表中进程（processes）和线程（threads）的数量                     
ldavg-1：   最后1分钟的系统平均负载（System load average）                          
ldavg-5：   过去5分钟的系统平均负载                                                 
ldavg-15： 过去15分钟的系统平均负载

**sar –r 查看内存使用情况**

[root@localhost ~]# sar -r 
Linux 2.6.18-194.26.1.el5 (localhost)   2012年04月29日

09时39分42秒       LINUX RESTART

09时40分01秒 kbmemfree kbmemused  %memused kbbuffers  kbcached kbswpfree kbswpused  %swpused  kbswpcad 
09时50分01秒    481572       553492           53.47     35592         384508   2097144         0                 0.00           0 
10时00分01秒    480960       554104           53.53     36032         384512   2097144         0                 0.00           0 
10时10分01秒    404952       630112           60.88     77764         399432   2097144         0                 0.00           0 
10时20分01秒    375824       659240           63.69     87356         410892   2097144         0                 0.00           0 
10时30分01秒    371860       663204           64.07     87756         411064   2097144         0                 0.00           0 
…

kbmemfree：空闲物理内存量；

kbmemused：使用中的物理内存量；

%memused：物理内存量使用率；

kbbuffers：内核中作为缓冲区使用的物理内存容量；

kbcacheed：内核中作为缓存使用的物理内存容量；

kbswpfree：交换区的空闲容量；

kbswpused：使用中的交换区容量；

 

**sar –W 查看页面交换发生状况**

[root@localhost ~]# sar -W

14时30分01秒  pswpin/s pswpout/s 
14时40分01秒      0.00      0.00 
14时50分01秒      0.00      0.00 
15时00分01秒      0.00      0.00 
Average:         0.00      0.00

…

 

**sar –b 查看I/O和传送速率的统计信息**

[root@localhost ~]# sar -b 1 5 
Linux 2.6.18-194.26.1.el5 (localhost)   2012年04月29日

15时08分18秒       tps      rtps      wtps   bread/s   bwrtn/s 
15时08分19秒      0.00      0.00      0.00      0.00      0.00 
15时08分20秒      0.00      0.00      0.00      0.00      0.00 
15时08分21秒      0.00      0.00      0.00      0.00      0.00 
15时08分22秒     13.27      0.00     13.27      0.00    220.41 
15时08分23秒      0.00      0.00      0.00      0.00      0.00 
Average:         2.66      0.00      2.66      0.00     44.17

tps：     每秒钟物理设备的 I/O 传输总量                    
rtps:    每秒钟从物理设备读入的数据总量                  
wtps:    每秒钟向物理设备写入的数据总量                  
bread/s: 每秒钟从物理设备读入的数据量，单位为 块/s    
bwrtn/s: 每秒钟向物理设备写入的数据量，单位为 块/s

 

其他还有：

sar –c   每秒钟创建的进程数

sar -n DEV  输出网络设备状态的统计信息

 

注：默认情况是对过去时间段进行数据统计，一般从最近的0：00开始显示。如果想继续查看一天前的报告，可以用-f选项指定保存在/var/log/sa目录下的日志文件中。如果想周期性的查看当前数据可以命令后面加上数字参数，如sar –q 1 3 ，表示：1秒1次，共3次。



安装了,还需要启动. 

问题: mona@pascal:~$ sudo sar -d Cannot open /var/log/sysstat/sa20: No such file or directory Please check if data collecting is enabled in /etc/default/sysstat 

解决办法: Open /etc/default/sysstat using your favorite file editor and change ENABLED="false" to ENABLED="true" vi /etc/default/sysstat

 ---- # Should sadc collect system activity informations? Valid values # are "true" and "false". Please do not put other values, they # will be overwritten by debconf! ENABLED="true"

 ---- Restart sysstat: sudo service sysstat restart