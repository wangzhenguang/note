[TOC]


#### oracle安装过程中的弹窗不显示
运行时添加加上后面参数即可
./runInstaller -jreLoc /etc/alternatives/jre_xx


#### mini centos安装桌面
```
yum groupinstall "GNOME Desktop" "Graphical Administration Tools"

ln -sf /lib/systemd/system/runlevel5.target /etc/systemd/system/default.target

reboot
```

https://blog.csdn.net/xiaocmxiao/article/details/52052918


### oracle常用语句

```
# 登录
sqlplus user/pwd@ip:port/sid
# 清屏
host clear/cls
# 当前用户
show user 
# 当前用户下的表
select * from tab; 
# 表结构
desc tablename；
# 设置展示的行宽
show linesize
set linesize xx
#设置colname列为8个字符 
col colname format/for a8
#设置colname列为4位数字
col colname for 9999

# 修改出错的语句
change linenum 
c /errorword/word
/ #执行

# nvl 空值时取值 
nav(colname,num)

# 去掉重复的值 
select distinct deptno from emp;
distinct 作用于所有后面列
# 连接符||
select ename||'的薪水是'||sal 信息 from emp

# 查询字典语句
select * from v$nls_parameters;



```

#### 建表空间 用户 授权

```
#新建用户
create user test identified by 123;
#建表空间
create tablespace ts_test datafile '/data/oracle/test.dbf' size 200M;
# 给test用户设置默认表空间
alter user test default tablespace ts_test;

#给用户设置权限
grant create session,create table,create view,create sequence,unlimited tablespace to test;


```