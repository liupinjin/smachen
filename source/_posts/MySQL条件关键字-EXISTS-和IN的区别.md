---
title: MySQL条件关键字 EXISTS 和IN的区别
date: 2017-11-14 13:07:06
categories:
- 技术
- MySQL
tags:
- MySQL
- Exists
- IN
---
Exists和IN的使用方法和性能分析
环境：表A和表B，两张表以c字段关联，并在大表建立索引
<h2>使用方法</h2><h3>Exists</h3>exists()后面的子查询被称做相关子查询 ,返回布尔值（true/false）,而不是返回列表值。
执行方式是主表先运行查询一次，再去子查询里查询与其对应的结果，如果是true则输出，否则不输出，再根据主查询中的每一行去子查询里去查询。{% codeblock lang:sql %}
select * from A where c in (select c from B);
{% endcodeblock %}
<h3>IN</h3>IN相当于多个或（OR）条件叠加查询。in()后面的子查询，返回子查询的结果集，结果集必须只有一个字段。
执行方式是子查询先产生结果集，然后主查询再去结果集里去找符合要求的字段列表，符合要求的输出，否则不输出。{% codeblock lang:sql %}
select * from A where exists (select c from B where c=A.c);
{% endcodeblock %}
<h2>性能分析</h2>
IN的使用：select * from A where c in (select c from B);
EXISTS的使用：select * from A where exists (select c from B where c=A.c);
1.表A和表B两个表大小相当，那么用in和exists差别不大。 
2.表A小于表B，此时in的效率低于exists，exists用的是大表B的c列的索引。
3.表A大于表B，此时in的效率高于exists，in用的是大表A的c列的索引。
4.所以无论两张表哪个表大，用not exists都比not in要快，此时not in是全表扫描，not extsts 的子查询使用表的索引。
