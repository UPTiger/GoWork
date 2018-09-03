集群配置：

1. <clients>     
2.   <client host="s252" weight="2" maxusers="800">     
3.       <ip value="10.0.0.252"></ip>     
4.   </client>     
5.   <client host="gserver135" weight="1" maxusers="500">     
6.       <ip value="10.0.0.135"></ip>     
7.   </client>     
8. </clients>   

以上就是全部配置了，当然tsung集群的所有机器上都装有tsung，但是只需要在一台作为控制的机器上配置tsung.xml就行了，其它机器只要满足无密码提示的ssh登陆条件就ok，然后在控制机器上运行

tsung start  