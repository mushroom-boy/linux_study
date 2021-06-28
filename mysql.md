# .mysql额外小知识





## 1. mysql查看版本和当前数据库

mysql>select version()   查看版本

mysql>select database()  查看当前数据库



## 2. mysql-\g和\G的作用

\g 的作用是分号和在sql语句中写’;’是等效的

show tables\g  =  show tables； 

\G 的作用是将查到的结构旋转90度变成纵向



## 3. 系统数据库

下列四个库，已经库里的表禁止删除
| information_schema |
| mysql              |
| performance_schema |
| sys                |

系统数据库
information_schema：	虚拟库，主要存储了系统中的一些数据库对象的信息，例如用户表信息、列信息、权限信息、字符信息等
performance_schema：	主要存储数据库服务器的性能参数
mysql：				             	授权库，主要存储系统用户的权限信息
sys：					                  主要存储数据库服务器的性能参数

关系型数据库
	mysql
非关系数据库
	redis



## 4. mysql修改密码

1.

//以密码进去的就使用这种（mysql8.0改密码暂时只支持第2种）

```
mysql> alter mysql.user 'root'@'localhost' identified by "123"; 
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123';//8.0用这个
或
mysqladmin -uroot -p'123' password 'new_password'//123为旧密码
```

   

//以免密登录的

修改配置文件:vim /etc/my.cnf
skip_grant_tables

```
mysql> update mysql.user set authentication_string=password('tiger@@1229') where user='root' and host='localhost' ;  

mysql> flush privileges;
或
mysql> update mysql.user set authentication_string=password('GZgz2101..') where user='root' and host='localhost' ;

mysql> flush privileges;
```



3.

```
 修改用户密码
＝＝＝root修改自己密码
方法一：
    # mysqladmin -uroot -p'123' password 'new_password'	    //123为旧密码

方法二：
    mysql> UPDATE mysql.user SET authentication_string=password('new_password')
   		   WHERE user='root' AND host='localhost';
           FLUSH PRIVILEGES;

方法三：
    mysql> SET PASSWORD=password('new_password');
	       FLUSH PRIVILEGES;

==root修改其他用户密码
方法一：
    SET PASSWORD FOR user3@’localhost’=password(‘new_password’);

方法二：
    UPDATE mysql.user SET authentication_string=password(‘new_password’)
    	WHERE user=’user3’ AND host=’localhost’;
    FLUSH PRIVILEGES;


＝＝＝普通用户修改自己密码
    SET password=password(‘new_password’);

   
＝＝＝丢失root用户密码
    # vim /etc/my.cnf
    [mysqld]
    skip-grant-tables
    # service mysqld restart
    # mysql -uroot
    mysql> UPDATE mysql.user SET authentication_string=password(‘new_password’)
	WHERE user=’root’ AND host=’localhost’;
    mysql> FLUSH PRIVILEGES;
```













# mysql的安装

mysql yum源下载地址:https://dev.mysql.com/downloads/repo/yum/    //给你看的，不需要找，直接省略到下面的vim就行了，你要是喜欢也可以去看甚至用它来下载，但没这个必要。


vim /etc/yum.repos.d/mysql-community.repo

```
# Enable to use MySQL 5.7
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/  [替换]
enabled=1   [修改]
gpgcheck=0       //我们并不是从官网下的，关闭安全检测
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

[mysql-cluster-8.0-community]
name=MySQL Cluster 8.0 Community
baseurl=http://repo.mysql.com/yum/mysql-cluster-8.0-community/el/6/$basearch/
enabled=0   [修改]   //如果想下载8.0的mysql可以改为1
gpgcheck=0       //关闭安全检测
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

重新加载缓存

```
yum clean all && yum makecache fast

yum -y install mysql-community-server

systemctl start mysqld && systemctl enable mysqld

grep password /var/log/mysqld.log
```











# 1.sql、DB、DBMS分别是什么，他们之间的关系？

DB:

DataBase（数据库，是一个存储数据的仓库,数据库实际上在硬盘上以文件的形式存在）



DBMS:

DBMS的全称是Database Management System，即数据库管理系统，是一个软件，管理数据库文件的软件，

用户可以访问DBMS对数据进行增删改查操作，常见DBMS有： MySQL、oracle、DB2、sqlite、sqlserver等

 

SQL:

结构化查询语言，是一门标准通用的语言,SQL是用于访问数据库的标准化语言。标准的sql适合于所有的数据库产品。

SQL语句在执行的时候，实际上内部也会先进行编译，然后再执行sql。（sql语句的编译由DBMS完成。）

 

DQL（数据查询语言）: 查询语句，凡是select语句都是DQL。

DML（数据操作语言）：insert delete update，对表当中的数据进行增删改。

DDL（数据定义语言）：create drop alter，对表结构的增删改。

TCL（事务控制语言）：commit提交事务，rollback回滚事务。(TCL中的T是Transaction)

DCL（数据控制语言）: grant授权、revoke撤销权限等。

TPL（事务处理语言）：它的语句能确保被DML语句影响的表的所有行及时得以更新。TPL语句包括BEGIN TRANSACTION，COMMIT和ROLLBACK。

 

数据定义 语言包含定义数据库及其对象的语句，例如表，视图，触发器，存储过程等。 DDL

数据操作 语言包含允许您更新和查询数据的语句。                 DML

数据控制 语言允许授予用户权限访问数据库中特定数据的权限。           DCL

 

表里的一行内容称为 记录,表里的一列内容称为 字段

 

DBMS负责执行sql语句，通过执行sql语句来操作DB当中的数据。

DBMS -(执行)-> SQL -(操作)-> DB

dcl 数据控制dml 数据操作 数据定义ddl dql 数据查询





# 2.sql语句

 

## 2.1 查看表

mysql> show tables;                     //查看某个库的所有表

mysql> show create table t1;      //查看具体的某个表的信息

mysql> desc t1;                             //同上

 

查看表记录 

 select  *  from 表名;   

 select  字段,字段 from 表名;

查看表状态  

 show table status like '表名' ;

 

## 2.2 修改表

修改表：     
alter  修改表名称 修改字段名称  修改字段数据类型    修改字段的修饰符
insert 插入数据
delete 删除数据
update 更新数据 



  修改表名  （库好像不能修改名字）

rename table 原表名 to 新表名;     

```mysql> rename table t1 to t2;```

###    2.2.1  添加字段  

alter table 表名 add 字段  修饰符;    

```
alter table t1 add gender char(20);   

