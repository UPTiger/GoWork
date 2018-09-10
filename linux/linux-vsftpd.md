https://github.com/jlaffaye/ftp
https://github.com/Siim/ftp



https://security.appspot.com/vsftpd.html



https://github.com/rovinbhandari/FTP
-------------------------------------------------------------
https://zhidao.baidu.com/question/1370888350841621259.html

http://blog.51cto.com/1364952/1969802 高端



1. 首先服务器要安装ftp软件,查看是否已经安装ftp软件下：
   #which vsftpd
   如果看到有vsftpd的目录说明服务器已经安装了ftp软件
2. 查看ftp 服务器状态       #service vsftpd status
3. 启动ftp服务器        #service vsftpd start
4. 重启ftp服务器    #service vsftpd restart
5. 查看服务有没有启动   #netstat -an | grep 21
   tcp        0      0 0.0.0.0:21                  0.0.0.0:*                   LISTEN 
   如果看到以上信息，证明ftp服务已经开启。
6.如果需要开启root用户的ftp权限要修改以下两个文件
    #vi /etc/vsftpd.ftpusers中注释掉root
    #vi /etc/vsftpd.user_list中也注释掉root
    然后重新启动ftp服务。 
7. vsftpd 500 OOPS: cannot change directory   登陆报错:
   C:\>ftp 192.168.0.101   Connected to 192.168.0.101.
   220 (vsFTPd 2.0.5)   User (192.168.0.101:(none)): frank
   331 Please specify the password.   Password:
   500 OOPS: cannot change directory:/home/frank
   Login failed.   ftp> ls   500 OOPS: child died
   Connection closed by remote host.
   解决方法:   setsebool ftpd_disable_trans 1
   service vsftpd restart   就OK了！ 
   这是SELinux的设置命令,在不熟悉SELnux前，把SELinux关掉也可以的。

8. 永久开启，即os重启后自动开启ftp服务    方法一：
     cd /etc/xinetd.d ，编辑ftp服务的配置文件gssftp的设置：
     vi /etc/xinetd.d/gssftp  ，将 修改两项内容：   
     (a) server_args = -l –a  去掉-a 改为server_args = -l
     (b) disable=yes改为disable=no     (c) 保存退出。     方法二：
      (a) system-config-services , 进入图形界面的System services查看是否有 vsftpd项,如果没有转到2.,保存后退出
local_root=/mnt/ftp_user
9. 开始vsftp记录日志。修改/etc/vsftpd/vsftpd.conf 如下：

     xferlog_enable=YES
     xferlog_std_format=YES
     xferlog_file=/var/log/xferlog

     10、/var/log/xferlog 实例：

     Sun Feb 23 21:14:36 2014 4 212.73.193.130 915950 /LilleOL_IconSport4/win_230214_51_19.jpg b _ i r sipafranch ftp 0 * c
     Sun Feb 23 21:14:46 2014 5 212.73.193.130 1018969 /LilleOL_IconSport4/win_230214_51_18.jpg b _ i r sipafranch ftp 0 * c

     11.日志格式解析

     每列含义：

     Sun Feb 23 22:08:26 2014 | 6 | 212.73.193.130 | 1023575 | /Lille_IconSP/win_230214_52_11.jpg | b| _| i| r| sipafranch|ftp| 0| *| c

     | 记录                               | 含义                                                         |
     | ---------------------------------- | ------------------------------------------------------------ |
     | Sun Feb 23 22:08:26 2014           | FTP传输时间                                                  |
     | 6                                  | 传输文件所用时间。单位/秒                                    |
     | 212.73.193.130                     | ftp客户端名称/IP                                             |
     | 1023575                            | 传输文件大小。单位/Byte                                      |
     | /Lille_IconSP/win_230214_52_11.jpg | 传输文件名，包含路径                                         |
     | b                                  | 传输方式： a以ASCII方式传输; b以二进制(binary)方式传输;      |
     | _                                  | 特殊处理标志位："_"不做任何处理；"C"文件是压缩格式；"U"文件非压缩格式；"T"文件是tar格式； |
     | i                                  | 传输方向："i"上传；"o"下载；                                 |
     | r                                  | 用户访问模式：“a”匿名用户；"g"访客模式；"r"系统中用户;       |
     | sipafranch                         | 登录用户名                                                   |
     | ftp                                | 服务名称，一般都是ftp                                        |
     | 0                                  | 认证方式："0"无；"1"RFC931认证；                             |
     | *                                  | 认证用户id，"*"表示无法获取id                                |
     | c                                  | 完成状态："i"传输未完成；"c"传输已完成；                     |

