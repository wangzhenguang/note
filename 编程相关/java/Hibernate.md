
[TOC]

### 主键的生成策略

    1. increment:使用于short，int，long作为主键。不是使用的数据库自动增长机制。
    * Hibernate中提供的一种增长机制
    * 先进行查询：select max(id) form user
    * 再进行插入：获得最大值+1作为新的记录的主键
    * 并发访问的情况下，会有问题
    
    2. identity:使用于short，int，long作为主键。但是这个必须使用在自由增长的数据中。（不能使用在Oracle这种没有自动增长的数据库中）
    
    3. sequnce:适用于short，int，long作为主键。序列的增长方式
    * Oracle数据库没有自动增长，想自动增长需使用序列
    
    4.uuid:适用于char,varchar 类型作为主键
    
    5. native:本地策略。自动选择适用的生成策略。（只能short,int,long)
    
    6. assigned:主键生成不用Hibernate管理了。必须手动设置主键。

### 数据库的隔离级别
    在hibernate.cfg.xml配置文件中通过标签来配置
    
```
<property name="hibernate.connection.isolation">4</property>
```
- 如果不考虑隔离性：引发一些读的问题
    - 脏读： 一个事务读到了另一个事物为提交的数据
    - 不可重复读：一个事务读到了另一个事务已经提交的update数据，导致多次查询结果不一致
    - 虚读：一个事务读到了另一个事务已经提交的insert数据，导致多次查询的结果不一致。

- 通过设置数据库的隔离级别来解决上述读的问题（可选配置值）
    - 1 ：未提交读，以上读的问题都有可能发生
    - 2 ： 已提交读，避免脏读，但是不可重复读，虚读都有可能发生
    - 4 ：可重复读，避免脏读，不可重复读，但是虚度是有可能发生的（默认）
    - 8 ：串行化：以上读的情况都可以避免。


### Hibernate查询

- Query查询接口
```
    // select * from 
    Query query = session.createQuery("from User");
    query.list();
    
    Query query = session.createQuery("from User where name = ?");
    query.setString(0,name)
    //设置占位符别名
    Query query = session.createQuery("from User where name = :name");
    query.setString("name",name);

```

- Criteria查询接口
```
    // select * from
    Criteria criteria = session.createCriteria(User.class);
    criteria.list();
    
    // select * from table where name = ?
    criteria.add(Restrictions.eq("name",name));
    criteria.list();
    
    

```


### 一对多
> 集合需要自己创建

```
一对多的配置编写
一
<set name="sets"  >
    <key column="外键id"/>
    <one-to-many class="class全路径" />
    
</set>

多方
<many-to-one name="一方的字段名" class="一方的class路径" column="外键" />

```

### 一对多级联保存
    cascade="save-update",配置级联保存
    
- one 配置了级联保存，只要将所有数据关联上，保存即可
- many 配置级联保存，需要将（many方）每个数据都保存
- 两边都配置了级联保存，只需要关联，保存一个即可

### 一对多级联删除
    cascade="delete"
    
---
### cascade 取值
- none  不使用级联
- save-update   级联保存或更新
- delete    级联删除
- delete-orphan 孤儿删除（注意只能应用在一对多关系，配置在one方）
- all   除了delete-orphan的所有情况（包含save-update delete）
- all-delete-orphan 包含delete-orphan的所有情况 （包含save-update   delete  delete-orphan）
> 孤儿删除，只有在一对多的环境下才有孤儿删除，在一对多的关系中，可以将one的一方认为是父方，将many的一方认为是子方。在解除了父子关系的时候，将子方记录就直接删除。

---
### 某一方放弃外键的维护
 - 双方都维护外键的时候，会产生多余的sql语句
    产生的原因，session的一级缓存中的快照机制，会让双方都更新数据库，产生了多余的sql语句。
- 如果不想产生多余的sql语句，那么需要一方来放弃维护外键！在set标签上配置 inverse=“true”  默认值false不放弃


