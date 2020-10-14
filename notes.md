# Elasticsearch API

## Create an index with explicit mapping
```json
PUT /customers
{
  "mappings": {
    "properties": {
      "age":    { "type": "integer" },  
      "email":  { "type": "keyword"  }, 
      "name":   { "type": "text"  }     
    }
  }
}
```

## View the current mappings
```json
GET /customers/_mapping
{}
```

## View the details of specific fields
```json
GET /customers/_mapping/field/email
{}
```


## Add a field to existing index
```json
PUT /customers/_mapping
{
  "properties": {
    "security-id": {
      "type": "keyword"
    }
  }
}
```

## Delete a field from all documents
```json
POST customers/_update_by_query
{
  "script": {
    "source": """
        if (ctx._source.containsKey("security-id")) {
            ctx._source.remove("security-id");
        } else {
          ctx.op="noop";
        }
      """
    }
  },
  "query": {
    "match_all": {}
  }
}
```
## Reindex removing field
Create a temporary index copying all fields expect removed
```json
POST /_reindex
{
  "source": {
    "index": "customers"
  },
  "dest": {
    "index": "new_customers"
  },
  "script": {
    "source": "ctx._source.remove('security-id')"
  }
}
```
Delete old index
```json
DELETE customers
{}
```
Disable writing on new index
```json
PUT /new_customers/_settings
{
  "settings": {
    "index.blocks.write": true
  }
}
```
Clone the index
```json
POST /new_customers/_clone/customers
{}
```
Delete temporary index
```json
DELETE new_customers
{}
```
Enable writing
```json
PUT /customers/_settings
{
  "settings": {
    "index.blocks.write": false
  }
}
```

## Create a document
```json
POST /customers/_doc
{
  "security-id": "32938928983",
  "name": "John Smith",
  "email": "john.smith@gmail.com",
  "age": "35",
}
```
## Query a document
```json
GET /customers/_search
{
  "query": {
    "match": {
      "security-id": "32938928983"
    }
  }
}
```

## List all
```json
GET customers/_search
{
  "query": {"match_all": {}}
}
```

## Count all
```json
GET customers/_count
{}
```

## Count by query
```json
GET customers/_count
{
  "query": {"match": {"age":37}}
}
```


## Update a document by id
```json
POST customers/_update/{id}
{
  "script" : {
    "source": "ctx._source.age = 36"
  }
}
```

## Update a document by query
```json
POST customers/_update_by_query
{
  "script": {
    "source": "ctx._source.age = 37"
  },
  "query": {
    "match": {
      "age": "36"
    }
  }
}
```

## Bulk operations
```json
POST _bulk
{ "create" : { "_index" : "customers", "_id" : "1" } }
{ "name": "John Bohn", "age": 21, "email": "john.bohn@gmail.com"}
{ "update" : { "_index" : "customers", "_id" : "1" } }
{ "doc" : {"age" : "22"} }
{ "delete" : { "_index" : "customers", "_id" : "1" } }
```