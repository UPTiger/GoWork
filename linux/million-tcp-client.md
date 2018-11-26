https://github.com/fireflyc/million-tcp-client

*百万级TCP测试工具
 参考《模拟百万级TCP并发》
 mkdir -p build
cd build && cmake ..
make
然后执行
./tcp-client 网卡名 本地指定的任意IP 目标服务器IP 目标服务器端口
./tcp-client em1 172.16.18.194 172.16.5.190 8000

 程序依赖libpcap和libnet请自行安装依赖ubuntu是执行`apt-get install libpcap-dev libnet-dev`

https://blog.csdn.net/u011001084/article/details/54089182?utm_source=blogxgwz18
--------------------------------------------------------
yum install -y cmake

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