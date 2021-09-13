## 1、查看信息

```apl
http://localhost:9200/_cat/nodes: 所有节点信息
127.0.0.1 23 73 37    cdfhilmrstw * DESKTOP-V59Q1QR

http://localhost:9200/_cat/health: 健康状况
1628918976 05:29:36 elasticsearch green 1 1 9 9 0 0 0 0 - 100.0%

http://localhost:9200/_cat/master: 主节点
W7xjJ0CHQ6KtJk3Jo4t2_Q 127.0.0.1 127.0.0.1 DESKTOP-V59Q1QR

http://localhost:9200/_cat/indices: 查看所有索引->数据库
green open .kibana-event-log-7.14.0-000001 ryzImKYrR_OJI0k3u6pOrA 1 0    2   0    11kb    11kb
green open .kibana_7.14.0_001              ZBEAJs_KQtWJwBaGIy25Qw 1 0  111   7     6mb     6mb
green open .apm-custom-link                sgzoeUTDSuyNGg9OnjGm7w 1 0    0   0    208b    208b
green open kibana_sample_data_ecommerce    beDkAwxjTOabd76DN0_3pw 1 0 4675   0   3.9mb   3.9mb
green open .apm-agent-configuration        EkKZVa-0SdqaLlCTzzSSZQ 1 0    0   0    208b    208b
green open .kibana_task_manager_7.14.0_001 15dr0DVoSnCGIo1T1zKdiQ 1 0   14 348 590.4kb 590.4kb
green open .async-search                   kGsQrCSIT2ewHHYztsM27A 1 0    6   0    11kb    11kb
green open .tasks                          b94hykzXQyO9cIVEvg7zQQ 1 0    2   0  13.7kb  13.7kb
```

## 2、添加文档

保存一条数据，指定索引，指定类型

```apl
PUT /index/type/record_id(必须指定id)
return {
	"_index":index,
	"_type":type,
	"_id":record_id,
	"_version": 1,
	"result":"created", # 表示新创建
	"_shard":分片信息
}
POST /index/type/id(可选带不带id)
return{
	···
	"_id":"随机生成唯一id"
	···
}
```

## 3、查询文档

```apl
GET /index/type/id --------------> GET /shakespeare/_doc/1 新版本
return{
	"_seq_no":1,
	"_primary_term": 1,
	"_source":{
		res
	}
}
实现乐观锁
"_seq_no":1,
"_primary_term": 1
--------------------------------> CAS

A客户端: PUT /index/type/record_id?_seq_no=1&_primary_term=1
B客户端: PUT /index/type/record_id?_seq_no=1&_primary_term=1

原理:CAS(raw, excepted, update)
含义:如果原来的值raw，是我期望的那个值excepted,就将raw改为新的值update
修改完成后?_seq_no=1&_primary_term=1 的值加一
```

## 4、更新文档

```apl
POST /index/type/id/_update
{
	"doc":{
		"name":"马文澍"
	}
}
更新成功后版本号加一，序列号加一，除非更新数据与原数据相同

POST /index/type/id
{
	"name":"马文澍1"
}
更新后始终版本号加一，序列号加一
```

## 5、删除文档

```apl
DELETE /index/type/id # 删除文档
DELETE /index # 删除索引
```

## 6、批量操作

```json
POST /customer/external/_bulk
{"index":{"_id":"1"}}
{"name":"John"}
{"index":{"_id":"2"}}
{"name":"Bobo"}

其中index:索引->save

return
{
  "took" : 249,
  "errors" : false,
  "items" : [
    {
      "index" : {
        "_index" : "customer",
        "_type" : "external",
        "_id" : "1",
        "_version" : 1,
        "result" : "created",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 0,
        "_primary_term" : 1,
        "status" : 201
      }
    },
    {
      "index" : {
        "_index" : "customer",
        "_type" : "external",
        "_id" : "2",
        "_version" : 1,
        "result" : "created",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 1,
        "_primary_term" : 1,
        "status" : 201
      }
    }
  ]
}
```