### 多对多配置示例
```
    <set name="set1" table="中间表名称" >
        <key column="字段名称" />
        <many-to-many class="对象全路径" column="另一多方的外键名称"/>
    </set>
    
    <set name="set2" table="中间表名称（两个需一致）">
        <key column="字段名称"/>
        <many-to-many class="类全路径" column="字段名称"  />
        
    </set>
```
> **多对多时必须有一方放弃外键维护**


### 查询方法
---
#### 唯一标识OID的检索方式
- session.get(对象.class,id)



#### HQL的检索方式

- 基本查询格式
session.createQuery("from Customer").list();

- 使用别名的方式
session.createQuery("from Customer").list();
session.createQuery("select c from Customer c").list();

- 排序查询
session.createQuery("from Customer order by cust_id").list();

session.createQuery("from Customer order by cust_id desc").list();

- 分页查询
    - setFirstResult(a)  从哪条记录开始，如果查询时从第一条开启，值是0
    - setMaxResults(b)  每页查询的记录条数
```
session.createQuery("from Customer").setFirstResult(0)
    .setMaxResults().list();
```

- 带条件查询
```
    Query query = session.createQuery("from Customer where name like ? order by id desc");
    query.setParameter(0,"%字%")；

```

**setParameter ?的位置，默认从0开始**

**按名称绑定参数的条件查询 ？换成 :名称的方式）**

- 投影查询
    - 投影查询就是想查询某一字段或者几个字段

```
List<Object[]> list = session.createQuery("select c.name,c.id from Customer c").list();

List<User> list = session.createQuery("select new User(u.name,u.id) from User u").list();

```

- 聚合函数查询
```
    session.createQuery("select count(c) from Customer c").list();
    
    session.createQuery("select sum(u.id) from User u" ).list();
```

#### QBC的检索方式
**QBC:query by Criteria**
```
//基本查询
    session.createCriteria(User.class).list();
    
    //排序查询
    Criteria criteria = session.createCriteria(User.class);
    criteria.addOrder(Order.desc("uid");//设置排序
    criteria.list();
    
    
    //分页查询
    criteria.setFirstResult(0);
    criteria.setMaxResults(3);
    
    /*
        条件查询
        * Restrictions.eq           -- 相等
        * Restrictions.gt           -- 大于号
        * Restrictions.ge           -- 大于等于
        * Restrictions.lt           -- 小于
        * Restrictions.le           -- 小于等于
        * Restrictions.between      -- 在之间
        * Restrictions.like         -- 模糊查询
        * Restrictions.in           -- 范围
        * Restrictions.and          -- 并且
        * Restrictions.or           -- 或者
    */
    
    criteria.add(Restrictions.or(Restrictions.eq("name","name"),Restrictions.ge("id",4L)));
    
    //聚合查询
    criteria.setProjection(Projections.rowCount());
    criteria.list().get(0).longValue();
    
    //离线条件查询
    DetachedCriteria criteria = DetachedCriteria.forClass(User.class);
    criteria.add(Restrictions.eq("name","name");
    criteria.getExecutableCriteria(session).list();
    
```

#### SQL检索方式
```
    SQLQuery sqlQuery = session.createSQLQuery("select * from user where name=?");
    sqlQuery.setParameter(0,"张三")；
    sqlQuery.addEntity(User.class);
    sqlQuery.list();
```


### HQL多表查询

    内连接查询
        显示内连接 select * from user u inner join orders o on u.uid = o.uid
        隐式内连接 select * from user u,orders o where u.uid = o.uid
    外连接
        左外连接 select * from user u left join orders o on u.uid = o.uid;
        右外连接 select * from user u right join orders o on u.uid=o.oid;
    
> 非迫切返回的结果是Object[], 迫切返回的结果是对象

```
    //内连接使用inner join 默认返回object数组
    List<Object[]> list = session.createQuery("from User u inner join u.orders").list();
    //迫切内连接
    List<User> list = session.createQuery("from User u inner join fetch u.orders").list();
    
    
    //迫切左外链接 
   List<User> list = session.createQuery("from User u left join fetch u.orders").list();
   
```