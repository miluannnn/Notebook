# Elasticsearch

[TOC]

### 概念

-  接近实时 
          Elasticsearch是一个接近实时的搜索平台。这意味着，从索引一个文档直到这个文档能够被搜索到有一个轻微的延迟（通常是1秒）。

-  集群（`cluster`） 
          一个集群就是由一个或多个节点组织在一起，它们共同持有你整个的数据，并一起提供索引和搜索功能。一个集群由一个唯一的名字标识，这个名字默认就是“Elasticsearch”。这个名字是重要的，因为一个节点只能通过指定某个集群的名字，来加入这个集群。在产品环境中显式地设定这个名字是一个好习惯，但是使用默认值来进行测试/开发也是不错的。

-  节点（`node`） 
          一个节点是你集群中的一个服务器，作为集群的一部分，它存储你的数据，参与集群的索引和搜索功能。和集群类似，一个节点也是由一个名字来标识的，默认情况下，这个名字是一个随机的漫威漫画角色的名字，这个名字会在启动的时候赋予节点。这个名字对于管理工作来说挺重要的，因为在这个管理过程中，你会去确定网络中的哪些服务器对应于Elasticsearch集群中的哪些节点。
  一个节点可以通过配置集群名称的方式来加入一个指定的集群。默认情况下，每个节点都会被安排加入到一个叫做“elasticsearch”的集群中，这意味着，如果你在你的网络中启动了若干个节点，并假定它们能够相互发现彼此，它们将会自动地形成并加入到一个叫做“elasticsearch”的集群中。
  在一个集群里，只要你想，可以拥有任意多个节点。而且，如果当前你的网络中没有运行任何Elasticsearch节点，这时启动一个节点，会默认创建并加入一个叫做“elasticsearch”的集群。

-  索引（`index`） 
          一个索引就是一个拥有几分相似特征的文档的集合。比如说，你可以有一个客户数据的索引，另一个产品目录的索引，还有一个订单数据的索引。一个索引由一个名字来标识（必须全部是小写字母的），并且当我们要对对应于这个索引中的文档进行索引、搜索、更新和删除的时候，都要使用到这个名字。索引类似于关系型数据库中Database的概念。在一个集群中，如果你想，可以定义任意多的索引。

-  类型（`type`） 
          在一个索引中，你可以定义一种或多种类型。一个类型是你的索引的一个逻辑上的分类/分区，其语义完全由你来定。通常，会为具有一组共同字段的文档定义一个类型。比如说，我们假设你运营一个博客平台并且将你所有的数据存储到一个索引中。在这个索引中，你可以为用户数据定义一个类型，为博客数据定义另一个类型，当然，也可以为评论数据定义另一个类型。类型类似于关系型数据库中Table的概念。

- 文档（`document`） 
          一个文档是一个可被索引的基础信息单元。比如，你可以拥有某一个客户的文档，某一个产品的一个文档，当然，也可以拥有某个订单的一个文档。文档以JSON（JavaScript Object Notation）格式来表示，而JSON是一个到处存在的互联网数据交互格式。 
  在一个index/type里面，只要你想，你可以存储任意多的文档。注意，尽管一个文档，物理上存在于一个索引之中，文档必须被索引/赋予一个索引的type。文档类似于关系型数据库中Record的概念。实际上一个文档除了用户定义的数据外，还包括_index、_type和_id字段。

