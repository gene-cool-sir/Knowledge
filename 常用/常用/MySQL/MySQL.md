# MySQL

1.  查看表信息

```sql
SELECT
    ENGINE,  --  表使用的存储引擎。
VERSION,  --  表的存储引擎特定版本号。
ROW_FORMAT,  --  表的行格式。
TABLE_ROWS,  --  表中的行数。
AVG_ROW_LENGTH,  --  表中平均一行的长度。
DATA_LENGTH,  --  表中数据的长度，不包含索引的长度。
MAX_DATA_LENGTH,  --  表中数据的最大长度。
INDEX_LENGTH,  --  表中索引的总长度。
DATA_FREE,  --  表中空闲空间的长度。
AUTO_INCREMENT,  --  表的下一个自动增量值。
CREATE_TIME,  --  表的创建时间。
UPDATE_TIME,  --  表的最后一次更新时间。
CHECK_TIME,  --  表的最后一次检查时间。
TABLE_COLLATION,  --  表的字符集和校对规则。
CHECKSUM,  --  表的校验和。
CREATE_OPTIONS,  --  表的创建选项。
TABLE_COMMENT --  表的注释。
FROM information_schema.tables
WHERE TABLE_SCHEMA='table-name'
```
