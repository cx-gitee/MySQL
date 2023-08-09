# MySQL-初级

## 1. MySQL引入

mysql就是基于网络通信的应用程序，底层一定用的socket

mysql服务端支持mysql自己的客户操作数据，也支持其他编程语言来作为客户端操作的数据，怎么解决

语言不通的问题呢？

- 让服务端精通所有编程语言
- 统一语言（sql语句）

## 2. MySQL安装

参考mysql安装文档：https://www.mysql.com/news-and-events/newsletter/

## 3. sql语句初体验

```sql
-- 每条sql语句都是以分号;结束。


-- 库 --> 文件夹
-- 表 --> 文件
-- 记录 --> 文件内容

show databases; -- 查看所有数据库
\s -- 查看数据库字符编码以及其他数据信息。
\c --结束当前sql语句
exit; -- 退出mysql
quit; -- 退出mysql
help 命令 --查看命令的帮助信息。
```

- 操作库

  ```sql
  -- 增
  create database db1;
  create database db1 charset=utf-8;  -- 推荐
  -- 删
  drop database db2;
  -- 改
  alter database db2 charset utf8;  -- mysql8.0
  -- 查
  show databases; -- 查看所有
  show create database db1; -- 查看单个
  ```

  

- 操作表

  ```sql
  select database(); -- 查看当前使用的数据库
  use db1; -- 切换使用数据库
  
  -- 增
  create table movies(id int, name char); -- 创建表(默认编码=当前使用的数据库字符编码)
  create table movies(id int, name char) charset utf8;
  -- 删
  drop table movies; -- 删除movies表
  -- 改
  alter table movies modify name char(4); -- 修改字段类型
  alter table movies modify name Name(5); -- 修改字段名和类型
  -- 查
  show tables; -- 查看当前数据库所有的表
  show create table movies; -- 查看创建表的sql语句
  describe movies; -- 查看一张表的结构
  
  
  -- 所有对表的操作，都可以使用绝对路径的方式，这样即使不切换数据库，也可以操作数据库对应的数据表
  create databse
  create table db2.movies(id int name char);
  ```

  

- 操作记录   

  ```sql
  -- 增
  insert into movies value (1, '流浪地球'); -- 插入一条记录
  insert into movies values (2, '三体'), (3, '长安三万里');
  -- 删
  delete from movies where name='三体';
  -- 改
  update movies set name='满江红' where id=1;
  -- 查
  select * from movies; -- 查看当前表的所有记录
  select id, name from movies; -- 查看当前表所有记录
  select user,host from mysql.user; -- 查看表所有记录 - 绝对路径
  ```
  
  

## 5. SQL语句分类

| 类型 | 描述                                       | 关键字                       |
| ---- | ------------------------------------------ | ---------------------------- |
| DDL  | 数据库定义语言，用来定义和管理数据库数据表 | create,alter,drop            |
| DML  | 数据库操作语言，用来操作数据               | insert,update,delete         |
| DQL  | 数据库查询语言，用来查询数据               | select                       |
| DCL  | 数据库控制语言，权限控制                   | grant,revoke,commit,rollback |

## 6. 库操作

语法：

```sql
-- 增
create database db1;
create database {if not exists} <库名> charset=utf-8;  -- 推荐
-- 删
drop database {if not exists} <库名>;
-- 改
alter database <库名> charset utf8;  -- mysql8.0
-- 查
show databases; -- 查看所有
show create database <库名>; -- 查看单个
```

## 7. 表操作

#### 存储引擎

表类型 <==> 存储引擎

```sql
show engines; -- 查看所有引擎

create table t1 (id int, name char) engine=innodb;
create table t2 (id int, name char) engine=myisam;
create table t3 (id int, name char) engine=memory;
create table t4 (id int, name char) engine=blackhole;


insert into t1 value(1, 'a');
insert into t2 value(1,'a');
insert into t3 value(1,'a');
insert into t4 value(1,'a');
```

#### 创建表语法

```SQL
create table <表名>{
<字段名1> <字段类型>[(宽度)] [约束条件],
<字段名2> <字段类型>[(宽度)] [约束条件],
<字段名3> <字段类型>[(宽度)] [约束条件],
<字段名4> <字段类型>[(宽度)] [约束条件1 约束条件2],
};
-- 宽度指的是字符个数或者说字符串长度。

-- 约束条件 注意顺序
[unsigned][not null]
```

#### 修改表语法

```sql
-- 修改存储引擎
alter table <表名> engine=<存储引擎名>;
-- 修改表名
alter table <表名> rename <新表名>;
-- 增加字段
alter table <表名> add <字段名> <字段类型>[(宽度)] [约束条件] [first|after <字段名>];
-- 删除字段
alter table <表名> drop <字段名>;

-- 修改字段
alter table <表名> modify <字段名> <新字段名>[(宽度)] [约束条件];
alter table <表名> change <旧字段名> <新字段名> <新字段类型>[(宽度)] [约束条件];
```

