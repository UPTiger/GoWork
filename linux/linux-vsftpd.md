https://github.com/jlaffaye/ftp
https://github.com/Siim/ftp



https://security.appspot.com/vsftpd.html



https://github.com/rovinbhandari/FTP
-------------------------------------------------------------
https://zhidao.baidu.com/question/1370888350841621259.html

http://blog.51cto.com/1364952/1969802 �߶�



1. ���ȷ�����Ҫ��װftp���,�鿴�Ƿ��Ѿ���װftp����£�
   #which vsftpd
   ���������vsftpd��Ŀ¼˵���������Ѿ���װ��ftp���
2. �鿴ftp ������״̬       #service vsftpd status
3. ����ftp������        #service vsftpd start
4. ����ftp������    #service vsftpd restart
5. �鿴������û������   #netstat -an | grep 21
   tcp        0      0 0.0.0.0:21                  0.0.0.0:*                   LISTEN 
   �������������Ϣ��֤��ftp�����Ѿ�������
6.�����Ҫ����root�û���ftpȨ��Ҫ�޸����������ļ�
    #vi /etc/vsftpd.ftpusers��ע�͵�root
    #vi /etc/vsftpd.user_list��Ҳע�͵�root
    Ȼ����������ftp���� 
7. vsftpd 500 OOPS: cannot change directory   ��½����:
   C:\>ftp 192.168.0.101   Connected to 192.168.0.101.
   220 (vsFTPd 2.0.5)   User (192.168.0.101:(none)): frank
   331 Please specify the password.   Password:
   500 OOPS: cannot change directory:/home/frank
   Login failed.   ftp> ls   500 OOPS: child died
   Connection closed by remote host.
   �������:   setsebool ftpd_disable_trans 1
   service vsftpd restart   ��OK�ˣ� 
   ����SELinux����������,�ڲ���ϤSELnuxǰ����SELinux�ص�Ҳ���Եġ�

8. ���ÿ�������os�������Զ�����ftp����    ����һ��
     cd /etc/xinetd.d ���༭ftp����������ļ�gssftp�����ã�
     vi /etc/xinetd.d/gssftp  ���� �޸��������ݣ�   
     (a) server_args = -l �Ca  ȥ��-a ��Ϊserver_args = -l
     (b) disable=yes��Ϊdisable=no     (c) �����˳���     ��������
      (a) system-config-services , ����ͼ�ν����System services�鿴�Ƿ��� vsftpd��,���û��ת��2.,������˳�
local_root=/mnt/ftp_user
9. ��ʼvsftp��¼��־���޸�/etc/vsftpd/vsftpd.conf ���£�

     xferlog_enable=YES
     xferlog_std_format=YES
     xferlog_file=/var/log/xferlog

     10��/var/log/xferlog ʵ����

     Sun Feb 23 21:14:36 2014 4 212.73.193.130 915950 /LilleOL_IconSport4/win_230214_51_19.jpg b _ i r sipafranch ftp 0 * c
     Sun Feb 23 21:14:46 2014 5 212.73.193.130 1018969 /LilleOL_IconSport4/win_230214_51_18.jpg b _ i r sipafranch ftp 0 * c

     11.��־��ʽ����

     ÿ�к��壺

     Sun Feb 23 22:08:26 2014 | 6 | 212.73.193.130 | 1023575 | /Lille_IconSP/win_230214_52_11.jpg | b| _| i| r| sipafranch|ftp| 0| *| c

     | ��¼                               | ����                                                         |
     | ---------------------------------- | ------------------------------------------------------------ |
     | Sun Feb 23 22:08:26 2014           | FTP����ʱ��                                                  |
     | 6                                  | �����ļ�����ʱ�䡣��λ/��                                    |
     | 212.73.193.130                     | ftp�ͻ�������/IP                                             |
     | 1023575                            | �����ļ���С����λ/Byte                                      |
     | /Lille_IconSP/win_230214_52_11.jpg | �����ļ���������·��                                         |
     | b                                  | ���䷽ʽ�� a��ASCII��ʽ����; b�Զ�����(binary)��ʽ����;      |
     | _                                  | ���⴦���־λ��"_"�����κδ���"C"�ļ���ѹ����ʽ��"U"�ļ���ѹ����ʽ��"T"�ļ���tar��ʽ�� |
     | i                                  | ���䷽��"i"�ϴ���"o"���أ�                                 |
     | r                                  | �û�����ģʽ����a�������û���"g"�ÿ�ģʽ��"r"ϵͳ���û�;       |
     | sipafranch                         | ��¼�û���                                                   |
     | ftp                                | �������ƣ�һ�㶼��ftp                                        |
     | 0                                  | ��֤��ʽ��"0"�ޣ�"1"RFC931��֤��                             |
     | *                                  | ��֤�û�id��"*"��ʾ�޷���ȡid                                |
     | c                                  | ���״̬��"i"����δ��ɣ�"c"��������ɣ�                     |