alter table t1 add grade char(20) after id; 

alter table t1 add zhuanye char(20) first;

mysql> alter table t2 add phone int(11);

mysql> alter table t2 add phone int(11) after id; //将 新添加的字段放到id后

mysql> alter table t2 add phone int(11) first;   //将新添加的字段放到第一位
```



​                     

### 2.2.2  删除修改字段

alter  table 表名 drop 字段; 

```mysql> alter table t2 drop phone;```



修改字段 alter table 表名 change 旧字段 新字段 修饰符;  change修改字段名称，类型，约束，顺序

alter table 表名 modify 字段 属性 修饰符；   modify 不能修改字段名称

```
\> alter table t1 change zhuanye major varchar(20) after gender; 

\> alter table t1 modify major varchar(20) after age; 

mysql> alter table t2 change name name1 varchar(10);

mysql> alter table t2 modify name1 varchar(20);
```



​            

====================

### 2.2.3 插入记录或数据  

修改记录                     

``` (inster update delete where)```

  添加记录  

``` 
insert into 表名 values (),(),(),();  

insert into 表名(字段，字段，字段) values (),(),(); 

insert into t1 values(1,'2','yingge',18,'12','male');

insert into t1(name,grade) values ('tigerfive','1');

insert into t1(name,grade) values ('youngfit','2'),('houzi','2'); 

inert into t1 set name='jianjian',grade='1';

mysql> insert into t2 values(1,'zengjun','nv','广州千锋13教室'),(2,'wucheng','nv','深圳');

mysql> insert into t2(id,name1,sex) values(3,'huanghehong','nan'),(4,'xulike','nv');
```

​           

​           

### 2.2.4 修改记录或数据（更新）

```
update  表名 set 字段=' '  where 主键字段=' ';

update t1 set grade=4 where name='yingge'; 

 mysql> update t2 set sex='女' where id=3;

 mysql> update t2 set address='北京三里屯' where id ='4';

 mysql> update t2 set address='广州海珠区' where id='3';
```



​               

在记录更新前，一定要用条件查询一下。避免条件不严谨造成误更改 

​               

### 2.2.5 删除记录和删除表

``` 
delete from 表名 where 主键字段=' ';   delete from 表名;    

delete from t1 where id=5;  

mysql> delete from t2 where id =4;

 mysql> delete from t2;  //禁止执行类似语句
```



 

删除表 drop table 表名；

``` drop table t1;```

删除库 drop databse 库名； 

``` drop database company;```



## 2.3 创建表

语法：
create table 表名(
		字段名1  类型[(宽度) 约束条件],
		字段名2  类型[(宽度) 约束条件],
		字段名3  类型[(宽度) 约束条件]
)[存储引擎 字符集];

==在同一张表中，字段名是不能相同
==宽度和约束条件可选
==字段名和类型是必须的

``` mysql> CREATE  DATABASE school;
mysql> use school;
mysql> create table student1(
    -> id int,
    -> name varchar(50),                 
    -> sex enum('m','f'),
    -> age int
    -> )ENGINE=InnoDB DEFAULT CHARSET=utf8;;
Query OK, 0 rows affected (0.03 sec) 
```







# 3. mysql常见的储存引擎

MyISAM、InnoDB(事务型)、MEMORY(内存)、ARCHIVE(归档)等

MyISAM和InnoDB的区别 (了解)
Mysql在V5.1之前默认存储引擎是MyISAM；在此之后默认存储引擎是InnoDB
MyISAM不支持事务，而InnoDB支持。

InnoDB的AUTOCOMMIT默认是打开的，即每条SQL语句会默认被封装成一个事务，自动提交，这样会影响速度，所以最好是把多条SQL语句显示放在begin和commit之间，组成一个事务去提交。
InnoDB支持数据行锁定，MyISAM不支持行锁定，只支持锁定整个表。即 MyISAM同一个表上的读锁和写锁是互斥的，MyISAM并发读写时如果等待队列中既有读请求又有写请求，默认写请求的优先级高，即使读请求先到，所以 MyISAM不适合于有大量查询和修改并存的情况，那样查询进程会长时间阻塞。因为MyISAM是锁表，所以某项读操作比较耗时会使其他写进程饿死。
InnoDB支持外键，MyISAM不支持。
InnoDB的主键范围更大，最大是MyISAM的2倍。
InnoDB不支持全文索引，而MyISAM支持。
//记上面差不多就行了，其他的不一定考你
全文索引是指对char、 varchar和text中的每个词（停用词除外）建立倒排序索引。MyISAM的全文索引其实没啥用，因为它不支持中文分词，必须由使用者分词后加入空 格再写到数据表里，而且少于4个汉字的词会和停用词一样被忽略掉。
MyISAM支持GIS数据，InnoDB不支持。即MyISAM支持以下空间数据对象：Point,Line,Polygon,Surface等。
没有where的count(*)使用MyISAM要比InnoDB快得多。因 为MyISAM内置了一个计数器，count(*)时它直接从计数器中读，
而InnoDB必须扫描全表。所以在InnoDB上执行count(*)时一般 要伴随where，且where中要包含主键以外的索引列。
为什么这里特别强调“主键以外”？因为InnoDB中primary index是和raw data存放在一起的，而secondary index则是单独存放，
然后有个指针指向primary key。所以只是count(*)的话使用secondary index扫描更快，而primary key则主要在扫描索引
同时要返回raw data时的作用较大。





# 4.表复制

key不会被复制: 主键、外键和索引



复制一张表

 ``` 
