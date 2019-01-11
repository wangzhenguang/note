create table test_tb(NO int,name String) partitioned by (part string)  
row format delimited fields terminated by ' ' stored as testfile;

create external table test_tb(NO int,name String)
row format delimited fields terminated by ' ' stored as testfile
location 'hdfs:....';

desc formatted tablename

load data inpath '$pwd' into/over table tablename;

show partitions;
alter table tablename add partition (partitionname= 'value')
alter table tablename drop partition (partition_name ='value')

  
  
set hive.enforce.bucketing =true;
set mapreduce.job.reduces =4;
cluster by (id)  根据id分区和排序
distribute by(id) sort by (no) 根据id分区 no排序

sort by (reduce 有序全局无序)

分桶提高join数据


full outer join 

left semi join 返回左表
right semi join


### 保存查询结果的几种方式
- create table tmp as select * from table;
- insert into table tmp select * from table;
- insert overwrite  local directory '/home/tmp.txt' select * from table;


### hive函数
### 自定义函数
### transform
  

 
 ### todo 
 beeline jar问题



