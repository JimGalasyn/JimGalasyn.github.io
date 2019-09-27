---
---
KSQL REST API Reference {#ksql-rest-api}
=======================

REST Endpoint
-------------

The default REST API endpoint is `http://0.0.0.0:8088/`.

Change the server configuration that controls the REST API endpoint by
setting the `listeners` parameter in the KSQL server config file. For
more info, see [ksql-listeners]{role="ref"}. To configure the endpoint
to use HTTPS, see [config-ksql-for-https]{role="ref"}.

Content Types
-------------

The KSQL REST API uses content types for requests and responses to
indicate the serialization format of the data and the API version.
Currently, the only serialization format supported is JSON. The only
version supported is v1. Your request should specify this serialization
format and version in the `Accept` header as:

    Accept: application/vnd.ksql.v1+json

The less specific `application/json` content type is also permitted.
However, this is only for compatibility and ease of use, and you should
use the versioned value if possible.

The server also supports content negotiation, so you may include
multiple, weighted preferences:

    Accept: application/vnd.ksql.v1+json; q=0.9, application/json; q=0.5

For example, content negotiation is useful when a new version of the API
is preferred, but you are not sure if it is available yet.

Here\'s an example request that returns the results from the
`LIST STREAMS` command:

``` {.sourceCode .bash}
curl -X "POST" "http://localhost:8088/ksql" \
     -H "Content-Type: application/vnd.ksql.v1+json; charset=utf-8" \
     -d $'{
  "ksql": "LIST STREAMS;",
  "streamsProperties": {}
}'
```

Here\'s an example request that retrieves streaming data from
`TEST_STREAM`:

``` {.sourceCode .bash}
curl -X "POST" "http://localhost:8088/query" \
     -H "Content-Type: application/vnd.ksql.v1+json; charset=utf-8" \
     -d $'{
  "ksql": "SELECT * FROM TEST_STREAM;",
  "streamsProperties": {}
}'
```

Errors
------

All API endpoints use a standard error message format for any requests
that return an HTTP status indicating an error (any 4xx or 5xx
statuses):

``` {.sourceCode .http}
HTTP/1.1 <Error Status>
Content-Type: application/json

{
    "error_code": <Error code>
    "message": <Error Message>
}
```

Some endpoints may include additional fields that provide more context
for handling the error.

Get the Status of a KSQL Server
-------------------------------

The `/info` resource gives you information about the status of a KSQL
server, which can be useful for health checks and troubleshooting. You
can use the `curl` command to query the `/info` endpoint:

``` {.sourceCode .bash}
curl -sX GET "http://localhost:8088/info" | jq '.'
```

Your output should resemble:

::: {.codewithvars}
bash

{

:   

    \"KsqlServerInfo\": {

    :   \"version\": \"{{ site.release }}\", \"kafkaClusterId\":
        \"j3tOi6E\_RtO\_TMH3gBmK7A\", \"ksqlServiceId\": \"[default]()\"

    }

}
:::

Run a KSQL Statement
--------------------

The `/ksql` resource runs a sequence of KSQL statements. All statements,
except those starting with SELECT, can be run on this endpoint. To run
SELECT statements use the `/query` endpoint.

::: {.note}
::: {.admonition-title}
Note
:::

If you use the SET or UNSET statements to assign query properties by
using the REST API, the assignment is scoped only to the current
request. In contrast, SET and UNSET assignments in the KSQL CLI persist
throughout the CLI session.
:::

