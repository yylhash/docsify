  ## 1.mysql的调优经验
  ### 1.1 优化查询语句：

- **索引失效：** 一般是先通过执行计划分析是否走了索引，以及所走的索引是否符合预期，如果因为索引设计的不合理、或者索引失效导致的，那么就可以修改索引，或者修改SQL语句。或者强制执行使用某个索引。
- **使用合适的索引：** 确保查询中的字段有合适的索引，特别是在 WHERE 子句中经常用到的字段。
- **避免全表扫描：** 尽量避免使用 `SELECT *`，只选择实际需要的字段，并优化查询条件。
- **优化复杂查询：** 避免复杂的 JOIN 操作，尽量将查询分解成多个简单的查询。

### 1.2 优化数据库结构：

- **合理设计表结构：** 正规化设计数据库，避免过度冗余，确保表之间的关系清晰。
- **使用合适的数据类型：** 避免使用过大的数据类型，尽量使用能满足需求的最小数据类型，减小存储空间和提高查询性能。

## 2.索引失效的问题如何排查？
通过查看查询的执行计划，可以确定查询是否使用了预期的索引。不同数据库系统有不同的工具来查看执行计划：
- MySQL: 使用`EXPLAIN` 语句。
```sql
EXPLAIN SELECT * FROM table_name WHERE column_name = 'value';
```
- SQL Server: 使用 SET STATISTICS PROFILE ON; 或查看执行计划图。
> 首先，key一定要有值，不能是NULL
 其次，type应该是ref、eq_ref、range、const等这几个
 还有，extra的话，如果是NULL，或者using index，using index condition都是可以的

### 2.1.索引失效的几种情况
> 索引列参与计算
 ```sql
 select * from mytable where age = 12;
 ```
 以上SQL是可以走索引的，但是如果我们在字段中增加计算的话，就会索引失效：
 ```sql
 select * from mytable where age +1 = 12;
 ```
 但是如果是如下形式的计算还是可以走索引的：
 ```sql
 select * from mytable where age = 12 - 1;
 ```
> 对索引列进行函数操作
 ```sql
 SELECT * FROM mytable WHERE create_time = '2023-04-01 00:00:00';
 ```
 以上SQL是可以走索引的，但是如果我们在字段中增加函数操作的话，就会索引失效：
 ```sql
 SELECT * FROM mytable WHERE YEAR(create_time) = 2022;
 ```
> 使用OR
 ```sql
 SELECT * FROM mytable WHERE name = 'Hollis' and age > 18;
 ```
 但是如果使用OR的话，并且OR的两边存在<或者>的时候，就会索引失效，如：
 ```sql
 SELECT * FROM mytable WHERE name = 'Hollis' OR age > 18;
 ```
 但是如果OR两边都是=判断，并且两个字段都有索引，那么也是可以走索引的，如：
 ```sql
 mysql> explain SELECT * FROM mytable WHERE name = 'Hollis' OR age = 18;
 ```
> 在使用`LIKE`语句时，是否使用索引取决于具体的匹配模式和数据库系统。一般情况下，只有当`LIKE`语句的模式以常量开头时，索引才会被使用。例如，`LIKE`'abc%' 可以使用索引，而`LIKE`'%abc%'和`LIKE`'_abc%' 通常不会使用索引。
> 隐式类型转换
 ```sql
 select * from mytable where name = 1; 
 ```
 以上情况，name是一个varchar类型，但是我们用int类型查询，这种是会导致索引失效的。
 这种情况有一个特例，如果字段类型为int类型，而查询条件添加了单引号或双引号，则Mysql会参数转化为int类型，这种情况也能走索引：
 ```sql
 select * from mytable where age = '1';
 ```
> is not null
 以下情况是索引失效的：
 ```sql
 SELECT * FROM mytable WHERE name is not null
 ```
> order by
 ```sql
 SELECT * FROM mytable order by age
 ```
 当进行order by的时候，如果数据量很小，数据库可能会直接在内存中进行排序，而不使用索引。







 以上内容来源自网络，需要删除请联系我！！！