- 分片和复制（`shards`  & `replicas`） 
          一个索引可以存储超出单个结点硬件限制的大量数据。比如，一个具有10亿文档的索引占据1TB的磁盘空间，而任一节点都没有这样大的磁盘空间；或者单个节点处理搜索请求，响应太慢。为了解决这个问题，**Elasticsearch提供了将索引划分成多份的能力，这些份就叫做分片**。当你创建一个索引的时候，你可以指定你想要的分片的数量。每个分片本身也是一个功能完善并且独立的“索引”，这个“索引”可以被放置到集群中的任何节点上。 
  分片之所以重要，主要有两方面的原因：

  - 允许你水平分割/扩展你的内容容量
  - 允许你在分片（潜在地，位于多个节点上）之上进行分布式的、并行的操作，进而提高性能/吞吐量

  ​          至于一个分片怎样分布，它的文档怎样聚合回搜索请求，是完全由Elasticsearch管理的，对于作为用户的你来说，这些都是透明的。
  ​          在一个网络/云的环境里，失败随时都可能发生，在某个分片/节点不知怎么的就处于离线状态，或者由于任何原因消失了。这种情况下，有一个故障转移机制是非常有用并且是强烈推荐的。为此目的，Elasticsearch允许你创建分片的一份或多份拷贝，这些拷贝叫做复制分片，或者直接叫复制。复制之所以重要，主要有两方面的原因：

  		-  在分片/节点失败的情况下，提供了高可用性。因为这个原因，注意到复制分片从不与原/主要（original/primary）分片置于同一节点上是非常重要的。
  		-  扩展你的搜索量/吞吐量，因为搜索可以在所有的复制上并行运行

### ES Restful API基本使用：

ES为开发者提供了非常丰富的基于HTTP协议的Rest API，只需要向ES服务端发送简单的Rest请求，就可以实现非常强大的功能。本篇文章主要介绍ES中常用操作的Rest API的使用，同时会讲解ES的源代码工程中的API接口文档，通过了解这个API文档的接口描述结构，就基本上可以实现ES中的绝大部分功能。

> Mapping详解

Mapping是ES中的一个很重要的内容，它类似于传统关系型数据中table的schema，用于定义一个索引（index）的某个类型（type）的数据的结构。
在传统关系型数据库，我们必须首先创建table并同时定义其schema，如下面的SQL语句。下面代码中小括号内的代码的作用就是定义person_info的schema（模式）。

```mysql
create table person_info
(
    name varchar(20),
    age tinyint 
)
```

在ES中，我们无需手动创建type（相当于table）和mapping(相关与schema)。在默认配置下，ES可以根据插入的数据自动地创建type及其mapping。在下面的API介绍部分中，会做相关的试验。当然，在实际使用过程中我们可能就想硬性规定mapping，可以通过配置文件关闭ES的自动创建mapping功能。

mapping中主要包括字段名、字段数据类型和字段索引类型这3个方面的定义。

字段名：与传统数据库字段名作用一样，就是给字段起个唯一的名字，好让系统和用户能识别。
字段数据类型：定义该字段保存的数据的类型，不符合数据类型定义的数据不能保存到ES中。下表列出的是ES中所支持的数据类型。（大类是对所有类型的一种归类，小类是实际使用的类型。）

------

​						大类																		包含的小类
​						String																			string
​				Whole number												byte, short, integer, long
​				Floating point																float, double
​					Boolean																		boolean
​						Date																			date

------

如果索引类型设置为analyzed，在表示ES会先对这个字段进行分析（一般来说，就是自然语言中的分词），ES内置了不少分析器（analyzer），如果觉得它们对中文的支持不好，也可以使用第三方分析器。如果将ES当做真正的搜索引擎，那么挑选正确的分析器是至关重要的。

> 常用的Rest API介绍

下面介绍一下ES中的一些常用的Rest API。掌握了这些API的用法，基本上就可以简单地使用ES了。

我们需要借助能够发送HTTP请求的工具调用这些API，工具是可以任意的，包括网页浏览器。这里利用Linux上的curl命令来发送HTTP请求。基本的命令结构为：

```xml
  curl <-Xaction> url -d 'body'
  # 这里的action表示HTTP协议中的各种动作，包括GET、POST、PUT、DELETE等。
```

（1）创建一个新的索引

```
curl -XPUT "localhost:9200/index_test"
```

（2）删除一个索引

```
curl -XDELETE "localhost:9200/index_test"
```

（1）创建索引的mapping。

