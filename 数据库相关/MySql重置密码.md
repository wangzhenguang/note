
### 
sudo /usr/local/mysql/bin/mysqld_safe --skip-grant-tables

sudo /usr/local/mysql/bin/mysql -u root

UPDATE mysql.user SET authentication_string=PASSWORD('新密码') WHERE User='root';

UPDATE mysql.user SET authentication_string=PASSWORD('新密码') WHERE User='root';


### 第二种

#### 关闭数据库

```
停用服务
sudo /usr/local/mysql/support-files/mysql.server stop

进入mysql的安装目录
sudo /usr/local/mysql/bin

重启服务
./mysqld_safe --skip-grant-tables

重开一个终端
配置一个别名 
alias mysql=/usr/local/mysql/bin/mysql

进入mysql命令模式
use mysql
flush privileges；获取权限

set password for 'root'@localhost='新密码'；

```