http://lib.csdn.net/base/linux
--------------------------------------------------------------------------------
top命令按1，看到几个CPU就代表是几核的。
查看CPU有几颗逻辑cpu,4代表有4个逻辑CPU，同时CPU的型号也打印出了，服务器一般都是至强的CPU
[root@svn ~]# cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c 
4 Intel(R) Xeon(R) CPU E5-2407 v2 @ 2.40GHz 

**查看cpu是几核的，也可以用命令显示，4代表就是4核**
[root@svn ~]# cat /proc/cpuinfo |grep "cores"|uniq
cpu cores : 4

**查看物理CPU的个数，1代表只有一个物理CPU**
[root@svn ~]# cat /proc/cpuinfo |grep "physical id"|sort |uniq|wc -l
1

逻辑CPU数量=物理cpu数量 x cpu cores 这个规格值 x 2(如果支持并开启ht)

---------------网络配备----------





-------------------end-----------

--------------------------------------------------------------------------------------------------------------------------

#查看当前系统版本 
cat /etc/redhat-release  
CentOS release 6.6 (Final) 
uname -a:
file /bin/ls
#查看当前目录文件大小
du -sh *
df -Th
#linux LVM分区查看dm设备
iostat -d
#查找dm-N对应的挂载点

#sar -d 1
挂载磁盘
https://help.aliyun.com/document_detail/25426.html?spm=5176.11065259.1996646101.searchclickresult.2e961dd1jnLq4c

---------------------------------------------------------------------------------------------------------
磁盘ext3转换ext4
1.df -TH
/dev/vdb1 ext3 1.1T 80M 1.1T 1% /mnt

2.umount /dev/vdb1
3.tune2fs -O extents,uninit_bg,dir_index /dev/vdb1
4.fsck -pk /dev/vdb1
5.mkfs.ext4 /dev/vdb1（可以不执行有可能会破坏数据）
6.mount /dev/vdb1 /mnt
7.恢复fstab
8.修改echo /dev/vdb1 /mnt ext4 defaults 0 0 >> /etc/fstab

#性能查看器
**1. 使用w命令查看登录用户正在使用的进程信息** 
w 
who
users

