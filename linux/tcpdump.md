# flex   
yum -y install flex  

# bison  
yum -y install bison

wget -c http://www.tcpdump.org/release/libpcap-1.9.0.tar.gz 
wget -c http://www.tcpdump.org/release/tcpdump-4.9.2.tar.gz
 
tar -zxvf libpcap-1.9.0.tar.gz    
cd libpcap-1.9.0   
./configure    
sudo make install    

cd ..    
tar -zxvf tcpdump-4.9.2.tar.gz    
cd tcpdump-4.9.2    
./configure    
sudo make install   

yum -y install bison

通过网卡eth1来监听端口80发出去的host包到192.168.109.8的报文
tcpdump -XvvennSs 0 -i eth0 tcp[20:2]=0x4745 or tcp[20:2]=0x4854 -w /tmp/capture.pcap
任意网卡目标是192.168.109.*的 80端口数据
/usr/local/sbin/tcpdump -i any  port 80 and dst host "192.168.109.*" -w /tmp/capture.pcap
加上源地址IP
tcpdump -i any -p -s 0 port 80 and dst host "192.168.109.*" and src host "10.70.32.**" -w /tmp/capture.pcap 
https://blog.csdn.net/xundh/article/details/77703853
https://www.cnblogs.com/ggjucheng/archive/2012/01/14/2322659.html
--------------------- 




http://www.mamicode.com/info-detail-2089175.html

http://www.jianshu.com/p/8d9accf1d2f1 

https://mp.weixin.qq.com/s?__biz=MzAxODI5ODMwOA==&mid=2666539134&idx=1&sn=5166f0aac718685382c0aa1cb5dbca45&scene=5&srcid=0527iHXDsFlkjBlkxHbM2S3E#rd 



