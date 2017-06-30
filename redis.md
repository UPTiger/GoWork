http://download.redis.io/releases/redis-3.2.9.tar.gz


wget http://download.redis.io/releases/redis-3.2.9.tar.gz
tar -zxvf redis-3.0.7.tar.gz
编译：
make  
运行：
/src/redis-server
运行成功如下图：
将redis做成一个服务：
修改redis.conf,将后台运行选项打开

# By default Redis does not run as a daemon. Use 'yes' if you need it.
# Note that Redis will write a pid file in /var/run/redis.pid when daemonized.
daemonize yes
编写脚本，vim /etc/init.d/redis:
 

# chkconfig: 2345 10 90
# description: Start and Stop redis
  
REDISPORT=6379 #实际环境而定
EXEC=/root/redis-3.0.7/src/redis-server #实际环境而定
REDIS_CLI=/root/redis-3.0.7/src/redis-cli #实际环境而定
  
PIDFILE=/var/run/redis.pid
CONF="/root/redis-3.0.7/redis.conf" #实际环境而定
  
case "$1" in
        start)
                if [ -f $PIDFILE ]
                then
                        echo "$PIDFILE exists, process is already running or crashed."
                else
                        echo "Starting Redis server..."
                        $EXEC $CONF
                fi
                if [ "$?"="0" ]
                then
                        echo "Redis is running..."
                fi
                ;;
        stop)
                if [ ! -f $PIDFILE ]
                then
                        echo "$PIDFILE exists, process is not running."
                else
                        PID=$(cat $PIDFILE)
                        echo "Stopping..."
                        $REDIS_CLI -p $REDISPORT SHUTDOWN
                        while [ -x $PIDFILE ]
                        do
                                echo "Waiting for Redis to shutdown..."
                                sleep 1
                        done
                        echo "Redis stopped"
                fi
                ;;
        restart|force-reload)
                ${0} stop
                ${0} start
                ;;
        *)
                echo "Usage: /etc/init.d/redis {start|stop|restart|force-reload}" >&2
                exit 1
esac
　　

 
 
运行效果如下图：
 