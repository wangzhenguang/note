[TOC]

### zookeeper安装

``` 
# 复制一份配置文件
cp zoo_sample.conf zoo.conf

# 修改配置文件
vi zoo.conf
dataDir=/data/zookeeper # 数据工作目录
# 写入集群 名称pid ip leader follower通信端口 投票端口  
server.1=ip:2888:3888
server.2=ip:2888:3888
server.3=ip:2888:3888

# 将pid写入工作目录中
echo 1 > myid

# 复制app文件到其他机器并修改pid

# 最后查看防火墙端口


bin/zkServer.sh status

# 命令连接zk客户端
zkCli.sh -server ip:port(2181)
```

###  ssh 启动zookeeper
```
#!/bin/sh
for i in 1 2
do
ssh server$i "source /etc/profile;/.../zookeeper/bin/zkServer.sh start"
done 

```


>export 定义的变量，会对自己所在的shell进程及其子进程生效
b=1 定义的变量，只对自己所在的shell进程生效
在script.sh中定义的变量，在当前登录的shell进程中 source script.sh时 脚本中定义的变量也会进入当前登录的进程里



zookeep api
---


































