## Check cluster health
```shell
$ curl elasticsearch:9200/_cluster/health?pretty
```
## List all index
```shell
$ curl <elastic_host>/_cat/indices?v
```
## Identifying cluster problem
```shell
$ curl <elastic_host>/_cluster/allocation/explain?pretty
```
## Fix `cannot allocate because allocation is not permitted to any of the nodes` issue
```shell
$ curl -X PUT \
    -H 'Content-Type: application/json' \
    -d '{ "index" : { "number_of_replicas": 0 } }' \
    <elastic_host>/<index_name>/_settings
```
## Reroute failed shards
### The safer way
```shell
$ curl -X POST '<elastic_host>/_cluster/reroute?retry_failed'
```
### Reroute specific index
```shell
$ curl -X POST \
    -H 'Content-Type: application/json' \
    -d '{ "commands" : [ { "allocate_stale_primary" : { "index" : "<index_name>", "shard" : 5, "node" : "0", "accept_data_loss" : true } } ] }' \
    'localhost:9200/_cluster/reroute?pretty'
```
