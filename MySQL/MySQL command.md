# MySQL 命令总结

## DDL

1. 连接：`mysql -uroot -p`
2. 显示所有数据库：`show databases`
3. 创建数据库：`create database dbname`
4. 删除数据库：`drop database dbname`
5. 使用数据库：`use dbname`
6. 显示所有表：`show tables`
7. 创建表：`create table tname(cname1 ctype1 constraints,cname2 ctype2 constraints,...)`
8. 查看表定义：`desc tname`
9. 查看创建表定义： `show create table tname`
10. 删除表：`drop table tname`
11. 修改表类型：`alter table tname modify [column] column_definition [first|after cname]`
12. 增加表字段：`alter talbe tname add [column] column_definition [first|after cname]`
13. 删除表字段：`alter table tname drop [column] cname`
14. 字段改名：`alter table tname change ocname cname cdefinition [first|after cname]`
15. 表改名：`alter table otname [to] rename tname`

## DML

1. 插入记录：`insert into tname (cname1,cname2,...) values (cvalue1,cvalue2,...),(cvalue1,...)` 不指定字段名时，values 后面的顺序要和字段顺序一样
2. 更新记录：`update tname set cname1=cvalue1,cname2=cvalue2,...[where condition]`
3. 多表更新：`update tname1 a,tname2 b set a.cname=b.cname,b.cname=a.cname [where condition]`
4. 删除记录：`delete from tname [where condition]`
5. 删除多表记录：`delete t1,t2,... from t1,t2,...[where condition]`
6. 查询记录：`select * from tname [where condition]`
7. 去重复查询：`select distinct cname from tname [where condition]`
8. 条件查询：`select * from tname where cname=value`
9. 排序：`select * from tname [where condition] [order by cname1 [desc|asc],cname2 [desc|asc],...]`
10. 限制：`select * from tname [where condition] [limit offset_start, row_count]` 起始偏移量（offset_start）默认为 0，row_count 表示显示的行数
11. 聚合：`select cname fun_name from tname [where condition] [group by cname1,cname2,...] [with rollup] [having condition]`
    - `fun_name` 表示要做的聚合操作 (`sum ()`,`count ()`,`max ()`,`min ()`)
    - `group by` 表示要进行聚合的字段
    - `with rollup` 对聚合后的结果汇总
    - `having` 对聚合后的结果过滤
12. 表连接
    - 内连接：`select * from tname1,tname2 [where condition]`
    - 外连接：
        - 左连接：`select cn1,cn2 from tn1 left join tn2 on tn1.cn2==tn2.cn2`
        - 右连接：`select cn1,cn2 from tn1 right join tn2 on tn1.cn2==tn2.cn2`
13. 子查询：`select * from tn1 where cn in (select * from tn2)` 关键字包括 `in`,`not in`,`=`,`!=`
14. 记录联合：`select * from tn1 union|union all select * from tn2`

## DCL

1. 创建用户授予权限：`grant select,insert on dataname.* to 'username'@'localhost' identified by 'password'`
2. 变更权限：`revoke select on dataname.* from 'username'@'localhost'`

## others

1. 数据库支持的存储引擎： `show engines`
2. 创建表时设置存储引擎：`create table tn (cn int)engine=innodb defualt charset=utf8`
3. 修改存储引擎：`alter table tn engine=myisam dufault charset=gbk`
4. 设置 auto_increment 的初始值，默认从 1 开始：`alter table tn auto_increment=10`
5. 查询当前线程最后插入记录使用的值：`select last_insert_id()`
6. 关闭外键检查：`set foreign_key_checks=0`
7. 查看表的状态：`show table status like 'tn'`
8. 增加外键：`alter table tn1 constraint fkname add foreign key(indexname) references tn2(indexname) on delete no action on update cascade`
    - cascade 跟随父表更新删除
    - restrict 默认值，和 no action 一样，限制父表更新删除
    - set null 父表更新删除，子表设置为 null
9. 创建表时添加外键：`create table tn (cn int primary key,foreign key(cn) references tn2(cn) on update cascade on delete restrict)`
10. 删除外键：`alter table tn drop foreign key fkname`
11. 查看字符集：`show character set，information_schema.character_sets`
12. 查看字符集的校对规则：`show collation like 'utf8%'，information_schema.collations`
13. 服务器级字符集，在 my.cnf 文件中设置： `[mysqld] character-set-server=utf8`
14. 查看服务器字符集：`show variables like 'character_set_server'`
15. 显示数据库字符集和校对规则：`show variables like 'character_set_database'，show variables like 'collation_database'`
16. 修改数据库字符集：`alter database db character set utf8`
17. 显示表字符集：`show create table tn`
18. 修改表字符集：`alter table tn default character set utf8`
19. 设置连接字符集，在 my.cnf 文件中设置：`[mysql] default-character-set=utf8`，或每次通过命令 `set names utf8`
20. 导出表：`mysqldump -uroot -p db>newdb.sql`
    -d 不导出插入的数据
    -t 不导出表创建表结构
    -n 不导出创建数据库的语句
    --quick 该选项用于转储大的表。它强制 mysqldump 从服务器一次一行地检索表中的行而不是检索所有的行，并在输出前将它缓存到内存中
    --extended-insert 使用包括几个 values 列表的多行 insert 语法。这样使转储文件更小，重载文件时可以加速插入
    --no-create-info 不导出每个转储表的 create table 语句
    --default-character-set=Latin1 按照原有的字符集导出所有数据，这样导出的文件中，所有中文都是可见的，不会保存成乱码