mysql> create table t10(select * from t3);
mysql> create table t10(select id,name from t3);
 ```

复制表结构

    mysql> create table t4(select * from t3 where 5=4);
    mysql> create table t4(select  id,name  from t3 where 5=4);
复制记录

    mysql> insert into t3 select * from t10 where id=9;
删除表

    mysql> drop table t1;
删除库

    mysql> drop database gnu;










# 5. 表完整性约束

了解(！！！)

作用：用于保证数据的完整性和一致性

约束条件					                                                  	说明
PRIMARY KEY (PK)		    标识该字段为该表的主键，可以唯一的标识记录，不可以为空  UNIQUE + NOT NULL

FOREIGN KEY (FK)		    标识该字段为该表的外键，实现表与表（父表主键/子表1外键/子表2外键）之间的关联

NOT NULL				     	标识该字段不能为空
UNIQUE KEY  (UK)		    标识该字段的值是唯一的，可以为空，一个表中可以有多个UNIQUE KEY

AUTO_INCREMENT		  标识该字段的值自动增长（整数类型，而且为主键）
DEFAULT						   为该字段设置默认值
UNSIGNED                        无符号，正数
ZEROFILL                           使用0填充，例如0000001





## 5.1MySQL约束

约束(Constraint)

什么是约束？常见的约束有哪些呢？
在创建表的时候，可以给表的字段添加相应的约束，添加约束的目的是为了保证表中数据的
合法性、有效性、完整性。
常见的约束有哪些呢？
		非空约束(not null)：约束的字段不能为NULL
		唯一约束(unique)：约束的字段不能重复
		主键约束(primary key)：约束的字段既不能为NULL，也不能重复（简称PK）
		外键约束(foreign key)：...（简称FK）
		检查约束(check)：注意Oracle数据库有check约束，但是mysql没有，目前mysql不支持该约束。

    null not null
    字符串  空         （只适用于5.1，5.5）
    enum  第一个预定义值
    timestamp   当前时间   
    default 
    mysql> alter table t2 modify id int not null default 8;
                
    unique（key）    唯一的
    mysql> alter table t2 modify id int unique;
    mysql> alter table t200 drop index id;     //删除
         
    auto_increment      自增    每张表只能一个字段为自增
    mysql> create table t4(id int unique auto_increment,name char(10));
    
    primary key（key）
    每张表里只能有一个主键，不能为空，而且唯一       
    mysql> create table t7(hostname char(20) primary key,ip char(150));
    mysql> create table t9(hostname char(20),ip char(150),primary key(hostname));
           
    联合主键
    mysql> create table t9(hostname char(20),ip char(150),primary key(hostname,ip));
            
    mysql> alter table t101  drop  primary key;   //删除主键
    
    index(key)
    索引   优化查询速度
    mysql> create table t100(hostname char(20) primary key,ip char(150),index (ip));
    mysql> create table t101(hostname char(20) primary key,ip char(150),index dizhi(ip));
            
    mysql> create index dizhi on t105(ip);
            
    mysql> alter table t101  drop  index dizhi;   //删除index索引

 外键foreign key (key)

```
mysql> create table t1(id int,manager char(10) primary key) engine = innodb;
mysql> create table t2(id int,admin char(10),foreign key (admin) references  t1 (manager)) engine = innodb ；
mysql> create table xingzheng(id int,admin char(10),foreign key (admin) references guanli(manager) on delete cascade)  engine = innodb;
```













## 5.2sql语句执行环节：

​	select		5
​		...
​	from			1
​		...		
​	where			2
​		...	
​	group by		3
​		...
​	having		4
​		...
​	order by		6
​		...
​	limit			7
​		...;







# 6. 常见的数据类型



数值类型：
	    整数类型      TINYINT SMALLINT MEDIUMINT INT BIGINT			    
	    浮点数类型     FLOAT DOUBLE
	    定点数类型     DEC
	    位类型		    BIT
	
字符串类型：
	    CHAR系列  	CHAR  VARCHAR
        TEXT系列   	TINYTEXT TEXT MEDIUMTEXT LONGTEXT
        BLOB 系列	    TINYBLOB BLOB MEDIUMBLOB LONGBLOB 
        BINARY系列 	BINARY VARBINARY 
        枚举类型：	  ENUM
        集合类型：     SET
        
时间和日期类型： 	DATE TIME DATETIME TIMESTAMP YEAR 







# 7.索引

索引的分类
	普通索引
唯一索引
全文索引
单列索引
多列索引
空间索引

```
创建索引
＝＝＝创建表时
语法：
	CREATE TABLE 表名 (
				字段名1  数据类型 [完整性约束条件…],
				字段名2  数据类型 [完整性约束条件…],
				[UNIQUE | FULLTEXT | SPATIAL ]   INDEX | KEY
				[索引名]  (字段名[(长度)]  [ASC |DESC]) 
				);

