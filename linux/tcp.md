netstat -nat|awk '{print awk $NF}'|sort|uniq -c|sort -n
lsof -i:8101
netstat -nat|grep -i "8101"|wc -l
ss -t state fin-wait-1
**ss -s
cp /dev/null nohup.out
--------------------------------------------------------
netstat -nat|awk '{print awk $NF}'|sort|uniq -c|sort -n
      1 established)
      1 State
      2 SYN_RECV
     27 LISTEN
     36 TIME_WAIT
    578 FIN_WAIT1
   2143 ESTABLISHED

netstat -st

高人：
https://blog.csdn.net/dog250/article/details/81697403
如果主动断开端调用了close关掉了进程，它会进入FIN_WAIT1状态，如果接收端的接收窗口呈现关闭状态(零窗口)，此时它会不断发送零窗口探测包。发送多少次呢？有两种实现： 
低版本内核(至少社区3.10及以下)：永久尝试，如果探测ACK每次都返回，则没完没了。
高版本内核(至少社区4.6及以上)：限制尝试tcp_orphan_retries次，不管是否收到探测ACK。 
--------------------- 
作者：dog250 

https://blog.csdn.net/bytxl/article/details/46544169
--------------------------------------------------------
# 防止linux出现大量 FIN_WAIT1,提高性能
https://blog.csdn.https://blog.csdn.net/talent210/article/details/65441819net/talent210/article/details/65441819
fin_wait1状态过多。fin_wait1状态是在server端主动要求关闭tcp连接，并且主动发送fin以后，等待client端回复ack时候的状态。fin_wait1的产生原因有很多，需要结合netstat的状态来分析。
netstat -nat|awk '{print awk $NF}'|sort|uniq -c|sort -n
上面的命令可以帮助分析哪种tcp状态数量异常
netstat -nat|grep ":8101"|awk '{print $5}' |awk -F: '{print $1}' | sort| uniq -c|sort -n

## [服务器大量的fin_wait1 状态长时间存在原因分析](https://www.cnblogs.com/10087622blog/p/7281883.html)
https://www.cnblogs.com/10087622blog/p/7281883.html

&&&参考下面文章修改
** https://blog.csdn.net/bytxl/article/details/46437363
tcp_orphan_retries ：INTEGER
默认值是7
在近端关闭TCP连接﹐没有得到确认之前，要进行多少次重试。（参见官方文档）默认值是7个﹐相当于 50秒 - 16分钟﹐视 RTO 而定。（有说法是：这个参数控制主动关闭段发送FIN，没有收到回应，重复发送FIN的次数。由于发送了FIN，处于FIN_WAIT_1状态。）如果您的系统是负载很大的web服务器﹐那么也许需要降低该值﹐这类 sockets 可能会耗费大量的资源。另外参的考 tcp_max_orphans 。(事实上做NAT的时候,降低该值也是好处显著的,我本人的网络环境中降低该值为3)

#最近测试发现socket会永远停留在FIN_WAIT_1这个状态，而不会重传超时RESET后closed。
--------------------- 
作者：bytxl 
来源：CSDN 
原文：https://blog.csdn.net/bytxl/article/details/46437363 
版权声明：本文为博主原创文章，转载请附上博文链接！
--------------------------------------------------------
ss -s
[root@localhost ~]# ss -t state fin-wait-1
ss -tn state fin-wait-1|grep -v Recv-Q|awk 'BEGIN{sum=0}{sum+=$2}END{printf "%.2f\n",sum}'

也就是先设置fin-wait-1状态，然后再发fin包，
理论上说，fin-wait-1的状态应该很难看到才对，因为只要收到对方的ack，就应该迁移到fin-wait-2了，如果收到对方的fin，则应该迁移到
closing状态。但是该服务器上就很多。难道没有收到ack或者fin包？抓包验证下：
进而抓包， tcpdump -i eth0  "tcp[tcpflags] & (tcp-fin) != 0" -s 90 -p -n

根据报文，看到了fin包的重传，而有时候刚好遇到对端通告的窗口为0，所以进入fin-wait-1后呆的时间就比较长。
-----------------------------
$ netstat -s | more
$ netstat -st #tcp
$ netstat -su #udp
$ netstat -sw #raw
$ netstat -nap
$ netstat -naptu | more
----------------------------- 
比如我看到fin-wait-1之后，我可以抓之后的交互包，

[root@localhost ~]# ss -to state Fin-wait-1 |head -200 |grep -i 10.96.152.219
0 387543 服务器ip 客户端ip 1 timer:(persist,1.140ms,0)
在这里我们看到了坚持定时器，通过脚本，我获取了fin-wait-1的链路，有的定时器的长度退避之后，达到了120s，然后一直120s进行探测。


