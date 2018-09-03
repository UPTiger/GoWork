

1.查看linux状态：sestatus

2.临时关闭：

```
##设置SELinux 成为permissive模式
##setenforce 1 设置SELinux 成为enforcing模式
setenforce 0
```

3.永久关闭：

vi /etc/selinux/config

将SELINUX=enforcing改为SELINUX=disabled  设置后需要重启才能生效 

---------------------------s-------------------------------------------------

sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config

grep SELINUX=disabled /etc/selinux/config

setenforce 0

---------------------------s-------------------------------------------------



| systemctl stop firewalld.service    |
| ----------------------------------- |
| systemctl disable firewalld.service |
| systemctl status firewalld.service  |

----------------------------------s---------------------------------------------------

查询防火墙状态    :    [root@localhost ~]# service   iptables status

 停止防火墙   :    [root@localhost ~]# service   iptables stop 

启动防火墙   :    [root@localhost ~]# service   iptables start 

重启防火墙   :    [root@localhost ~]# service   iptables restart 

永久关闭防火墙    :    [root@localhost ~]# chkconfig   iptables off

 永久关闭后启用    :    [root@localhost ~]# chkconfig   iptables on 



-----------------------------------s--