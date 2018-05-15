http://www.blogjava.net/yongboy/archive/2016/08/08/431498.html
官网：http://tsung.erlang-projects.org/
在线手册：http://tsung.readthedocs.io/en/latest/index.html
--------------------------------------------------------------
http://www.awaimai.com/628.html

--------------------------------------------------------------
     Tsung的官网是：http://tsung.erlang-projects.org/ 
     最新的版本是1.6.0，去年9月份更新的，下载地址是：http://tsung.erlang-projects.org/dist/tsung-1.6.0.tar.gz

安装Tsung：
1. wget http://tsung.erlang-projects.org/dist/tsung-1.6.0.tar.gz
2. tar -zxvf tsung-1.6.0.tar.gz 
3. cd tsung-1.6.0/
4. ./configure
5. make && make install

 说明安装成功。
注意：tsung是一个erlang开发的测试软件，如果遇到任何问题，请检查你的erlang是否正常运作。关于erlang的安装，请参照：http://www.cnblogs.com/lsm19870508/p/5365019.html中erlang部分进行环境配置。

安装perl Template,用于生成报告模版：
1.sudo apt-get install perl-modules
2.wget http://cpan.org/modules/by-module/Template/Template-Toolkit-2.27.tar.gz
	tar -zxvf Template-Toolkit-2.27.tar.gz
3.cd Template-Toolkit-2.27/
4.perl Makefile.PL
5.make
6.make test
7.sudo make install   

安装gnuplot ：
apt-get install gnuplot 
--------------------------------------------------------------
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







 

 

 

 

