# MySQL 优化


查询语句执行次数

```sql
-- 查看 student 表操作行数
mysql> SHOW GLOBALS STATUS LIKE 'student_rows_deleted';
mysql> SHOW GLOBALS STATUS LIKE 'student_rows_inserted';
mysql> SHOW GLOBALS STATUS LIKE 'student_rows_read';
mysql> SHOW GLOBALS STATUS LIKE 'student_rows_updated';
```

定位低效率语句