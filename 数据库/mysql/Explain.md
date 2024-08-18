## id

表示查询中执行select子句或者操作表的顺序，**`id`的值越大，代表优先级越高，越先执行**。id相同，具有同样的优先级，具体顺序由优化器决定。

## select_type

select查询类型

### SIMPLE

表示最简单的 select 查询语句，也就是在查询中不包含子查询或者 `union`交并差集等操作。

### PRIMARY

当查询语句中包含任何复杂的子部分，最外层查询则被标记为`PRIMARY`

### SUBQUERY

当 `select` 或 `where` 列表中包含了子查询，该子查询被标记为：`SUBQUERY` 

### DERIVED

表示包含在`from`子句中的子查询的select，在我们的 `from` 列表中包含的子查询会被标记为`derived`

### UNION

如果`union`后边又出现的`select` 语句，则会被标记为`union`；若 `union` 包含在 `from` 子句的子查询中，外层 `select` 将被标记为 `derived`。

### UNION RESULT

代表从`union`的临时表中读取数据

## **table**

查询的表名，并不一定是真实存在的表，有别名显示别名，也可能为临时表，

## **partitions**

查询时匹配到的分区信息，对于非分区表值为`NULL`，当查询的是分区表时，`partitions`显示分区表命中的分区情况。

## **type**

查询使用了何种类型，它在 `SQL`优化中是一个非常重要的指标，以下性能从好到坏依次是：`system` > `const` > `eq_ref` > `ref` > `ref_or_null` > `index_merge` > `unique_subquery` > `index_subquery` > `range` > `index` > `ALL`

### system

当表仅有一行记录时(系统表)，数据量很少，往往不需要进行磁盘IO，速度非常快。

### const

表示查询时命中 `primary key` 主键或者 `unique` 唯一索引，或者被连接的部分是一个常量(`const`)值。这类扫描效率极高，返回数据量少，速度非常快。

### eq_ref

查询时命中主键`primary key` 或者 `unique key`索引， `type` 就是 `eq_ref`

### ref

区别于`eq_ref` ，`ref`表示使用非唯一性索引，会找到很多个符合条件的行。

### index_merge

使用了索引合并优化方法，查询使用了两个以上的索引

### unique_subquery

使用内部子查询替换为索引查找。

```
value IN (SELECT primary_key FROM single_table WHERE some_expr)
```

### index_subquery

区别于`unique_subquery`，用于非唯一索引，可以返回重复值。

### range

用索引选择行，仅检索给定范围内的行。简单点说就是针对一个有索引的字段，给定范围检索数据。在`where`语句中使用 `bettween...and`、`<`、`>`、`<=`、`in` 等条件查询 `type` 都是 `range`。

### index

`Index` 与`ALL` 其实都是读全表，区别在于`index`是遍历索引树读取，而`ALL`是从硬盘中读取。

### all

将遍历全表以找到匹配的行，性能最差。

## **possible_keys**

表示在`MySQL`中通过哪些索引，能让我们在表中找到想要的记录，一旦查询涉及到的某个字段上存在索引，则索引将被列出，**但这个索引并不定一会是最终查询数据时所被用到的索引**。

## **key**

区别于`possible_keys`，key是查询中实际使用到的索引，若没有使用索引，显示为`NULL`

## key_len

表示查询用到的索引长度（字节数），原则上长度越短越好 。

## ref

常见的有：`const`，`func`，`null`，字段名。

- 当使用常量等值查询，显示`const`，
- 当关联查询时，会显示相应关联表的`关联字段`
- 如果查询条件使用了`表达式`、`函数`，或者条件列发生内部隐式转换，可能显示为`func`
- 其他情况`null`

## rows

以表的统计信息和索引使用情况，估算要找到我们所需的记录，需要读取的行数。这是评估`SQL` 性能的一个比较重要的数据，`mysql`需要扫描的行数，很直观的显示 `SQL` 性能的好坏，一般情况下 `rows` 值越小越好。

## **filtered**

用于表示经过 WHERE 子句或其他过滤条件后保留下来的行的估计比例。越低越好。较高的`filtered`值可能意味着查询优化器预期可以有效地减少需要处理的行数。

## **Extra**

附加信息

### Using index

我们在相应的 `select` 操作中使用了覆盖索引，通俗一点讲就是查询的列被索引覆盖，使用到覆盖索引查询速度会非常快，`SQl`优化中理想的状态。

### Using where

查询时未找到可用的索引，进而通过`where`条件过滤获取所需数据，但要注意的是并不是所有带`where`语句的查询都会显示`Using where`

### Using temporary

表示查询后结果需要使用临时表来存储，一般在排序或者分组查询时用到。

### Using filesort

表示无法利用索引完成的排序操作，也就是`ORDER BY`的字段没有索引，通常这样的SQL都是需要优化的。

### Impossible where

表示在我们用不太正确的`where`语句，导致没有符合条件的行。