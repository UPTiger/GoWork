#�鿴��ǰϵͳ�汾 
cat /etc/redhat-release  
CentOS release 6.6 (Final) 
#�鿴��ǰĿ¼�ļ���С
du -sh *
df -h
#linux LVM�����鿴dm�豸
iostat -d
#����dm-N��Ӧ�Ĺ��ص�
sar -d 1
#���ܲ鿴��
top
----- https://linux.cn/article-4452-1.html 
1.1 vmstat����ĺ���Ϊ��ʾ�����ڴ�״̬����Virtual Memor Statics���������������Ա�����ڽ��̡��ڴ桢I/O��ϵͳ��������״̬��
Procs�����̣�
r: ���ж����н������������ֵҲ�����ж��Ƿ���Ҫ����CPU�������ڴ���1��
b: �ȴ�IO�Ľ���������Ҳ���Ǵ��ڷ��ж�˯��״̬�Ľ�������չʾ������ִ�к͵ȴ�CPU��Դ����������������ֵ������CPU��Ŀ���ͻ����CPUƿ����
Memory���ڴ棩
swpd: ʹ�������ڴ��С�����swpd��ֵ��Ϊ0������SI��SO��ֵ����Ϊ0�������������Ӱ��ϵͳ���ܡ�
free: ���������ڴ��С��
buff: ����������ڴ��С��
cache: ����������ڴ��С�����cache��ֵ���ʱ��˵��cache�����ļ����࣬���Ƶ�����ʵ����ļ����ܱ�cache������ô���̵Ķ�IO bi��ǳ�С��
Swap����������
si: ÿ��ӽ�����д���ڴ�Ĵ�С���ɴ��̵����ڴ档
so: ÿ��д�뽻�������ڴ��С�����ڴ������̡�
ע�⣺�ڴ湻�õ�ʱ����2��ֵ����0�������2��ֵ���ڴ���0ʱ��ϵͳ���ܻ��ܵ�Ӱ�죬����IO��CPU��Դ���ᱻ���ġ���Щ���ѿ��������ڴ棨free�����ٵĻ�ӽ���0ʱ������Ϊ�ڴ治�����ˣ����ܹ⿴��һ�㣬��Ҫ���si��so�����free���٣�����si��soҲ���٣����ʱ����0������ô���õ��ģ�ϵͳ������ʱ�����ܵ�Ӱ��ġ�
IO�����������
�����ڵ�Linux�汾��Ĵ�СΪ1kb��
bi: ÿ���ȡ�Ŀ���
bo: ÿ��д��Ŀ���
ע�⣺������̶�д��ʱ����2��ֵԽ���糬��1024k)���ܿ���CPU��IO�ȴ���ֵҲ��Խ��
system��ϵͳ��
in: ÿ���ж���������ʱ���жϡ�
cs: ÿ���������л�����
ע�⣺����2��ֵԽ�󣬻ῴ�����ں����ĵ�CPUʱ���Խ��
CPU���԰ٷֱȱ�ʾ��
us: �û�����ִ��ʱ��ٷֱ�(user time)��us��ֵ�Ƚϸ�ʱ��˵���û��������ĵ�CPUʱ��࣬����������ڳ�50%��ʹ�ã���ô���Ǿ͸ÿ����Ż������㷨���߽��м��١�
sy: �ں�ϵͳ����ִ��ʱ��ٷֱ�(system time)��sy��ֵ��ʱ��˵��ϵͳ�ں����ĵ�CPU��Դ�࣬�Ⲣ�������Ա��֣�����Ӧ�ü��ԭ��
wa: IO�ȴ�ʱ��ٷֱȡ�wa��ֵ��ʱ��˵��IO�ȴ��Ƚ����أ���������ڴ��̴��������������ɣ�Ҳ�п��ܴ��̳���ƿ�������������
id: ����ʱ��ٷֱ�
�� vmstat �п��Կ�����CPU�󲿷ֵ�ʱ���˷��ڵȴ�IO���棬���������ڴ����Ĵ���������ʻ��ߴ��̵Ĵ�������ɵģ�bi��bo Ҳ������ 1024k��Ӧ����������IOƿ����
2.2 iostat��������ֶ�˵�����£�
rrqm/s: ÿ����� merge �Ķ�������Ŀ���� delta(rmerge)/s
wrqm/s: ÿ����� merge ��д������Ŀ���� delta(wmerge)/s
r/s: ÿ����ɵĶ� I/O �豸�������� delta(rio)/s
w/s: ÿ����ɵ�д I/O �豸�������� delta(wio)/s
rsec/s: ÿ������������� delta(rsect)/s
wsec/s: ÿ��д���������� delta(wsect)/s
rkB/s: ÿ���K�ֽ������� rsect/s ��һ�룬��Ϊÿ������СΪ512�ֽڡ�(��Ҫ����)
wkB/s: ÿ��дK�ֽ������� wsect/s ��һ�롣(��Ҫ����)
avgrq-sz: ƽ��ÿ���豸I/O���������ݴ�С (����)��delta(rsect+wsect)/delta(rio+wio)
avgqu-sz: ƽ��I/O���г��ȡ��� delta(aveq)/s/1000 (��Ϊaveq�ĵ�λΪ����)��
await: ƽ��ÿ���豸I/O�����ĵȴ�ʱ�� (����)���� delta(ruse+wuse)/delta(rio+wio)
svctm: ƽ��ÿ���豸I/O�����ķ���ʱ�� (����)���� delta(use)/delta(rio+wio)
%util: һ�����аٷ�֮���ٵ�ʱ������ I/O ����������˵һ�����ж���ʱ�� I/O �����Ƿǿյġ��� delta(use)/s/1000 (��Ϊuse�ĵ�λΪ����)
���Կ�������Ӳ���е� sdb ���������Ѿ� 100%���������ص� IO ƿ������һ�����Ǿ���Ҫ�ҳ��ĸ������������Ӳ�̶�д���ݡ�
----
2.3 iotop


