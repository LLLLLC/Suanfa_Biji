```json
POST customer/external/_bulk
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
```

```json
POST /_bulk
{ "delete": { "_index": "website", "_type": "blog", "_id": "123" }}
{ "create": { "_index": "website", "_type": "blog", "_id": "123" }}
```

```json
POST /_bulk
{ "delete": { "_index": "website", "_type": "blog", "_id": "123" }}
{ "create": { "_index": "website", "_type": "blog", "_id": "123" }}
{ "title": "My first blog post" }
{ "index": { "_index": "website", "_type": "blog" }}
{ "title": "My second blog post" }
{ "update": { "_index": "website", "_type": "blog", "_id": "123"} }
{ "doc" : {"title" : "My updated blog post"} }
```

```json
GET bank/_search
{
    "query":{
        "match_all":{

        }
    },
    "sort":[
        {
            "account_number":{
                "order":"desc"
            }
        }
    ]
}
```

```json
GET bank/_search
{
  "query": {
    "multi_match": {
      "query": "mill",
      "fields": [
        "state",
        "address"
      ]
    }
  }
}
```

```json
GET bank/_search
{
  "query": {
    "multi_match": {
      "query": "mill AK",
      "fields": [
        "state",
        "address"
      ]
    }
  }
}
```

```json
GET bank/_search
{
  "query": {
    "match": {
      "address": "mill"
    }
  },
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "age"
      }
    },
    "avg_age": {
      "avg": {
        "field": "age"
      }
    }
  },
  "size": 0
}
```

```json
PUT /my-index
{
  "mappings": {
    "properties": {
      "age": {
        "type": "integer"
      },
      "email": {
        "type": "keyword"
      },
      "name": {
        "type": "text"
      }
    }
  }
}
```

