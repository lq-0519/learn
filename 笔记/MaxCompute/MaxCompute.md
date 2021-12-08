# INSERT INTO | INSERT OVERWRITE

`insert into`或 `insert overwrite`操作可以将 `select`查询的结果保存至目标表中。二者的区别是：

- `insert into`：直接向表或静态分区中插入数据。您可以在`insert`语句中直接指定分区值，将数据插入指定的分区。如果您需要插入少量测试数据，可以配合[VALUES](https://help.aliyun.com/document_detail/73778.htm#concept-uhn-rdb-wdb)使用。
- `insert overwrite`：先清空表中的原有数据，再向表或静态分区中插入数据。

MaxCompute的`insert`语法与通常使用的MySQL或Oracle的`insert`语法有差别。在`insert overwrite`后需要加`table`关键字，非直接使用`table_name`。`insert into`可以省略`table`关键字。

insert overwrite 不支持指定列名

## partition

需要插入数据的分区信息，不允许使用函数等表达式，只能是常量







