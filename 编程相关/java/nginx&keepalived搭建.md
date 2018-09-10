
### ngix搭建
---

```
#下载并解压ngix，并进入到解压目录
cd /usr/local/src/ngginx

# 检查安装环境，并制定将来要安装的路径
./configure --prefix=/usr/local/nginx

# 安装所需的依赖
yum -y install gcc pcre-devel openssl openssl-devel

#编译安装
make && make install

# 启动nginx
./usr/local/nginx/sbin/nginx


# 测试 端口是否有ngnix进程
netstat -ntlp | grep 80

# 配置代理
修改配置文件

server{

    listen 80;
    server_name   主机名
    
    # 反向代理
    local /{
    
        root html;
        proxy_pass  代理服务器
    }
}

# 重启
kill -HUP `cat /usr/local/nginx/logs/nginx.pid`



```


#### 资源文件配置&负载均衡

```

#### 动态资源配置
location ~.*\.(jsp|do|action)$ {
    
    proxy_pass 代理服务器地址
}

# 静态资源配置
location ~.*.(html|js|css|gif|jpg|jpeg|png)$ {
    expires 3d;
}

# 负载均衡

http{
    upstream tomcats {
        server 代理服务器1 weight=1;
        server 代理服务器2 weight=1;
        server 代理服务器3 weight=1;
        
    }
}


location ~.*\.(jsp|do|action){

        proxy_pass http://tomcats  #tomcats是上面配置服务器组
}


```





keepalived
---


#### 安装配置
```

#keepalived安装
#下载keepalived官网:http://keepalived.org

# 解压压缩包
tar -zxvf keepalived.tar.gz -C /usr/local/src
cd /usr/local/src

./configure --prefix=/usr/local/keepalived

#  编译&安装
make && make install

# 将keepalive添加到系统服务中
cp /usr/local/keepalived/sbin/keepalived /usr/bin

# 加入开机启动项
cp /usr/local/keepalived/etc/rc.d/init.d/keepalived /etc/init.d/keepalived

# 复制配置文件
cp /usr/local/keepalived/etc/sysconfig/keepalived /etc/sysconfig
mkdir -p /etc/keepalived
cp /usr/local/keepalived/etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf

# 添加x权限
chmod +x /etc/init.d/keepalived

# 将keepalived 添加到开启启动
chkconfig --add keepalived
chkconfig keepalived on

```


#### 配置虚拟ip

``` 
# 修改配置文件 
vi /etc/keepalived/keepalived.conf
# master节点
global_defs{

    vrrp_script chk_health{
        # 查询nginx的进程是否大于2 如果小于代表异常  当前服务的权重 -2
        script "[[ `ps -ef | grep nginx | grep -v grep | wc -l` -ge 2 ]] && exit 0 || exit 1"
        interval 1 #每隔1秒执行上述脚本，去检查用户的程序nginx
        weight -2
    }

    vrrp_instance VI_1 {
        state MASTER # 指定主节点，其他为BACKUP即可
        interface eth0 #绑定虚拟网卡
        virtual_touter_id 11 #VRRP组名 其他节点必须一致
        priority 100 # 主节点优先级(1-254) 备用节点必须比主节点低
        advert_int 1 #组之间心跳间隔
        authentication {
            # 设置验证信息 相同组必须一致
            auth_type PASS
            auth_pass 123456
        
        }
        
        virtual_ipaddress{
            # 设置虚拟ip
            192.168.1.11/24 # 必须和同组节点在同一网段 
        } 
    }
    
    track_script{
        chk_health
    }
    
    # 设置通知脚本
    notify_master "/usr/local/keepalived/sbin/notify.sh master"
    notify_backup "/usr/local/keepalived/sbin/notify.sh backup"
    notify_fault "/usr/local/keepalived/sbin/notify.sh fault"
}

# BACKUP节点配置
global_defs{
    vrrp_instance VI_1 {
        state BACKUP # 
        interface eth0 #绑定虚拟网卡
        virtual_touter_id 11 #VRRP组名 其他节点必须一致
        priority 99 # 主节点优先级(1-254) 备用节点必须比主节点低
        advert_int 1 #组之间心跳间隔
        authentication {
            # 设置验证信息 相同组必须一致
            auth_type PASS
            auth_pass 123456
        
        }
        
        virtual_ipaddress{
            # 设置虚拟ip  同组节点一致
            192.168.1.11/24 # 必须和同组节点在同一网段 
        } 
    }
}
```


#### notify.sh
``` 
#!/bin/bash
case "$1" in
    master)
        /usr/local/nginx/sbin/nginx
        exit 0
     ;;
    backup)
        /usr/local/nginx/sbin/nginx -s stop
        /usr/local/nginx/sbin/nginx
        exit 0
    ;;
    fault)
        /usr/local/nginx/sbin/nginx -s stop
        exit 0
    ;;
    *)
        echo 'Usage:notify.sh {master|backup|fault}'
        exit1
    ;;
esac    

```

启动keepalived
service keepalived start

















