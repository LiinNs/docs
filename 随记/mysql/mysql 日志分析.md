### Mysql 日志分析

MySQL的二进制日志binlog可以说是MySQL最重要的日志，它记录了所有的DDL和DML语句（除了数据查询语句select），以事件形式记录，还包含语句所执行的消耗的时间，MySQL的二进制日志是事务安全型的。  

mysqlbinlog常见的选项有以下几个：

> start-datetime：从二进制日志中读取指定等于时间戳或者晚于本地服务器的时间
> stop-datetime：从二进制日志中读取指定小于时间戳或者等于本地服务器的时间 取值和上述一样
> start-position：从二进制日志中读取指定position 事件位置作为开始。
> stop-position：从二进制日志中读取指定position 事件位置作为事件截至
> -d：根据指定库读取binlog
> -r：重定向 `mysqlbinlog mysqlbin.000001 --start-position=365  -r pos.sql`

每次服务器（数据库）重启，服务器会调用`mysql> flush logs;`，新创建一个binlog日志；  
在mysqldump备份数据时加 -F 选项也会刷新binlog日志。  


#### 查看 mysql 启动相关配置 

`ps auxww|grep mysqld`

~~~ bash
root       104  0.0  0.0  11840  2704 pts/0    S    Apr13   0:00 /bin/sh /www/server/mysql/bin/mysqld_safe --datadir=/www/server/data --pid-file=/www/server/data/50f20b07bcef.pid
mysql      827  1.2 15.8 4302000 962940 pts/0  Sl   Apr13 121:09 /www/server/mysql/bin/mysqld --basedir=/www/server/mysql --datadir=/www/server/data --plugin-dir=/www/server/mysql/lib/plugin --user=mysql --log-error=50f20b07bcef.err --open-files-limit=65535 --pid-file=/www/server/data/50f20b07bcef.pid --socket=/tmp/mysql.sock --port=3306
root     35910  0.0  0.0   9104   876 pts/3    S+   09:13   0:00 grep --color=auto mysqld
~~~

binlog日志与数据库文件在同目录中 `--datadir=/www/server/data`

#### binlog



- 方式1 mysqlbinlog

`mysqlbinlog --base64-output=DECODE-ROWS -v mysql-bin.000144 |more`

option `--base64-output=DECODE-ROWS`： 会显示出row模式带来的sql变更。

option `-v` ：显示statement模式带来的sql语句

~~~ log
# at 4
#210413 14:55:55 server id 1  end_log_pos 125 CRC32 0xa7cbfad3  Start: binlog v 4, server v 8.0.23 created 210413 14:55:55 at startup
ROLLBACK/*!*/;
BINLOG '
e0B1YA8BAAAAeQAAAH0AAAAAAAQAOC4wLjIzAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAB7QHVgEwANAAgAAAAABAAEAAAAYQAEGggAAAAICAgCAAAACgoKKioAEjQA
CigB0/rLpw==
'/*!*/;
# at 125
#210413 14:55:55 server id 1  end_log_pos 156 CRC32 0xa86556f5  Previous-GTIDs
# [empty]
# at 156
#210413 15:00:51 server id 1  end_log_pos 233 CRC32 0xb17b248c  Anonymous_GTID  last_committed=0        sequence_number=1       rbr_only=no     original_committed_timestamp=1618297251326033 immediate_commit_timestamp=1618297251326033     transaction_length=250
# original_commit_timestamp=1618297251326033 (2021-04-13 15:00:51.326033 CST)
# immediate_commit_timestamp=1618297251326033 (2021-04-13 15:00:51.326033 CST)
/*!80001 SET @@session.original_commit_timestamp=1618297251326033*//*!*/;
/*!80014 SET @@session.original_server_version=80023*//*!*/;
/*!80014 SET @@session.immediate_server_version=80023*//*!*/;
SET @@SESSION.GTID_NEXT= 'ANONYMOUS'/*!*/;
~~~

解释：
server id 1: 数据库主机的服务号；
end_log_pos: 125 sql结束时的pos节点

- 方式2 mysql

~~~ sql
-- 查看当前的binlog 模式 row,mixed,statement
SHOW GLOBAL VARIABLES LIKE "%binlog_format%";

-- 设置 binlog 的模式
SET GLOBAL binlog_format = ¨STATEMENT¨;

-- 列出所有的binlog
show [binary|master] logs;

-- 列出当前使用的binlog状态
show master status;

-- 创建新的binlog文件
flush logs;

-- 查看binlog详细记录
show binlog events [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count];
~~~


#### redo log(innoDb log)

~~~ sql
-- 查看innodb log变量
show variables like "%innodb_log%”;

+------------------------------------+----------+
| Variable_name                      | Value    |
+------------------------------------+----------+
| innodb_log_buffer_size             | 16777216 |
| innodb_log_checksums               | ON       |
| innodb_log_compressed_pages        | ON       |
| innodb_log_file_size               | 50331648 |
| innodb_log_files_in_group          | 2        |
| innodb_log_group_home_dir          | ./       |
| innodb_log_spin_cpu_abs_lwm        | 80       |
| innodb_log_spin_cpu_pct_hwm        | 50       |
| innodb_log_wait_for_flush_spin_hwm | 400      |
| innodb_log_write_ahead_size        | 8192     |
+------------------------------------+----------+

-- 查看innodb状态
show engine innodb status;
~~~

> innodb_log_buffer_size: redolog缓存区的大小，即16m
> innodb_log_file_size: redolog文件的大小，即48m
> innodb_log_files_in_group: 日志文件组中文件数量
> innodb_log_group_home_dir: 日志文件组路径即 mysql/data


#### undo log(innoDb log)

#### 备份Mysql

`bash> mysqldump -uroot -p -B -F -R -x --master-data=2 ops|gzip >/opt/backup/ops_$(date +%F).sql.gz`

参数说明：

> -B：指定数据库
> -F：刷新日志
> -R：备份存储过程等
> -x：锁表
> --master-data：在备份语句里添加CHANGE MASTER语句以及binlog文件及位置点信息

#### reference

1. https://www.cnblogs.com/f-ck-need-u/p/9001061.html#auto_id_6
2. https://www.cnblogs.com/f-ck-need-u/p/9010872.html
3. www.junmajinlong.com
4. https://www.cnblogs.com/jevo/p/3281139.html
5. https://tjjsjwhj.me/2020/04/13/mysql%E4%B9%8Bredolog/
6. https://www.cnblogs.com/kevingrace/p/5907254.html 从备份和binlog恢复mysql
7. https://www.cxyxiaowu.com/10740.html SQL 和 NoSql 基础 非常好的博客 公众号