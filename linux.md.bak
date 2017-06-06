#查看当前系统版本 
cat /etc/redhat-release  
CentOS release 6.6 (Final) 
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