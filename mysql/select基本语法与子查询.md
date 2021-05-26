# select

## 数据类型

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
