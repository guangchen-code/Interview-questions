# MySQL 存储过程与函数

> 存储过程和函数是事先经过编译并存储在数据库中的一段 SQL 语句的集合，调用存储过程和函数可以简化应用开发人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的
> 存储过程和函数的区别在于函数必须有返回值，而存储过程没有，存储过程的参数可以使用 `in`，`out`，`inout` 类型，而函数的参数只能是 `in` 类型的。如果有函数从其他类型的数据库迁移到 MySQL，那么就可能因此需要将函数改造成存储过程

## 存储过程和函数的相关操作

在对存储过程或函数进行操作时，需要首先确认用户是否具有相应的权限。例如，创建存储过程或者函数需要 `CREATE ROUTINE` 权限，修改或者删除存储过程或者函数需要 `ALTER ROUTINE` 权限，执行存储过程或者函数需要 `EXECUTE` 权限

### 创建，修改存储过程或者函数

```sql
CREATE PROCEDURE sp_name ([proc_parameter[,...]])
[characteristic ...] routine_body

CREATE FUNCTION sp_name ([func_parameter[,...]])
RETURNS type
[characteristic ...] routine_body

    proc_parameter:[ IN | OUT | INOUT ] param_name type

    func_parameter:param_name type

    type:Any valid MySQL data type

    characteristic:LANGUAGE SQL| [NOT] DETERMINISTIC| { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }| SQL SECURITY { DEFINER |INVOKER }| COMMENT 'string'

    routine_body:Valid SQL procedure statement or statements

ALTER {PROCEDURE | FUNCTION} sp_name [characteristic ...]

    characteristic:{ CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }| SQL SECURITY { DEFINER | INVOKER }| COMMENT 'string'
```

调用过程的语法：`CALL sp_name([parameter[,...]]);`

调用函数与调用过程存在一些差异。可以以调用内置函数的相同方式调用一个存储函数 `SELECT functionname();`

MySQL 的存储过程和函数中允许包含 DDL 语句，也允许在存储过程中执行提交（`COMMIT`，即确认之前的修改）或者回滚（`ROLLBACK`，即放弃之前的修改），但是存储过程和函数中不允许执行 `LOAD DATA INFILE` 语句。此外，存储过程和函数中可以调用其他的过程或者函数

通常我们在执行创建过程和函数之前，都会通过 `DELIMITER $$` 命令将语句的结束符从 `;` 修改成其他符号，这里使用的是 `$$`，这样在过程和函数中的 `;` 就不会被 MySQL 解释成语句的结束而提示错误。在存储过程或者函数创建完毕，通过 `DELIMITER ;` 命令再将结束符改回成 `;`

和视图的创建语法稍有不同，存储过程和函数的 `CREATE` 语法不支持使用 `CREATE OR REPLACE` 对存储过程和函数进行修改，如果需要对已有的存储过程或者函数进行修改，需要执行 `ALTER` 语法

下面对 `characteristic` 特征值的部分进行简单的说明：

- `LANGUAGE SQL` 说明下面过程的 BODY 是使用 SQL 语言编写，这条是系统默认的，为今后 MySQL 会支持的除 SQL 外的其他语言支持的存储过程而准备
- `DETERMINISTIC` 确定的，即每次输入一样输出也一样的程序
- `NOT DETERMINISTIC` 非确定的，默认是非确定的。当前，这个特征值还没有被优化程序使用
- `{ CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }` 这些特征值提供子程序使用数据的内在信息，这些特征值目前只是提供给服务器，并没有根据这些特征值来约束过程实际使用数据的情况
- `CONTAINS SQL` 表示子程序不包含读或写数据的语句
- `NO SQL` 表示子程序不包含 SQL 语句
- `READS SQL DATA` 表示子程序包含读数据的语句，但不包含写数据的语句
- `MODIFIES SQL DATA` 表示子程序包含写数据的语句。如果这些特征没有明确给定，默认使用的值是 `CONTAINS SQL`
- `SQL SECURITY { DEFINER | INVOKER }` 可以用来指定子程序该用创建子程序者的许可来执行，还是使用调用者的许可来执行。默认值是 `DEFINER`
- `COMMENT 'string'` 存储过程或者函数的注释信息

### 删除存储过程或者函数

一次只能删除一个存储过程或者函数，删除存储过程或者函数需要有该过程或者函数的 ALTER ROUTINE 权限，具体语法如下：

```sql
DROP {PROCEDURE | FUNCTION} [IF EXISTS] sp_name
```

### 查看存储过程或者函数

查看存储过程或者函数的状态

```sql
SHOW {PROCEDURE | FUNCTION} STATUS [LIKE 'pattern']
```

查看存储过程或者函数的定义

```sql
SHOW CREATE {PROCEDURE | FUNCTION} sp_name
```

通过查看 `information_schema.Routines` 了解存储过程和函数的信息

### 变量的使用

> 存储过程和函数中可以使用变量，变量是不区分大小写的

通过 `DECLARE` 可以定义一个局部变量，该变量的作用范围只能在 `BEGIN…END` 块中，可以用在嵌套的块中。变量的定义必须写在复合语句的开头，并且在任何其他语句的前面。可以一次声明多个相同类型的变量。如果需要，可以使用 `DEFAULT` 赋默认值

定义一个变量的语法如下：

```sql
DECLARE var_name[,...] type [DEFAULT value]
```

直接赋值使用 SET，可以赋常量或者赋表达式，具体语法如下：

```sql
SET var_name = expr [, var_name = expr] ...
```

