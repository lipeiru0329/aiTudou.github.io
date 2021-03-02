---
title: MySQL-Engine
date: 2020-07-02 22:11:13
tags:
---

## MYSQL Engin
<!-- More -->

### InnoDB

1. 经常更新的表，适合处理多重并发的更新请求。
2. 支持事务。
3. 可以从灾难中恢复（通过bin-log日志等）。
4. 外键约束。只有他支持外键。
5. 支持自动增加列属性auto_increment。
6. InnoDB不支持FULLTEXT类型的索引。
7. AUTO_INCREMENT类型的字段，InnoDB中必须包含只有该字段的索引
8. DELETE FROM table时，InnoDB不会重新建立表，而是一行一行的删除。

### Mylsam

1. 不支持事务的设计，但是并不代表着有事务操作的项目不能用MyIsam存储引擎，可以在service层进行根据自己的业务需求进行相应的控制。
2. 不支持外键的表设计。
3. 查询速度很快，如果数据库insert和update的操作比较多的话比较适用。
4. 整天 对表进行加锁的场景。
5. MyISAM极度强调快速读取操作。
6. MyIASM中存储了表的行数，于是SELECT COUNT(*) FROM TABLE时只需要直接读取已经保存好的值而不需要进行全表扫描。如果表的读操作远远多于写操作且不需要数据库事务的支持，那么MyIASM也是很好的选择。


### Heap

1. 那些内容变化不频繁的代码表，或者作为统计操作的中间结果表，便于高效地堆中间结果进行分析并得到最终的统计结果。
2. 目标数据比较小，而且非常频繁的进行访问，在内存中存放数据，如果太大的数据会造成内存溢出。可以通过参数max_heap_table_size控制Memory表的大小，限制Memory表的最大的大小。
3. 数据是临时的，而且必须立即可用得到，那么就可以放在内存中。
4. 存储在Memory表中的数据如果突然间丢失的话也没有太大的关系。

---

## 常见问题

1. Mylsam 为什么insert/update更快

    因为插入要保证主键不能重复，判断主键不能重复，采用的方式在不同的索引下面会有很大的性能差距，聚簇索引遍历所有的叶子节点，非聚簇索引也判断所有的叶子节点，但是聚簇索引的叶子节点除了带有主键还有记录值，记录的大小往往比主键要大的多。这样就会导致聚簇索引在判定新记录携带的主键是否重复时进行昂贵的I/O代价。

2. InnoDB 主键一般要求：

    1.插入速度严重依赖于插入顺序，按照主键的顺序插入是最快的方式，否则将会出现页分裂，严重影响性能。因此，对于InnoDB表，我们一般都会定义一个自增的ID列为主键。
    
    2.更新主键的代价很高，因为将会导致被更新的行移动。因此，对于InnoDB表，我们一般定义主键为不可更新。

3. InnoDB 可以用非聚簇索引吗

    可以的，除了主键索引都是非聚簇索引

    如下除主键外都是二级索引，或叫做辅助索引。(key+主键key)
    ```
    Create Table: CREATE TABLE `article` (
        `id` int(11) NOT NULL AUTO_INCREMENT,
        `title` varchar(255) NOT NULL,
        `shortName` varchar(255) NOT NULL,
        `authorId` int(11) NOT NULL,
        `createTime` datetime NOT NULL,
        `state` int(11) NOT NULL,
        `totalView` int(11) DEFAULT NULL,
        PRIMARY KEY (`id`),
        UNIQUE KEY `idx_short_name_title` (`title`,`shortName`),
        KEY `idx_author_id` (`authorId`)
    ) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=latin1
    ```

    ![](http://img.2cto.com/Collfiles/20170904/20170904100157327.jpg)

4. InnoDB 和Mylsam 在排序的时候表现：

    用聚簇索引也比用非聚簇索引好, 因为聚簇索引设计的时候 就带了排序
