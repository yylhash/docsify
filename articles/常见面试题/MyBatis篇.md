## 1、maybits有哪些标签，大于和小于号能不能之间使用？
**`<select>`**：用于定义查询语句。

**`<insert>`**：用于定义插入语句。

**`<update>`**：用于定义更新语句。

**`<delete>`**：用于定义删除语句。

**`<resultMap>`**：用于定义结果集映射规则。

**`<sql>`**：用于定义可重用的SQL片段。

**`<include>`**：用于包含外部定义的SQL片段。

通常在XML 中使用实体引用来表示大于号和小于号。在XML 中，大于号和小于号的实体引用分别是 `>` 和 `<`。

```sql
<condition><![CDATA[x > 5]]></condition>
```
## 2、maybits写根据条件入参判断拼接sql使用哪些标签？
**`<if>`标签**：用于根据条件判断是否包含某个SQL片段。

**`<choose>`、`<when>`和`<otherwise>`标签**：类似于Java中的`switch`语句，根据不同的条件选择执行不同的SQL片段。

**`<trim>`标签**：用于去除生成的SQL语句中多余的字符，比如去除SQL片段前后的"AND"或者"OR"。

**`<foreach>`标签**：用于遍历集合参数，生成对应的SQL片段。

## 3、Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？
而在同一个namespace内，每个语句（select、insert、update、delete等）都需要有一个唯一的id来标识。因此，在同一个XML映射文件的同一个namespace下，id是不能重复的。如果你有多个XML映射文件，每个文件都有独立的namespace，那么在不同的文件中可以使用相同的id，因为namespace的不同会区分它们的唯一性。