Coordinate Multiple Requests {#coordinate_multiple_requests}
----------------------------

To submit multiple, interdependent requests, there are two options. The
first is to submit them as a single request, similar to the example
request above:

``` {.sourceCode .http}
POST /ksql HTTP/1.1
Accept: application/vnd.ksql.v1+json
Content-Type: application/vnd.ksql.v1+json

{
  "ksql": "CREATE STREAM pageviews_home AS SELECT * FROM pageviews_original WHERE pageid='home'; CREATE TABLE pageviews_home_count AS SELECT userid, COUNT(*) FROM pageviews_home GROUP BY userid;"
}
```

The second method is to submit the statements as separate requests and
incorporate the interdependency by using `commandSequenceNumber`. Send
the first request:

``` {.sourceCode .http}
POST /ksql HTTP/1.1
Accept: application/vnd.ksql.v1+json
Content-Type: application/vnd.ksql.v1+json

{
  "ksql": "CREATE STREAM pageviews_home AS SELECT * FROM pageviews_original WHERE pageid='home';"
}
```

Make note of the `commandSequenceNumber` returned in the response:

``` {.sourceCode .http}
HTTP/1.1 200 OK
Content-Type: application/vnd.ksql.v1+json

[
  {
    "statementText":"CREATE STREAM pageviews_home AS SELECT * FROM pageviews_original WHERE pageid='home';",
    "commandId":"stream/PAGEVIEWS_HOME/create",
    "commandStatus": {
      "status":"SUCCESS",
      "message":"Stream created and running"
    },
    "commandSequenceNumber":10
  }
]
```

Provide this `commandSequenceNumber` as part of the second request,
indicating that this request should not execute until after command
number 10 has finished executing:

``` {.sourceCode .http}
POST /ksql HTTP/1.1
Accept: application/vnd.ksql.v1+json
Content-Type: application/vnd.ksql.v1+json

{
  "ksql": "CREATE TABLE pageviews_home_count AS SELECT userid, COUNT(*) FROM pageviews_home GROUP BY userid;",
  "commandSequenceNumber":10
}
```

Run A Query And Stream Back The Output
--------------------------------------

The `/query` resource lets you stream the output records of a `SELECT`
statement via a chunked transfer encoding. The response is streamed back
until the `LIMIT` specified in the statement is reached, or the client
closes the connection. If no `LIMIT` is specified in the statement, then
the response is streamed until the client closes the connection.

Get the Status of a CREATE, DROP, or TERMINATE
----------------------------------------------

CREATE, DROP, and TERMINATE statements returns an object that indicates
the current state of statement execution. A statement can be in one of
the following states:

-   QUEUED, PARSING, EXECUTING: The statement was accepted by the server
    and is being processed.
-   SUCCESS: The statement was successfully processed.
-   ERROR: There was an error processing the statement. The statement
    was not executed.
-   TERMINATED: The query started by the statement was terminated. Only
    returned for `CREATE STREAM|TABLE AS SELECT`.

If a CREATE, DROP, or TERMINATE statement returns a command status with
state QUEUED, PARSING, or EXECUTING from the `/ksql` endpoint, you can
use the `/status` endpoint to poll the status of the command.

Terminate a Cluster {#ksql_cluster_terminate}
-------------------

If you don\'t need your KSQL cluster anymore, you can terminate the
cluster and clean up the resources using this endpoint. To terminate a
KSQL cluster, first shutdown all the servers except one. Then, send the
`TERMINATE CLUSTER` request to the `/ksql/terminate` endpoint in the
last remaining server. When the server receives a `TERMINATE CLUSTER`
request at `/ksql/terminate` endpoint, it writes a `TERMINATE CLUSTER`
command into the command topic. Note that `TERMINATE CLUSTER` request
can only be sent via the `/ksql/terminate` endpoint and you cannot send
it via the CLI. When the server reads the `TERMINATE CLUSTER` command,
it takes the following steps:

-Sets the KSQL engine mode to `NOT ACCEPTING NEW STATEMENTS` so no new
statements will be passed to the engine for execution. -Terminates all
persistent and transient queries in the engine and performs the required
clean up for each query. -Deletes the command topic for the cluster.

**Example request**

> ``` {.sourceCode .http}
> POST /ksql/terminate HTTP/1.1
> Accept: application/vnd.ksql.v1+json
> Content-Type: application/vnd.ksql.v1+json
>
> {}
> ```

You can customize the clean up process if you want to delete some or all
of the Kafka topics too:

**Provide a List of Kafka Topics to Delete** You can provide a list of
kafka topic names or regular expressions for kafka topic names along
with your `TERMINATE CLUSTER` request. The KSQL server will delete all
topics with names that are in the list or that match any of the regular
expressions in the list. Only the topics that were generated by KSQL
queries will be considered for deletion. Topics that were not generated
by KSQL queries will not be deleted even if they match the provided
list. The following example will delete topic `FOO` along with all
topics with prefix `bar`.

> ``` {.sourceCode .http}
> POST /ksql/terminate HTTP/1.1
> Accept: application/vnd.ksql.v1+json
> Content-Type: application/vnd.ksql.v1+json
>
> {
>   "deleteTopicList": ["FOO", "bar.*"]
> }
> ```
