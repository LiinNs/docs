# EXPLAIN 执行计划

可以分析 SELECT、DELETE、INSERT、REPLACE、UPDATE

EXPLAIN语句会根据SELECT执行时的读取到表的顺序，产生一条关于表的信息

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