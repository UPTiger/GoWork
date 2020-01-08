yum install lsof
lsof 的常用命令：
lsof -i:(端口号)
lsof -i:3306 （3306默认是mysql服务器的端口）
lsof -i:3690 （3690默认是svn服务端的端口）
用 lsof | grep deleted  命令列出现在被删除但依旧使用的文件;

系统文件占用的空间可以查明：
du -sh --max-depth=1 ./
但有30G空间被不明占用。
使用losf | grep deleted 命令查看有大量被删除的文件，其中Tomcat日志文件占用量比较大。
重启Tomcat后这部分空间被释放
============================================================