#### 删除表复制表

```sql
-- 删除表
drop table <表名>;
-- 复制表
create table <表名> select * from <旧表名> [条件];
-- 复制表结构
create table <表名> like <旧表名>;
```

## 8.  数据类型

- 整型

  - int

    | 类型      | 大小   | 范围（有符号)                             | 范围（无符号)            | 描述       |
    | --------- | ------ | ----------------------------------------- | ------------------------ | ---------- |
    | tinyint   | 1Bytes | (-128,127)                                | (0,255)                  | 很小的整数 |
    | smallint  | 2Bytes | (-32768,32767)                            | (0,65535)                | 较小的整数 |
    | mediumint | 3Bytes | (-8388608,8388607)                        | (0,16777215)             | 一般的整数 |
    | int       | 4Bytes | (-2147483648,2147483647)                  | (0,4294967295)           | 标准的整数 |
    | bigint    | 8Bytes | -9223372036854775808,9223372036854775807) | (0,18446744073709551615) | 极大的整数 |

  - float

    乘二取整

    3.625

    3 -> 11

    0.625 -> 0.625*2 = 1.25 0.25 * 2 = 0.5 0.5 * 2 = 1 -> 0.101

    3.625 -> 11.101

    浮点数的表现形式：符号、尾数、基数、指数

    

    | 类型    | 大小         | 范围(有符号)                                                 | 范围(无符号)                                        | 描述         |
    | ------- | ------------ | ------------------------------------------------------------ | --------------------------------------------------- | ------------ |
    | float   | 4Bytes       | (-3.402833466E+38,-1.175494351E-38),0,(1.175494351E-38),(3.402823466351E+38) | 0,(1.75494351E-38,3.402823466E+38)                  | 单精度浮点数 |
    | double  | 8Bytes       | (-1.7976931348623157E+308,-2.2250738585072014E-308),0,(2.2250738585072014E-308,1.7976931348623157E+308) | 0,(2.2250738585072014E-308,1.7976931348623157E+308) | 双精度浮点数 |
    | decimal | decimal(m,d) | 依赖于m,n的值                                                | 依赖于m,n的值                                       | 小数值       |

- 字符

  - char(定长字符串)

    > char(10)最多存储10个字符，如果超过10个字符，会直接报错，空格补齐。
    >
    > 缺点：浪费空间
    >
    > 优点：存储数据快
    >
    > 范围：0-255字符

  - varchar(变长字符串)

    > varchar(10)最多存储10个字符，如果超过10个字符，会直接报错，直接存储。
    >
    > 优点：节省空间
    >
    > 缺点：存储数据慢
    >
    > gbk 65532/2=32766 utf-8 65532/3=21844 --> 字节
    >
    > 范围：0-65535字节  -- 一行的最大字节数
    >
    > 最大字符数 = (65535-行其他字段总字节数-null标志字节数-长度标志字节数)/字符集单字符最大字节数

- 时间日期

  - year

  - date

  - time

  - datetime

    8字节，1000-9999

  - timestamp

    4字节，1970-2038

    ```sql
    use db3;
    create table user (
    	id int,
        name varchar(16),
        born year,
        birth date,
        active time,
        reg_time datetime
    );
    insert into user values (1, "fei", now(), now(), now(), now();
    insert into user values (2, "jack", '2025', '2025-06-05', '12:00:00', '2025-06-05 12:00:00');
    ```

    

- 枚举

  > enum：单选

  ```sql
  create table t10(
  	id int,
      name varchar(10),
      gender enum('male','felmale','other')
  );
  insert into t10 values(1,'fei','female');
  ```

  

- 集合

  > set：多选

  ```sql
  create table t11(
  	id int,
      name varchar(10),
      hobby set('tea','cola','beer')
  );
  insert into t11 values(1,'fei','tea');
  ```

  


## 9. 约束条件

```sql
-- 约束条件 注意顺序
[unsigned][zerofill][not null]
-- unsigned: 无符号
-- zerofill: 0填充
-- not null: 非空
-- default: 默认
-- unique: 唯一
create table user(id int, name varchar(16) unique);
insert into user values(1,'fei');

create table user(
    id int unique,
    name varchar(16) unique
);

drop table user;

create table user(
    id int,
    name varchar(16),
    unique(id),
    unique(name)
);

-- 联合唯一 --
ip:port
create table app(
    id int,
    host varchar(15),
    post int,
    unique(host, post),
    unique(id)
);

insert into app values
(1,'192.168.3.1',3306),
(2,'192.168.3.1',9000),
(3,'192.168.3.2',3306);
```

- primary key: 主键(约束特性：不为空，且唯一)

  ```sql
  -- 单列主键
  create table t10(
  	id int primary key,
      name varchar(16)
  );
  
  create table t11 (
  	name varchar(16),
      age int unique not null
  );
  
  -- 复合主键
  create table app1(
      id int,
      host varchar(15),
      post int,
      primary key(host, post),
      unique(id)
  );
  
  insert into app1 values
  (1,'192.168.3.1',3306),
  (2,'192.168.3.1',9000),
  (3,'192.168.3.2',3306);
  ```

  

