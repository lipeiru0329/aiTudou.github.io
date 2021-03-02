---
title: elastic-search
date: 2020-06-12 00:03:00
tags:
---

## Elastic Search

1. 它提供了强大的搜索功能，可以实现类似百度、谷歌等搜索。

2. 可以搜索日志或者交易数据，用来分析商业趋势、搜集日志、分析系统瓶颈或者运行发展等等
<!-- More -->
3. 可以提供预警功能（持续的查询分析某个数据，如果超过一定的值，就进行警告）

4. 分析商业信息，在百万级的大数据中轻松的定位关键信息

### 近实时 但是不是实时 但是搜索特别快

ES并不是一个标准的数据库，它不像MongoDB，它侧重于对存储的数据进行搜索。因此要注意到它 不是 __实时读写__ 的，这也就意味着，刚刚存储的数据，并不能马上查询到。

当然这里还要区分查询的方式，ES也有数据的查询以及搜索，这里的近实时强调的是 __搜索__

### 集群 (cluster)

在ES中，对用户来说集群是很透明的。你只需要指定一个集群的名字（默认是elasticsearch），启动的时候，凡是集群是这个名字的，都会默认加入到一个集群中。

你不需要做任何操作，选举或者管理都是自动完成的。

对用户来说，仅仅是一个名字而已！

### 节点 (Node)

跟集群的概念差不多，ES启动时会设置这个节点的名字，一个节点也就是一个ES得服务器。

默认会自动生成一个名字，这个名字在后续的集群管理中还是很有作用的，因此如果想要手动的管理或者查看一些集群的信息，最好是自定义一下节点的名字。

### 索引 (Index)

索引是一类文档的集合，所有的操作比如索引（索引数据）、搜索、分析都是基于索引完成的。

在一个集群中，可以定义任意数量的索引。

### 类型 (Type)

类型可以理解成一个索引的逻辑分区，用于标识不同的文档字段信息的集合。但是由于ES还是以索引为粗粒度的单位，因此一个索引下的所有的类型，都存放在一个索引下。这也就导致不同类型相同字段名字的字段会存在类型定义冲突的问题。

在2.0之前的版本，是可以插入但是不能搜索；在2.0之后的版本直接做了插入检查，禁止字段类型冲突。

