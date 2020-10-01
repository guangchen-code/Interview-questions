# SQL (Structure Query Language) 基础

## SQL (Structure Query Language) 基础

- DDL (Data Definition Language) 语句：数据定义语句，这些语句定义了不同的数据段，数据库，表，列，索引等数据库对象
- DML (Data Manipulation Language) 语句：数据操纵语句，用于添加，删除，更新和查询数据库记录，并检查数据完整性
- DCL (Data Control Language) 语句：数据控制语句，用于控制不同数据段直接的许可和访问级别的语句。这些语句定义了数据库，表，字段，用户的访问权限和安全级别

### DDL 语句

#### 数据库

1. 连接数据库
    `mysql -u root -p`
    `mysql` 代表客户端命令
    `-u` 后面跟连接的数据库用户
    `-p` 表示需要输入密码

2. 查看数据库
    `show databases;`
3. 选择要操作的数据库
    `use dbname;`
4. 查看数据库的表
    `show tables;`
5. 删除数据库
    `drop database dbname;`

#### 表

1. 创建表

    ```sql
    CREATE TABLE tablename(
        column_name1 column_type1 constraints,
        ...
        column_name_n column_type_n constraints
    );
    ```

    `column_name` 是列的名字
    `column_type` 是列的类型
    `constraints` 是这个列的约束条件

2. 查看表的定义

    ```sql
    DESC tablename;
    ```

    查看更全面的表的定义

    ```sql
    SHOW CREATE TABLE tablename;
    ```

3. 删除表

    ```sql
    DROP TABLE tablename;
    ```

4. 修改表

    ```sql
    修改表的类型
    ALTER TABLE tablename
    MODIFY [COLUMN] col_name column_definition
    [FIRST | AFTER col_name];
    增加表字段
    ALTER TABLE tablename
    ADD [COLUMN] col_name column_definition
    [FIRST | AFTER col_name];
    删除表字段
    ALTER TABLE tablename
    DROP [COLUMN] col_name;
    字段改名，同时修改字段类型
    ALTER TABLE tablename
    CHANGE [COLUMN] old_col_name new_col_name column_definition
    [FIRST | AFTER col_name];
    修改字段排列顺序
    前面介绍的的字段增加和修改语法（ADD/CNAHGE/MODIFY）中，都有一个可选项 FIRST|AFTER col_name，这个选项可以用来修改字段在表中的位置，默认 ADD 增加的新字段是加在表的最后位置，而 CHANGE/MODIFY 默认都不会改变字段的位置
    表改名
    ALTER TABLE tablename
    RENAME [TO] new_tablename;
    ```

### DML 语句

1. 插入记录

    ```sql
    INSERT INTO tablename [(field1,field2,……fieldn)]
    VALUES(value1,value2,……valuesn)[,(value1,value2,......valuesn)]*;
    ```

    对于含可空字段、非空但是含有默认值的字段、自增字段，可以不用在 INSERT 后的字段列表里面出现，values 后面只写对应字段名称的 value，这些没写的字段可以自动设置为 NULL、
    默认值、自增的下一个数字，这样在某些情况下可以大大缩短 SQL 语句的复杂性。

    也可显示的插入特定的值，设置为可空的字段都显示为 NULL

    ```sql
    INSERT INTO tablename (field1,field3)
    VALUES(value1,value3);
    ```

2. 更新记录

    ```sql
    UPDATE tablename
    SET field1=value1，field2=value2，……fieldn=valuen
    [WHERE CONDITION];
    ```

    同时更新多个表中数据

    ```sql
    UPDATE t1 [a],t2 [b]
    SET a.field1=expr1,b.field2=expr2
    [WHERE CONDITION];
    // a 和 b 是 t1 和 t2 这两个表的别名
    ```

3. 删除记录

    ```sql
    DELETE FROM tablename
    [WHERE CONDITION];
    ```

    一次删除多个表的数据

    ```sql
    DELETE t1,t2…tn
    FROM t1,t2…tn
    [WHERE CONDITION]
    ```

    如果 from 后面的表名用别名，则 delete 后面的也要用相应的别名，否则会提示语法错误

