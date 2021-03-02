---
title: db-basic
date: 2020-11-18 00:22:23
tags:
---


## DB Basic knowledge

### truncate 、delete与drop区别

1. drop 语句将删除表的结构 -- truncate/delete 删除内容
2. delete可以放事务里面，不会自动提交，drop，truncate不行
3. Truncate is a quick way to empty a table. It removes everything without logging each row. -- the delete command removes rows from a table, while logging each deletion
4. Truncate will fail if there are foreign key relationships on the table

### Group by vs Having VS Where

1. Group BY:

- GROUP BY Clause is utilized with the SELECT statement.
- GROUP BY aggregates the results on the basis of selected column: COUNT, MAX, MIN, SUM, AVG, etc.
- GROUP BY returns only one result per group of data.
- GROUP BY Clause always follows the WHERE Clause.
- GROUP BY Clause always precedes the ORDER BY Clause(http://slideplayer.com/slide/15440670/).

2. Having
HAVING Clause utilized in SQL as a conditional Clause with GROUP BY Clause. This conditional clause returns rows where aggregate function results matched with given conditions only. It added in the SQL because WHERE Clause cannot be combined with aggregate results, so it has a different purpose. The primary purpose of the WHERE Clause is to deal with non-aggregated or individual records.

- HAVING Clause always utilized in combination with GROUP BY Clause.
- HAVING Clause restricts the data on the group records rather than individual records.
- WHERE and HAVING can be used in a single query.

3. Where

- In some cases, you need to filter out the individual records. In such cases, you can use WHERE Clause, Whereas in other cases you need to filter the groups with the specific condition. In such cases, you can use HAVING Clause.
- WHERE Clause filters the records tuple by tuple while HAVING Clause filters the whole group.
- A query may have both the clauses( WHERE and HAVING Clause).
- Where Clause applied first and then Having Clause.
- WHERE Clause restricts records before GROUP BY Clause, whereas HAVING Clause restricts groups after GROUP BY Clause are performed.
- WHERE Clause can be utilized with SELECT, UPDATE, DELETE, and INSERT, whereas HAVING can be utilized only with SELECT statement.

### Mybatics vs Hibernate

1. Hibernate是全自动，而Mybatics是半自动。

Hibernate完全可以通过对象关系模型实现对数据库的操作，拥有完整的JavaBean对象与数据库的映射结构来自动生成sql。而Mybatics仅有基本的字段映射，对象数据以及对象实际关系仍然需要通过手写sql来实现和管理。

2. Hibernate数据库移植性远大于Mybatics。

Hibernate通过它强大的映射结构和hql语言，大大降低了对象与数据库（oracle、mysql等）的耦合性，而Mybatics由于需要手写sql，因此与数据库的耦合性直接取决于程序员写sql的方法，如果sql不具通用性而用了很多某数据库特性的sql语句的话，移植性也会随之降低很多，成本很高。

3. Hibernate拥有完整的日志系统，Mybatics则欠缺一些。

Hibernate日志系统非常健全，涉及广泛，包括：sql记录、关系异常、优化警告、缓存提示、脏数据警告等；而Mybatics则除了基本记录功能外，功能薄弱很多。

4. Mybatics相比Hibernate需要关心很多细节

Hibernate配置要比Mybatics复杂的多，学习成本也比Mybatics高。但也正因为Mybatics使用简单，才导致它要比Hibernate关心很多技术细节。Mybatics由于不用考虑很多细节，开发模式上与传统jdbc区别很小，因此很容易上手并开发项目，但忽略细节会导致项目前期bug较多，因而开发出相对稳定的软件很慢，而开发出软件却很快。Hibernate则正好与之相反。但是如果使用Hibernate很熟练的话，实际上开发效率丝毫不差于甚至超越Mybatics。

5. sql直接优化上，Mybatics要比Hibernate方便很多

由于Mybatics的sql都是写在xml里，因此优化sql比Hibernate方便很多。而Hibernate的sql很多都是自动生成的，无法直接维护sql；虽有hql，但功能还是不及sql强大，见到报表等变态需求时，hql也歇菜，也就是说hql是有局限的；Hibernate虽然也支持原生sql，但开发模式上却与orm不同，需要转换思维，因此使用上不是非常方便。总之写sql的灵活度上Hibernate不及Mybatics。


Mybatics：

1. 入门简单，即学即用，提供了数据库查询的自动对象绑定功能，而且延续了很好的SQL使用经验，对于没有那么高的对象模型要求的项目来说，相当完美。

2. 可以进行更为细致的SQL优化，可以减少查询字段。

3. 缺点就是框架还是比较简陋，功能尚有缺失，虽然简化了数据绑定代码，但是整个底层数据库查询实际还是要自己写的，工作量也比较大，而且不太容易适应快速数据库修改。

4. 二级缓存机制不佳。

Hibernate：

1. 功能强大，数据库无关性好，O/R映射能力强，如果你对Hibernate相当精通，而且对Hibernate进行了适当的封装，那么你的项目整个持久层代码会相当简单，需要写的代码很少，开发速度很快，非常爽。

2. 有更好的二级缓存机制，可以使用第三方缓存。

3. 缺点就是学习门槛不低，要精通门槛更高，而且怎么设计O/R映射，在性能和对象模型之间如何权衡取得平衡，以及怎样用好Hibernate方面需要你的经验和能力都很强才行。