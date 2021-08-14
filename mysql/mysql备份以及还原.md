# MySQL 备份与还原

## 备份

mysqldump -uusername -hhost -p db > DIR/backup.sql

## 还原

`mysql -uusername -p db < backup.sql`

```sql
mysql> user db;
mysql> source backup.sql;
```

[1](https://blog.csdn.net/helloxiaozhe/article/details/77680255)
