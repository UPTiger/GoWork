http://www.blogjava.net/yongboy/archive/2016/08/08/431498.html
官网：http://tsung.erlang-projects.org/

https://www.cnblogs.com/tsbc/p/4272974.html

https://www.cnblogs.com/lemon-flm/p/8093684.html

https://www.cnblogs.com/tsbc/p/4272974.html

在线手册：http://tsung.readthedocs.io/en/latest/index.html
--------------------------------------------------------------
http://www.awaimai.com/628.html 查看结果

高级篇：http://www.blogjava.net/yongboy/archive/2016/07/30/431396.html

--------------------------------------------------------------
     Tsung的官网是：http://tsung.erlang-projects.org/ 
     最新的版本是1.6.0，去年9月份更新的，下载地址是：http://tsung.erlang-projects.org/dist/tsung-1.6.0.tar.gz

一、安装环境

```
yum install gcc -y  
yum install perl -y  
yum install unixODBC
yum install unixODBC-devel
yum install -y ncurses-devel
yum install -y gnuplotgdlibpngzlib
yum install perl-ExtUtils-Embed -y
yum -y install perl-Time-HiRes 
yum install openssl-devel
yum install gcc-c++
yum -y install perl-Time-HiRes perl-Time-HiRes-Value perl-File-Tail  rrdtool rrdtool-perl

yum -y install make gcc gcc-c++ kernel-devel m4 ncurses-devel openssl-devel unixODBC-devel libtool libtool-ltdl-devel
```

二、安装erlang

```
# cd /usr/local/src
# http://www.erlang.org/download/otp_src_18.1.tar.gz
# tar zvxf otp_src_18.1.tar.gz
# cd otp_src_18.1
# ./configure --prefix=/usr/local/erlang --without-javac
# make
# make install
vi /etc/profile
export PATH=$PATH:/usr/local/erlang/bin/
或者
ln -s /usr/local/erlang/bin/erl /usr/local/bin/erl 
ln -s /usr/local/erlang/bin/erlc /usr/local/bin/erlc



# source /etc/profile
```

报错：

configure: error: No curses library functions found

configure: error: /bin/sh '/root/otp/erts/configure' failed for erts

```
# yum -y install ncurses-devel
# ./configure --prefix=/home/erlang --without-javac
```

三、安装Tsung：

```
# cd /usr/local/src
1. wget http://tsung.erlang-projects.org/dist/tsung-1.7.0.tar.gz
2. tar -zxvf tsung-1.7.0.tar.gz 
3. cd tsung-1.7.0/
4. ./configure --prefix=/usr/local
5. make && make install

```

 说明安装成功。
注意：tsung是一个erlang开发的测试软件，如果遇到任何问题，请检查你的erlang是否正常运作。关于erlang的安装，请参照：http://www.cnblogs.com/lsm19870508/p/5365019.html中erlang部分进行环境配置。

四、安装perl Template,用于生成报告模版：

```
1.yum install perl-modules
2.wget http://cpan.org/modules/by-module/Template/Template-Toolkit-2.26.tar.gz
tar -zxvf Template-Toolkit-2.26.tar.gz
3.cd Template-Toolkit-2.26/
4.perl Makefile.PL
5.make
6.make test
7.sudo make install   

```



五、安装gnuplot ：

```
yum install gnuplot
```

 

http://www.awaimai.com/628.html
**http://blog.csdn.net/jeepxiaozi/article/details/42784201

1. \# vim /etc/profile   

2. export PATH=$PATH:$JAVA_HOME/bin:/usr/local/erlang/bin:/usr/local/tsung/bin:/usr/local/nginx/sbin:$PATH(修改自己实际变量)  

3. :x保存,退出  

4. \# source /etc/profile   

5. 不报错则成功  

6. \# tsung -v   

7. \# erl -v  

8. 测试  

   ------------------------------------------------------------------

1、静态加载用户：
         <user session="session_name" start_time="10" unit="second"></user>
        产生一个用户，该用户执行名为session_name的session，10s后执行。
    2、随机加载用户
      <arrivalphase phase="1" duration="3" unit="second">
         <users maxnumber="5" arrivalrate="1" unit="second"/>
      </arrivalphase>
       其中产生用户速度配置有两种：1、arrivalrate:每s产生多少用户
                                                   2、interarrival:每多少s产生一个用户
       注：可配置多个arrivalphase，按照phase排序来顺序执行。



tsung -f raw.xml start

 /usr/local/tsung/lib/tsung/bin/tsung_stats.pl



**netstat -n | awk '/^tcp/{++S[$NF]}END{for(a in S) print a,S[a]}'**



-----------------------------------------------------------------------------------------------------------

## <!-- 计划加载用户-->

              <arrivalphase> ：阶段相位，可配置多个阶段

                     phase : 阶段序号

                     duration : 加载时间数

                     unit : 时间单位

                     <users> : 用户加载速度

                            interarrival/arrivalrate : 间隔interarrival unit加载一个用户/间隔1 unit加载arrivalrate个用户

                            unit : 时间单位

                            maxnumber : 用户最大加载数

                           

       例：<arrivalphase phase="1" duration="20" unit="second">

                     <users maxnumber="1500" interarrival="0.1" unit="second"></users>

              </arrivalphase>

              <arrivalphase phase="2" duration="10" unit="second">

                     <users arrivalrate="15" unit="second"></users>

              </arrivalphase>

              <arrivalphase phase="3" duration="10" unit="minute">

                     <users  maxnumber="2500"  interarrival="2" unit="second"></users>

              </arrivalphase>

              上述例子中，运行测试以后，会在第一阶段(20s)每间隔0.1s加载一个用户，最大加载1500个用户。

              第二阶段(10s)没间隔1s加载15个用户。

              第三阶段(10m)没间隔2s加载1个用户，最大加载数为2500。

              3个阶段是按顺序加载的，总加载时间是 20s + 10s + 10m

             

       注： 用户被加载后会立即执行测试场景，并不是在加载全部用户后才开始测试场景。

              这在jabber服务端测试中，有涉及到用户之间发送消息的请求时应该注意保持用户在线，

              否则将有可能部分用户接收不到消息

        -->

       <load>

              <arrivalphase phase="1" duration="10" unit="second">

                     <users interarrival="0.1" unit="second"></users>

              </arrivalphase>

       </load>

 -------------------dd---------------------------------







 

 

 

 

