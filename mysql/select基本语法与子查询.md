# select

## 数据类型

索引 https://mp.weixin.qq.com/s/QruFD8_v3ZzTcqdLjwMmOg

json数据类型

## 函数索引

~~~SQL
ALTER TABLE UserJson ADD COLUMN name VARCHAR(128)
AS (json_unquote(json_extract(data, "$.name"))) VIRTUAL;

ALTER TABLE UserJson ADD INDEX idx_name (name);
~~~

## 表

~~~SQL
-- 查看表结构
SHOW CREATE TABLE XXX;
SHOW TABLE STATUS XXX;
DESC XXX;
SHOW FULL COLUMNS FROM XXX;
~~~

wordpress 表结构设计

ON UPDATE RESTRICT

metadata 元数据 描述数据的数据

information_schema TABLES CLOMUNS

utf8mb4_ci

ALTER TABLE ONLINE | OFFLINE

Summary of Online Status for DDL Operation

分区表 PARTITIONS

- 将一张表或者索引分解为多个更小、更可管理的部分
- 只支持水平分区
- 局部分区索引
  每个分区保存自己的数据和索引
- 分区列必须是唯一索引的一个组成部分

分区类型 RANGE LIST HASH KEY COLUMNS

PARTITION BY RANGE(id)(
  PARTION p0 VALUES LESS THAN(10),
  PARTION p0 VALUES LESS THAN(20)
);

PARTITIONS 4

ORDER BY
GROUP BY
COUNT
HAVING 对聚合后的结果进行过滤

## WHERE 与 HAVING 有什么区别

AND OR IN NOT

SQL（像多数语言一样）在处理OR操作符前，优先处理AND操作符。

~~~SQL
SELECT prod_name, prod_price 
FROM Products 
WHERE vend_id = 'DLL01' OR vend_id = 'BRS01'  AND prod_price >= 10;
~~~

当SQL看到上述WHERE子句时，它理解为：由供应商BRS01制造的价格为10美元以上的所有产品，以及由供应商DLL01制造的所有产品，而不管其价格如何。换句话说，由于AND在求值过程中优先级更高，操作符被错误地组合了。

IN操作符一般比一组OR操作符执行得更快。

~~~SQL
insert into t (a, b, c) select a, b, c from t_b;
select * into t_member_copy from t_member;

CREATE TABLE t_member_copy AS SELECT * FROM t_member;
~~~
