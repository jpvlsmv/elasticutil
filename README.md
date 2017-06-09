# elasticutil
Wrappers for Elasticsearch REST API

I got frustrated with dealing with curl in order to talk to my elasticsearch cluster.  So I scripted it.

## es Usage
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
## patch_template
Modify an existing template with json changes

I've had to figure out the right data format and place to patch in to modify ES templates, 
so here's a script for it
### patch_template Usage
```
# patch_template [-h [https://]host] [-p port] [-d deltafile.json] [-v] [-n] templatename
  -h host of the ES cluster
  -p ES REST port
  -d file containing the changes to overwrite in the template (see below)
  -v verbose
  -n print the curl command, do not run it (it does run a GET for the template)
  
The changes in the file must be aligned to the document element in the ES template, i.e.
need to be an object starting under the template's named object.

jmoore@home:~$ cat delta.json
{
  "settings": {
    "index": {
      "routing": {
        "allocation": {
          "total_shards_per_node": "7",
          "exclude": {
            "role": "head"
}}}}}}
  ```
  
### Example use
```
jmoore@home:~$ patch_template -n -v -d delta.json sessions_template
Patching sessions_template on localhost:9200 with file delta.json
curl -sS -v -XPUT localhost:9200/_template/sessions_template -d@-
*   Trying 127.0.0.1...
* Connected to localhost (127.0.0.1) port 9200 (#0)
> GET /_template/sessions_template HTTP/1.1
> Host: localhost:9200
> User-Agent: curl/7.47.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Content-Type: application/json; charset=UTF-8
< Content-Length: 7876
<
{ [7876 bytes data]
* Connection #0 to host uscisesc001 left intact
jmoore@home:~$ echo '{"aliases": {"alldata": {}}}' | patch_template sessions_template
jmoore@home:~$
```

# Suggestions, pull requests, etc welcome!
