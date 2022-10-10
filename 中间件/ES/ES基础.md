# 1. 基本语法

## 1.1 基本查询

### 1.1.1  _cat

- `GET /_cat/nodes:` 查看所有节点
- `GET /_cat/health：`查看 es 健康状况
- `GET /_cat/master：`查看主节点
- `GET /_cat/indices：`查看所有索引

### 1.1.2 查询索引

- `GET indices/type/id?if_seq_no=0&if_primary_term=1`

  > 返回结果距离：
  >
  > ```json
  > {
  >     "_index":"customer", //在哪个索引
  >     "_type":"external", //在哪个类型
  >     "_id":"1", //记录 id
  >     "_version":2, //版本号
  > 	"_seq_no":1, //并发控制字段，每次更新就会+1，用来做乐观锁
  >     "_primary_term":1, //同上，主分片重新分配，如重启，就会变化
  >     "found":true,
  >     "_source":{ //真正的内容
  >         "name":"John Doe"
  >     }
  > }
  > ```
  >
  > 

## 1.2 新增

- `PUT([Post) indices/type/id`

  > Post 可以不指定id，
  >
  > Put 必须指定id

  

## 1.3 删除

## 1.4 更新

`POST(PUT) indices/type/id(/_update 可选) ?if_seq_no=0&if_primary_term=1`

## 1.5 批量处理

```json
// 举例说明
POST customer/external/_bulk
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
```

复杂的案例：

```json
{
    "delete":{
        "_index":"website",
        "_type":"blog",
        "_id":"123"
    }
}
{
    "create":{
        "_index":"website",
        "_type":"blog",
        "_id":"123"
    }
}
{
    "title":"My first blog post"
}
{
    "index":{
        "_index":"website",
        "_type":"blog"
    }
}
{
    "title":"My second blog post"
}
{
    "update":{
        "_index":"website",
        "_type":"blog",
        "_id":"123",
        "_retry_on_conflict":3
    }
}
{
    "doc":{
        "title":"My updated blog post"
    }
}
```

