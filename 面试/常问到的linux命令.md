##### 农行笔试

* 解压缩.zip文件用什么命令gzip
* cp -f：强制复制
* mkdir命令创建父目录：mkdir -p
* /etc 这个目录用来存放系统管理所需要的配置文件和子目录 

##### less命令

  对文件或其它输出进行分页显示的工具
 这个命令常常和其他命令在一起用. 

##### ps -aux | grep xxx

  用来查看某个服务的进程 

##### du -h –max-depth=1 

  查看文件夹大小 

##### 磁盘空间

  df -h 

##### tail

  这个命令线上的实时日志，我们有时候需要看现在有没有刷错误日志，特别常用。 

  tail -f xxx.log | grep ERROR -C 10
 查看错误日志的前后10行 

  tail -10000 xxx.log
 查看日志的最后10000行 

##### free -m

  内存使用 

##### top

动态查看 cpu，内存使用，cpu -p [pid]查看指定进程

##### netstat

  往往某些服务启动不了，是所需端口被占用了，如果我们需要知道2809号端口的情况，我们可以这样，如下命令：
 netstat -pan|grep 2809 

  查看所有监听端口 

  netstat -antp
 查看所有已经建立的连接 

  netstat -s
 查看网络统计信息 

##### 环境变量

-    /etc/profile    
-    ~/.bashrc    
-    source ~/.bashrc   

##### wc

  统计数量
 比如统计线上这一天刷了多少错误日志:
 cat xxx.log | grep ERROR | wc -l 

##### find ./ -name  '*.sh'

  找到当前目录下以sh结尾的文件 

##### grep

  cat xxx.log | grep ERROR
 cat xxx.log | grep -v INFO 排除INFO日志