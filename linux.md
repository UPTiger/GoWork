#查看当前系统版本 
cat /etc/redhat-release  
CentOS release 6.6 (Final) 
#查看当前目录文件大小
du -sh *
df -h
#linux LVM分区查看dm设备
iostat -d
#查找dm-N对应的挂载点
sar -d 1
#性能查看器
top
----- https://linux.cn/article-4452-1.html 
1.1 vmstat命令的含义为显示虚拟内存状态（“Virtual Memor Statics”），但是它可以报告关于进程、内存、I/O等系统整体运行状态。
Procs（进程）
r: 运行队列中进程数量，这个值也可以判断是否需要增加CPU。（长期大于1）
b: 等待IO的进程数量，也就是处在非中断睡眠状态的进程数，展示了正在执行和等待CPU资源的任务个数。当这个值超过了CPU数目，就会出现CPU瓶颈了
Memory（内存）
swpd: 使用虚拟内存大小，如果swpd的值不为0，但是SI，SO的值长期为0，这种情况不会影响系统性能。
free: 空闲物理内存大小。
buff: 用作缓冲的内存大小。
cache: 用作缓存的内存大小，如果cache的值大的时候，说明cache处的文件数多，如果频繁访问到的文件都能被cache处，那么磁盘的读IO bi会非常小。
Swap（交换区）
si: 每秒从交换区写到内存的大小，由磁盘调入内存。
so: 每秒写入交换区的内存大小，由内存调入磁盘。
注意：内存够用的时候，这2个值都是0，如果这2个值长期大于0时，系统性能会受到影响，磁盘IO和CPU资源都会被消耗。有些朋友看到空闲内存（free）很少的或接近于0时，就认为内存不够用了，不能光看这一点，还要结合si和so，如果free很少，但是si和so也很少（大多时候是0），那么不用担心，系统性能这时不会受到影响的。
IO（输入输出）
（现在的Linux版本块的大小为1kb）
bi: 每秒读取的块数
bo: 每秒写入的块数
注意：随机磁盘读写的时候，这2个值越大（如超出1024k)，能看到CPU在IO等待的值也会越大。
system（系统）
in: 每秒中断数，包括时钟中断。
cs: 每秒上下文切换数。
注意：上面2个值越大，会看到由内核消耗的CPU时间会越大。
CPU（以百分比表示）
us: 用户进程执行时间百分比(user time)。us的值比较高时，说明用户进程消耗的CPU时间多，但是如果长期超50%的使用，那么我们就该考虑优化程序算法或者进行加速。
sy: 内核系统进程执行时间百分比(system time)。sy的值高时，说明系统内核消耗的CPU资源多，这并不是良性表现，我们应该检查原因。
wa: IO等待时间百分比。wa的值高时，说明IO等待比较严重，这可能由于磁盘大量作随机访问造成，也有可能磁盘出现瓶颈（块操作）。
id: 空闲时间百分比
从 vmstat 中可以看到，CPU大部分的时间浪费在等待IO上面，可能是由于大量的磁盘随机访问或者磁盘的带宽所造成的，bi、bo 也都超过 1024k，应该是遇到了IO瓶颈。
2.2 iostat它的相关字段说明如下：
rrqm/s: 每秒进行 merge 的读操作数目。即 delta(rmerge)/s
wrqm/s: 每秒进行 merge 的写操作数目。即 delta(wmerge)/s
r/s: 每秒完成的读 I/O 设备次数。即 delta(rio)/s
w/s: 每秒完成的写 I/O 设备次数。即 delta(wio)/s
rsec/s: 每秒读扇区数。即 delta(rsect)/s
wsec/s: 每秒写扇区数。即 delta(wsect)/s
rkB/s: 每秒读K字节数。是 rsect/s 的一半，因为每扇区大小为512字节。(需要计算)
wkB/s: 每秒写K字节数。是 wsect/s 的一半。(需要计算)
avgrq-sz: 平均每次设备I/O操作的数据大小 (扇区)。delta(rsect+wsect)/delta(rio+wio)
avgqu-sz: 平均I/O队列长度。即 delta(aveq)/s/1000 (因为aveq的单位为毫秒)。
await: 平均每次设备I/O操作的等待时间 (毫秒)。即 delta(ruse+wuse)/delta(rio+wio)
svctm: 平均每次设备I/O操作的服务时间 (毫秒)。即 delta(use)/delta(rio+wio)
%util: 一秒中有百分之多少的时间用于 I/O 操作，或者说一秒中有多少时间 I/O 队列是非空的。即 delta(use)/s/1000 (因为use的单位为毫秒)
可以看到两块硬盘中的 sdb 的利用率已经 100%，存在严重的 IO 瓶颈，下一步我们就是要找出哪个进程在往这块硬盘读写数据。
----
2.3 iotop