----
��������Ч 
������ chkconfig iptables on 
�رգ� chkconfig iptables off ���� /sbin/chkconfig --level 2345 iptables off

2) ��ʱ��Ч��������ʧЧ
service ��ʽ
������ service iptables start 
�رգ� service iptables stop
iptables��ʽ
�鿴����ǽ״̬��
/etc/init.d/iptables status
��ʱ�رշ���ǽ��
/etc/init.d/iptables stop
����iptables:
/etc/init.d/iptables restart

���ļ�
vi /etc/sysconfig/iptables
��ϵͳԭʼ���õ�:RH-Firewall-1-INPUT���������������������У�
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 39764 -j ACCEPT
-A RH-Firewall-1-INPUT -m state --state NEW -m udp -p udp --dport 39764 -j ACCEPT
----
 ---------------vsftpd-------------------------------------------------
#�鿴�Ƿ��Ѿ���װvsftpd 
rpm -qa | grep vsftpd 
#���û�У��Ͱ�װ�������ÿ������� 
yum -y install vsftpd 
******
chkconfig vsftpd on 
service vsftpd status
/etc/init.d/vsftpd restart
******
�޸������ļ�
vi /etc/vsftpd/vsftpd.conf 
 
#�������������� 
listen=YES 
#�趨�������������� 
anonymous_enable=NO 
#�趨�����û����Է��ʡ�ע����ʹ�����������û����ڸ���Ŀ�趨ΪNO����������������û����޷����� 
local_enable=YES 
#ʹ�û������뿪��Ŀ¼ 
chroot_list_enable=YES 
#�趨֧��ASCIIģʽ���ϴ������ع���
ascii_upload_enable=YES 
ascii_download_enable=YES 
#PAM��֤�ļ�����PAM������/etc/pam.d/vsftpd������֤ 
pam_service_name=vsftpd 
#�趨���������û����� 
guest_enable=YES 
#ָ�������û��������û���CentOS���Ѿ������õ�ftp�û��� 
guest_username=ftp 
#�趨�����û�����vsftp��CentOS FTP�����ļ����·������������û����Ե�CentOS FTP�����ļ�(�����ļ���=�����û���) 
user_config_dir=/etc/vsftpd/vuser_conf 
#����vsftpd��־����ѡ�� 
xferlog_enable=YES 
xferlog_std_format=YES 
xferlog_file=/var/log/xferlog 
dual_log_enable=YES 
vsftpd_log_file=/var/log/vsftpd.log 

������֤
#��װBerkeley DB���ߣ��ܶ����Ҳ���db_load���������û�а�װ����� 
yum install db4 db4-utils 
 
#�����û������ı���ע���������û�����ż�������� 
vi /etc/vsftpd/vuser_passwd.txt 
 
test 
123456 
 
#���������û���֤��db�ļ� 
db_load -T -t hash -f /etc/vsftpd/vuser_passwd.txt /etc/vsftpd/vuser_passwd.db 
 
#�༭��֤�ļ���ȫ��ע�͵�ԭ����䣬�������������� 
vi /etc/pam.d/vsftpd 
 
auth required pam_userdb.so db=/etc/vsftpd/vuser_passwd 
account required pam_userdb.so db=/etc/vsftpd/vuser_passwd 
 
#���������û������ļ� 
mkdir /etc/vsftpd/vuser_conf/ 
#�ļ�������vuser_passwd.txt������˻�������������������Ч 
vi /etc/vsftpd/vuser_conf/test 
 
#�����û���Ŀ¼,����ʵ������޸� 
local_root=/data/ftp 
write_enable=YES 
anon_umask=022 
anon_world_readable_only=NO 
anon_upload_enable=YES 
anon_mkdir_write_enable=YES 
anon_other_write_enable=YES 
---------------Nginx-------------------------------------------------
������/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
������/usr/local/nginx/sbin/nginx -s  reload
ֹͣ��ps�Cef | grepnginx���鿴���̺ţ�
kill -9 �����̺�
kill -9 �ӽ��̺ţ������ж����
---------------Firewalld-------------------------------------------------
systemctl status firewalld	�鿴����ǽ״̬��
systemctl stop firewalld	��ʱ�رշ���ǽ����������Ժ󣬷���ǽ�Զ�������
systemctl disable firewalld ���ùرշ���ǽ��������󣬷���ǽ�����Զ�������
systemctl enable firewalld �򿪷���ǽ���
�鿴�������ķ����б�systemctl list-unit-files|grep enabled
----------------------------------------------------------------

----------------------------------------------------------------