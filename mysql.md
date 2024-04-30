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

## 查询解析

```mysql
-- 公共表表达式（CTE）
WITH variables AS (
    -- 定义变量
    SELECT 'TLL06486' AS shop_id, 20240101 AS start_date, 20240426 AS end_date
),
-- 联合所有查询的CTE
combined AS (
    -- 从两个表中选取数据并添加source列
    SELECT
        stat_shop_id,
        report_amount,
        NULL AS total_amount,
        'ads_dbs_report_food_di' AS source
    FROM
        ads_dbs_report_food_di
    JOIN
        variables
    ON
        ads_dbs_report_food_di.stat_shop_id = variables.shop_id
        AND ads_dbs_report_food_di.business_date BETWEEN variables.start_date AND variables.end_date
    UNION ALL
    SELECT
        stat_shop_id,
        NULL,
        total_amount,
        'ads_dbs_trade_shop_di' AS source
    FROM
        ads_dbs_trade_shop_di
    JOIN
        variables
    ON
        ads_dbs_trade_shop_di.stat_shop_id = variables.shop_id
        AND ads_dbs_trade_shop_di.business_date BETWEEN variables.start_date AND variables.end_date
)
-- 根据门店编号分组并计算报货金额和流水金额的总和
SELECT
    stat_shop_id AS 门店编号,
    SUM(CASE WHEN source = 'ads_dbs_report_food_di' THEN report_amount / 0.8 ELSE 0 END) AS 报货金额,
    SUM(CASE WHEN source = 'ads_dbs_trade_shop_di' THEN total_amount ELSE 0 END) AS 流水金额
FROM
    combined
GROUP BY
    门店编号;

```

## 常用函数

### WITH

WITH 关键字在 SQL 中用于创建一个临时名称的结果集，也称为公共表表达式（Common Table Expression，CTE）。它可以在同一个查询中被多次引用，从而简化查询、提高性能和可读性。

CTE，可以进行调用

```mysql
WITH variables AS (
    SELECT 'TLL06486' AS shop_id, 20240101 AS start_date, 20240426 AS end_date
)
SELECT *
FROM variables;
```

子查询，别名

```mysql
SELECT *
FROM (
    SELECT 'TLL06486' AS shop_id, 20240101 AS start_date, 20240426 AS end_date
) AS shop_data;

```

### UNION

`UNION`与`UNION ALL`

上下拼接，查询列顺序必须相同，可以使用别名。UNION 会删除结果中的重复行，包含数据库本身的重复行。UNION ALL 不会。

```mysql
WITH orders AS (
  SELECT 1 AS id, '2023-03-01' AS order_date, 100 AS total_amount, 'paid' AS status
  UNION ALL
  SELECT 2 AS id, '2023-03-02' AS order_date, 200 AS total_amount, 'paid' AS status
  UNION ALL
  SELECT 3 AS id, '2023-03-03' AS order_date, 300 AS total_amount, 'unpaid' AS status
  UNION ALL
  SELECT 4 AS id, '2023-03-04' AS order_date, 400 AS total_amount, 'paid' AS status
),
paid_orders AS (
  SELECT id, order_date, total_amount
  FROM orders
  WHERE status = 'paid'
),
unpaid_orders AS (
  SELECT id, order_date, total_amount
  FROM orders
  WHERE status = 'unpaid'
)
SELECT id, order_date, total_amount
FROM paid_orders
UNION ALL
SELECT id, order_date, total_amount
FROM unpaid_orders;

```

### JOIN 

```mysql
WITH users AS (
  SELECT 1 AS id, 'Alice' AS name, 25 AS age
  UNION ALL
  SELECT 2 AS id, 'Bob' AS name, 30 AS age
  UNION ALL
  SELECT 3 AS id, 'Charlie' AS name, 35 AS age
),
orders AS (
  SELECT 1 AS id, 1 AS user_id, '2023-03-01' AS order_date, 100 AS total_amount
  UNION ALL
  SELECT 2 AS id, 1 AS user_id, '2023-03-02' AS order_date, 200 AS total_amount
  UNION ALL
  SELECT 3 AS id, 2 AS user_id, '2023-03-03' AS order_date, 300 AS total_amount
)
SELECT u.id, u.name, u.age, o.id AS order_id, o.order_date, o.total_amount
FROM users AS u
RIGHT JOIN orders AS o
ON u.id = o.user_id;
```

## CASE

1.返回固定值

```mysql
WITH numbers AS (
  SELECT 1 AS num
  UNION ALL
  SELECT 2 AS num
  UNION ALL
  SELECT 3 AS num
)
SELECT num,
       CASE
           WHEN num = 1 THEN 'one'
           WHEN num = 2 THEN 'two'
           WHEN num = 3 THEN 'three'
           ELSE 'other'
       END AS num_text
FROM numbers;

```

2.