**随时查看系统的历史信息（曾经使用过系统的用户信息）** 
last root
-----------------------------------------------------------------------------------------------------
设置本机时间
yum install ntpdate
dig pool.ntp.org
ntpdate pool.ntp.org / ntpdate 202.112.29.82
显示时区
TC time: Tue 2018-11-20 03:30:23
Time zone: America/New_York (EST, -0
设置服务器为UTC时区
timedatectl set-timezone UTC
timedatectl set-local-rtc 0 # 将硬件时钟调整为与本地时钟一致, 0 为设置为 UTC 时间
timedatectl status

也可以直接用下面命令直接更换时区
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
-----------------------------------------------------------------------------------------
TOP

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

----------------------------------------------------
陶工：
#开放端口
firewall-cmd --zone=public --add-port=6379/tcp --permanent
firewall-cmd --reload
#关闭端口
firewall-cmd --zone=public --remove-port=6379/tcp --permanent
firewall-cmd --reload
#查询端口是否开放
firewall-cmd --query-port=3306/tcp
----------------------------------------------------

[CentOS](http://www.linuxidc.com/topicnews.aspx?tid=14) 7.0默认使用的是firewall作为防火墙，使用iptables必须重新设置一下
1、直接关闭防火墙
systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动
2、设置 iptables service
yum -y install iptables-services
如果要修改防火墙配置，如增加防火墙端口3306
vi /etc/sysconfig/iptables
增加规则
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
保存退出后
systemctl restart iptables.service #重启防火墙使配置生效
systemctl enable iptables.service #设置防火墙开机启动
最后重启系统使设置生效即可。
——————————————————————————
**CentOS查看CPU温度:**
1.yum install lm_sensors;
2.sensors-detect
3.sensors
4.watch sensors
**CentOS查找不到netstat lsof** 
yum install net-tools 
yum install lsof
**CentOS查找端口**
netstat -lnp |grep 88
ps 1777
kill -9 1777
——————————————————————————
同步系统时间：https://www.cnblogs.com/intval/p/5763929.html
1.  安装ntpdate工具
\# yum -y install ntp ntpdate
2.  设置系统时间与网络时间同步
\# ntpdate cn.pool.ntp.org
3.  将系统时间写入硬件时间
\# hwclock --systohc
4.强制系统时间写入CMOS中防止重启失效
　　hwclock -w
　　或clock -w
四、定时执行时间同步任务，所以我们利用crontab -e 来添加定时任务
\#* */1 * * * root ntpdatetime.nuri.net;hwclock -w 
![img](https://images2015.cnblogs.com/blog/513841/201608/513841-20160812102124078-171184924.png) 
/etc/init.d/crond restart
service crond restart
30 5 * * * /usr/sbin/ntpdate -u asia.pool.ntp.org;hwclock -w
每天早上5点30分
查看日志看某个job有没有执行/报错tail -f /var/log/cron 
在本示例中，我们用一个新的 20 GiB 数据盘（设备名为 /dev/xvdb）创建一个单分区数据盘并挂载一个 ext3 文件系统。使用的实例是 I/O 优化实例，操作系统为 CentOS 6.8。
1. [远程连接实例](https://help.aliyun.com/document_detail/25425.html)。
2. 运行 `fdisk -l` 命令查看实例是否有数据盘。如果执行命令后，没有发现 **/dev/vdb**，表示您的实例没有数据盘，无需格式化数据盘，请忽略本文后续内容。
   - 如果您的数据盘显示的是 **dev/xvd?**，表示您使用的是非 I/O 优化实例。
   - 其中 *?* 是 a−z 的任一个字母。
3. 创建一个单分区数据盘，依次执行以下命令：
   1. 运行 `fdisk /dev/vdb`：对数据盘进行分区。
   2. 输入 `n` 并按回车键：创建一个新分区。
   3. 输入 `p` 并按回车键：选择主分区。因为创建的是一个单分区数据盘，所以只需要创建主分区。
      > **说明：**如果要创建 4 个以上的分区，您应该创建至少一个扩展分区，即选择 `e`。
   4. 输入分区编号并按回车键。因为这里仅创建一个分区，可以输入 1。
   5. 输入第一个可用的扇区编号：按回车键采用默认值 1。
   6. 输入最后一个扇区编号：因为这里仅创建一个分区，所以按回车键采用默认值。
   7. 输入 `wq` 并按回车键，开始分区。
      ```
      [root@iXXXXXXX ~]# fdisk /dev/vdbDevice contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabelBuilding a new DOS disklabel with disk identifier 0x5f46a8a2.Changes will remain in memory only, until you decide to write them.After that, of course, the previous content won't be recoverable.Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)WARNING: DOS-compatible mode is deprecated. It's strongly recommended to  switch off the mode (command 'c') and change display units to  sectors (command 'u').Command (m for help): nCommand actione   extendedp   primary partition (1-4)pPartition number (1-4): 1First cylinder (1-41610, default 1): 1Last cylinder, +cylinders or +size{K,M,G} (1-41610, default 41610):Using default value 41610Command (m for help): wqThe partition table has been altered!Calling ioctl() to re-read partition table.Syncing disks.
      ```
4. 查看新的分区：运行命令 `fdisk -l`。如果出现以下信息，说明已经成功创建了新分区 /dev/vdb1。
   ```
   [root@iXXXXXXX ~]# fdisk -lDisk /dev/vda: 42.9 GB, 42949672960 bytes255 heads, 63 sectors/track, 5221 cylindersUnits = cylinders of 16065 * 512 = 8225280 bytesSector size (logical/physical): 512 bytes / 512 bytesI/O size (minimum/optimal): 512 bytes / 512 bytesDisk identifier: 0x00053156Device Boot      Start         End      Blocks   Id  System/dev/vda1   *           1        5222    41942016   83  LinuxDisk /dev/vdb: 21.5 GB, 21474836480 bytes16 heads, 63 sectors/track, 41610 cylindersUnits = cylinders of 1008 * 512 = 516096 bytesSector size (logical/physical): 512 bytes / 512 bytesI/O size (minimum/optimal): 512 bytes / 512 bytesDisk identifier: 0x5f46a8a2Device Boot      Start         End      Blocks   Id  System/dev/vdb1               1       41610    20971408+  83  Linux
   ```
5. 在新分区上创建一个文件系统：运行命令 `mkfs.ext4 /dev/vdb1`。(mkfs.ext4 -L /dev/vdb1)
   - 本示例要创建一个 ext4 文件系统。您也可以根据自己的需要，选择创建其他文件系统，例如，如果需要在 Linux、Windows 和 Mac 系统之间共享文件，您可以使用 `mkfs.vfat` 创建 VFAT 文件系统。
   - 创建文件系统所需时间取决于数据盘大小。
     ```
     [root@iXXXXXXX ~]# mkfs.ext4 /dev/vdb1mke2fs 1.41.12 (17-May-2010)Filesystem label=OS type: LinuxBlock size=4096 (log=2)Fragment size=4096 (log=2)Stride=0 blocks, Stripe width=0 blocks1310720 inodes, 5242852 blocks262142 blocks (5.00%) reserved for the super userFirst data block=0Maximum filesystem blocks=4294967296160 block groups32768 blocks per group, 32768 fragments per group8192 inodes per groupSuperblock backups stored on blocks: 32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 4096000Writing inode tables: doneCreating journal (32768 blocks): doneWriting superblocks and filesystem accounting information: doneThis filesystem will be automatically checked every 37 mounts or180 days, whichever comes first.  Use tune2fs -c or -i to override.
     ```
6. （建议）备份 **etc/fstab**：运行命令 `cp /etc/fstab /etc/fstab.bak`。
7. 向 **/etc/fstab** 写入新分区信息：运行命令 `echo /dev/vdb1 /mnt ext4 defaults 0 0 >> /etc/fstab`。
   > **说明**：Ubuntu 12.04 不支持 barrier，所以对该系统正确的命令是：`echo '/dev/vdb1 /mnt ext4 barrier=0 0 0' >> /etc/fstab`。
   如果需要把数据盘单独挂载到某个文件夹，比如单独用来存放网页，请将以上命令 /mnt 替换成所需的挂载点路径。
8. 查看 **/etc/fstab** 中的新分区信息：运行命令 `cat /etc/fstab`。
   ```
    [root@iXXXXXXX ~]# cat /etc/fstab # # /etc/fstab # Created by anaconda on Thu Feb 23 07:28:22 2017 # # Accessible filesystems, by reference, are maintained under '/dev/disk' # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info # UUID=3d083579-f5d9-4df5-9347-8d27925805d4 /                       ext4    defaults        1 1 tmpfs                   /dev/shm                tmpfs   defaults        0 0 devpts                  /dev/pts                devpts  gid=5,mode=620  0 0 sysfs                   /sys                    sysfs   defaults        0 0 proc                    /proc                   proc    defaults        0 0 /dev/vdb1 /mnt ext4 defaults 0 0
   ```
9. 挂载文件系统：运行命令 `mount /dev/vdb1 /mnt`。
10. 查看目前磁盘空间和使用情况：运行命令 `df -h`。如果出现新建文件系统的信息，说明挂载成功，可以使用新的文件系统了。
    挂载操作完成后，不需要重启实例即可开始使用新的文件系统。
    ```
    [root@iXXXXXXX ~]# mount /dev/vdb1 /mnt[root@iXXXXXXX ~]# df -hFilesystem      Size  Used Avail Use% Mounted on/dev/vda1        40G  6.6G   31G  18% /tmpfs           499M     0  499M   0% /dev/shm/dev/vdb1        20G  173M   19G   1% /mnt
    ```