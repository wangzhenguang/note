[TOC]

### redis的安装

```
tar -zxf redis.tar
make 

# 安装
make install PREFIX=/redis

cd redis
#前端启动redis
redis-server

##修改redis conf文件
daemonize yes
#启动redis
./redis-server redis.conf 

# 链接服务
./redis-cli
./redis-cli -h ip地址 -p port

 

```

### redis集群搭建

```
# 修改redis.conf
vi redis.conf
daemonize yes
prot 1234
cluster-enable yes

#启动所有的redis
redis-server redis.conf

```
##### 集群环境搭建
```
yum install ruby

# 下载redis.gem 安装
gem install redis.gem

cd redis/src

cp *.rb /usr/local/redis-cluster/

./redis-trib.rb create --replicas 1 ip:port ip:port...

```

#### 连接redis

```
# 连接redis集群 -c必须加
redis-cli -p port -c
#查看集群信息
cluster info 

```

