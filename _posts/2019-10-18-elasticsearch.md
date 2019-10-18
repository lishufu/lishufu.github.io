---
layout: post
current: post
cover:  assets/images/summit.jpg
navigation: True
title: elasticsearch语法总结
date: 2019-10-18 04:00:00
tags: [es]
class: post-template
subclass: 'post tag-getting-started'
author: lishufu
---



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Elasticsearch是一个基于Apache Lucene(TM)的开源搜索引擎。无论在开源还是专有领域，Lucene可以被认为是迄今为止最先进、性能最好的、功能最全的搜索引擎库。  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;但是，Lucene只是一个库。想要使用它，你必须使用Java来作为开发语言并将其直接集成到你的应用中，更糟糕的是，Lucene非常复杂，你需要深入了解检索的相关知识来理解它是如何工作的。Elasticsearch也使用Java开发并使用Lucene作为其核心来实现所有索引和搜索的功能，但是它的目的是通过简单的RESTful API来隐藏Lucene的复杂性，从而让全文搜索变得简单。不过，Elasticsearch不仅仅是Lucene和全文搜索，我们还能这样去描述它： 
* 分布式的实时文件存储，每个字段都被索引并可被搜索  
* 分布式的实时分析搜索引擎  
* 可以扩展到上百台服务器，处理PB级结构化或非结构化数据  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;而且，所有的这些功能被集成到一个服务里面，你的应用可以通过简单的RESTful API、各种语言的客户端甚至命令行与之交互。  
上手Elasticsearch非常容易。它提供了许多合理的缺省值，并对初学者隐藏了复杂的搜索引擎理论。它开箱即用（安装即可使用），只需很少的学习既可在生产环境中使用。  
Elasticsearch在Apache 2 license下许可使用，可以免费下载、使用和修改。  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;随着你对Elasticsearch的理解加深，你可以根据不同的问题领域定制Elasticsearch的高级特性，这一切都是可配置的，并且配置非常灵活。  

推荐大家看看[Elasticsearch 权威指南（中文版）](https://es.xiaoleilu.com/)  
这里我对es一些常规用法做一些简单介紹：

* ### es常用查询语句  
一般使用query指定查询语句，类似于select查询语句。  

1. match查询 按分词器进行查询，下面的例子会将desc字段中，包含首届或者第一节的文档都查询出来，并计算其相关度，_score表示其文档相似度分数。
```elasticsearch
GET aqi_show_detail/_search
{
      "query": {
          "match": {
            "desc": "首届 第一节"
          }
      }
}
```  
2. match_phrase 和match查询基本一样，match_phrase会将要查询的条件当作一个字符串整体进行查询，下面的查询就查询不到结果,因为会将其当作一个整体进行匹配。
```elasticsearch
GET aqi_show_detail/_search
{
      "query": {
          "match_phrase": {
            "desc": "首届 第一节"
          }
      }
}
```  

3. multi_match 多段查询 如果我们希望两个字段进行匹配，其中一个字段有这个文档就满足的话，使用multi_match
```
GET aqi_show_detail/_search
{
    "query": {
      "multi_match": {
        "query": "国旗",
        "fields": ["epFocus", "desc"]
      }
    }
}
```  

4. query_string 使用查询解析器来解析其内容的查询， 比如一个字段包含field1并且包含field2或者包含field3
```elasticsearch
GET aqi_show_detail/_search
{
    "query": {
      "query_string": {
        "default_field": "desc",
        "query": "field1 AND field2 OR field3"
      }
        
    }
}
```

5. term 用于关键字匹配，精准匹配关键字  
```elasticsearch
GET aqi_show_detail/_search
{
    "query": {
        "term": {
          "eqVid": {
            "value": "eeee3e07d3f1be9f81646eefddf2b0cc"
          }
        }
    }
}
```  
***补：match在匹配时会对所查找的关键词进行分词，然后按分词匹配查找，而term会直接对关键词进行查找。一般模糊查找的时候，多用match，而精确查找时可以使用term***  

6. terms 多个关键字匹配，同时包含多个关键字
```elasticsearch
GET aqi_show_detail/_search
{
    "query": {
        "terms": {
          "tagNames": [
            "普通话",
            "晚会"
          ]
        }
        }
    }
}
```  

7. range 区间查询，一般用于时间区间的过滤
```elasticsearch
GET aqi_show_detail/_search
{
    "query": {
      "range": {
        "score": {
          "gte": 7,
          "lte": 10
        }
      }      
    }
}
```  
* gt:>  大于
* lt:<  小于
* gte:>=  大于或等于
* lte:<=  小于或等于  
8. filter  过滤查询，对查询的结果进行过滤，类似于having过滤，不会对结果进行打分   
```elasticsearch
GET aqi_show_detail/_search
{
    "query": {
      "bool": {
        "must": [
          {
            "match": {
              "desc": "首届"
            }
          }
        ], 
        "filter": {
          "range": {
            "score": {
              "gte": 7,
              "lte": 10
            }
          }
        }
      }
    }
}
```  
***filter：不需要计算相关度分数，不需要按照相关度分数进行排序，同时还有内置的自动cache最常用filter的数据。<br />query:相反，要计算相关度分数，按照分数进行排序，而且无法cache结果***


9. bool 布尔查询   
* must: 必须满足的条件 （相当于and） 
* should: 可以满足也可以不满足的条件 （相当于or） 
* must_not: 不需要满足的条件 （相当于not） 

10. 聚合查询  
* 桶：满足特定条件的文档的集合，支持嵌套，比如北京放在中国桶里面，中国桶放在亚洲桶里面。
* 指标：对桶内的文档进行统计计算，通常是简单的数学运算(像是min、max、avg、sum）。
* aggs 聚合的模板  
当query和aggs一起存在时，会先执行query的主查询，主查询query执行完后会搜出一批结果，而这些结果才会被拿去aggs拿去做聚合，同样也支持嵌套。  
```elasticSearch
GET aqi_show_detail/_search
{
    "query": {
      "match_all": {}
      },
      "aggs": {
        "out_aggs": {
          "terms": {
            "field": "score"
          },
          "aggs": {
            "inner_aggs": {
              "terms": {
                "field": "stype"
            }
          }
        }
      }
    }
}
```  
常用的桶有terms、filter、top_hits  
* terms桶: 针对某个field的值进行分组，field有几种值就分成几组  
> order支持的参数  
> _count : 按照文档数排序  
> _key : 按照每个桶的字符串值的字母顺序排序  
* filter桶：用法和filter一样，不过filter桶会给自己创建新的桶，而且不会依附query或者其他桶  
* top_hits桶：在某个桶底下找出这个桶的前几笔hits，返回的hits格式和主查询query返回的hits格式一模一样
> from、size  
> sort : 设置返回的hits的排序  
> _source : 设置返回的字段   
```elasticSearch
GET aqi_show_detail/_search
{
    "query": {
      "match_all": {}
      },
      "aggs": {
        "out_aggs": {
          "terms": {
            "field": "score"
          },
          "aggs": {
            "inner_aggs": {
              "top_hits": {
                "size": 10,
                "from": 0
              }
          }
        }
      }
    }
}
```

* ### 拓展分享(后续会补充完整)： 
* es分布式  
* es倒排索引原理  
* Text vs keyword 
> ElasticSearch 5.0以后，string类型有重大变更，移除了string类型，string字段被拆分成两种新的数据类型: text用于全文搜索的,而keyword用于关键词搜索。 
> Text：会分词，然后进行索引  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;支持模糊、精确查询  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;不支持聚合  
> keyword：不进行分词，直接索引  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;支持模糊、精确查询  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;支持聚合  
