---------------MySQL-------------------------------------------------
#�Զ�������1�����޸������ļ�����mysql�����ò�����������������
vim my.cnf
expire_logs_days = 7 // ��ʾ��־����7�죬����7��������Ϊ���ڵ� 
# mysql -u root -p > show binary logs; > show variables like '%log%'; > set global expire_logs_days = 7;

#�ֶ�������2�����Ƽ���
���û�����Ӹ��ƣ�����ͨ������������������ݿ���־�����֮ǰ����־�ļ���
reset master
 
����������ڸ��ƹ�ϵ��Ӧ��ͨ�� PURGE ���������� bin-log ��־���﷨���£�
 
# mysql -u root -p
> purge master logs to 'mysql-bin.010��; //���mysql-bin.010��־
> purge master logs before '2016-02-28 13:00:00'; //���2016-02-28 13:00:00ǰ����־
> purge master logs before date_sub(now(), interval 3 day); //���3��ǰ��bin��־
 
ע�⣬��Ҫ�����ֶ�ȥɾ��binlog���ᵼ��binlog.index����ʵ���ڵ�binlog��ƥ�䣬������expire_logs_dayʧЧ
	-------
#PURGE MASTER LOGS BEFORE DATE_SUB(CURRENT_DATE,INTERVAL 7 DAY);

show slave status
hknavi-relay-bin

---------------Nginx-------------------------------------------------
������/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
������/usr/local/nginx/sbin/nginx -s  reload
ֹͣ��ps�Cef | grepnginx���鿴���̺ţ�
kill -9 �����̺�
kill -9 �ӽ��̺ţ������ж����



----------------------------------------------------------------