21. 导入数据：`mysql -uroot -p db<newdb.sql`
22. 创建索引：`create [unique|fulltext|spatial]index indexname on tname(cname(length),...)`
23. 增加索引：`alter table tn add index indexname(length)`
24. 删除索引：`drop index indexname on tname 或 alter table tname drop index indexname`
25. 创建视图： `create [or replace] [algorithm = {undefined | merge | temptable}] view viewname as select_statement [with [cascaded | local] check ption]`
26. 修改视图：`alter [algorithm = {undefined | merge | temptable}] view viewname as select_statement [with [cascaded | local] check option]`
27. 删除视图：`drop view viewname`
28. 查看视图：`show create view viewname，show table status like 'viewname'`
29. 修改定界符：` delimiter ;`
30. 创建存储过程： `create procedure pname([parameter[,...]])[characteristic ...] routine_body`
31. 创建函数：`create function fname([parameter[,...]])returns type[characteristic ...] routine_body`
32. 修改存储过程或函数：`alter {procedure|function} name [characteristic...]`
33. 调用过程：`call name(parameter[,...])`
34. 删除存储过程或函数：`drop {procedure|function} [if exists] name`
35. 查看存储过程或函数的状态：`show [procedure|function] status like 'name'`
36. 查看存储过程或函数的定义：`show create [procedure|function] name`
37. 查看 information_schema.routines 表了解存储过程和函数的信息`
38. 定义变量：`declare varname[,...] type [default value]`
39. 变量赋值：`set varname=value,[varname=value]...`
40. 通过查询赋值：`select cname into varname from tname`
41. 定义条件：`declare condition_name condition for sqlstate_value|mysql_error_code`
42. 定义条件处理：`create continue|exit|undo handler for (sqlstate_value|condition_name|sqlwarning|not fond|sqlexception|mysql_error_code)[,...] p_statement`
43. 创建光标：`declare cursor_name cursor for select_statement`
44. open 光标：`open cursor_name`
45. fetch 光标：`fetch cursor_name into var_name[,var_name]...`
46. close 光标：`close curosr_name`
47. 变量，条件，处理程序，光标都是通过 declare 定义的，它们之间是有先后顺序要求的。（变量，条件，光标，处理程序）
48. if 语句：`if search_condition then statement_list [elseif search_condition then statement_list]... [else statement_list] endif`
49. case 语句：`case case_value when when_value then statement_list [when when_value then statement_list]...[else statement_list] end case 或 case when search_condition then statement_list [when search_condition then statement_list]... [else statement_list] end case`
50. loop 语句: `[begin_label:]loop statement_list end loop[end_label]`
51. leave 语句：`leava label `退出循环
52. iterate 语句：`iterate label `放弃循环剩下的语句，进入下一个循环
53. repeat 语句：`[begin_label:] repeat statement_list until search_condition end repeat [end_label]` 满足条件退出循环，至少执行一次
54. while 语句：`[begin_label:] while search_condition do statement_list end while[end_label]` 满足条件执行循环
55. 事件调度器：`create event eventname on schedule every 5 second do insert into tname values(1,2,3)`
56. 查看事件：`show events`
57. 查看事件状态：`show variables like '%scheduler%'`
58. 打开事件调度器：`set global event_scheduler = 1`
59. 禁用事件：`alter event eventname disabled`
60. 删除事件：`drop event eventname`
61. 清空表：`truncate table tablename`
62. 查看线程：`show processlist`
63. 创建触发器：`create trigger triggername before|after update|delete|insert on tname for each row begin ... end`
64. 删除触发器：`drop trigger triggername`
65. 查看触发器：`show triggers` 或 `information_schema.triggers` 表
66. 表锁：`lock tables tname {read [local]|[low_priority]write},tname... 或 lock table tname read|write`
67. 表解锁：`unlock tables`
68. 开始事务：`start transaction 或 begin`
69. 提交事务：`commit [and chain|release]`
70. 回滚：`rollback [to savepoint pointname]`
71. 关闭自动提交：`set autocommit=0`
72. 指定事务回滚的位置：`savepoint pointname`
73. 删除位置：`release savepoint pointname`
74. 查看 SQL `mode：select @@sql_mode`
75. 设置 SQL `mode：set (session|global)? sql_mode='modes'`
76. range 分区：`create table tname (...)partition by range [columns](cname)(partition p0 values less than(10),partition p1 values less than(20),...artition pn values less than maxvalue)`
77. 删除分区 (range|list)：`alter table tname drop partition p0`
78. 增加分区 (range|list)：`alter table tname add partition (partition p3 values less than (30))`
79. list 分区：`create table tname (...) partition by list[columns](cname)(partition p0 in (1,3,5),partition p1 in (2,4,6),...)`
80. hash 分区 (整数)：`create table tname (...) partition by [linear] hash (cname) partitions n`
81. key 分区：`create table tname(...) partition by [linear] key (cname) partitions n``
82. 子分区：`create table tname(...)partition by range|list (cname) subpartition by hash|key(cname) subpartitions n (partition p0 values less than 10),partition p1 values less than (20))`
83. 重组分区 (range|list)：`alter table tname reorganize partition p0,p1 into (partition p0 values less than (20))`
84. 合并分区 (key|hash)：`alter table tname coalesce partition 2`
85. 增加分区 (key|hash)：`alter table tname add partition partitions 8`
86. 显示 SQL 执行频率：`show status like 'com%'`
87. 显示 SQL 执行计划：`explain select * from tname` 或 `explain extended select * from tname` 或 `explain partitions select * from tname`
88. 检查是否支持 profile：`select @@have_profiling`
89. 检查是否开启 profiling：`select @@profiling`
90. 设置 profiling：`set profiling = 1`
91. 查看 profiles：`show profiles`
92. 查看 profile：`show profile [all|cpu|block io|context switch| page faults] for query queryid`
93. 查看索引使用情况：`show status like 'handler_read%'`
94. 分析表：`analyze [local|no_write_to_binlog] table tname[,tname]...`
95. 检查表：`check table tname[,tname]... [quick|fast|medium|extended|changed]`
96. 优化表：`optimize [local|no_write_to_binlog] table tname[,tname]...`
97. 设置 MyISAM 表非唯一索引的更新： `alter table tname enable|disable keys`
98. 设置唯一性校验：`set unique_checks=0|1`
99. 显示表的索引：`show index from tname`
100. SQL 提示：`select * from tname use index(indexname)` 或 `select * from tname ignore index (indexname)` 或 `select * from tname force index(indexname)`
101. 随机排序：`order by rand()`
102. 不排序：`order by null`
103. bit_or(cn) 和 `bit_and(cname)`
104. 优化表的数据类型：`select * from tn procudure analyse()`
105. 查看表锁争夺情况：`show status like 'table%'`
106. 降低更新请求的优先级：`set low_priority_updates=1`
107. 查看行锁争夺情况：`show status like 'innodb_row_lock%'`
108. 共享读锁：`select * from tn where ... lock in share mode`
109. 排他写锁：`select ... for update`
110. 行锁是给索引加锁，不然会给所有行加锁，相同索引的值都会加锁
111. 查看隔离级别：`select @@tx_isolation`
112. 查看发生死锁的原因：`show innodb status`
113. 查看所有线程的状态：`show engine innodb status`
114. 查看缓存区大小：`show variables like 'key_buffer_size'`
115. 查看查询缓存：`show variables like 'query_cache'`
116. 显示所有命令 `mysql --help`
117. 查看当前连接的用户：`select current_user()`
118. 连接时设置连接字符集：`mysql -uroot -p --default-character-set=utf8`
119. 在 mysqld 开启 binlog：`bin-log=master-bin`
120. 在 mysqld 设置 binlogindex：`bin-log-index=master-bin.index`
121. 查看所有 binlog：`show master logs`
122. 查看最新 binlog：`show master status`
123. binlog 读取：`mysqlbinlog binlogname-bin.000001` 或 `show binlog events in 'binlogname-bin.000001' [from pos] [limit]`
124. 刷新 binlog（所有新的记录都记录在新的 binlog 中）：`flush logs`
125. 清空 binlog：`reset master`
126. 删除 binlog.000006 之前的 binlog：`purge master logs to 'binlog.000006'`
127. 删除某时间之前的 binlog：`purge master logs before '2017-11-8 14:47:00'`
128. 从 binlog 恢复：`mysqlbinlog --stop-position=1234 binlog-bin.000002 | mysql -uroot -p`
129. 在 mysqld 设置日志的过期天数：`expire_logs_day=n`
130. 设置查询日志：`set global general_log=1`
131. 导出表：`select * from tn into outfile 'filename'`
132. 导入表：`load data infile 'filename' into table tn [fileds terminated by ',' (optionally)? enclosed by '"' ...]`
133. 创建用户 (授予权限)：`grant all privileges on *.* to name@'127.0.0.1' identified by '123' [with grant option|max_queries_per_hours n|ax_updates_per_hours n|max_connections_per_hours n|max_user_connections n]`
134. 查看用户权限：`show grants for user@host`
135. 收回权限：`revoke all privileges,grant option on *.* from user@host[,user...]`
136. 修改密码：`set password for user@host=password('123')`
137. 删除用户：`drop user username@host`
138. 刷新权限：`flush privileges|flush user_resources|mysqladmin reload`
139. 设置 mysqld 跳过权限表：`skip-grant-tables`
