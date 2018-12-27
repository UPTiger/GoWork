cd /home/developer/module/zookeeper-3.4.13/ && bin/zkServer.sh start
cd /home/developer/module/kafka_2.11-2.0.0/ && bin/kafka-server-start.sh config/server.properties
[root@server6 zookeeper]$ bin/zkServer.sh start
*启动集群依次在server6、tsung8、tsung9节点上启动kafka
[root@server6 kafka]$ bin/kafka-server-start.sh config/server.properties &
[root@tsung8 kafka]$ bin/kafka-server-start.sh config/server.properties &
[root@tsung9 kafka]$ bin/kafka-server-start.sh config/server.properties &

*关闭集群
[root@server6 kafka]$ bin/kafka-server-stop.sh stop
[root@tsung8 kafka]$ bin/kafka-server-stop.sh stop
[root@tsung9 kafka]$ bin/kafka-server-stop.sh stop

丢包 时延
https://blog.csdn.net/steve_frank/article/details/81838513

*查看当前服务器中的所有topic 
[root@server6 kafka]$ bin/kafka-topics.sh --zookeeper server6:2181 --**list** 
*创建topic
[root@server6 kafka]$ bin/kafka-topics.sh --zookeeper server6:2181 --create --replication-factor 3 --partitions 1 --topic first 
*删除topic
[root@server6 kafka]$ bin/kafka-topics.sh --zookeeper server6:2181 --delete --topic first
*发送消息
[root@server6 kafka]$ bin/kafka-console-producer.sh --broker-list server6:9092 --topic first
\>hello world
\>atguigu  atguigu
*消费消息

2.0版本：
bin/kafka-console-consumer.sh --bootstrap-server r730-104:9092 --topic test --from-beginning

1.2版本：
[root@tsung8 kafka]$ bin/kafka-console-consumer.sh --zookeeper server6:2181 --from-beginning --topic first

*查看某个Topic的详情
[root@server6 kafka]$ bin/kafka-topics.sh --zookeeper server6:2181 --describe --topic first 

bin/kafka-console-producer.sh --broker-list server6:9092 --topic first
bin/kafka-console-producer.sh --broker-list server6:9092 --topic worldcpu
bin/kafka-console-consumer.sh --zookeeper server6:2181 --from-beginning --topic worldcpu

**ERROR Error when sending message to topic test with key: null, value: 6 bytes with error: (org.apache.kafka.clients.producer.internals.ErrorLoggingCallback)** 

vi config/server.properties

listeners监听注释打开并指定当前机器IP与端口

具体参见[kafka不能远程连接问题](http://onwise.xyz/2017/06/14/kafka%E4%B8%8D%E8%83%BD%E8%BF%9C%E7%A8%8B%E8%BF%9E%E6%8E%A5%E9%97%AE%E9%A2%98/)

listeners=PLAINTEXT://:9092
advertised.listeners=PLAINTEXT://server6:9092
metadata.broker.list=server6:9092
zookeeper.connect=server6:2181,tsung8:2181,tsung9:2181

其它几个文件也使用server6与/etc/hosts中相对应

Retrying leaderEpoch request for partition first-0 as the leader reported an error: NOT_LEADER_FOR_PARTITION (kafka.server.ReplicaFetcherThread)