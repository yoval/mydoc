# Mysql

## 数据查询

完整语法

```mysql
SELECT
  哪些数据(列名（AGG_FUNC、DISTINCT），即要显示的数据，*代表全部列)
FROM
  哪张表（表名）
WHERE
  查寻条件 AND 其它条件 OR 别的条件
ORDER BY 
	<列名> ASC 查询结果按某列排序（ASC升序，DESC降序）
LIMIT 100 仅返回前100行
OFFSET 100 从前100行开始返回
```

### 查询条件

| 关键词              | 含义             | 举例                        |
| ------------------- | ---------------- | --------------------------- |
| =, !=, <, <=, >, >= | 等于、不等于，   | <列名> != 4                 |
| BETWEEN … AND …     | 在两个数之间     | <列名> BETWEEN 1.5 AND 10.5 |
| NOT BETWEEN … AND … | 不在两个数之间   | <列名> NOT BETWEEN 1 AND 10 |
| IN (…)              | 在一个列表       | <列名> IN (2, 4, 6)         |
| NOT IN (…)          | 不在一个列表     | <列名> NOT IN (1, 3, 5)     |
| %                   | 通配符，字符数>0 | <列名> like "%abc%"         |
| -                   | 通配符，字符数=1 | <列名> like "abc-"          |

### 特殊关键词

- `DISTINCT` 唯一值，如 `SELECT DISTINCT <列名> FROM <表名>`

- `NULL` 空值，如`SELECT * FROM <表名> WHERE <列名> IS NOT NULL`

  

### 多表查询

类似excel的xlookup，两张表有相同的列，用`JOIN`进行拼接。

其中：

- LEFT JOIN ，把第二张表匹配到第一张表，保留第一张表的所有行。
- RIGHT JOIN ，把第一张表匹配到第二张表，保留第二张表的所有行。
- INNER JOIN （JOIN），只会保留两个表都存在的行。
- FULL JOIN，保留两张表的所有行。

```mysql
SELECT
  <表名1>.<列名1>,
  <表名2>.<列名2>,
FROM
  <表名1>
  INNER JOIN <表名2> ON <表名1>.<共同列> = <表名2>.<共同列>;
WHERE ...
```

### 表达式查询

```mysql
SELECT
  business_date AS 日期,
  stat_shop_name AS 门店名称,
  report_amount/0.8 AS 报货金额
FROM
  ads_dbs_report_food_di
```

- `AS`，给表达式取了个别名，它同时可以给表、普通列取别名。
- `report_amount/0.8 ` 选取数据可以直接是表达式。

### 统计函数

数据库是先对数据做`WHERE`，然后对结果做`GROUP BY` 。

| 函数                    | 含义                                                         |
| ----------------------- | ------------------------------------------------------------ |
| COUNT(*), COUNT(column) | 计数！COUNT(*) 统计数据行数，COUNT(column) 统计column非NULL的行数. |
| MIN(column)             | 找column最小的一行.                                          |
| MAX(column)             | 找column最大的一行.                                          |
| AVG(column)             | 对column所有行取平均值.                                      |
| SUM(column)             | 对column所有行求和.                                          |

例，将报货金额进行求和。

```mysql
SELECT
  stat_shop_name AS 门店名称,
  SUM(report_amount) / 0.8 AS 报货金额
FROM
  ads_dbs_report_food_di
GROUP BY
  stat_shop_name
```

- `HAVING` 函数，对`GROUP BY`再“`WHERE`”一次，一般没啥必要。

### 其它内置函数

#### 日期函数

| 函数          | 描述                                   |
| ------------- | -------------------------------------- |
| NOW()         | 返回当前日期和时间。`SELECT NOW()`     |
| CURDATE()     | 返回当前日期。`SELECT CURDATE()`       |
| CURTIME()     | 返回当前时间。`SELECT CURTIME()`       |
| DATE()        | 提取日期或日期时间表达式的日期部分。   |
| DAY()         | 返回月份中的一天（0-31）。             |
| DAYNAME()     | 返回工作日的名称。星期几（英文）       |
| MONTH()       | 返回经过日期（1-12）的月份。           |
| MONTHNAME()   | 返回月份的名称。                       |
| YEAR()        | 返回年份。                             |
| DATE_FORMAT() | 以其他格式显示日期和时间值。           |
| EXTRACT()     | 提取日期的一部分。                     |
| DATE_ADD()    | 将指定的时间值（或间隔）添加到日期值。 |
| DATE_SUB()    | 从日期值中减去指定的时间值（或间隔）。 |
| DATEDIFF()    | 返回两个日期之间的天数                 |





## 插入数据

`INSERT INTO table_name (column1,column2,...) VALUES (value1,value2,...);`

## 数据修改

`UPDATE table_name SET column1_name = value1, column2_name = value2,...WHERE condition;`

## 数据删除

`DELETE FROM table_name WHERE condition;`

## 实例

### 语句

```mysql
-- 查询每个门店的上次橙子报货时间、上次柠檬报货时间以及2024年4月的流水金额
SELECT
  t1.门店编号,
  t1.上次橙子报货时间,
  t1.上次柠檬报货时间,
  t2.流水金额
FROM
  -- 子查询1：获取每个门店的上次橙子报货时间和上次柠檬报货时间
  (SELECT
    stat_shop_id AS 门店编号,
    MAX(CASE WHEN orange_report_cnt > 0 THEN business_date END) AS 上次橙子报货时间,
    MAX(CASE WHEN is_lemon_report > 0 THEN business_date END) AS 上次柠檬报货时间
  FROM
    ads_dbs_report_food_di
  GROUP BY
    stat_shop_id) AS t1
JOIN
  -- 子查询2：获取每个门店2024年4月的流水金额
  (SELECT
    stat_shop_id AS 门店编号,
    LEFT(business_date, 6) AS 月份,
    SUM(total_amount) AS 流水金额
  FROM
    ads_dbs_report_food_di
  WHERE
    LEFT(business_date, 6) = '202404'
  GROUP BY
    stat_shop_id, 月份) AS t2
ON
  t1.门店编号 = t2.门店编号;
```

### 说明

`CASE`的语法是

```mysql
CASE
  WHEN condition1 THEN result1
  WHEN condition2 THEN result2
  ...
  ELSE resultN
END
```

```mysql
SELECT
    stat_shop_id as 门店编号,
    orange_report_cnt as 柠檬报货数量,
    CASE 
        WHEN orange_report_cnt > 0 THEN '有报柠檬'
        WHEN orange_report_cnt IS NULL OR orange_report_cnt <= 0 THEN '未报柠檬'
    END as 报柠檬状态
FROM
    ads_dbs_report_food_di;
```

