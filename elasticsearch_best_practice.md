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
