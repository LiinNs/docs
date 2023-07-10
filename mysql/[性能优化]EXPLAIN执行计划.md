# EXPLAIN 执行计划

https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain-join-types

可以分析 SELECT、DELETE、INSERT、REPLACE、UPDATE

EXPLAIN 语句会根据 SELECT 执行时的读取到表的顺序，产生一条关于表的信息

id select_type table partitions type possible_keys key ken_len ref rows filtered extra

## type

- system
- const
- eq_ref
- ref
- fulltext
- ref_or_null
- index_merge
- unique_subquery
- index_subquery
- range
- index
- All

MySQL can use indexes on columns more efficiently if they are declared as the same type and size. 

Check whether the numbers are even close to the truth by comparing the rows product with the actual number of rows that the query returns. If the numbers are quite different, you might get better performance by using STRAIGHT_JOIN in your SELECT statement and trying to list the tables in a different order in the FROM clause.

[索引](https://mp.weixin.qq.com/s/QruFD8_v3ZzTcqdLjwMmOg)