也可以通过查询将结果赋给变量，这要求查询返回的结果必须只有一行，具体语法如下：

```sql
SELECT col_name[,...] INTO var_name[,...] table_expr
```

### 定义条件和处理

条件的定义和处理可以用来定义在处理过程中遇到问题时相应的处理步骤

条件的定义

```sql
DECLARE condition_name CONDITION FOR condition_value

    condition_value:SQLSTATE [VALUE] sqlstate_value
        | mysql_error_code
```

条件的处理

```sql
DECLARE handler_type HANDLER FOR condition_value[,...] sp_statement

    handler_type:CONTINUE | EXIT | UNDO

    condition_value:SQLSTATE [VALUE] sqlstate_value
        | condition_name
        | SQLWARNING
        | NOT FOUND
        | SQLEXCEPTION
        | mysql_error_code
```

`handler_type`：`CONTINUE` 表示继续执行下面的语句，`EXIT` 则表示执行终止

`condition_value` 的值可以是通过 `DECLARE` 定义的 `condition_name`，可以是 `SQLSTATE` 的值或者 `mysql-error-code` 的值或者 `SQLWARNING`、`NOT FOUND`、`SQLEXCEPTION`，这 3 个值是 3 种定义好的错误类别，分别代表不同的含义：

1. `SQLWARNING` 是对所有以 01 开头的 `SQLSTATE` 代码的速记
2. `NOT FOUND` 是对所有以 02 开头的 `SQLSTATE` 代码的速记
3. `SQLEXCEPTION` 是对所有没有被 `SQLWARNING` 或 `NOT FOUND` 捕获的 `SQLSTATE` 代码的速记

### 光标的使用

在存储过程和函数中可以使用光标对结果集进行循环的处理。光标的使用包括光标的声明，OPEN， FETCH 和 CLOSE，其语法分别如下：

1. 声明光标：

    ```sql
    DECLARE cursor_name CURSOR FOR select_statement
    ```

2. OPEN 光标：

    ```sql
    OPEN cursor_name
    ```

3. FETCH 光标：

    ```sql
    FETCH cursor_name INTO var_name [, var_name] ...
    ```

4. CLOSE 光标：

    ```sql
    CLOSE cursor_name
    ```

**注意：** 变量、条件、处理程序、光标都是通过 `DECLARE` 定义的，它们之间是有先后顺序的要求的。变量和条件必须在最前面声明，然后才能是光标的声明，最后才可以是处理程序的声明

### 流程控制

1. IF 语句

    IF 实现条件判断，满足不同的条件执行不同的语句列表，具体语法如下：

    ```sql
    IF search_condition THEN statement_list
    [ELSEIF search_condition THEN statement_list] ...
    [ELSE statement_list]
    END IF
    ```

2. CASE 语句

    CASE 实现比 IF 更复杂一些的条件构造，具体语法如下：

    ```sql
    CASE case_value
    WHEN when_value THEN statement_list
    [WHEN when_value THEN statement_list] ...
    [ELSE statement_list]
    END CASE
    Or：

    CASE
    WHEN search_condition THEN statement_list
    [WHEN search_condition THEN statement_list] ...
    [ELSE statement_list]
    END CASE
    ```

3. LOOP 语句

    LOOP 实现简单的循环，退出循环的条件需要使用其他的语句定义，通常可以使用 LEAVE 语句实现，具体语法如下：

    ```sql
    [begin_label:] LOOP
    statement_list
    END LOOP [end_label]
    ```

    如果不在 statement_list 中增加退出循环的语句，那么 LOOP 语句可以用来实现简单的死循环

4. LEAVE 语句

    用来从标注的流程构造中退出，通常和 BEGIN ... END 或者循环一起使用

5. ITERATE 语句

    ITERATE 语句必须用在循环中，作用是跳过当前循环的剩下的语句，直接进入下一轮循环

6. REPEAT 语句

    有条件的循环控制语句，当满足条件的时候退出循环，具体语法如下：

    ```sql
    [begin_label:] REPEAT
    statement_list
    UNTIL search_condition
    END REPEAT [end_label]
    ```

7. WHILE 语句

    WHILE 语句实现的也是有条件的循环控制语句，即当满足条件时执行循环的内容，具体语法如下：

    ```sql
    [begin_label:] WHILE search_condition DO
    statement_listEND WHILE [end_label]
    ```

    WHILE 循环和 REPEAT 循环的区别在于：WHILE 是满足条件才执行循环，REPEAT 是满足条件退出循环；WHILE 在首次循环执行之前就判断条件，所以循环最少执行 0 次，而 REPEAT 是在首次执行循环之后才判断条件，所以循环最少执行 1 次

### 事件调度器

事件调度器可以将数据库按自定义的时间周期触发某种操作，可以理解为时间触发器

下面是一个最简单的事件调度器，每 5 秒向表中插入数据

```sql
CREATE EVENT eventname
ON SCHEMA
EVERY 5 SECOND
DO
INSERT INTO tablename VALUES(value)
```

- 事件名称在 CREATE EVENT 关键字后指定
- 通过 ON SCHEMA 子句指定事件在何时执行及执行频次
- 通过 DO 子句指定要执行的具体操作或事件

查看事件：`SHOW EVENTS;`

查看调度器：`SHOW VARIABLES LIKE '%scheduler'` 默认是关闭的

打开调度器：`SET GLOBAL event_scheduler=1;`，事件才能启动

查看后台进程：`SHOW PROCESSLIST;`

禁用事件：`ALTER EVENT eventname DISABLE;`

删掉事件：`DROP EVENT eventname;`