4. 查询记录

    ```sql
    SELECT *
    FROM tablename
    [WHERE CONDITION];
    // * 表示要将所有的记录都选出来，也可以用逗号分割的所有字段来代替
    ```

    查询不重复的记录
    有时需要将表中的记录去掉重复后显示出来，可以用 distinct 关键字来实现

    ```sql
    SELECT DISTINCT colname1,colname2
    FROM tablename;
    ```

    条件查询
    在很多情况下，用户并不需要查询所有的记录，而只是需要根据限定条件来查询一部分数据，用 where 关键字可以实现

    排序和限制

    ```sql
    SELECT *
    FROM tablename
    [WHERE CONDITION]
    [ORDER BY field1 [DESC|ASC] ， field2 [DESC|ASC]，……fieldn [DESC|ASC]]
    ```

    其中，DESC (descending order) 和 ASC (ascending order) 是排序顺序关键字，DESC 表示按照字段进行降序排列，ASC 则表示升序排列，如果不写此关键字默认是升序排列。ORDER BY 后面可以跟多个不同的排序字段，并且每个排序字段可以有不同的排序顺序。如果排序字段的值一样，则值相同的字段按照第二个排序字段进行排序，以此类推。如果只有一个排序字段，则这些字段相同的记录将会无序排列

    对于排序后的记录，如果希望只显示一部分，而不是全部，这时，就可以使用 LIMIT 关键字

    ```sql
    SELECT ……
    [LIMIT offset_start,row_count]
    ```

    其中 offset_start 表示记录的起始偏移量，row_count 表示显示的行数。在默认情况下，起始偏移量为 0，只需要写记录行数就可以，这时候，显示的实际就是前 n 条记录

    聚合

    ```sql
    SELECT [field1,field2,……fieldn] fun_name
    FROM tablename
    [WHERE where_contition]
    [GROUP BY field1,field2,……fieldn [WITH ROLLUP]]
    [HAVING where_contition];
    ```

    fun_name 表示要做的聚合操作，也就是聚合函数，常用的有 sum（求和）、count (*)（记录数）、max（最大值）、min（最小值）

    GROUP BY 关键字表示要进行分类聚合的字段

    WITH ROLLUP 是可选语法，表明是否对分类聚合后的结果进行再汇总

    HAVING 关键字表示对分类后的结果再进行条件的过滤

    **注意：** having 和 where 的区别在于 having 是对聚合后的结果进行条件的过滤，而 where 是在聚合前就对记录进行过滤，如果逻辑允许，我们尽可能用 where 先过滤记录，这样因为结果集减小，将对聚合的效率大大提高，最后再根据逻辑看是否用 having 进行再过滤

    表连接
    表连接分为内连接和外连接，它们之间的最主要区别是內连接仅选出两张表中互相匹配的记录，而外连接会选出其他不匹配的记录。

    我们最常用的是内连接

    ```sql
    SELECT ename,deptname
    FROM emp,dept
    WHERE emp.deptno=dept.deptno;
    ```

    外连接有分为左连接和右连接

    左连接：包含所有的左边表中的记录甚至是右边表中没有和它匹配的记录

    ```sql
    SELECT ename,deptname
    FROM emp
    LEFT JOIN dept
    ON emp.deptno=dept.deptno;
    ```

    右连接：包含所有的右边表中的记录甚至是左边表中没有和它匹配的记录。右连接和左连接类似，两者之间可以互相转化。

    ```sql
    SELECT ename,deptname
    FROM dept
    RIGHT JOIN emp
    ON dept.deptno=emp.deptno;
    ```

    子查询
    某些情况下，当我们查询的时候，需要的条件是另外一个 select 语句的结果，这个时候，就要用到子查询。用于子查询的关键字主要包括 in、not in、=、!=、exists、not exists 等

    ```sql
    SELECT *
    FROM emp
    WHERE deptno
        IN(SELECT deptno
           FROM dept);
    ```

    记录联合
    我们经常会碰到这样的应用，将两个表的数据按照一定的查询条件查询出来后，将结果合并到一起显示出来，这个时候就需要用 union 和 union all 关键字来实现这样的功能

    ```sql
    SELECT *
    FROM t1
    UNION|UNION ALL
    SELECT *
    FROM t2;
    ```

    UNION 和 UNION ALL 的主要区别是 UNION ALL 是把结果集直接合并在一起，而 UNION 是将 UNION ALL 后的结果进行一次 DISTINCT，去除重复记录后的结果

### DCL 语句

grant 和 revoke

帮助的使用

1. 如果不知道帮助能够提供些什么，可以用 `? contents` 命令来显示所有可供查询的的分类
2. 对于列出的分类，可以使用 `? 类别名称` 的方式针对用户感兴趣的内容做进一步的查看
3. 在实际应用当中，如果需要快速查阅某项语法时，可以使用关键字进行快速查询

例如，想知道 show 命令都能看些什么东西，可以用 `? show` 命令
