---
title: elastic-search-进阶
date: 2020-07-20 16:41:18
tags:
---

## ES 的进阶课程

这届主要写原理和优化，基础知识请看之前elastic-search

<!-- More -->

### Index 策略

1. Rollover：

    当现有索引被认为太大或太旧时，滚动索引API会将别名滚动到新的索引。 API接受单个别名和条件列表。 别名只能指向一个索引。 如果索引满足指定的条件，则创建一个新的索引，并将别名切换到指向新的索引。

    ```
    curl -XPUT 'localhost:9200/logs-000001 ?pretty' -d'
    {
        "aliases": {
            "logs_write": {}
        }
    }'
    # Add > 1000 documents to logs-000001
    curl -XPOST 'localhost:9200/logs_write/_rollover ?pretty' -d'
    {
        "conditions": {
            "max_age":   "7d",
            "max_docs":  1000
        }
    }'
    ```

    