-----------------------2018事故分析--------------------------------

表现故障

1.有些设备不能从FTP上载文件

查看日志有显示为i的情况，有些设备可以下载成功，比如国内测试都是OK的

2.客户在墨西哥城测试连接不成功

增加FTP对PASV支持后可以正常访问

1. \#启用被动模式  

2. pasv_enable=YES  

3. pasv_promiscuous=YES  

4. pasv_min_port=60000  

5. pasv_max_port=65000  

6. pasv_addr_resolve=yes #告知开起自定义IP

7. pasv_address=agt10.ifengstar.com #EC2是内容，被动模式需要发送公网地址给客户端

8. pasv_promiscuous=YES #如果不设置这个IP设置不会成功

9. listen=YES  

10. listen_ipv6=NO

    加上原来的port_enable=YES,服务器支持主动和被动连接。

11. ec2控制台增加入口端口

12. ------------------------结果---------------------------------------





# 查看是否已经安装vsftpd

rpm -qa | grep vsftpd 

# 如果没有，就安装，并设置开机启动

yum -y install vsftpd 

------

chkconfig vsftpd on 
service vsftpd status
/etc/init.d/vsftpd restart

------

修改配置文件
vi /etc/vsftpd/vsftpd.conf 

# 服务器独立运行

listen=YES 

# 设定不允许匿名访问

anonymous_enable=NO 

# 设定本地用户可以访问。注：如使用虚拟宿主用户，在该项目设定为NO的情况下所有虚拟用户将无法访问

local_enable=YES 

# 使用户不能离开主目录

chroot_list_enable=YES 

# 设定支持ASCII模式的上传和下载功能

ascii_upload_enable=YES 
ascii_download_enable=YES 

# PAM认证文件名。PAM将根据/etc/pam.d/vsftpd进行认证

pam_service_name=vsftpd 

# 设定启用虚拟用户功能

guest_enable=YES 

# 指定虚拟用户的宿主用户，CentOS中已经有内置的ftp用户了

guest_username=ftp 

# 设定虚拟用户个人vsftp的CentOS FTP服务文件存放路径。存放虚拟用户个性的CentOS FTP服务文件(配置文件名=虚拟用户名)

user_config_dir=/etc/vsftpd/vuser_conf 

# 配置vsftpd日志（可选）

xferlog_enable=YES 
xferlog_std_format=YES 
xferlog_file=/var/log/xferlog 
dual_log_enable=YES 
vsftpd_log_file=/var/log/vsftpd.log 

进行认证

# 安装Berkeley DB工具，很多人找不到db_load的问题就是没有安装这个包

yum install db4 db4-utils 

# 创建用户密码文本，注意奇行是用户名，偶行是密码

vi /etc/vsftpd/vuser_passwd.txt 

test 
123456 

# 生成虚拟用户认证的db文件

db_load -T -t hash -f /etc/vsftpd/vuser_passwd.txt /etc/vsftpd/vuser_passwd.db 

# 编辑认证文件，全部注释掉原来语句，再增加以下两句

vi /etc/pam.d/vsftpd 

auth required pam_userdb.so db=/etc/vsftpd/vuser_passwd 
account required pam_userdb.so db=/etc/vsftpd/vuser_passwd 

# 创建虚拟用户配置文件

mkdir /etc/vsftpd/vuser_conf/ 

# 文件名等于vuser_passwd.txt里面的账户名，否则下面设置无效

vi /etc/vsftpd/vuser_conf/test 

# 虚拟用户根目录,根据实际情况修改

local_root=/data/ftp 
write_enable=YES 
anon_umask=022 
anon_world_readable_only=NO 
anon_upload_enable=YES 
anon_mkdir_write_enable=YES 
anon_other_write_enable=YES 