JDK老版本下载路径

http://www.oracle.com/technetwork/java/javase/archive-139210.html



手动解压JDK的压缩包，然后设置环境变量

1.在/usr/目录下创建java目录

[root@localhost ~]# mkdir/usr/java
[root@localhost ~]# cd /usr/java

2.下载jdk,然后解压

[root@localhost java]# curl -O http://download.[Oracle](https://www.linuxidc.com/topicnews.aspx?tid=12).com/otn-pub/java/jdk/7u80-b15/jdk-7u80-linux-x64.tar.gz 
[root@localhost java]# tar -zxvf jdk-7u80-linux-x64.tar.gz

3.设置环境变量

[root@localhost java]# vi /etc/profile

在profile中添加如下内容:

\#set java environment
JAVA_HOME=/usr/java/jdk1.7.0_80
JRE_HOME=/usr/java/jdk1.7.0_80/jre
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH

让修改生效:

[root@localhost java]# source /etc/profile

4.验证JDK有效性

[root@localhost java]# java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b02, mixed mode)