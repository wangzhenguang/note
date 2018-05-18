[TOC]

### solr安装
```
#安装
tar -zxf solr-xx.tgz

 ### 复制war到tomcat 
 cp solr-xxx/dist/solr-xxx.war /usr/local/solr/tomcat/webapps/solr.war
 
 ### 复制jar包
 
 cp  solr-xxx/example/lib/ext/* /usr/local/solr/tomcat/webapps/solr/WEB-INF/lib/
 
 ### 配置solr home
 cp -r solr-xxx/example/solr /usr/local/solr/solrhome
 
 ### 修改web.xml
 <env-entry>
    <solr-home>
 </env-entry>
 

```

#### 添加中文解析器
 
```
# 下载IKAnalyzer

#将IKAnalyzer的jar包添加到tomcat下的solr工程里

#  将IKAnalyzer的配置文件和词典添加到solr工程的classes目录下
cp IKAnalyzer.cfg.xml ext_stopword.dic mydict.dic

```

#### 定义域
```
# 进入solr-home
cd /usr/local/solr/solrhome/collection1/conf
# 修改配置文件
vi schema.xml

#添加fieldType
<filedType name="text_ik" class="solr.TextField">
    <analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"
</filedType>

```

#### 定义业务域
```
vi schema.xml

<field name="业务字段" type="类型" indexed="建索引" stored="存取" />


<field name="item_title" type="text_ik" indexed="true" stored="true" />
 
 
 <field name="item" type="text_ik" indexed="true" stored="false" multiValued="true" />
 <copyField source="item_title" dest="item"/>
```

### solr集群搭建

#### zookeeper部分
```
# 新建一个solr-cloud目录
mkdir solr-cloud
# 复制多份 zookeeper,在zookeeper目录新建data目录
cd zookeeper
mkdir data

cd data 
touch myid
echo '1' >> myid

cd conf 
mv zoo_sample.cfg zoo.cfg

vi zoo.cfg

dateDir = /zookeerper/data
clientPort=2181 #修改端口，多个不能冲突
# 添加
server.1 =ip:2881:3881 #不能和上面端口冲突
server.2=ip:2882:3882
server.3=ip:2883:3883

#其他zookeeper同样配置，只需修改myid  dateDir clientPort
# 启动zookeeper
```

#### solr部分

```
#搭建4个tomcat 装入solr

#tomcatsolr项目中 配置对应的solrhome
cd tomcat/webapps/solr/WEB-INF/web.xml
 <env-entry>
    <solr-home>solr home dir</solr-home>
 </env-entry>

# 修改solrhome里的配置文件
cd /solr-cloud/solrhome/
vi solr.xml
#修改对应的host hostport
<solrcloud>
    <str name="host">对应的tomcat ip</str>
    <int name="hostPort">tomcat 端口</int>
    <str name="hostContext">${hostContext:solr}</str>
    <int name="zkClientTimeout">${zkClientTimeout:30000}</int>
    <bool name="genericCoreNodeNames">${genericCoreNodeNames:true}</bool>
 </solrcloud>


# 修改tomcat配置 添加 JAVA_OPTS
vi tomcat/bin/catalina.sh

JAVA_OPTS="-DzkHost= zookeepr ip : port, ..."


# 上传zookeeper配置文件，zookeeper统一管理
cd /solr/example/scripts/cloud-scripts/zkcli.sh 
./zkcli.sh -zkhost solr-ip:port,..(上面配置的多个zookeeper中的ip和端口) -cmd upconfig -confder /usr/local/solr-cloud/solrhome/coolection1/conf -confname myconf

# 链接zookeeper
./zkcli.sh -server ip:port

#进入zookeeper集群查看上传的配置文件
cd /usr/local/solr-cloud/solrhome/zookeeper1/binz   
ls /configs/myconf 

# 启动所有的tomcat
# 创建 collection 

solr 地址/admin/collections?action=CREATE&name=collection2&numShards=2&replicationFactor=2
# solr 删除collection

solr地址/admin/collections?action=DELETE&name=collection1

```

### solrj管理集群
 