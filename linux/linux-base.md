#升级所有包，改变软件设置和系统设置,系统版本内核都升级
yum -y update 
#升级所有包，不改变软件设置和系统设置，系统版本升级，内核不改变
yum -y upgrade
安装开发工具集：
yum groupinstall "Development Tools"
------------------------------------------------------------
查看版本号：cat /etc/redhat-release
查看名称：hostnamectl
修改主机名称：hostnamectl set-hostname  xxxxx
查看主机名称： cat /etc/hostname 
------------------------------------------------------------
显示硬盘情况 df -a -h -T
查看硬盘分区 fdisk -l 查看新加入的硬盘
------------------------------------------------------------
查看内存
dmidecode | grep -P -A5 "Memory\s+Device" | grep Size | grep -v Range
查看内存频率
dmidecode | grep -A16 "Memory Device"|grep 'Speed'
dmidecode | grep -A16 "Memory Device"

free -h
------------------------------------------------------------

查看CPU信息（型号）
[root@AAA ~]# cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
        Intel(R) Xeon(R) CPU E5-2630 0 @ 2.30GHz
# 查看物理CPU个数
[root@AAA ~]# cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l
# 查看每个物理CPU中core的个数(即核数)
[root@AAA ~]# cat /proc/cpuinfo| grep "cpu cores"| uniq
cpu cores    : 6
# 查看逻辑CPU的个数
[root@AAA ~]# cat /proc/cpuinfo| grep "processor"| wc -l

-----------热清除nohup.out-----------

第一种：cp /dev/null nohup.out
第二种：cat /dev/null > nohup.out

-----------nohup.out 不输出内容-----------

//只输出错误信息到日志文件
nohup ./program >/dev/null 2>log &
//什么信息也不要
nohup ./program >/dev/null 2>&1 &

0:表示标准输入
1:标准输出,在一般使用时，默认的是标准输出
2:标准错误信息输出

-----------安装vim-----------
rpm -qa|grep vim
yum -y install vim-enhanced
yum -y install vim*
-----------------------------
方法一
1、赋予脚本可执行权限（/opt/script/autostart.sh是你的脚本路径）
chmod +x /opt/script/autostart.sh 
2、打开/etc/rc.d/rc/local文件，在末尾增加如下内容
/opt/script/autostart.sh 
3、在centos7中，/etc/rc.d/rc.local的权限被降低了，所以需要执行如下命令赋予其可执行权限
chmod +x /etc/rc.d/rc.local
方法二
1、将脚本移动到/etc/rc.d/init.d目录下
mv  /opt/script/autostart.sh /etc/rc.d/init.d
2、增加脚本的可执行权限
chmod +x  /etc/rc.d/init.d/autostart.sh
3、添加脚本到开机自动启动项目中
cd /etc/rc.d/init.d
chkconfig --add autostart.sh
chkconfig autostart.sh on
-----------------------------


-----------------------------


-----------------------------


-----------------------------
    public static final int START_DELIMITER_LEN = 2;