>ES 7.0 or more recent, you need to read [this](https://www.elastic.co/guide/en/elasticsearch/reference/current/removal-of-types.html). To sum it up, mapping types are going to be removed and only one type per index is going to be the norm as of ES 7 onwards.

```
Reason:Initially, we spoke about an “index” being similar to a “database” in an SQL database, and a “type” being equivalent to a “table”.

This was a bad analogy that led to incorrect assumptions. In an SQL database, tables are independent of each other. The columns in one table have no bearing on columns with the same name in another table. This is not the case for fields in a mapping type.

In an Elasticsearch index, fields that have the same name in different mapping types are backed by the same Lucene field internally. In other words, using the example above, the user_name field in the user type is stored in exactly the same field as the user_name field in the tweet type, and both user_name fields must have the same mapping (definition) in both types.

This can lead to frustration when, for example, you want deleted to be a date field in one type and a boolean field in another type in the same index.

On top of that, storing different entities that have few or no fields in common in the same index leads to sparse data and interferes with Lucene’s ability to compress documents efficiently.

For these reasons, we have decided to remove the concept of mapping types from Elasticsearch.
```

### 文档 (Document)

文档是存储数据信息的基本单元，使用json来表示。

### 分片与备份

在ES中，索引会备份成分片，每个分片是独立的lucene索引，可以完成搜索分析存储等工作。

分片的好处：

- 如果一个索引数据量很大，会造成硬件硬盘和搜索速度的瓶颈。如果分成多个分片，分片可以分摊压力。

- 分片允许用户进行水平的扩展和拆分

- 分片允许分布式的操作，可以提高搜索以及其他操作的效率

拷贝一份分片就完成了分片的备份，那么备份有什么好处呢？

- 当一个分片失败或者下线时，备份的分片可以代替工作，提高了高可用性。

- 备份的分片也可以执行搜索操作，分摊了搜索的压力。

ES默认在创建索引时会创建5个分片，这个数量可以修改。

不过需要注意：

- 分片的数量只能在创建索引的时候指定，不能在后期修改

- 备份的数量可以动态的定义

|   关系型数据库    |   Elasticsearch      |
|       ---       |        ----          |
| 数据库Database   | 索引Index，支持全文检索 |
| 表Table         |      类型Type         |
| 数据行Row        | 文档Document，但不需要固定结构，不同文档可以具有不同字段集合 |
| 数据列Column     |      字段Field       |
| 模式Schema       | 映像Mapping          |

## 实现的客户端

### REST HTTP

>HTTP在大多数编程语言中得到很好的支持，这是连接到Elasticsearch的最常见的方法。如果要使用HTTP，还有一个重要的选择：使用一个现有的Elasticsearch基于HTTP的库，或者只是创建一个小的包装器，需要使用HTTP客户端的操作。 由于HTTP是一个通用协议，并支持各种各样的用例，一些重要的事情需要由客户端实现：连接池和保持活动。需要连接池以避免必须支付每个请求的TCP连接建立成本。更重要的，如果它使用HTTPS，这带来额外的加密握手成本。连接池经常需要保持活动支持，因为我们希望避免连接由于空闲而中断。 虽然最初显而易见的是，连接建立实际上是重要的，但是考虑建立TCP连接需要三次握手。简单地说，使用50毫秒的ping时间，除了获取和释放本地资源（处理客户端端口，连接管理等）所花费的时间之外，建立连接需要大约75毫秒 - 这个没有考虑在两端处理请求/响应（例如，串行化）所花费的时间。没有连接池，这个时间被添加在每个请求的顶部。对于我们建议用于安全和隐私的HTTPS，连接建立开销有时可以以秒为单位测量，这甚至更显着。考虑到最终用户的响应时间必须在100毫秒以下才能被观察为“即时”的基本建议，即使非加密的开销也使得这种限制几乎不可能保持在内。 由Elasticsearch编写和支持的官方（非Java）客户端都使用HTTP底层与Elasticsearch进行通信。一般建议是使用封装HTTP API的正式客户端，因为他们负责处理所有这些细节。 HTTP客户端实现可能相当快，其中一些甚至与本机协议的速度竞争。 Elasticsearch的HTTP API被广泛使用，并且具有相当多的社区支持。然而，性能取决于客户端库，并且通常需要进行配置或调整才能最大化。

### Java

- 传输客户端（Transport client）(Transport会在ES7.0开始不更新，ES8.0删除这个Client)

  TransportClient 是ElasticSearch（java）客户端封装对象，使用transport远程连接到Elasticsearch集群，该transport node并不会加入集群，而是简单的向ElasticSearch集群上的节点发送请求。

  ![](https://yqfile.alicdn.com/39e05f57974d9afed2fe80d081d338f95374352f.png)

```
// on startup
TransportClient client = new PreBuiltTransportClient(Settings.EMPTY)    // @1
        .addTransportAddress(new TransportAddress(InetAddress.getByName("192.168.1.10"), 9300))     // @2
        .addTransportAddress(new TransportAddress(InetAddress.getByName("192.168.1.11"), 9300));   
// on shutdown
client.close();
```

配置：
- cluster.name
指定集群名称。
- client.transport.sniff
是否开启集群嗅探功能，下文会详细介绍。
- client.transport.ignore_cluster_name
是否忽略连接节点的集群名称校验，设置为true表示忽略，避免连接的节点并不在同一个集群中。
- client.transport.ping_timeout
ping命令的响应超时时间，默认为5s。
- client.transport.nodes_sampler_interval 对连接节点发送ping命令的频率，默认为5s，即常说的心跳检测间隔时间。


接下来重点描述一下client.transport.sniff参数，集群群嗅探机制。
在创建TransportClient时可以通过addTransportAddress来静态的增加ElasticSearch集群中的节点，如果开启集群群嗅探机制，即开启节点动态发现机制，允许动态添加和删除节点。

当启用嗅探功能时，首先客户端会连接addTransportAddress中的节点上。在此之后，客户端将调用这些节点上的内部集群状态API来发现可用的数据节点。客户端的内部节点列表将仅被发现的数据数据节点替换。默认情况下，这个列表每5秒刷新一次。也就意味着如果该节点不是数据节点，则列表可能不包括它连接的原始节点。

例如，如果您最初连接到一个主节点，在嗅探之后，如果发现了有其对应的数据节点，则不会再向该主节点发出请求，而是向任何数据节点发出请求。传输客户端排除非数据节点的原因是为了避免只向主节点发送搜索流量。


- 节点客户端（Node client）

  节点客户端实际上是一个集群中的节点（但不保存数据，不能成为主节点）。因为它是一个节点，它知道整个集群状态（所有节点驻留，分片分布在哪些节点，数据在集群中的具体位置等等）,这意味着它可以执行 APIs 而且少了一个网络跃点。能够直接转发请求到对应的节点上。Node客户端与transport client非常相似：它是官方Elasticsearch发行版的一部分，需要客户端运行Java等，但也有一些显着的差异。 如果群集对传输客户端是否已连接到群集中的某个节点非常不感兴趣，那么节点客户端将被视为群集的一部分。这意味着节点客户端的存在被存储在群集状态，并且群集中的所有其他节点将尝试建立到客户端的几个tcp连接。如果群集很大或使用多个客户端，这可能是一个显着的缺点。 这可能看起来有点荒谬，但是目前需要它，以便使服务器节点能够将对集群状态的更改传播到客户端。其最终结果是，节点客户端始终具有最新的集群状态和与Elasticsearch集群中每个其他节点的连接，这使得它能够在本地执行操作路由，是其自身请求的协调器等等。这会为每个请求跳过网络跳转，并导致集群中其余节点的工作量减少

### 总结

1. Node和transport最大的区别就是在客户端节点是不是集群中的一部分
2. 如果Transport打开了sniff，那么能实现的功能和Node基本一样，但是他不是聚群里的一部分（node节点集群里面其他节点会尝试跟他建立TCP连接，但是因为他是节点客户端，不存数据，所以说如果大量客户端存在的时候就是一个明显的缺点：
    1. tcp特别多 
    2. 有数据的节点很少


## Go 实现

### olivere/elastic

目前被广泛应用的是第三方开发者做的olivere/elastic

GitHub：https://github.com/lipeiru0329/ELK

### Refresh 三种策略

```
/**
 * Don't refresh after this request. The default.
 */
NONE,
/**
 * Force a refresh as part of this request. This refresh policy does not scale for high indexing or search throughput but is useful
 * to present a consistent view to for indices with very low traffic. And it is wonderful for tests!
 */
IMMEDIATE,
/**
 * Leave this request open until a refresh has made the contents of this request visible to search. This refresh policy is
 * compatible with high indexing and search throughput but it causes the request to wait to reply until a refresh occurs.
 */
WAIT_UNTIL; 
```

具体来说

WAIT_UNTIL:
>在返回请求结果之前，会等待刷新请求所做的更改。并不是强制立即刷新，而是等待刷新发生
。Elasticsearch会自动刷新已更改每个index.refresh_interval的分片，默认为一秒。该设置是动态的。
请求持续为打开状态，直到修改的内容变为可搜索为止。此刷新策略与高索引和搜索吞吐量兼容，但它会导致请求等待回复，直到刷新发生

IMMEDIATE:
>强制刷新相关的主分片和副分片（而不是整个索引），使更新的分片状态变为可搜索。
在使用之前，一定要仔细考虑使用该参数会不会导致性能不佳。
强制刷新作为请求的一部分,这种方式并不适用于索引和查询高吞吐量的场景，
但是作为流量小时提供一致性的视图的确是很使用的。

NONE:
>Don’t refresh after this request. The default.
这是默认的一种方式，调用request修改以后，并不进行强制刷新，刷新的时间间隔为refresh_interval设置的参数。

### 各种term query的 QueryBuild 构建

https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-terms-query.html

---


### 小知识：
__pretty__:

    在任意的查询字符串中增加pretty参数，会让Elasticsearch美化输出(pretty-print)JSON响应以便更加容易阅读。

    _source字段不会被美化，它的样子与我们输入的一致。

http://localhost:9200/twitter1/_search?pretty

```
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 1,
      "relation": "eq"
    },
    "max_score": 1.0,
    "hits": [
      {
        "_index": "twitter1",
        "_type": "tweet",
        "_id": "3",
        "_score": 1.0,
        "_source": {
          "user": "kimchy",
          "post_date": "2009-11-15T14:12:12",
          "message": "trying out Elasticasdasdsad Search"
        }
      }
    ]
  }
}
```

http://localhost:9200/twitter1/_search
```
{
    "took": 12,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 1,
            "relation": "eq"
        },
        "max_score": 1.0,
        "hits": [
            {
                "_index": "twitter1",
                "_type": "tweet",
                "_id": "3",
                "_score": 1.0,
                "_source": {
                    "user": "kimchy",
                    "post_date": "2009-11-15T14:12:12",
                    "message": "trying out Elasticasdasdsad Search"
                }
            }
        ]
    }
}
```

## 总结：
- Elasticsearch支持的场景
  - 数据安全性场景：ElasticSearch的shard支持replication，一份数据可以保存多份，如果某一台机器挂掉了，数据在其他机器上还有，不用担心丢失。
  - 访问安全性场景：随着x-pack开源，ElasticSearch支持验证，不用担心未授权的访问。或者借助第三方search-guard等。
  - 迁移特性：ElasticSearch支持众多的插件，在和其他开源系统之间导入，导出数据都很简单。
  - 数据完整性：ElasticSearch支持保存数据原文。

- Elasticsearch不支持的场景
  - 不支持事务，如前所述。
  - 类似数据库中通过外键的复杂的多表关联操作，Elasticsearch天生支持不足。
  - 读写有一定延时，写入的数据，最快1s中能被检索到。

>默认的刷新频率设置是1秒，也就是说文档从Index请求到对外可见能够被搜到，最少要1秒钟，强制的，你的网络和CPU再快也不行。这么做是Lucene为了提高写操作的吞吐量而做出的延迟牺牲，当然这个设置是可以手动调整的，但是并不建议你去动它，会极大地影响搜索性能。不同的应用对实时性的定义并不一样，这取决于你的需求。

- ES不是关系数据库，因此如果您的数据会受益于外键等等，那么ES不是您主要数据存储的好选择
- ES没有任何内置的身份验证或授权系统，需要借助X-pack收费工具或者第三方search-guard，对于安全性要求高的选型考量因素之一。

---

1. 如果信息获取及分析的能力是你的首要需求，那么无疑Elasticsearch是一个好的选择。
2. 如果你的数据并不频繁的update操作，也没有事务性操作，那么完全可以用Elasticsearch替代其他存储。
3. 实时性要求高的场景，需要结合ACID特性的数据库和Elasticsearch结合使用。


>数据库如何与Elasticsearch结合使用？
    
1. 设计时候注意：

    - 创建的每个Elasticsearch索引都应该由符合ACID的数据存储支持。
    - 数据库应该是真实的最终来源，从中填充索引。
    - 如果异常情况发生（节点丢失，中断或误操作 ）导致丢失了索引，您将能够完全恢复它。
    - 一般的用法是另外的数据库比如NOSQL里面有一份，然后实时同步到ES，这样一个用于键值查询，一个用于各种其他查询。 如果ES升级了之类的，比如数据结构变了，那么老版本数据可以不要，直接NOSQL再导入一份到新版本，还可以恢复。
    - logstash的同步插件如logstash_input_jdbc 不支持同步删除操作，建议改为更新操作加标记flag，或者通过业务逻辑实现同步删除操作。

> ElasticSearch 关键(以下的都是http的用法，但是golang都有对应的第三方客户端，但是http比较稳)

1. [FieldData](https://www.elastic.co/guide/en/elasticsearch/reference/current/fielddata.html#fielddata)

- Test Field Data default is false

  So if you try to sort, aggregate, or access values from a script on a text field, you will see this exception:

  ```
  Fielddata is disabled on text fields by default.  Set `fielddata=true` on
  [`your_field_name`] in order to load  fielddata in memory by uninverting the
  inverted index. Note that this can however use significant memory.
  ```

  - Check your index: curl --location --request GET 'http://localhost:19200/merchant/'
  ```
  {
    "mappings": {
      "properties": {
        "my_field": {   --> Use field
          "type": "text",
          "fields": {
            "keyword": {  --> Use keyword
              "type": "keyword"
            }
          }
        }
      }
    }
  }
  ```
  - You can use field and keyword to sort and filter
  - Or you have to enable textfield
  ```
  PUT my_index/_mapping
  {
    "properties": {
      "my_field": { 
        "type":     "text",
        "fielddata": true
      }
    }
  }
  ```

- Size/From

  这个是分页类比limit，offset

- Scroll

  scroll 查询 可以用来对 Elasticsearch 有效地执行大批量的文档查询，而又不用付出深度分页那种代价。

  游标查询会取某个时间点的快照数据。 查询初始化之后索引上的任何变化会被它忽略。 它通过保存旧的数据文件来实现这个特性，结果就像保留初始化时的索引 视图 一样。

  ```
  GET /old_index/_search?scroll=1m     // 查询过去1分钟的结果
  {
      "query": { "match_all": {}},
      "sort" : ["_doc"], 
      "size":  1000
  }
  ```