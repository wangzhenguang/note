 
 
 修改配置文件
 ---
hadoop-env.sh 配置JAVA_HOME
  
core-site.xml
```xml
<property>
    <!-- 指定hadoop所使用的文件系统 namenode地址-->
    <name>fs.defaultFS</name>
    <value>hdfs://hdfsname:port</value>
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
    <value>hdfsname</value>
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
export HADOOP_HOME=/itcast/hadoop-2.4.1
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









































