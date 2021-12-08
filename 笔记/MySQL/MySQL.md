# 索引

- 搜索时禁止使用左模糊或全模糊，因为索引文件具有B树的最左前缀匹配原则，使用左模糊是无法使用索引的。

- 建立了唯一索引后，在插入数据前都要事先查询一下要插入的数据是否已经存在，否则直接插入可能会报错。
  - 另外无须担心建立唯一索引会影响插入效率，这点影响可以忽略不计。

# Mybatis

## include标签

sql标签定义sql片段, 使用include标签引用sql, 可以复用sql代码

可以引用公共的sql片段

```xml
<include refid="namespace.sqlid"/>
```

# 省略from

可以省略from字句

例如:

select 2*3   返回6

select now()  返回当前时间

# 正则表达式

regular expression

MySQL支持正则表达式

# group by

select子句后面只能出现检索列和有效的表达式

例如:

```sql
select username, avg(sum)
from invitationCodeList
group by username
order by avg(sum) desc
```

## having

having过滤组

where过滤行

where是优先于having执行的, where过滤掉的行是不会出现在分组中的

# count(1)与count(*)

[高性能MySQL count(1)与count(*)的差别 - cool小伙 - 博客园 (cnblogs.com)](https://www.cnblogs.com/xiaozengzeng/p/12078845.html)

有主键或联合主键的情况下，count()略比count(1)快一些。 
没有主键的情况下count(1)比count()快一些。 
如果表只有一个字段，则count(*)是最快的。

# 不推荐使用外键约束

每次做DELETE和UPDATE都需要考虑外键的存在，使开发非常痛苦

使用外键的好处

- 保证数据的完整性一致性
- 将判断数据完整性的操作交给了数据库，可以减少应用层的代码

使用外键带来的问题

- 性能开销：如果表a有两个外键执行表b表c，那么每次修改数据时都需要关联b和c，带来很多性能开销

# 表连接

LEFT：保留左边表的全部数据

RIGHT：保留右边表的全部数据

# UNION

union相当于一个select语句中存在多个where条件

使用union时，多个select子句检索列必须相同，或者MySQL可以隐式的进行转换

union可以应用于不同的表，只要不同的select子句有相同的检索列就行了

UNION ALL不会忽略重复的列

# 全文本搜索

InnoDB不支持全文搜索的，MyISAM支持

# INSERT

使用INSERT时尽量指定要插入的列有哪些，而不要仅仅使用VALUES

# UPDATE

**IGNORE**关键字：我们在更新多行数据时，如果某一行更新出现错误，整个UPDATE操作就会终止，并且之前更新过的行也会被恢复成原来的样子，使用IGNORE关键字可以实现，及时出现错误，UPDATE操作也会继续执行下去，忽略出现错误的行

# 执行引擎

执行引擎可以混合使用，但是有一个缺点，外键是不能跨引擎使用的

# 视图

**为什么使用视图？**

1. 重用SQL语句
2. 可以使用表的组成部分而不是整个表
3. 保护数据。

# 触发器

sql语句

```sql
CREATE TRIGGER trigger_name AFTER INSERT ON t1 FOR EACH ROW SELECT '123'
```

可以使用NEW和OLD作为虚拟表，来引用更新前和更新后的值









