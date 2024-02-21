25.Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签？
|                                                              |
| ------------------------------------------------------------ |
| `<resultMap>`：定义结果集的映射关系，将数据库中的列映射到Java对象的属性。  `<sql>`：定义可重用的SQL片段，可以在多个地方引用。  `<include>`：引用其他地方定义的SQL片段。 `<if>`、`<choose>`、`<when>`、`<otherwise>`：用于在SQL语句中添加条件判断，根据条件动态生成SQL。  `<foreach>`：用于循环遍历集合，并将集合中的元素作为参数传递给SQL语句。 |
26.mybatis动态sql是做什么的？都有哪些动态sql？能简述一下动态sql的执行原理不？
`<if>`、`<choose>`、`<when>`、`<otherwise>`：用于在SQL语句中添加条件判断，根据条件动态生成SQL。
27Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？
而在同一个namespace内，每个语句（select、insert、update、delete等）都需要有一个唯一的id来标识。因此，在同一个XML映射文件的同一个namespace下，id是不能重复的。如果你有多个XML映射文件，每个文件都有独立的namespace，那么在不同的文件中可以使用相同的id，因为namespace的不同会区分它们的唯一性。