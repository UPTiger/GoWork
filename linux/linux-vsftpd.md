https://github.com/jlaffaye/ftp
https://github.com/Siim/ftp
https://github.com/rovinbhandari/FTP
-------------------------------------------------------------
https://zhidao.baidu.com/question/1370888350841621259.html
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
�� (b) ��redhat�������� ��װ�˷��񣨿�ʼ--ɾ��/���ӳ��򣩣�200K���ҡ� (c) #setup
����  ��ʱ�ܿ���vsftpd���ʱѡ�д�services��,������˳�.

local_root=/mnt/ftp_user