创建普通索引示例：
	CREATE TABLE department10 (
		dept_id INT,
		dept_name VARCHAR(30) ,
		comment VARCHAR(50),
		INDEX index_dept_name (dept_name)
		);


创建唯一索引示例：
	CREATE TABLE department11 (
		dept_id INT,
		dept_name VARCHAR(30) ,
		comment VARCHAR(50),
		UNIQUE INDEX index_dept_name (dept_name)
		);


创建全文索引示例：
	CREATE TABLE department12 (
		dept_id INT,
		dept_name VARCHAR(30) ,
		comment VARCHAR(50),
		log text,
		FULLTEXT INDEX index_log (log)
		);


创建多列索引示例：
	CREATE TABLE department13 (
		dept_id INT,
		dept_name VARCHAR(30) ,
		comment VARCHAR(50),
		INDEX index_dept_name_comment (dept_name, comment)
		);


===CREATE在已存在的表上创建索引
语法：
	CREATE  [UNIQUE | FULLTEXT | SPATIAL ]  INDEX  索引名 
 					        ON 表名 (字段名[(长度)]  [ASC |DESC]) ;

创建普通索引示例：
	CREATE INDEX index_dept_name ON department  (dept_name);

创建唯一索引示例：
	CREATE UNIQUE INDEX index_dept_name ON department (dept_name);

创建全文索引示例：
	CREATE FULLTEXT INDEX index_dept_name ON department (dept_name);

创建多列索引示例：
	CREATE INDEX index_dept_name_ comment ON department (dept_name, comment);


===ALTER TABLE在已存在的表上创建索引
语法：
	ALTER TABLE 表名 ADD  [UNIQUE | FULLTEXT | SPATIAL ] INDEX
				   		    索引名 (字段名[(长度)]  [ASC |DESC]) ;

创建普通索引示例：
	ALTER TABLE department ADD INDEX index_dept_name (dept_name);

创建唯一索引示例：
	ALTER TABLE department ADD UNIQUE INDEX index_dept_name (dept_name);


创建全文索引示例：
	ALTER TABLE department ADD FULLTEXT INDEX index_dept_name (dept_name);

创建多列索引示例：
	ALTER TABLE department ADD INDEX index_dept_name_comment (dept_name, comment);

测试示例
   EXPLAIN SELECT * FROM department WHERE dept_name=‘hr’;

删除索引
  show create table employee6;
  DROP INDEX 索引名 ON 表名;
```



# 8.视图

视图简介
MySQL视图是一个虚拟表，其内容由查询定义。同真实的表一样，视图包含一系列带有名称的列和行
数据。但是，视图并不在数据库中以存储的数据值集形式存在。行和列数据来自由定义视图的查询所
引用的表，并且在引用视图时动态生成。对其中所引用的基础表来说，MySQL视图的作用类似于筛选。
定义视图的筛选可以来自当前或其它数据库的一个或多个表，或者其它视图。通过视图进行查询没有任
何限制，通过它们进行数据修改时的限制也很少。
视图是存储在数据库中的SQL查询语句，它主要出于两种原因：安全原因，视图可以隐藏一些数据，如：
一些敏感的信息，另一原因是可以使复杂的查询易于理解和使用。



# 9.权限机制

授权远程登陆

```
grant 权限 on 库.表 to 用户名@"范围" indentfied by "密码";
1.grant select,insert,update,delete on redmine1.* to jira@"%" identified by "123";
2.grant all on *.* to jira@"%" identified by "123";
使用命令授权：         
mysql> grant select(id),insert(id) on tigerfive.t1 to 'xiaowu'@'172.16.70.%' identified by '123';
mysql> grant select,insert on tigerfive.t1 to 'xiaowu'@'172.16.70.%' identified by '123';         
mysql> grant all on  *.* to 'xiaowu'@'172.16.70.%' identified by '123';         mysql> grant all on  *.* to 'xiaowu'@'172.16.70.%' ;
mysql> grant all on  *.* to 'xiaowu'@'%';
```



```
权限控制机制         
    刷新权限
    mysql > flush privileges;
	# mysqladmin flush-privileges -u root -p1     

 user表
    登录认证：用户能否进mysql查看user
    权限认证：如果能进，看user表的权限有没有，没有就看下一个db表，
			  db表没有看tables_priv,tables_priv没有就去看columns_priv
                              
当db表的host字段为空的时候才会用到host表
db或者host 任何一个select是N,都是没权限	
	
	grant 权限 on db.table to user@host  identified by 'password';
	
撤销权限
     mysql> revoke all on *.* from 'xiaowu'@'%';
	 
删除账户：
    mysql> drop user tigerfive;
		   
创建账户：
    mysql> create user tigerfive;

root账户没了或者root密码丢失：                       
    # mysqld_safe --skip-grant-tables --user=mysql &	 
	 
```

MySQL用户管理
1. 登录和退出MySQL
    示例：
    mysql -h192.168.5.240 -P 3306 -u root -p123  -e ‘select user,host from mysql.user’
    -h	指定主机名                       【默认为localhost】
    -P	MySQL服务器端口              【默认3306】
    -u	指定用户名                       【默认root】
    -p	指定登录密码                    【默认为空密码】
	此处mysql为指定登录的数据库 
    -e	接SQL语句	 
    
    ```
    不区分大小写
    lower_case_table_names=1  不区分大小写
    validate_password=off     密码规则关闭
    
    怎么查看一台机器开放的端口
    ss -tunlp
    netstat -tunlp
    lsof -i :port
    ```
    
    





# 10.备份

数据备份
	冷备
		tar scp
		lvm快照
		离线备份
	热备
		物理备份
		逻辑备份

冷备份发生在数据库已经正常关闭的情况下，当正常关闭时会提供给我们一个完整的数据库。冷备份是将关键性文件拷贝到另外位置的一种说法。对于备份数据库信息而言，冷备份是最快和最安全的方法。

热备份是在数据库运行的情况下，备份数据库操作的sql语句，当数据库发生问题时，可以重新执行一遍备份的sql语句。

完备，差备，增备

```
物理备份
	tar scp 
	lvm-快照
	xtrabackup
