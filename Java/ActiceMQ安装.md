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

!/bin/sh

description: Auto start ActiveMQ

BEGIN INIT INFO

Provides:          activemq

Required-Start:    remote_fs network $syslog

Required-Stop:     remote_fs network $syslog

Default-Start:     2 3 4 5

Default-Stop:      0 6

chkconfig: 2345 64 36

Short-Description: Starts ActiveMQ

Description:       Starts ActiveMQ Message Broker Server

END INIT INFO

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/usr/java/jdk1.8.0_171/bin:/usr/java/jdk1.8.0_171/jre/bin:/root/bin

export PATH

JAVA_CMD=java1234

ACTICVEMQ_HOME=/usr/local/activemq-5.15.3

------------------------------------------------------------------------

Licensed to the Apache Software Foundation (ASF) under one or more

contributor license agreements.  See the NOTICE file distributed with

this work for additional information regarding copyright ownership.

The ASF licenses this file to You under the Apache License, Version 2.0

(the "License"); you may not use this file except in compliance with

-------------------------------------------------------------------------------------------------------------------------



activemq.xml 

**[html]** [view plain](https://blog.csdn.net/zbw18297786698/article/details/52994612#) [copy](https://blog.csdn.net/zbw18297786698/article/details/52994612#)

        <!-- destroy the spring context on shutdown to stop jetty -->
        <shutdownHooks>
            <bean xmlns="http://www.springframework.org/schema/beans" class="org.apache.activemq.hooks.SpringContextHook" />
        </shutdownHooks>
<!-- 添加访问ActiveMQ的账号密码 -->  

1. <!-- 添加访问ActiveMQ的账号密码 -->  
2. ​        <plugins>  
3. ​            <simpleAuthenticationPlugin>  
4. ​                <users>  
5. ​                    <authenticationUser username="hkadmin" password="hk667" groups="users,admins"/>  
6. ​                </users>  
7. ​            </simpleAuthenticationPlugin>  
8. ​        </plugins>  
9. ![1525348705683](C:\Users\M06\AppData\Local\Temp\1525348705683.png)

2.用户名密码文件为:credentials.properties 

```
## --------------------------------------------------------------------------- 
## Licensed to the Apache Software Foundation (ASF) under one or more 
## contributor license agreements. See the NOTICE file distributed with 
## this work for additional information regarding copyright ownership. 
## The ASF licenses this file to You under the Apache License, Version 2.0 
## (the "License"); you may not use this file except in compliance with 
## the License. You may obtain a copy of the License at 
## 
## http://www.apache.org/licenses/LICENSE-2.0 
## 
## Unless required by applicable law or agreed to in writing, software 
## distributed under the License is distributed on an "AS IS" BASIS, 
## WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
## See the License for the specific language governing permissions and 
## limitations under the License. 
## --------------------------------------------------------------------------- 

# Defines credentials that will be used by components (like web console) to access the broker 

activemq.username=system    # 用户名
activemq.password=manager   # 密码
guest.password=password
```
## 查看进程netstat -lnutp

61613、61614、61616