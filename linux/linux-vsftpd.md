https://github.com/jlaffaye/ftp
https://github.com/Siim/ftp
https://github.com/rovinbhandari/FTP
-------------------------------------------------------------
https://zhidao.baidu.com/question/1370888350841621259.html
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
　 (b) 用redhat第三张盘 安装此服务（开始--删除/增加程序），200K左右　 (c) #setup
　　  此时能看到vsftpd项，此时选中此services项,保存后退出.

local_root=/mnt/ftp_user