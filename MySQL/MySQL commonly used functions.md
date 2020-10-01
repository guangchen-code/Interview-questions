# MySQL 常用函数

在 MySQL 数据库中，函数可以用在 `SELECT` 语句及其子句（例如 `where`、`order by`、`having` 等）中，也可以用在 `UPDATE`、`DELETE` 语句及其子句中

## 字符串函数

| 函数 | 功能 |
| - | - |
| concat(s1,s2,...sn) | 连接 s1,s2,...sn 为一个字符串 |
| insert(str,x,y,instr) | 将字符串 str 从第 x 位置开始，y 个字符长的子串替换为字符串 instr |
| lower(str) | 将 str 转换为小写 |
| upper(str) | 将 str 转换为大写 |
| left(str,x) | 返回 str 最左边的 x 个字符 |
| right(str,x) | 返回 str 最右边的 x 个字符 |
| lpad(str,n,pad) | 用字符串 pad 对 str 最左边进行填充，直到字符串长度为 n |
| rpad(str,n,pad) | 用字符串 pad 对 str 最右边进行填充，直到字符串长度为 n |
| ltrim(str) | 去掉 str 行首的空格 |
| rtrim(str) | 去掉 str 行尾的空格 |
| trim(str) | 去掉 str 首尾的空格 |
| repeat(str,x) | 返回 str 重复 x 次的结果 |
| replace(str,a,b) | 用字符串 b 替换 str 中所有出现的字符串 a |
| strcmp(s1,s2) | 比较字符串 s1 和 s2 的 ASCII 码值的大小 |
| substring(str,x,y) | 返回从 str 的 x 位置起 y 个字符长度的子串 |

1. `concat()` 函数与 NULL 连接时返回 NULL
2. `insert()` 和 `substring()` 的位置从 1 开始

## 数值函数

| 函数 | 功能 |
| - | - |
| abs(x) | 返回 x 的绝对值 |
| ceil(x) | 向上取整 |
| floor(x) | 向下取整 |
| mod(x,y) | 取模 |
| rand() | 返回 0 ~ 1 内的随机数 |
| round(x,y) | 返回 x 的四舍五入的有 y 位小数的值 |
| truncate(x,y) | 返回数字 x 截断为 y 为小数的值 |

1. `round()` 函数 y 默认为 0

## 日期和时间函数

| 函数 | 功能 |
| - | - |
|  curdate() |  返回当前日期 |
|  curtime() |  返回当前时间 |
|  now() |  返回当前的日期和时间 |
|  unix_timestamp(date) |  返回日期 date 的 unix 时间戳 |
|  from_unixtime(timestamp) |  返回 unix 的时间戳的日期值 |
|  week(date) |  返回日期 date 为一年中的第几周 |
|  year(date) |  返回 date 的年份 |
|  hour(date) |  返回 date 的小时 |
|  minute(date) |  返回 date 的分钟 |
|  month(date) |  返回 month 的月份 |
|  monthname(date) |  返回 date 的英文月份名 |
|  date_format(date,fmt) |  返回按字符串 fmt 格式化日期 date 值 |
|  date_add(date,interval expr type) |  返回一个日期或时间值加上一个时间间隔的日期值 |
|  date_diff(expr,expr2) |  返回起始时间 expr 和结束时间 expr2 之间的天数 |

1. `unix_timestamp()` 和 `from_unixtime()` 函数互为逆操作
2. `date_format()` 格式符

    | 格式符 | 格式说明 |
    | - | - |
    |  % s 和 % S |  两位数字形式的秒 (00,01,...,59) |
    | %i |  两位数字形式的分 (00,01,...,59) |
    | %H |  两位数字形式的小时，24 小时 (00,01,...,23) |
    | % h 和 % I |  两位数字形式的小时，12 小时 (01,02,...,12) |
    | %k |  数字形式的小时，24 小时 (0,1,...,23) |
    | %l |  数字形式的小时，12 小时 (1,2,...,12) |
    | %T | 24 小时的时间形式 (hh:mm:ss) |
    | %r | 12 小时的时间形式 (hh:mm:ssAM 或 hh:mm:ssPM) |
    | %p | AM 或 PM |
    | %W |  一周中每一天的名称 (Sunday,Monday,...,Saturday) |
    | %a |  一周中每一天名称的缩写 (Sun，Mon,...,Sat) |
    | %d |  两位数字表示月中的天数 (00,01,...,31) |
    | %e |  数字形式表示月中的天数 (1,2,...,31) |
    | %D |  英文后缀表示月中的天数 (1st,2nd,3rd,...) |
    | %w |  以数字形式表示周中的天数 (0=Sunday,1=Monday,...,6=Saturday) |
    | %j |  以 3 位数字表示年中的天数 (001,002,...,366) |
    | %U |  周 (0,1,52)，其中 Sunday 为周中的第一天 |
    |  %u |  周 (0,1,52), 其中 Monday 为周中的第一天 |
    |  %M |  月名 (January,February,...,December) |
    | %b |  缩写的月名 (Jan,Feb,...,Dec) |
    | %m |  两位数字表示的月份 (01,02,...,12) |
    | %c |  数字表示的月份 (1,2,...,12) |
    | %Y | 4 位数字表示的年份 |
    |  %y | 2 位数字表示的年份 |
    |  %% |  直接值 % |
    |  %% |  直接值 % |

3. `date_add(date,interval expr type)` 函数，interval 是间隔类型关键字，expr 是表达式，对应后面的类型，type 是间隔类型

    | 间隔类型 | 描述 | 格式 |
    | - | - | - |
    |  hour |  小时 |  hh |
    | minute |  分 |  mm |
    | second |  秒 |  ss |
    | year |  年 |  YY |
    | month |  月 |  MM |
    | day |  日 |  DD |
    | year_month |  年和月 |  YY-MM |
    | day_hour |  日和小时 |  DD hh |
    | day_minute |  日和分钟 |  DD hh:mm |
    | day_second |  日和秒 |  DD hh:mm:ss |
    | hour_minute |  小时和分 |  hh:mm |
    | hour_second |  小时和秒 |  hh:ss |
    | minute_second |  分钟和秒 |  mm:ss |

    ```sql
    select date_add(now(),interval 1_2 year_month)
    ```

### 流程函数

| 函数 | 功能 |
| - | - |
|  if(value,t f) |  如果 value 为真，返回 t，否则返回 f |
| ifnull(value1,value2) |  如果 value1 不为空，返回 value1，否则返回 value2 |
| case when [value1] then [result1]...else [default] end |  如果 value1 是真，返回 result1，否则返回 default |
| case [expr] when [value1] then [result1]...else [default] end |  如果 expr 等于 value1，返回 result1，否则返回 default |

1. `ifnull(value1,value2)` 函数一般用来替换 null 值。当 value1 为 null 时返回 value2

### 其它常用函数

| 函数 | 功能 |
| - | - |
|  database() |  返回当前数据库名 |
|  version() |  返回当前数据库版本 |
|  user() |  返回当前登录用户名 |
|  inet_aton(IP) |  返回 IP 地址的数字表示 |
|  inet_ntoa(num) |  返回数字代表的 IP 地址 |
|  password(str) |  返回字符串 str 的加密版本 |
|  md5(str) |  返回字符串 str 的 md5 值 |