逻辑备份
	mysqldump
	mysqlbinlog
```



## 10.1 mysqldump逻辑备份

```
日志管理
/etc/my.cnf
作用
配置文件
error log    错误日志           排错    /var/log/mysqld.log【默认开启】     
bin log      二进制日志         备份    增量备份 DDL DML DCL
Relay log    中继日志           复制    接收 replication master 
slow log     慢查询日志         调优    查询时间超过指定值

Error Log
log-error=/usr/local/mysqld/log/mysqld.log
编译安装的在/usr/lib/mysql/

Binary Log
log-bin=/usr/local/mysqld/log/mysqld-bin
server-id=2 这个数字随便写，不一定为2
bin-log日志查看要用mysqlbinlog + 路径

注：binlog日志
1. 重启mysqld 会截断
2. flush logs 会截断
3. reset master/slave 删除所有binlog
```



```
本身为客户端工具:
远程备份语法: # mysqldump  -h 服务器  -u用户名  -p密码   数据库名  > 备份文件.sql
本地备份语法: # mysqldump  -u用户名  -p密码   数据库名  > 备份文件.sql

-A, --all-databases			            
    备份所有库
-B, --databases bbs test mysql	
    备份多个数据库
--no-data，-d  
    不导出任何数据，只导出数据库表结构
```

备份库

```
备份单个库
mysqldump -uroot -p'GZgz2101..' gz2101 > gz2101.sql
备份多个库
mysqldump -u root -p'GZgz2101..' -B gz2101 gz2 > gz2101_gz2.sql
备份所有库
mysqldump -u root -p'GZgz2101..' -A > all.sql
```

备份表

```
备份一个表
mysqldump -uroot -p'GZgz2101..' gz2101 t1 >gz2101_t1.sql
备份多个表 
mysqldump -uroot -p'GZgz2101..' gz2101 t1 t2 >/root/gz2101/gz2101_t1-t2.sql
恢复表
mysql -u root -p'GZgz2101..'  gz2 < /root/gz2101/gz2101_t1-t2.sql    
```

恢复数据库和表
为保证数据一致性，应在恢复数据之前停止数据库对外的服务,停止binlog日志 
因为binlog使用binlog日志恢复数据时也会产生binlog日志。

```
mysql使用bin-log恢复日志
	修改配置
	vim /etc/my.cnf
	server-id=20
	log-bin=/var/log/mysql/mysql-bin
	重启服务
	systemctl restart mysqld
	
	常用命令
	> flush log   截断bin-log日志[让bin-log轮转]
	> reset maste 删除所有binlog 
	> SET SQL_LOG_BIN=0;  暂停 仅当前会话
	> SET SQL_LOG_BIN=1;

	查看日志[bin-log带加密的时候需要解密]
	# mysqlbinlog --base64-output=DECODE-ROWS -v  log-file
	[数据恢复时不需要解密]
	[root@mysql-1 mysql]# mysqlbinlog --start-position 219 --stop-position  321 mysql-bin.000002 |mysql -uroot -p123		#恢复语句
```







## 10.2 MySQL物理备份

```
安装软件 percona-xtrabackup
	rpm安装方式
	# wget https://downloads.percona.com/downloads/Percona-XtraBackup-2.4/Percona-XtraBackup-2.4.22/binary/redhat/7/x86_64/percona-xtrabackup-24-2.4.22-1.el7.x86_64.rpm
	# yum localinstall -y percona-xtrabackup-24-2.4.22-1.el7.x86_64.rpm
