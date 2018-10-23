查看版本号：cat /etc/redhat-release

查看名称：hostnamectl

修改主机名称：hostnamectl set-hostname  xxxxx

查看主机名称： cat /etc/hostname 



------------------------------------------------------------

查看CPU信息（型号）
[root@AAA ~]# cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
        Intel(R) Xeon(R) CPU E5-2630 0 @ 2.30GHz

# 查看物理CPU个数
[root@AAA ~]# cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

# 查看每个物理CPU中core的个数(即核数)
[root@AAA ~]# cat /proc/cpuinfo| grep "cpu cores"| uniq
cpu cores    : 6

# 查看逻辑CPU的个数
[root@AAA ~]# cat /proc/cpuinfo| grep "processor"| wc -l

-----------热清除nohup.out-----------

第一种：cp /dev/null nohup.out
第二种：cat /dev/null > nohup.out

-----------nohup.out 不输出内容-----------

//只输出错误信息到日志文件
nohup ./program >/dev/null 2>log &
//什么信息也不要
nohup ./program >/dev/null 2>&1 &

0:表示标准输入
1:标准输出,在一般使用时，默认的是标准输出
2:标准错误信息输出



    public static final int START_DELIMITER_LEN = 2;