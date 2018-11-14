 
 
 修改配置文件
 ---
hadoop-env.sh 配置JAVA_HOME
  
core-site.xml
```xml
<property>
    <!-- 指定hadoop所使用的文件系统 namenode地址-->
    <name>fs.defaultFS</name>
    <value>hdfs://host:port</value>
    <!--指定hadoop运行时产生文件的存储目录-->
    <name>hadoop.tmp.dir</name>
    <value>dir path</value>
    
</property>

```

hdfs-site.xml
```xml
 <!--指定hdfs副本数量-->
<property>
<name>dfs.relication</name>
<value>1</value>
</property>
<property>
			<name>dfs.secondary.http.address</name>
			<value>192.168.1.152:50090</value>
		</property>


```
mapred-site.xml
```xml
    <!--指定mapreduce 运行在yarn上-->
   <property>
   <name>mapreduce.framework.name</name>
   <value>yarn</value>
</property>
```

yarn-site.xml
```xml

    <!--指定resource manager 地址-->
<property>
    <name>yarn.resourcemanager.hostname</name>
    <value>host</value>
</property>
<!-- reducer获取数据方式-->
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>

```

配置hadoop环境变量 
```
vim /etc/proflie
export JAVA_HOME=/usr/java/jdk1.7.0_65
export HADOOP_HOME=/workflow/hadoop-2.4.1
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
source /etc/profile
```

格式化namenode
```
hdfs namenode -format
```

启动hadoop
---
先启动hdfs

sbin/start-dfs.sh

启动yarn
sbin/start-yarn.sh

hadoop-daemon.sh start namenode
hadoop-daemon.sh start datanode


验证是否启动成功
--- 
```
使用jps命令验证
27408 NameNode
28218 Jps
27643 SecondaryNameNode
28066 NodeManager
27803 ResourceManager
27512 DataNode

http://192.168.1.101:50070 （HDFS管理界面）
http://192.168.1.101:8088 （MR管理界面）
```






hdfs
---
```
hadoop fs -ls /
hadoop fs -cat file
hadoop fs -put file /
fs -moveFromLocal #本地拷贝到hdfs
fs -moveToLocal #hdfs到本地
fs -appendToFile # 追加一个文件到文件末尾
fs -copyToLocal
fs -getmerge #合并文件

```

hadoop jar hadoop-mapreduce-examples.jar wordcount /wordcount/input /wordcount/output







datanode
---

定期向namenode汇报自身所持有的block信息
```xml
<property>
<name>dfs.blockreport.intervalMsec</name>
<value>3600000</value>
</property>
```

datanode掉线判断时限参数

```xml
<property>
    <name>hearbeat.recheck.interval</name>
    <value>2000</value>   
    <!-- 毫秒-->
</property>
<property>
    <name>dfs.heartbeat.i nterval</name>
    <value>1</value>
    <!-- 秒-->
</property>
```
> datanode进程死亡或者异常时，namenode不会立即判定为死亡。需要经过一段时间，这段时间暂称为超时时长。
hdfs默认超时时长为10分钟+30秒。公式 timeout = 2 * heartbeat.recheck.interval + 10 * dfs.heartbeat.interval






### hadoop HA 

#### QJM 方式
qjm原理： 每个namenode上报信息给zookeeper ,当namenode的ZKFC程序检测到主nn已经宕机，会将standby的nn激活为active

> qjm集群必须拥有一个nameserviceid 用于区别其他集群，每个namenode都需要一个namenodeid用于区分不同的namenode,journalnode，datanode必须3台以上.

- hosts文件配置
```
10.211.55.1 node1 // namenode id: nn1
10.211.55.2 node2 // namenode id : nn2
10.211.55.3 node3 // datanode journalnode zookeeper
10.211.55.4 node4 // datanode journalnode zookeeper
10.211.55.5 node5 // datanode journalnode zookeeper
10.211.55.6 node6 // resource manager
10.211.55.7 node7 // resource manager

```


- hdfs-site.xml