在这里说一下脚本的思路：

\#!/bin/bash
while [ 1 ]
do
ss -tn state Fin-wait-1 src 服务器ip |awk '{print $4}'|grep -v 服务器端口1|grep -v 服务器端口2|sort -n >caq_old
ss -tn state Fin-wait-1 src 服务器ip |awk '{print $4}'|grep -v 服务器端口1|grep -v 服务器端口1|sort -n >caq_new
diff caq_old caq_new|grep '>'|awk '{print $2}' >diff_caq
sleep 1

\###1s之后，重新获取fin-wait-1状态的链路情况，如果新增的链路还存在，那么说明该链路至少存活超过1s，主要是抓坚持定时器比较长的情况
ss -tn state Fin-wait-1 src 服务器ip |awk '{print $4}'|grep -v 服务器端口1|grep -v  服务器端口2|sort -n >caq_new
while read line
do
grep -q $line caq_new
if [ $? -eq 0 ]
then
\###live for 1 second
echo $line
exit
else
continue;
fi
done < diff_caq

done

 

然后抓包：

./get_fin.sh |awk -F '[:]' '{print $2}'|xargs -t tcpdump -i eth1 -s 90 -c 200 -p -n port

可以看到部分抓包：

20:34:26.355115 IP 服务器ip和端口 > 客户端ip和端口: Flags [.], ack 785942062, win 123, length 0
20:34:26.358210 IP 客户端ip和端口 >服务器ip和端口: Flags [.], ack 1, win 0, length 0
20:34:28.159117 IP服务器ip和端口 > 客户端ip和端口: Flags [.], ack 1, win 123, length 0
20:34:28.162169 IP 客户端ip和端口 >服务器ip和端口: Flags [.], ack 1, win 0, length 0
20:34:31.761502 IP 服务器ip和端口 > 客户端ip和端口: Flags [.], ack 1, win 123, length 0
20:34:31.765416 IP 客户端ip和端口 > 服务器ip和端口: Flags [.], ack 1, win 0, length 0



netstat -nat|awk '{print awk $NF}'|sort|uniq -c|sort -n 上面的命令可以帮助分析哪种tcp状态数量异常 netstat -nat|grep ":80"|awk '{print $5}' |awk -F: '{print $1}' | sort| uniq -c|sort -n



ps -ef|grep httpd|wc -l命令
#ps -ef|grep httpd|wc -l
1388
统计httpd进程数，连个请求会启动一个进程，使用于Apache服务器。
表示Apache能够处理1388个并发请求，这个值Apache可根据负载情况自动调整，我这组服务器中每台的峰值曾达到过2002。
netstat -nat|grep -i "80"|wc -l命令
#netstat -nat|grep -i "80"|wc -l
4341
netstat -an会打印系统当前网络链接状态，而grep -i “80″是用来提取与80端口有关的连接的, wc -l进行连接数统计。
最终返回的数字就是当前所有80端口的请求总数。
netstat -na|grep ESTABLISHED|wc -l命令
#netstat -na|grep ESTABLISHED|wc -l
376
netstat -an会打印系统当前网络链接状态，而grep ESTABLISHED 提取出已建立连接的信息。 然后wc -l统计。
最终返回的数字就是当前所有80端口的已建立连接的总数。
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'命令
#netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
FIN_WAIT_1 286
FIN_WAIT_2 960
SYN_SENT 3
LAST_ACK 32
CLOSING 1
CLOSED 36
SYN_RCVD 144
TIME_WAIT 2520
ESTABLISHED 352
这条语句是在张宴那边看到，据说是从新浪互动社区事业部技术总监王老大那儿获得的，非常不错。返回参数的说明如下：
SYN_RECV表示正在等待处理的请求数；
ESTABLISHED表示正常数据传输状态；
TIME_WAIT表示处理完毕，等待超时结束的请求数。
Tag: 调优, 性能, 优化