----
重启后生效 
开启： chkconfig iptables on 
关闭： chkconfig iptables off 或者 /sbin/chkconfig --level 2345 iptables off

2) 即时生效，重启后失效
service 方式
开启： service iptables start 
关闭： service iptables stop
iptables方式
查看防火墙状态：
/etc/init.d/iptables status
暂时关闭防火墙：
/etc/init.d/iptables stop
重启iptables:
/etc/init.d/iptables restart

在文件
vi /etc/sysconfig/iptables
在系统原始配置的:RH-Firewall-1-INPUT规则链增加类似这样的行：
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 39764 -j ACCEPT
-A RH-Firewall-1-INPUT -m state --state NEW -m udp -p udp --dport 39764 -j ACCEPT
----
 ---------------vsftpd-------------------------------------------------
#查看是否已经安装vsftpd 
rpm -qa | grep vsftpd 
#如果没有，就安装，并设置开机启动 
yum -y install vsftpd 
******
chkconfig vsftpd on 
service vsftpd status
/etc/init.d/vsftpd restart
******
修改配置文件
vi /etc/vsftpd/vsftpd.conf 
 
#服务器独立运行 
listen=YES 
#设定不允许匿名访问 
anonymous_enable=NO 
#设定本地用户可以访问。注：如使用虚拟宿主用户，在该项目设定为NO的情况下所有虚拟用户将无法访问 
local_enable=YES 
#使用户不能离开主目录 
chroot_list_enable=YES 
#设定支持ASCII模式的上传和下载功能
ascii_upload_enable=YES 
ascii_download_enable=YES 
#PAM认证文件名。PAM将根据/etc/pam.d/vsftpd进行认证 
pam_service_name=vsftpd 
#设定启用虚拟用户功能 
guest_enable=YES 
#指定虚拟用户的宿主用户，CentOS中已经有内置的ftp用户了 
guest_username=ftp 
#设定虚拟用户个人vsftp的CentOS FTP服务文件存放路径。存放虚拟用户个性的CentOS FTP服务文件(配置文件名=虚拟用户名) 
user_config_dir=/etc/vsftpd/vuser_conf 
#配置vsftpd日志（可选） 
xferlog_enable=YES 
xferlog_std_format=YES 
xferlog_file=/var/log/xferlog 
dual_log_enable=YES 
vsftpd_log_file=/var/log/vsftpd.log 

进行认证
#安装Berkeley DB工具，很多人找不到db_load的问题就是没有安装这个包 
yum install db4 db4-utils 
 
#创建用户密码文本，注意奇行是用户名，偶行是密码 
vi /etc/vsftpd/vuser_passwd.txt 
 
test 
123456 
 
#生成虚拟用户认证的db文件 
db_load -T -t hash -f /etc/vsftpd/vuser_passwd.txt /etc/vsftpd/vuser_passwd.db 
 
#编辑认证文件，全部注释掉原来语句，再增加以下两句 
vi /etc/pam.d/vsftpd 
 
auth required pam_userdb.so db=/etc/vsftpd/vuser_passwd 
account required pam_userdb.so db=/etc/vsftpd/vuser_passwd 
 
#创建虚拟用户配置文件 
mkdir /etc/vsftpd/vuser_conf/ 
#文件名等于vuser_passwd.txt里面的账户名，否则下面设置无效 
vi /etc/vsftpd/vuser_conf/test 
 
#虚拟用户根目录,根据实际情况修改 
local_root=/data/ftp 
write_enable=YES 
anon_umask=022 
anon_world_readable_only=NO 
anon_upload_enable=YES 
anon_mkdir_write_enable=YES 
anon_other_write_enable=YES 
---------------Nginx-------------------------------------------------
启动：/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
重启：/usr/local/nginx/sbin/nginx -s  reload
停止：ps–ef | grepnginx（查看进程号）
kill -9 主进程号
kill -9 子进程号（可能有多个）
---------------Firewalld-------------------------------------------------
systemctl status firewalld	查看防火墙状态。
systemctl stop firewalld	临时关闭防火墙命令。重启电脑后，防火墙自动起来。
systemctl disable firewalld 永久关闭防火墙命令。重启后，防火墙不会自动启动。
systemctl enable firewalld 打开防火墙命令。
查看已启动的服务列表：systemctl list-unit-files|grep enabled
----------------------------------------------------------------

----------------------------------------------------------------