- auto_increment：自动递增

  ```sql
  create database db4;
  use db4;
  create table t1(
      id int primary key auto_increment,
      name varchar(16)
  );
  
  insert into t1(name) values
  ('fei'),
  ('jack'),
  ('rose');
  
  -- 清空表
  delete from t1; -- 只清空数据，不清空自增值
  truncate t1; -- 同时清空数据和自增值
  ```

- foreign key：外键约束  

  > 大型项目：不要用外键，一切外键概念务必在应用层解决
  >
  > 小型项目：外键随便用

  ```sql
  drop table dep;
  
  create table dep(
  	id int primary key,
      name varchar(16),
      `desc` varchar(64)
  );
  
  create table emp(
  	id int primary key,
      name varchar(16),
      gender enum('male','female'),
      moblie varchar(11),
      dep_id int,
      foreign key(dep_id) references dep(id)
      -- 同步执行
      on delete cascade
      on update cascade
  );
  
  
  
  insert into dep values
  (1,'研发部','造火箭'),
  (2,'销售部','卖火箭'),
  (3,'人事部','裁员');
  
  insert into emp values
  (1,'fei','female','131',1);
  insert into emp values
  (2,'大仙','female','150',2),
  (3,'tom','female','139',2),
  (4,'jack','male','135',3),
  (5,'rose','male','137',3);
  
  delete from emp where dep_id=1;
  delete from dep where id=1;
  ```

  

## 10关系

1. 多对多

   ```sql
   create table song(
       id int primary key auto_increment,
       name varchar(16) not null
   );
   create table singer(
       id int primary key auto_increment,
       name varchar(16) not null
       
   );
   create table song2singer(
       id int primary key auto_increment,
       singer_id int not null,
       song_id int not null,
       foreign key(singer_id) references singer(id) on delete cascade on update cascade,
       foreign key(song_id) references song(id) on delete cascade on update cascade
   );
   
   insert into song(name) values
   ('以父之名'),
   ('夜的第七章'),
   ('止战之殇'),
   ('夜曲'),
   ('北京欢迎你');
   
   insert into singer(name) values
   ('周杰伦'),
   ('刘欢'),
   ('韩红'),
   ('成龙');
   
   insert into song2singer(singer_id, song_id) values
   (1,1),
   (1,2),
   (1,3),
   (1,4),
   (2,5),
   (3,5),
   (4,5);
   ```

   

2. 多对一

   ```sql
   create table emp(
   	id int primary key,
       name varchar(16),
       gender enum('male','female'),
       moblie varchar(11),
       dep_id int,
       foreign key(dep_id) references dep(id)
       on delete cascade
       on update cascade
   );
   ```

   

3. 一对一

   ```sql
   create table customer(
       id int primary key,
       name varchar(16),
       gender enum('male','female'),
       mobile varchar(11)
   );
   create table owner(
   	id int primary key,
       room_number varchar(16),
       area int,
       is_loan enum(true,false),
       customer_id int unique,
       foreign key(customer_id) references customer(id)
       on delete cascade
       on update cascade
   );
   
   insert into customer values
   (1,'fei','male','131'),
   (2,'xu','female','150'),
   (3,'tom','female','139'),
   (4,'jack','male','135'),
   (5,'rose','male','137');
   
   
   insert into owner values
   (1,'688',300,false,1),
   (2,'199',180,true,2);
   ```
   
   

## 11. 记录的查询语法

```sql
select distinct[{去重}] <字段....> from<库名>.<表名>
	where <条件>
    group by <分组条件>
    having <过滤条件>
    order by <排序条件> {ASC | DESC} -- 默认升序ASC
    limit n;
```

例子

```sql
create database db5;
use db5;
create table emp(
	id int primary key,
    name varchar(16),
    gender enum('male','female') not null,
    age int not null,
    salary float(10, 2),
    dep varchar(32),
    notes varchar(64)
);


insert into emp(name,gender,age,salary,dep) values
('关羽','male',20,8000,'技术部'),
('张飞','male',25,12000,'技术部'),
('赵云','male',19,6800,'技术部'),
('马超','male', 26,11000,'技术部'),
('黄忠','female',48,15000,'技术部'),
('夏侯惇','male',36,34000,'技术部'),
('典韦','male',19,6500,'技术部'),
('吕布','female',20,9000,'技术部'),
 
 
('刘备','male',32,4000,'市场部'),
('诸葛亮','male',27,2700,'市场部'),
('庞统','male',19,6800,'市场部'),
('徐庶','male', 26,11000,'市场部'),
('荀彧','female',48,15000,'市场部'),
('鲁肃', 'male',36,34000,'市场部'),
('司马懿','male',19,6500,'市场部'),
 ('杨修','female',20,9000,'市场部'),
```









































