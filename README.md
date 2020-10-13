# Elasticsearch and Kibana

Run the server
```bash
$ docker-compose up
```

Check if it is running
```bash
$ curl -XGET "http://localhost:9200/_cat/nodes?v" -H 'Content-Type: application/json' -d'{  }'
```

Open Kibana
```
http://localhost:5601
```