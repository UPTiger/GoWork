



--------------tt-----------------

2018-07-16 10:08:35,890 [myid:3] - WARN  [WorkerSender[myid=3]:QuorumCnxManager@584] - Cannot open channel to 2 at election address /192.168.31.8:2999
java.net.NoRouteToHostException: 没有到主机的路由 (Host unreachable)

使用网络工具进行连接时出现10060错误码。

查看iptable显示没有Iptable

[root@localhost zookeeper-3.4.12]# sudo service iptables stop
Redirecting to /bin/systemctl stop iptables.service
Failed to stop iptables.service: Unit iptables.service not loaded.
[root@localhost zookeeper-3.4.12]# service iptable status
Redirecting to /bin/systemctl status iptable.service
Unit iptable.service could not be found.

查看fireware

[root@localhost zookeeper-3.4.12]# firewall-cmd --state
running
[root@localhost zookeeper-3.4.12]# systemctl status firewalld.service
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since 一 2018-07-16 09:10:57 CST; 1h 17min ago
     Docs: man:firewalld(1)
 Main PID: 715 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─715 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

说明防火墙是打开的

关闭并禁止防业墙

[root@localhost zookeeper-3.4.12]# systemctl stop firewalld.service
[root@localhost zookeeper-3.4.12]# systemctl disable firewalld.service
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
Removed symlink /etc/systemd/system/basic.target.wants/firewalld.service.



开启端口

```
firewall-cmd --zone=public --add-port=80/tcp --permanent
```
