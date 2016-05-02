# elasticutil
Wrapper for Elasticsearch REST API

I got frustrated with dealing with curl in order to talk to my elasticsearch cluster.  So I scripted it.

### Usage
```
es [-p portnumber] [-h host ] [-d "json data to post" ] [-vnGDPO] rest/api/you/want[?arg=xxx]
  -v verbose
  -n print the curl command, do not run it
  -G use http GET method
  -D use http DELETE method
  -P use http PUT method
  -O use http POST
  ```
  
By default, it calls /cluster/_health as a harmless example.

### Example use
```
jmoore@home:~$ es
Performing [GET] on [localhost]:[9200] at [_cluster/health] with []
{
  "cluster_name" : "cluster",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 5,
  "number_of_data_nodes" : 5,
  "active_primary_shards" : 149,
  "active_shards" : 309,
  "relocating_shards" : 0,
  "initializing_shards" : 2,
  "unassigned_shards" : 14,
  "number_of_pending_tasks" : 0
}
jmoore@home:~$ es -O _all/_flush
Performing [POST] on [localhost]:[9200] at [_all/_flush] with []
{
  "_shards" : {
    "total" : 325,
    "successful" : 307,
    "failed" : 2,
    "failures" : [ {
      "index" : "sessions-160426",
      "shard" : 0,
      "reason" : "BroadcastShardOperationFailedException[[sessions-160426][0] ]; nested: FlushNotAllowedEngineException[[sessions-160426][0] recovery is in progress, flush with committing translog is not allowed]; "
    }, {
      "index" : "sessions-160426",
      "shard" : 1,
      "reason" : "BroadcastShardOperationFailedException[[sessions-160426][1] ]; nested: RemoteTransportException[[uscisesn004][inet[/146.122.144.134:9300]][indices:admin/flush[s]]]; nested: FlushNotAllowedEngineException[[sessions-160426][1] recovery is in progress, flush with committing translog is not allowed]; "
    } ]
  }
}
```

### Suggestions, pull requests, etc welcome!
