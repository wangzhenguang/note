[TOC]


### centos7关闭默认防火墙，启用ipatables

```
#停止firewall
systemctl stop firewalld.service 
#禁止firewall开机启动
systemctl disable firewalld.service

#设置ipatables service
yum -y install iptables-service

vi /ect/sysconfig/iptables
#添加规则
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT

#保存退出
systemctl restart iptables.service
#设置防火墙开机启动
systemctl enable iptables.service


```

### centos 7最小化版本上如何启动ifconfig
> ip addr也可查看
```
    yum provides ifconfig # 查找提供的工具包
    
    yum install net-tools #安装工具包

```

#### centos 7防火墙相关命令

```
# 查看已经开放的端口
firewall-cmd --list-ports

# 查看防火墙状态
service firewalld status

# 开启防火墙
service firewalld start 
systemctl start firewalld.service

# 关闭防火墙
service firewalld stop
systemctl st
op firewalld.service

# 重启防火墙
systemctl restart firewalld.service
service firewalld restart

# 禁止防火墙自启
systemctl disable firewalld.service

# 设置防火墙开机自启
systemctl enable firewalld




# 开启端口
firewall-cmd --zone=public --add-port=80/tcp --permanent
# 命令含义：
–zone #作用域
–add-port=80/tcp #添加端口，格式为：端口/通讯协议
–permanent #永久生效，没有此参数重启后失效





```