kimi at 2008-09-01 03:01:08 in Apache, Linux
解决linux下大量的time_wait问题
vi /etc/sysctl.conf
编辑/etc/sysctl.conf文件，增加三行：
引用
fs.file-max = 1048576
net.ipv4.tcp_mem = 786432 2097152 3145728
net.ipv4.tcp_rmem = 4096 4096 16777216
net.ipv4.tcp_wmem = 4096 4096 16777216
net.ipv4.tcp_fin_timeout = 30
net.ipv4.tcp_keepalive_time = 1200
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 1
net.ipv4.ip_local_port_range = 1024    65500
net.ipv4.tcp_max_syn_backlog = 8192
net.ipv4.tcp_max_tw_buckets = 5000 
net.ipv4.route.gc_timeout = 100 
net.ipv4.tcp_syn_retries = 1 
net.ipv4.tcp_synack_retries = 1
说明：
net.ipv4.tcp_syncookies = 1 表示开启SYN Cookies。当出现SYN等待队列溢出时，启用cookies来处理，可防范少量SYN攻击，默认为0，表示关闭；
net.ipv4.tcp_tw_reuse = 1 表示开启重用。允许将TIME-WAIT sockets重新用于新的TCP连接，默认为0，表示关闭；
net.ipv4.tcp_tw_recycle = 1 表示开启TCP连接中TIME-WAIT sockets的快速回收，默认为0，表示关闭。
再执行以下命令，让修改结果立即生效：
引用 
/sbin/sysctl -p

用以下语句看了一下服务器的TCP状态：

引用 
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
返回结果如下：
ESTABLISHED 1423
FIN_WAIT1 1
FIN_WAIT2 262
SYN_SENT 1
TIME_WAIT 962
效果：处于TIME_WAIT状态的sockets从原来的10000多减少到1000左右。处于SYN_RECV等待处理状态的sockets为0，原来的为50～300。

通过上面的设置以后，你可能会发现一个新的问题，就是netstat时可能会出现这样的警告：

引用 
warning, got duplicate tcp line
这正是上面允许tcp复用产生的警告，不过这不算是什么问题，总比不允许复用而给服务器带来很大的负载合算的多
尽管如此，还是有解决办法的：
1、 安装rpm包：
[root@root2 opt]# rpm -Uvh net-tools-1.60-62.1.x86_64.rpm 
Preparing...                ########################################### [100%]
  1:net-tools              ########################################### [100%]
[root@root2 opt]#
对于下载的是源码的rpm则需要使用以下方法安装：
2、 安装rpm源码包方法：
a)         安装src.rpm:
# [root@root1 opt]# rpm -i net-tools-1.60-62.1.src.rpm
……
b)        制作rpm安装包：
[root@root1 opt]# cd /usr/src/redhat/SPECS/
[root@root1 SPECS]# rpmbuild -bb net-tools.spec
c)        rpm包的升级安装：
[root@root1 SPECS]# pwd
/usr/src/redhat/SPECS
[root@root1 SPECS]# cd ../RPMS/x86_64/
[root@root1 x86_64]# rpm -Uvh net-tools-1.60-62.1.x86_64.rpm
3、 再使用netstat来检查时系统正常： 
说明：
　　net.ipv4.tcp_syncookies = 1 表示开启SYN Cookies。当出现SYN等待队列溢出时，启用cookies来处理，可防范少量SYN攻击，默认为0，表示关闭；
　　net.ipv4.tcp_tw_reuse = 1 表示开启重用。允许将TIME-WAIT sockets重新用于新的TCP连接，默认为0，表示关闭；
　　net.ipv4.tcp_tw_recycle = 1 表示开启TCP连接中TIME-WAIT sockets的快速回收，默认为0，表示关闭。
　　net.ipv4.tcp_fin_timeout = 30 表示如果套接字由本端要求关闭，这个参数决定了它保持在FIN-WAIT-2状态的时间。
　　net.ipv4.tcp_keepalive_time = 1200 表示当keepalive起用的时候，TCP发送keepalive消息的频度。缺省是2小时，改为20分钟。
　　net.ipv4.ip_local_port_range = 1024    65000 表示用于向外连接的端口范围。缺省情况下很小：32768到61000，改为1024到65000。
　　net.ipv4.tcp_max_syn_backlog = 8192 表示SYN队列的长度，默认为1024，加大队列长度为8192，可以容纳更多等待连接的网络连接数。 
　　net.ipv4.tcp_max_tw_buckets = 5000 表示系统同时保持TIME_WAIT套接字的最大数量，如果超过这个数字，TIME_WAIT套接字将立刻被清除并打印警告信息。默认为180000，改为5000。对于Apache、Nginx等服务器，上几行的参数可以很好地减少TIME_WAIT套接字数量，但是对于Squid，效果却不大。此项参数可以控制TIME_WAIT套接字的最大数量，避免Squid服务器被大量的TIME_WAIT套接字拖死。 
  net.ipv4.route.gc_timeout = 100  路由缓存刷新频率， 当一个路由失败后多长时间跳到另一个
默认是300 
  net.ipv4.tcp_syn_retries = 1  对于一个新建连接，内核要发送多少个 SYN 连接请求才决定放弃。不应该大于255，默认值是5，对应于180秒左右。 

netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'