-----------------------2018�¹ʷ���--------------------------------

���ֹ���

1.��Щ�豸���ܴ�FTP�����ļ�

�鿴��־����ʾΪi���������Щ�豸�������سɹ���������ڲ��Զ���OK��

2.�ͻ���ī����ǲ������Ӳ��ɹ�

����FTP��PASV֧�ֺ������������

1. \#���ñ���ģʽ  

2. pasv_enable=YES  

3. pasv_promiscuous=YES  

4. pasv_min_port=60000  

5. pasv_max_port=65000  

6. pasv_addr_resolve=yes #��֪�����Զ���IP

7. pasv_address=agt10.ifengstar.com #EC2�����ݣ�����ģʽ��Ҫ���͹�����ַ���ͻ���

8. pasv_promiscuous=YES #������������IP���ò���ɹ�

9. listen=YES  

10. listen_ipv6=NO

    ����ԭ����port_enable=YES,������֧�������ͱ������ӡ�

11. ec2����̨������ڶ˿�

12. ------------------------���---------------------------------------





# �鿴�Ƿ��Ѿ���װvsftpd

rpm -qa | grep vsftpd 

# ���û�У��Ͱ�װ�������ÿ�������

yum -y install vsftpd 

------

chkconfig vsftpd on 
service vsftpd status
/etc/init.d/vsftpd restart

------

�޸������ļ�
vi /etc/vsftpd/vsftpd.conf 

# ��������������

listen=YES 

# �趨��������������

anonymous_enable=NO 

# �趨�����û����Է��ʡ�ע����ʹ�����������û����ڸ���Ŀ�趨ΪNO����������������û����޷�����

local_enable=YES 

# ʹ�û������뿪��Ŀ¼

chroot_list_enable=YES 

# �趨֧��ASCIIģʽ���ϴ������ع���

ascii_upload_enable=YES 
ascii_download_enable=YES 

# PAM��֤�ļ�����PAM������/etc/pam.d/vsftpd������֤

pam_service_name=vsftpd 

# �趨���������û�����

guest_enable=YES 

# ָ�������û��������û���CentOS���Ѿ������õ�ftp�û���

guest_username=ftp 

# �趨�����û�����vsftp��CentOS FTP�����ļ����·������������û����Ե�CentOS FTP�����ļ�(�����ļ���=�����û���)

user_config_dir=/etc/vsftpd/vuser_conf 

# ����vsftpd��־����ѡ��

xferlog_enable=YES 
xferlog_std_format=YES 
xferlog_file=/var/log/xferlog 
dual_log_enable=YES 
vsftpd_log_file=/var/log/vsftpd.log 

������֤

# ��װBerkeley DB���ߣ��ܶ����Ҳ���db_load���������û�а�װ�����

yum install db4 db4-utils 

# �����û������ı���ע���������û�����ż��������

vi /etc/vsftpd/vuser_passwd.txt 

test 
123456 

# ���������û���֤��db�ļ�

db_load -T -t hash -f /etc/vsftpd/vuser_passwd.txt /etc/vsftpd/vuser_passwd.db 

# �༭��֤�ļ���ȫ��ע�͵�ԭ����䣬��������������

vi /etc/pam.d/vsftpd 

auth required pam_userdb.so db=/etc/vsftpd/vuser_passwd 
account required pam_userdb.so db=/etc/vsftpd/vuser_passwd 

# ���������û������ļ�

mkdir /etc/vsftpd/vuser_conf/ 

# �ļ�������vuser_passwd.txt������˻�������������������Ч

vi /etc/vsftpd/vuser_conf/test 

# �����û���Ŀ¼,����ʵ������޸�

local_root=/data/ftp 
write_enable=YES 
anon_umask=022 
anon_world_readable_only=NO 
anon_upload_enable=YES 
anon_mkdir_write_enable=YES 
anon_other_write_enable=YES 