## 测试数据

### 导入数据

```apl
POST /_bulk
{"index":{"_index":"shakespeare","_id":0}}
{"type":"act","line_id":1,"play_name":"Henry IV", "speech_number":"","line_number":"","speaker":"","text_entry":"ACT I"}
{"index":{"_index":"shakespeare","_id":1}}
{"type":"scene","line_id":2,"play_name":"Henry IV","speech_number":"","line_number":"","speaker":"","text_entry":"SCENE I. London. The palace."}
{"index":{"_index":"shakespeare","_id":2}}
{"type":"line","line_id":3,"play_name":"Henry IV","speech_number":"","line_number":"","speaker":"","text_entry":"Enter KING HENRY, LORD JOHN OF LANCASTER, the EARL of WESTMORELAND, SIR WALTER BLUNT, and others"}
...
运行完成后生成索引为shakespeare, 类型为_docs,用_id标记区分的记录
GET /shakespeare/_doc/1
{
  "_index" : "shakespeare",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 1,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "type" : "scene",
    "line_id" : 2,
    "play_name" : "Henry IV",
    "speech_number" : "",
    "line_number" : "",
    "speaker" : "",
    "text_entry" : "SCENE I. London. The palace."
  }
}

```

### 检索数据

###### 1、match_all

```apl
查询所有
GET /shakespeare/_search
GET /shakespeare/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [ # 排序
    {
      "line_id": {
        "order": "asc"
      }
    }
  ],
  "from":10, #	分页
  "size":10,
  "_source": ["type","play_name","speaker"] # 回显字段
}
return 
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 101,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : []
  }
}
```

###### 2、match

```apl
GET /shakespeare/_search
{
  "query": {
    "match": {
      "speaker": "KING IV" # speaker 字段 只要包含 KINE或者 IV
    }
  }
}
```

###### 3、match_phrase

```apl
GET /shakespeare/_search
{
  "query": {
    "match_phrase": {
      "speaker": "KING HENRY IV" # speaker 字段 只要包含 KINE IV 短语
    }
  }
}
```

###### 4、multi_match

```apl
GET /shakespeare/_search
{
  "query": {
    "multi_match": {
      "query": "IV",
      "fields": ["play_name","speaker"]
    }
  }
}
```

###### 5、复合查询

```apl
GET /shakespeare/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "speech_number": "6"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "line_id": "78"
          }
        }
      ],
      "should": [
        {
          "match": {
            "line_id": "79"
          }
        }
      ]
    }
  }
}
```

###### 6、filter

```apl
GET /shakespeare/_search
{
  "query": {
    "bool": {
      "filter": [ # 同 must,但是不会计算相关性得分
        {
          "range": {
            "line_id": {
              "gte": 10,
              "lte": 20
            }
          }
        }
      ]
    }
  }
}
```

###### 7、term

```apl
GET /shakespeare/_search
{
  "query": {
    "term": { # 精确查找
      "line_id": {
        "value": "1"
      }
    }
  }
}
GET /shakespeare/_search
{
  "query": {
    "term": {
      "speaker": "KING HENRY"
    }
  }
}
# 等价于
GET /shakespeare/_search
{
  "query": {
    "match": {
      "speaker.keyword": "KING HENRY"
    }
  }
}
```

###### 8、聚合

```json
GET /users/_search
{
  "query": {
    "match_all": {}
  },
  "aggs": {
    "genderAgg": {
      "terms": {
        "field": "gender.keyword",
        "size": 10
      },
      "aggs": {
        "avgBalance": {
          "avg": {
            "field": "balance"
          }
        },
        "sumBalance": {
          "sum": {
            "field": "balance"
          }
        }
      }
    },
    "ageAgg":{
      "avg": {
        "field": "age"
      }
    }
  }
}
```

###### 9、分词

```apl
POST _analyze
{
  "analyzer": "ik_max_word",
  "text":"我是中国人"
}

POST _analyze
{
  "analyzer": "ik_smart",
  "text":"我是中国人"
}
```