```xml

<!--
 需要配置的项
nameservice名称
nameservice下的 nn 配置
nn的rpc,http 配置
edits 在jn 上存放位置
宕机后的切换方式
sshfence 需要免密登录
sshfence超时时间
-->




<configuration>
    <!--hdfs的nameservice名称-->
    <prorety>
        <name>dfs.nameservices</name>
        <value>bi</value>
    </prorety>
    <!--配置bi下面的namenode-->
    <prorety>
        <name>dfs.ha.namenodes.bi</name>
        <value>nn1,nn2</value>
    </prorety>
    <!--namenode1 rpc通信地址-->
    <prorety>
            <name>dfs.namenode.rpc-address.bi.nn1</name>
            <value>node1:9000</value>
      </prorety>
      <!-- namenode的http地址端口-->
      <prorety>
      <name>dfs.namenode.http-address.bi.nn1</name>
      <value>node1:50070</value>
</prorety>

   <!--namenode2 rpc通信地址-->
    <prorety>
            <name>dfs.namenode.rpc-address.bi.nn2</name>
            <value>node2:9000</value>
      </prorety>
      <!-- namenode2的http地址端口-->
      <prorety>
      <name>dfs.namenode.http-address.bi.nn2</name>
      <value>node2:50070</value>
</prorety>

<!-- 指定NameNode的edits元数据在JournalNode上的存放位置 -->
<prorety>
    <name>dfs.namenode.shared.edits.dir</name>
    <value>qjornal://node3:8485;node4:8485;node5:8485/bi</value>
</prorety>
<!-- 配置失败自动切换实现方式 -->
<prorety>
<name>dfs.client.failover.proxy.provider.bi</name>
    <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
</prorety>

<!-- 配置隔离机制方法，多个机制用换行分割，即每个机制暂用一行-->
<property>
<name>dfs.ha.fencing.methods</name>
<value>
sshfence
shell(/bin/true)
</value>
</property>

<!--主机私钥-->
<property>
      <name>dfs.ha.fencing.ssh.private-key-files</name>
      <value>/root/.ssh/id_rsa</value>
</property>
<!--配置sshfence超时时间-->
<property>
      <name>dfs.ha.fencing.ssh.connect-timeout</name>
      <value>30000</value>
</property>

<!--journalnode所在edtis文件保存的目录  journaldata的机器需要先创建该目录-->
<property>
  <name>dfs.journalnode.edits.dir</name>
  <value>/home/hadoop/journaldata</value>
</property>


</configuration>

```
 
- core-site.xml 配置
```xml
<configuration>
       <!-- hdfs nameservice路径 -->
    <property>
    <name>fs.defaultFS</name>
    <value>hdfs://bi/</value>
</property>
<!--指定hadoop临时目录-->
<property>
    <name>hadoop.tmp.dir</name>
    <value>/home/hadoop/hdpdata/tmp</value>
</property>

<!-- 指定zookeeper地址 -->
<property>
<name>ha.zookeeper.quorum</name>
<value>mini3:2181,mini4:2181,mini5:2181</value>
</property>



</configuration>
```
 

- mapred-site.xml 

```xml
<configuration>
<!--指定由yarn方式运行-->
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
</configuration>
```

- yarn-site.xml

```xml
<configuration>
<!-- 开启RM高可用 -->
<property>
<name>yarn.resourcemanager.ha.enabled</name>
<value>true</value>
</property>
<!-- 指定RM的cluster id -->
<property>
<name>yarn.resourcemanager.cluster-id</name>
<value>yrc</value>
</property>
<!-- 指定RM的名字 -->
<property>
<name>yarn.resourcemanager.ha.rm-ids</name>
<value>rm1,rm2</value>
</property>
<!-- 分别指定RM的地址 -->
<property>
<name>yarn.resourcemanager.hostname.rm1</name>
<value>mini6</value>
</property>
<property>
<name>yarn.resourcemanager.hostname.rm2</name>
<value>mini7</value>
</property>
<!-- 指定zk集群地址 -->
<property>
<name>yarn.resourcemanager.zk-address</name>
<value>node3:2181,node4:2181,node5:2181</value>
</property>
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
</configuration>

```

### 启动集群 检测

1.启动zookeeper
2.启动jn   hadoop-daemon.sh start journalnode
3. 在namenode 机器上执行 hdfs namenode -format，会在core-site.xml中hadoop.tmp.dir配置的目录下生成文件
将目录拷贝到另一台nanenode下；

4. hdfs zkfc -formatZK (执行一次就行 namenode机器上)
5. 启动hdfs start-dfs.sh 
6. 启动yran, start-yarn.sh / yarn-daemon.sh start resourcemanager