```

```
完全备份
	备份
		创建备份目录
		mkdir /xtrabackup/full -p    
		备份[需要mysql的用户名和密码]
		innobackupex --user=root --password='GZgz2101..' /xtrabackup/full/
	
	数据恢复 
		1. 停止数据库
			systemctl stop mysqld
		2. 清理环境
			rm -rf /var/lib/mysql/*
			日志可以选择行清理
		3. 重演回滚－－> 恢复数据
			3.1恢复之前的验证恢复
			innobackupex --apply-log /xtrabackup/full/2021-04-12_10-39-29
			3.2确认数据库目录：
			恢复之前需要确认配置文件内有数据库目录指定，不然xtrabackup不知道恢复到哪里
			grep datadir /etc/my.cnf
			datadir=/var/lib/mysql
			3.3恢复数据
			innobackupex --copy-back /xtrabackup/full/2021-04-12_10-39-29/
		4. 修改权限
			chown mysql.mysql  /var/lib/mysql  -R
		5. 启动数据库
			systemctl start mysqld

---------------	

增量备份
	原理：每次备份上一次备份到现在产生的新数据
	备份过程
		完整备份:周一
		 innobackupex --user=root --password='GZgz2101..' /xtrabackup
		 完整备份的文件为: /xtrabackup/2021-04-12_11-05-44/
		增量备份：周二　——　周三
			周二:
				在数据库中插入周二的数据
				进行增量备份[周二,此时增量是基于第一次的周一的完整备份]
				innobackupex --user=root --password='GZgz2101..' --incremental /xtrabackup/ --incremental-basedir=/xtrabackup/2021-04-12_11-05-44
				增量备份文件为: /xtrabackup/2021-04-13_00-03-16/
			周三:
				在数据库中插入周三的数据
				进行增量备份[周三,此时的增量是基于第二次的周二的增量备份]
				innobackupex --user=root --password='GZgz2101..' --incremental /xtrabackup/ --incremental-basedir=/xtrabackup/2021-04-13_00-03-16/
				增量备份文件为: /xtrabackup/2021-04-14_00-01-44/
		查看一下备份目录:
			# ls /xtrabackup/
			2021-04-12_11-05-44  2021-04-13_00-03-16  2021-04-14_00-01-44
			周一的完整备份       周二的增量备份       周三的增量备份
	恢复过程
		1. 停止数据库
			systemctl stop mysqld
		2. 清理环境
			rm -rf /var/lib/mysql/*
		3. 依次重演回滚redo log－－> 恢复数据
			innobackupex --apply-log --redo-only /xtrabackup/2021-04-12_11-05-44/
			
			innobackupex --apply-log --redo-only /xtrabackup/2021-04-12_11-05-44/ --incremental-dir=/xtrabackup/2021-04-13_00-03-16/
			
			innobackupex --apply-log --redo-only /xtrabackup/2021-04-12_11-05-44/ --incremental-dir=/xtrabackup/2021-04-14_00-01-44/
			
			innobackupex --copy-back /xtrabackup/2021-04-12_11-05-44/
		4. 修改权限
			chown -R mysql.mysql /var/lib/mysql
		5. 启动数据库
			systemctl start mysqld

	
	
	
-------------------------------------===========
差异备份
	每次备份上一次完整备份到现在产生的新数据
	备份过程
		1、完整备份：周一
		innobackupex --user=root --password='GZgz2101..' /xtrabackup
		完整备份的文件为: /xtrabackup/2021-04-12_14-13-28/
		2、差异备份： 周二 周三
		innobackupex --user=root --password='GZgz2101..' --incremental /xtrabackup/ --incremental-basedir=/xtrabackup/2021-04-12_14-13-28/ [周二]
			备份的文件为 2021-04-13_00-00-30
		innobackupex --user=root --password='GZgz2101..' --incremental /xtrabackup/ --incremental-basedir=/xtrabackup/2021-04-12_14-13-28/ [周三]
			备份的文件为 2021-04-14_00-00-19
			
	恢复过程
		1. 停止数据库
			systemctl stop mysqld
		2. 清理环境
			rm -rf /var/lib/mysql/
		3. 重演回滚redo log（周一，某次差异）－－> 恢复数据
			innobackupex --apply-log --redo-only /xtrabackup/2021-04-12_14-13-28    [全量备份]
			innobackupex --apply-log --redo-only /xtrabackup/2021-04-12_14-13-28/ --incremental-dir=/xtrabackup/2021-04-14_00-00-19/   [基于全量的某一天的差异]
			
			innobackupex --copy-back /xtrabackup/2021-04-12_14-13-28/    [通过redo后的全量备份，恢复数据]
		4. 修改权限
			chown -R mysql.mysql /var/lib/mysql
		5. 启动数据库
			systemctl start mysqld
```







# 11.主从复制(MySQL Replication)

主从复制（也称 AB 复制）允许将来自一个MySQL数据库服务器（主服务器）的数据复制到一个或多个MySQL数据库服务器（从服务器）。

MySQL中复制的优点包括：
	横向扩展解决方案 - 在多个从站之间分配负载以提高性能。在此环境中，所有写入和更新都必须在主服务器上进行。但是，读取可以在一个或多个从设备上进行。该模型可以提高写入性能（因为主设备专用于更新），同时显着提高了越来越多的从设备的读取速度。
	数据安全性 - 因为数据被复制到从站，并且从站可以暂停复制过程，所以可以在从站上运行备份服务而不会破坏相应的主数据。
	分析 - 可以在主服务器上创建实时数据，而信息分析可以在从服务器上进行，而不会影响主服务器的性能。
	远程数据分发 - 您可以使用复制为远程站点创建数据的本地副本，而无需永久访问主服务器。





## 主从复制的原理:(重点，一点要背熟)

​	主服务器上面的任何修改都会通过自己的 I/O tread(I/O 线程)保存在二进制日志 Binary log 里面。
​	从服务器上面也启动一个 I/O thread，通过配置好的用户名和密码, 连接到主服务器上面请求读取二进制日志，然后把读取到的二进制日志写到本地的一个Realy log（中继日志）里面。
​	从服务器上面同时开启一个 SQL thread 定时检查 Realy log(这个文件也是二进制的)，如果发现有更新立即把更新的内容在本机的数据库上面执行一遍。



主从
一主多从
互为主从

```
主服务器中无数据
主服务器:
	开启binlog
		vim /etc/my.cnf
		server-id=20
		log-bin=/var/log/mysql/mysql-bin
		
		mkdir /var/log/mysql/
		chown -R mysql.mysql /var/log/mysql/
		systemctl restart mysqld
	创建用户用于主从复制
		GRANT REPLICATION SLAVE ON *.*  TO  'repl'@'%'  identified by  '123';
		flush privileges;
	在从的上登录测试用户的可用性
	
从服务器
	告诉它主服务器的ip、user、password
	1. 配置server-id
	vim /etc/my.cnf
	server-id=233
	
	systemctl restart mysqld
	
	2. 在主服务器查看二进制日志的名字
		mysql> show master status \G
		*************************** 1. row ***************************
					File: mysql-bin.000001
				Position: 589
			Binlog_Do_DB: 
		Binlog_Ignore_DB: 
		Executed_Gtid_Set: 
		1 row in set (0.00 sec)
    3. 在从服务上执行 [告诉它主服务器的ip、user、password]  [如果配置错误，执行reset slave重新配置该步骤]
	登录mysql -uroot -p'密码',不要用repl那个账号,不是用它做从，只是用来关联
	CHANGE MASTER TO
	MASTER_HOST='10.3.143.20',
	MASTER_USER='repl',
	MASTER_PASSWORD='123',
	MASTER_LOG_FILE='mysql-bin.000001',
	MASTER_LOG_POS=589;

	4. 启动从服务去的线程复制  [如果查看状态验证失败，需要先stop slave，然后重新配置change master]
	mysql> start slave;
	Query OK, 0 rows affected (0.09 sec)

	5. 查看状态
	mysql> show slave status \G
		Slave_IO_Running: Yes               //
		Slave_SQL_Running: Yes
		
主从延迟高的原因
主服务器负载高
主从网络延迟、波动
从服务器负载高[从服务器的读需求过大]
配置不合理[配置文件]
主从配置差异过大[硬件性能]
```





mysql主从[gtid方式]
基于事务的Replication，就是利用GTID来实现的复制GTID（全局事务标示符）最初由google实现，在MySQL 5.6中引入.GTID在事务提交时生成，由UUID和事务ID组成.uuid会在第一次启动MySQL时生成，保存在数据目录下的auto .cnf文件里，事务ID则从1开始自增使用GTID的好处主要有两点：

1. 不再需要指定传统复制中的master_log_files和master_log_pos，使主从复制更简单可靠
2. 可以实现基于库的多线程复制，减小主从复制的延迟

```
主库配置
vim /etc/my.cnf
	[mysqld]
	log-bin=/var/log/mysql/mysql-bin
	server-id=1
	gtid_mode=ON
	enforce_gtid_consistency=1   # 强制执行GTID一致性。

	mysql> CREATE USER 'repl'@'%' IDENTIFIED BY '123';
	mysql> GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
	

从库配置
vim /etc/my.cnf
	[mysqld]
	server-id=2
	gtid_mode=ON
	enforce_gtid_consistency=1

	mysql> CHANGE MASTER TO
	MASTER_HOST='172.16.153.10',
	MASTER_USER='repl',
	MASTER_PASSWORD='123',
	MASTER_AUTO_POSITION=1;

	mysql> start slave;
	
主从复制过程中的问题
1. 开启了gtid在导出导入数据时， --set-gtid-purged=OFF 暂时关闭gtid，保证数据正常
2. server-uuid一致，导致主从服务的io线程出问题，修改uuid重启服务即可
```













# 12.读写分离

Mycat 是一个开源的分布式数据库系统，但是由于真正的数据库需要存储引擎，而 Mycat 并没有存 储引擎，所以并不是完全意义的分布式数据库系统。 那么 Mycat 是什么？Mycat 是数据库中间件，就是介于数据库与应用之间，进行数据处理与交互的中间服务。(**重要，要记**)

MyCAT 是使用 JAVA 语言进行编写开发，使用前需要先安装 JAVA 运行环境(JRE),由于 MyCAT 中使用了 JDK7 中的一些特性，所以要求必须在 JDK7 以上的版本上运行。

````
mysql-master 10.3.143.20
mysql-slave  10.3.143.233
mycat        10.3.143.200
````

1. 配置java环境

下载JDK

```
https://www.oracle.com/cn/java/technologies/javase/javase-jdk8-downloads.html   //地址会变，记了也没用
```

解压文件

```
tar xvzf jdk-8u271-linux-x64.tar.gz  -C /usr/local/
mv /usr/local/jdk1.8.0_271 /usr/local/java //不改也行，只是为了提高识别度
```

配置环境变量

```
vim /etc/profile.d/java.sh
	JAVA_HOME=/usr/local/java
	PATH=$PATH:$JAVA_HOME/bin
	export JAVA_HOME PATH
	
 source /etc/profile.d/java.sh
```

查看版本

```
# java -version

java version "1.8.0_271"
Java(TM) SE Runtime Environment (build 1.8.0_271-b09)
java HotSpot(TM) 64-Bit Server VM (build 25.271-b09, mixed mode)
```



2. mycat下载

```
http://dl.mycat.org.cn/1.6.5/Mycat-server-1.6.5-release-20180122220033-linux.tar.gz
```

解压

```
tar xvzf Mycat-server-1.6.5-release-20180122220033-linux.tar.gz -C /usr/local/
```



3. 配置Mycat

   认识配置文件

   MyCAT 目前主要通过配置文件的方式来定义逻辑库和相关配置:

/usr/local/mycat/conf/server.xml       //定义用户以及系统相关变量，如端口等。其中用户信息是前端应用程序连接 mycat 的用户信息。

/usr/local/mycat/conf/schema.xml       //定义逻辑库，表、分片节点等内容。

```
server.xml配置修改
​```
        <user name="root" defaultAccount="true">
                <property name="password">123456</property>
                <property name="schemas">guangzhou</property>
        </user>
<!--
        <user name="user">
                <property name="password">user</property>
                <property name="schemas">guangzhou</property>
                <property name="readOnly">true</property>
        </user>
-->
​```

schema.xml配置修该
​```
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">

  <schema name="guangzhou" 
        checkSQLschema="false" 
        sqlMaxLimit="100" 
        dataNode="gecan"> 
   <!--这里定义的是分库分表的信息-->     
   </schema>
   

  <dataNode name="gecan" 
          dataHost="gz1" database="gz2" />
        
        
  <dataHost name="gz1" 
            maxCon="1000" minCon="10" 
            balance="0"
            writeType="0" 
            dbType="mysql" dbDriver="native" 
            switchType="1"  slaveThreshold="100">
            
            
   <heartbeat>select user()</heartbeat>
       <!-- can have multi write hosts -->
   <writeHost host="hostM1" url="10.3.143.20:3306" 
              user="root"  password="GZgz2101..">
      <!-- can have multi read hosts -->
      <readHost host="hostS2" url="10.3.143.233:3306" 
                user="root" password="GZgz2101.." />
     </writeHost>
   </dataHost>
</mycat:schema>
```

在真实的 master 数据库上给用户授权

```
mysql> grant all on mycat_test.* to root@'%' identified by '1';
mysql> grant all on share.* to root@'%' identified by '1'; //(这个是我自己的库，给*也行)上面的和这个执行一个就行。
mysql> flush privileges;
```

启动 mycat

```
[root@mycat ~]# /usr/local/mycat/bin/mycat  start
```

支持一下参数
start | restart |stop | status

查看服务是否真是启动

```
jps
mycat status
ps -ef|grep java
```

查看日志

```
/usr/local/mycat/logs/mycat.log
```

过滤error  Exception



客户端测试
mysql -hmycat_ip -uroot -p'' -P8066



## 我的配置

vim /usr/local/mycat/conf/server.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mycat:server SYSTEM "server.dtd">
<mycat:server xmlns:mycat="http://io.mycat/">
        <system>
        <property name="nonePasswordLogin">0</property>
        <property name="useHandshakeV10">1</property>
        <property name="useSqlStat">0</property>
        <property name="useGlobleTableCheck">0</property>  

                <property name="sequnceHandlerType">2</property>
        <property name="subqueryRelationshipCheck">false</property>
                <property name="processorBufferPoolType">0</property>
                <property name="handleDistributedTransactions">0</property>

                <property name="useOffHeapForMerge">1</property>
        <property name="memoryPageSize">64k</property>
                <property name="spillsFileBufferSize">1k</property>

                <property name="useStreamOutput">0</property>

                <property name="systemReserveMemorySize">384m</property>


                <property name="useZKSwitch">false</property>


        </system>
        

        <user name="dj" defaultAccount="true">
                <property name="password">123456</property>
                <property name="schemas">gz,gx,gd</property>
        </user>

        <user name="tom" defaultAccount="true">
                <property name="password">123456</property>
                <property name="schemas">gz</property>
</user>
</mycat:server>
```







vim /usr/local/mycat/conf/schema.xml

```
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">

  <schema name="gz" 
        checkSQLschema="false" 
        sqlMaxLimit="100" 
        dataNode="mgz">
   <!--这里定义的是分库分表的信息-->
   </schema>

 <schema name="gx" 
        checkSQLschema="false" 
        sqlMaxLimit="100" 
        dataNode="mgz1">
   <!--这里定义的是分库分表的信息-->
   </schema>

 <schema name="gd" 
        checkSQLschema="false" 
        sqlMaxLimit="100" 
        dataNode="mgz2">
   <!--这里定义的是分库分表的信息-->
   </schema>



  <dataNode name="mgz" 
          dataHost="gz1" database="share" />

  <dataNode name="mgz1" 
          dataHost="gz1" database="share1" />

  <dataNode name="mgz2" 
          dataHost="gz1" database="share2" />


  <dataHost name="gz1" 
            maxCon="1000" minCon="10" 
            balance="0"
            writeType="0" 
            dbType="mysql" dbDriver="native" 
            switchType="1"  slaveThreshold="100">


   <heartbeat>select user()</heartbeat>
       <!-- can have multi write hosts -->
   <writeHost host="hostM1" url="192.168.186.13:3306" 
              user="root"  password="1">
      <!-- can have multi read hosts -->
      <readHost host="hostS2" url="192.168.186.18:3306" 
                user="root" password="1" />
     </writeHost>
   </dataHost>
</mycat:schema>
```













# 13.mysql优化

查看引擎：

	mysql>show engines;
	mysql>show create table t1;
	mysql>show table status like 't1';		
切换默认引擎：

```
[mysqld]
default-storage-engine = innodb
```

修改已经存在的表的引擎

```
mysql> alter table t2 engine=myisam;
```

字符集设置

```
5.5/5.6/5.7版本设置：
[mysqld]
character_set_server = utf8
```

慢查询配置

```
5.7版本
[mysqld]    
slow_query_log=1
slow_query_log_file=/var/log/mysql-slow/slow.log
long_query_time=3
client连接mysql慢
[mysqld]
skip-name-resolve
```

sql语句中常用的时间函数

```
SELECT date_sub(date_sub(date_format(now(),'%y-%m-%d '),interval extract( day from now())-1 day),interval 1 month)；
select date_sub('2019-06-01',interval 1 month);
上个月第一天
select date_sub(date_sub(date_format(now(),'%y-%m-%d'),interval extract(  day from now()) day),interval 0 month)
上个月最后一天

select date_sub(date_sub(date_format(now(),'%y-%m-%d'),interval extract(  day from now())-1 day),interval 0 month)
select date_format(now(),'%Y-%m-01 ')；
这个月第一天
```

```
now()        当前日期时间
date_format  时间日期格式化  date_format('2021-04-14 10:43:28','%Y-%m-%d %h:%i:%s');
						     date_format('给定时间','格式化类型')
							 
date_sub     时间计算        date_sub('2019-06-01',interval 1 month); 给定时间一月前的日期

select date_sub(date_format(now(),'%y-%m-01'),interval 1 month);
```

