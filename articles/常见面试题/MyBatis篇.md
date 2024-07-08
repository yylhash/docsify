## 1、maybits有哪些标签，大于和小于号能不能之间使用？
`<select>`：用于定义查询语句。

`<insert>`：用于定义插入语句。

`<update>`：用于定义更新语句。

`<delete>`：用于定义删除语句。

`<resultMap>`：用于定义结果集映射规则。

`<sql>`：用于定义可重用的SQL片段。

`<include>`：用于包含外部定义的SQL片段。

通常在XML 中使用实体引用来表示大于号和小于号。在XML 中，大于号和小于号的实体引用分别是 `>` 和 `<`。

```sql
<condition><![CDATA[x > 5]]></condition>
```
## 2、maybits写根据条件入参判断拼接sql使用哪些标签？
`<if>`标签：用于根据条件判断是否包含某个SQL片段。

`<choose>`、`<when>`和`<otherwise>`标签：类似于Java中的`switch`语句，根据不同的条件选择执行不同的SQL片段。

`<trim>`标签：用于去除生成的SQL语句中多余的字符，比如去除SQL片段前后的"AND"或者"OR"。

`<foreach>`标签：用于遍历集合参数，生成对应的SQL片段。

## 3、Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？
而在同一个namespace内，每个语句（select、insert、update、delete等）都需要有一个唯一的id来标识。因此，在同一个XML映射文件的同一个namespace下，id是不能重复的。如果你有多个XML映射文件，每个文件都有独立的namespace，那么在不同的文件中可以使用相同的id，因为namespace的不同会区分它们的唯一性。

## 4、#和$的区别是什么？什么情况必须用$
在Mybatis的mapper文件中，可以使用#{param}和${param}来作为动态参数的替换。
#{}和${}在预编译处理中是不一样的。#{}类似jdbc中的PreparedStatement，对于传入的参数，在预处理阶段会使用?代替，可以有效的避免SQL注入。
```sql
SELECT* FROM users WHERE id = #{userId}
```
这里的#{userId} 会被替换为 ?，并且 userId 的值在执行时会被安全地设置为参数值。
使用 $ 传递参数时，MyBatis 会将其视为字面量，并在构建 SQL 语句时直接替换成参数的实际值。这意味着参数值会直接拼接到 SQL 语句中。
```sql
SELECT*FROM ${tableName}
```
由于 $ 导致的是直接替换，如果参数内容是用户输入，这可能导致 SQL 注入的风险，因为恶意的输入可以被拼接成一部分 SQL 语句执行。
所以我们在Mybatis中，能使用#{}的地方应尽量使用#{}，但是有一些情况是必须要用${}的，比如我们要把他用在order by、group by 等语句后面的时候。
```sql
order by ${sortParam} ${sortType} 
order by id desc;
```
当我们通过以上方式进行动态的选择排序字段的时候，就需要使用${}；
### 4.1、如果一定要使用${}的话，有以下几种方式可以避免被SQL注入：
参数验证：在将变量传递到 SQL 语句之前，对输入的参数进行严格的验证。确保输入符合预期的格式，并且移除或转义可能的 SQL 控制字符。例如，如果你知道一个参数应该是一个整数，你可以先将其转换为整数。
使用枚举或固定列表：如果可能的参数值是已知且数量有限的，比如排序字段（ASC, DESC），可以使用枚举或预定义的字符串列表来限制输入。比如说字段名，那么也可以定义一个枚举或者常量类，然后用户输入时做判断，是否规定的枚举项或者常量。
```java
public enum SortOrder {
    ASC, DESC
}
public void someMethod(SortOrder order) {
// 在 MyBatis 映射中使用 ${order}
// 检查是否是枚举项
SortOrder.valueOf(order);
}
```
SQL注入是一种常见的网络安全漏洞，攻击者通过在应用程序的用户输入中插入恶意的SQL代码，试图欺骗数据库执行非预期的查询。
SQL注入导致对数据库的未经授权的访问、数据泄露、数据破坏，甚至完整的数据库被攻陷。
攻击者常常通过在用户输入中注入SQL代码，改变应用程序对数据库的查询语句，以实现他们的恶意目的。
### 4.2、如何防止被SQL注入
使用预编译语句： 使用预编译的语句或参数化的语句，而不是通过字符串拼接构建SQL查询。这样可以防止攻击者通过在用户输入中插入恶意代码来改变SQL查询的结构。
如使用JDBC的时候，使用PreparedStatement而不是Statement
```java
// 错误的例子（容易受到SQL注入攻击）：
String userInput = "admin'; DROP TABLE users;--";
String query = "SELECT * FROM users WHERE username='" + userInput + "'";

// 正确的例子（使用预编译语句）：
String userInput = "admin'; DROP TABLE users;--";
String query = "SELECT * FROM users WHERE username=?";
PreparedStatement preparedStatement = connection.prepareStatement(query);
preparedStatement.setString(1, userInput);
```