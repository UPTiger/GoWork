ActiceMQ安装

启动、访问、查看状态和停止activemq服务

```
# ./activemq/bin/activemq start
# wget 192.168.2.137:8161
# ./activemq/bin/activemq status
# ./activemq/bin/activemq stop
```

activemq注册为系统服务，开启开机自启

------

1.建立软连接

```
# ln -s /usr/local/activemq/bin/activemq /etc/init.d/activemqd
```

2.注册为系统服务

```
# vi /etc/init.d/activemqd
```

- 添加下面内容到/etc/init.d/activemq脚本

```
# chkconfig: 345 63 37
# description: Auto start ActiveMQ
JAVA_HOME=/usr/local/jdk1.8.0_144
JAVA_CMD=java1234
```

**/usr/java/jdk1.8.0_171/bin/env增加ACTIVEMQ_HOME,JAVA_HOME 

**echo $PATH

** 增加PATH=    

​	export PATH 代替JAVA_HOME

***重复第一步

3.开启开机自启

```
# chkconfig activemq on
# reboot
```

4.可以以系统服务的方式启动、查看状态和停止服务

```
# service activemq start
# service activemq status
# service activemq stop
```

------





**[html]** [view plain](https://blog.csdn.net/zbw18297786698/article/details/52994612#) [copy](https://blog.csdn.net/zbw18297786698/article/details/52994612#)

1. <!-- 添加访问ActiveMQ的账号密码 -->  
2. ​        <plugins>  
3. ​            <simpleAuthenticationPlugin>  
4. ​                <users>  
5. ​                    <authenticationUser username="zhangsan" password="123" groups="users,admins"/>  
6. ​                </users>  
7. ​            </simpleAuthenticationPlugin>  
8. ​        </plugins>  
9. ![1525348705683](C:\Users\M06\AppData\Local\Temp\1525348705683.png)