```
curl -XPUT 'localhost:9200/index_test/_mapping/test_type' -d ' 
{
  "test_type": { # 注意，这里的test_type与url上的test_type名保存一致
      "properties": {
        "name": {
          "type": "string",
          "index": "not_analyzed"
        },
        "age": {
          "type": "integer"
        }
      }
    }
  }'
```

 (2）删除mapping。

```
curl -XDELETE 'localhost:9200/index_test/_mapping/test_type'
```

（3）查看索引的mapping

```
curl -XGET 'localhost:9200/index_test/_mapping/test_type'
```

### 精准查找

> term查找数字

```json
{
    "term" : {
        "price" : 20
    }
}
```

> 查询多个精确值

```json
{
    "terms" : {
        "price" : [20, 30]
    }
}
```

**包含，而不是相等**

一定要了解 term 和 terms 是 包含（contains） 操作，而非 等值（equals） （判断）。 如何理解这句话呢？

如果我们有一个 term（词项）过滤器 { "term" : { "tags" : "search" } } ，它会与以下两个文档同时匹配：

```json
{ "tags" : ["search"] }
{ "tags" : ["search", "open_source"] } 
```

> 精确相等

```json
GET /my_index/my_type/_search
{
    "query": {
        "constant_score" : {
            "filter" : {
                 "bool" : {
                    "must" : [
                        { "term" : { "tags" : "search" } }, 
                        { "term" : { "tag_count" : 1 } } 
                    ]
                }
            }
        }
    }
}
```



### 布尔过滤器

一个 bool 过滤器由三部分组成：

```json
{
   "bool" : {
      "must" :     [],
      "should" :   [],
      "must_not" : [],
   }
}
```

`must `
	所有的语句都 必须（`must`） 匹配，与 AND 等价。 
`must_not` 
	所有的语句都 不能（`must not`） 匹配，与 NOT 等价。 
`should `
	至少有一个语句要匹配，与 OR 等价。 

例如在a=1且b=2的数据中，找出c=1或者d=2的数据:

```json
{"query": {
   "bool": {
    "should": [
      {"match": {
           "a": "1",
           "b": "2",
           "c": "1"
        }}
    ]，
    "should": [
      {"match": {
          "a": "1",
          "b": "2",
          "d": "2"
    }}
    ]
}}}
```

### 范围

- gt: > 大于（greater than） 
- lt: < 小于（less than） 
- gte: >= 大于或等于（greater than or equal to） 
- : <= 小于或等于（less than or equal to）

```
"range" : {
    "price" : {
        "gte" : 20,
        "lte" : 40
    }
}
```

> 日期范围

range 查询同样可以应用在日期字段上：

```
"range" : {
    "timestamp" : {
        "gt" : "2014-01-01 00:00:00",
        "lt" : "2014-01-07 00:00:00"
    }
}
```

当使用它处理日期字段时， range 查询支持对 **日期计算（date math）** 进行操作，比方说，如果我们想查找时间戳在过去一小时内的所有文档：

```
"range" : {
    "timestamp" : {
        "gt" : "now-1h"
    }
}
```

> 字符串范围

字典序

```
"range" : {
    "title" : {
        "gte" : "a",
        "lt" :  "b"
    }
}
```

### 处理Null值

回想在之前例子中，有的文档有名为 tags （标签）的字段，它是个多值字段，一个文档可能有一个或多个标签，也可能根本就没有标签。如果一个字段没有值，那么如何将它存入倒排索引中的呢？

在 Elasticsearch 中，使用 exists 查询的方式如下：

```
GET /my_index/posts/_search
{
    "query" : {
        "constant_score" : {
            "filter" : {
                "exists" : { "field" : "tags" }
            }
        }
    }
}
```

> 缺失查询

个 missing 查询本质上与 exists 恰好相反：它返回某个特定 无 值字段的文档，与以下 Sql 表达的意思类似：

```
GET /my_index/posts/_search
{
    "query" : {
        "constant_score" : {
            "filter": {
                "missing" : { "field" : "tags" }
            }
        }
    }
}
```

