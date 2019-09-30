---
---
Install KSQL with Docker
========================

You can deploy KSQL by using Docker containers. Starting with {{ site.cp
}} 4.1.2, Confluent maintains images at [Docker
Hub](https://hub.docker.com/u/confluentinc) for [KSQL
Server](https://hub.docker.com/r/confluentinc/cp-ksql-server/) and the
[KSQL command-line interface
(CLI)](https://hub.docker.com/r/confluentinc/cp-ksql-cli/).

KSQL runs separately from your {{ site.ak-tm }} cluster, so you specify
the IP addresses of the cluster\'s bootstrap servers when you start a
container for KSQL Server. To set up {{ site.cp }} by using containers,
see [ce-docker-quickstart]{role="ref"}.

Use the following settings to start containers that run KSQL in various
configurations.

-   [ksql-headless-server-settings]{role="ref"}
-   [ksql-headless-server-with-interceptor-settings]{role="ref"}
-   [ksql-interactive-server-settings]{role="ref"}
-   [ksql-interactive-server-with-interceptor-settings]{role="ref"}
-   [ksql-connect-to-secure-cluster-settings]{role="ref"}
-   [ksql-configure-with-java]{role="ref"}
-   [ksql-server-view-logs]{role="ref"}
-   [ksql-enable-processing-log]{role="ref"}
-   [ksql-cli-connect-to-dockerized-server]{role="ref"}
-   [ksql-cli-config-file]{role="ref"}
-   [ksql-cli-connect-to-hosted-server]{role="ref"}

When your KSQL processes are running in containers, you can
[interact \<ksql-interact-with-containerized-ksql\>]{role="ref"} with
them by using shell scripts and Docker Compose files.

-   [ksql-wait-for-http-endpoint]{role="ref"}
-   [ksql-wait-for-message-in-container-log]{role="ref"}
-   [ksql-run-custom-code-before-launch]{role="ref"}
-   [ksql-execute-script-in-cli]{role="ref"}

Scale Your KSQL Server Deployment
---------------------------------

You can scale KSQL by adding more capacity per server (vertically) or by
adding more servers (horizontally). Also, you can scale KSQL clusters
during live operations without loss of data. For more information, see
[ksql-capacity-planning-scaling]{role="ref"}.

Assign Configuration Settings in the Docker Run Command
-------------------------------------------------------

You can dynamically pass configuration settings into containers by using
environment variables. When you start a container, set up the
configuration with the `-e` or `--env` flags in the `docker run`
command.

For a complete list of KSQL parameters, see
[KSQL Configuration Parameter Reference \<ksql-param-reference\>]{role="ref"}.

In most cases, to assign a KSQL configuration parameter in a container,
you prepend the parameter name with `KSQL_` and substitute the
underscore character for periods. For example, to assign the
`ksql.queries.file` setting in your `docker run` command, specify:

    -e KSQL_KSQL_QUERIES_FILE=<path-in-container-to-sql-file>

Also, you can set configuration options by using the `KSQL_OPTS`
environment variable. For example, to assign the `ksql.queries.file`
setting in your `docker run` command, specify:

    -e KSQL_OPTS="-Dksql.queries.file=/path/in/container/queries.sql"

Properties set with `KSQL_OPTS` take precedence over values specified in
the KSQL configuration file. For more information, see
[set-ksql-server-properties]{role="ref"}.

KSQL Server
-----------

The following commands show how to run KSQL Server in a container.

### KSQL Headless Server Settings (Production) {#ksql-headless-server-settings}

You can deploy KSQL Server into production in a non-interactive, or
*headless*, mode. In headless mode, interactive use of the KSQL cluster
is disabled, and you configure KSQL Server with a predefined `.sql` file
and the `KSQL_KSQL_QUERIES_FILE` setting. For more information, see
[restrict-ksql-interactive]{role="ref"}.

Use the following command to run a headless, standalone KSQL Server
instance in a container:

``` {.sourceCode .bash}
docker run -d \
  -v /path/on/host:/path/in/container/ \
  -e KSQL_BOOTSTRAP_SERVERS=localhost:9092 \
  -e KSQL_KSQL_SERVICE_ID=ksql_standalone_1_ \
  -e KSQL_KSQL_QUERIES_FILE=/path/in/container/queries.sql \
  confluentinc/cp-ksql-server:{{ site.release }}
```

`KSQL_BOOTSTRAP_SERVERS`

:   A list of hosts for establishing the initial connection to the Kafka
    cluster.

`KSQL_KSQL_SERVICE_ID`

:   The service ID of the KSQL server, which is used as the prefix for
    the internal topics created by KSQL.

`KSQL_KSQL_QUERIES_FILE`

:   A file that specifies predefined KSQL queries.

### KSQL Headless Server with Interceptors Settings (Production) {#ksql-headless-server-with-interceptor-settings}

{{ site.cp }} supports pluggable *interceptors* to examine and modify
incoming and outgoing records. Specify interceptor classes by assigning
the `KSQL_PRODUCER_INTERCEPTOR_CLASSES` and
`KSQL_CONSUMER_INTERCEPTOR_CLASSES` settings. For more info on
interceptor classes, see
[Confluent Monitoring Interceptors \<controlcenter\_clients\>]{role="ref"}.

Use the following command to run a headless, standalone KSQL Server with
the specified interceptor classes in a container:

``` {.sourceCode .bash}
docker run -d \
  -v /path/on/host:/path/in/container/ \
  -e KSQL_BOOTSTRAP_SERVERS=localhost:9092 \
  -e KSQL_KSQL_SERVICE_ID=ksql_standalone_2_ \
  -e KSQL_PRODUCER_INTERCEPTOR_CLASSES=io.confluent.monitoring.clients.interceptor.MonitoringProducerInterceptor \
  -e KSQL_CONSUMER_INTERCEPTOR_CLASSES=io.confluent.monitoring.clients.interceptor.MonitoringConsumerInterceptor \
  -e KSQL_KSQL_QUERIES_FILE=/path/in/container/queries.sql \
  confluentinc/cp-ksql-server:{{ site.release }}
```

`KSQL_BOOTSTRAP_SERVERS`

:   A list of hosts for establishing the initial connection to the Kafka
    cluster.

`KSQL_KSQL_SERVICE_ID`

:   The service ID of the KSQL server, which is used as the prefix for
    the internal topics created by KSQL.

`KSQL_KSQL_QUERIES_FILE`

:   A file that specifies predefined KSQL queries.

`KSQL_PRODUCER_INTERCEPTOR_CLASSES`

:   A list of fully qualified class names for producer interceptors.

`KSQL_CONSUMER_INTERCEPTOR_CLASSES`

:   A list of fully qualified class names for consumer interceptors.

### KSQL Interactive Server Settings (Development) {#ksql-interactive-server-settings}

Develop your KSQL applications by using the KSQL command-line interface
(CLI), or the graphical interface in {{ site.c3 }}, or both together.

Run a KSQL Server that enables manual interaction by using the KSQL CLI:

``` {.sourceCode .bash}
docker run -d \
  -p 127.0.0.1:8088:8088 \
  -e KSQL_BOOTSTRAP_SERVERS=localhost:9092 \
  -e KSQL_LISTENERS=http://0.0.0.0:8088/ \
  -e KSQL_KSQL_SERVICE_ID=ksql_service_2_ \
  confluentinc/cp-ksql-server:{{ site.release }}
```

`KSQL_BOOTSTRAP_SERVERS`

:   A list of hosts for establishing the initial connection to the Kafka
    cluster.

`KSQL_KSQL_SERVICE_ID`

:   The service ID of the KSQL server, which is used as the prefix for
    the internal topics created by KSQL.

`KSQL_LISTENERS`

:   A list of URIs, including the protocol, that the broker listens on.
    If you are using IPv6, set to `http://[::]:8088`.

In interactive mode, a KSQL CLI instance running outside of Docker can
connect to the KSQL server running in Docker.

### KSQL Interactive Server with Interceptors Settings (Development) {#ksql-interactive-server-with-interceptor-settings}

Run a KSQL Server with interceptors that enables manual interaction by
using the KSQL CLI:

``` {.sourceCode .bash}
docker run -d \
  -p 127.0.0.1:8088:8088 \
  -e KSQL_BOOTSTRAP_SERVERS=localhost:9092 \
  -e KSQL_LISTENERS=http://0.0.0.0:8088/ \
  -e KSQL_KSQL_SERVICE_ID=ksql_service_3_ \
  -e KSQL_PRODUCER_INTERCEPTOR_CLASSES=io.confluent.monitoring.clients.interceptor.MonitoringProducerInterceptor \
  -e KSQL_CONSUMER_INTERCEPTOR_CLASSES=io.confluent.monitoring.clients.interceptor.MonitoringConsumerInterceptor \
  confluentinc/cp-ksql-server:{{ site.release }}
```

`KSQL_BOOTSTRAP_SERVERS`

:   A list of hosts for establishing the initial connection to the Kafka
    cluster.

`KSQL_KSQL_SERVICE_ID`

:   The service ID of the KSQL server, which is used as the prefix for
    the internal topics created by KSQL.

`KSQL_LISTENERS`

:   A list of URIs, including the protocol, that the broker listens on.
    If you are using IPv6, set to `http://[::]:8088`.

`KSQL_PRODUCER_INTERCEPTOR_CLASSES`

:   A list of fully qualified class names for producer interceptors.

`KSQL_CONSUMER_INTERCEPTOR_CLASSES`

:   A list of fully qualified class names for consumer interceptors.

For more info on interceptor classes, see
[Confluent Monitoring Interceptors \<controlcenter\_clients\>]{role="ref"}.

In interactive mode, a CLI instance running outside of Docker can
connect to the server running in Docker.

::: {#ksql-connect-to-secure-cluster-settings}
Connect KSQL Server to a Secure Kafka Cluster, Like {{ site.ccloud }}
============================================================
:::

KSQL Server runs outside of your Kafka clusters, so you need specify in
the container environment how KSQL Server connects with a Kafka cluster.

Run a KSQL Server that uses a secure connection to a Kafka cluster:

``` {.sourceCode .bash}
docker run -d \
  -p 127.0.0.1:8088:8088 \
  -e KSQL_BOOTSTRAP_SERVERS=REMOVED_SERVER1:9092,REMOVED_SERVER2:9093,REMOVED_SERVER3:9094 \
  -e KSQL_LISTENERS=http://0.0.0.0:8088/ \
  -e KSQL_KSQL_SERVICE_ID=default_ \
  -e KSQL_KSQL_SINK_REPLICAS=3 \
  -e KSQL_KSQL_STREAMS_REPLICATION_FACTOR=3 \
  -e KSQL_SECURITY_PROTOCOL=SASL_SSL \
  -e KSQL_SASL_MECHANISM=PLAIN \
  -e KSQL_SASL_JAAS_CONFIG="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"<username>\" password=\"<strong-password>\";" \
  confluentinc/cp-ksql-server:{{ site.release }}
```

`KSQL_BOOTSTRAP_SERVERS`

:   A list of hosts for establishing the initial connection to the Kafka
    cluster.

`KSQL_KSQL_SERVICE_ID`

:   The service ID of the KSQL server, which is used as the prefix for
    the internal topics created by KSQL.

`KSQL_LISTENERS`

:   A list of URIs, including the protocol, that the broker listens on.
    If you are using IPv6 , set to `http://[::]:8088`.

`KSQL_KSQL_SINK_REPLICAS`

:   The default number of replicas for the topics created by KSQL. The
    default is one.

`KSQL_KSQL_STREAMS_REPLICATION_FACTOR`

:   The replication factor for internal topics, the command topic, and
    output topics.

`KSQL_SECURITY_PROTOCOL`

:   The protocol that your Kafka cluster uses for security.

`KSQL_SASL_MECHANISM`

:   The SASL mechanism that your Kafka cluster uses for security.

`KSQL_SASL_JAAS_CONFIG`

:   The Java Authentication and Authorization Service (JAAS)
    configuration.

Learn about [KSQL Security \<ksql-security\>]{role="ref"}.

### Configure a KSQL Server by Using Java System Properties {#ksql-configure-with-java}

Use the `KSQL_OPTS` environment variable to assign configuration
settings by using Java system properties. Prepend the KSQL setting name
with `-D`. For example, to set the KSQL service identifier in the
`docker run` command, use:

    -e KSQL_OPTS="-Dksql.service.id=<your-service-id>"

Run a KSQL Server with a configuration that\'s defined by Java
properties:

``` {.sourceCode .bash}
docker run -d \
  -v /path/on/host:/path/in/container/ \
  -e KSQL_BOOTSTRAP_SERVERS=localhost:9092 \
  -e KSQL_OPTS="-Dksql.service.id=ksql_service_3_  -Dksql.queries.file=/path/in/container/queries.sql" \
  confluentinc/cp-ksql-server:{{ site.release }}
```

`KSQL_BOOTSTRAP_SERVERS`

:   A list of hosts for establishing the initial connection to the Kafka
    cluster.

`KSQL_OPTS`

:   A space-separated list of Java options.

The previous example assigns two settings, `ksql.service.id` and
`ksql.queries.file`. Specify more configuration settings by adding them
in the `KSQL_OPTS` line. Remember to prepend each setting name with
`-D`.

### View KSQL Server Logs {#ksql-server-view-logs}

Use the `docker logs` command to view KSQL logs that are generated from
within the container:

``` {.sourceCode .bash}
docker logs -f <container-id>
```

Your output should resemble:

    [2019-01-16 23:43:05,591] INFO stream-thread [_confluent-ksql-default_transient_1507119262168861890_1527205385485-71c8a94c-abe9-45ba-91f5-69a762ec5c1d-StreamThread-17] Starting (org.apache.kafka.streams.processor.internals.StreamThread:713)
    ...

### Enable the Processing Log {#ksql-enable-processing-log}

KSQL emits a log of record processing events, called the processing log,
to help you debug KSQL queries. For more information, see
[ksql\_processing\_log]{role="ref"}.

Assign the following configuration settings to enable the processing
log.

    # — Processing log config —
    KSQL_LOG4J_PROCESSING_LOG_BROKERLIST: kafka:29092
    KSQL_LOG4J_PROCESSING_LOG_TOPIC: demo_processing_log
    KSQL_KSQL_LOGGING_PROCESSING_TOPIC_NAME: demo_processing_log
    KSQL_KSQL_LOGGING_PROCESSING_TOPIC_AUTO_CREATE: "true"
    KSQL_KSQL_LOGGING_PROCESSING_STREAM_AUTO_CREATE: "true"

KSQL Command-line Interface (CLI)
---------------------------------

Develop the KSQL queries and statements for your real-time streaming
applications by using the KSQL CLI, or the graphical interface in {{
site.c3 }}, or both together. The KSQL CLI connects to a running KSQL
Server instance to enable inspecting Kafka topics and creating KSQL
streams and tables. For more information, see
[install\_cli-config]{role="ref"}.

The following commands show how to run the KSQL CLI in a container and
connect to a KSQL Server.

### Connect KSQL CLI to a Dockerized KSQL Server {#ksql-cli-connect-to-dockerized-server}

Run a KSQL CLI instance in a container and connect to a KSQL Server
that\'s running in a different container.

``` {.sourceCode .bash}
# Run KSQL Server.
docker run -d -p 10.0.0.11:8088:8088 \
  -e KSQL_BOOTSTRAP_SERVERS=localhost:9092 \
  -e KSQL_OPTS="-Dksql.service.id=ksql_service_3_  -Dlisteners=http://0.0.0.0:8088/" \  
  confluentinc/cp-ksql-server:{{ site.release }}

# Connect the KSQL CLI to the server.
docker run -it confluentinc/cp-ksql-cli http://10.0.0.11:8088 
```

`KSQL_BOOTSTRAP_SERVERS`

:   A list of hosts for establishing the initial connection to the Kafka
    cluster.

`KSQL_OPTS`

:   A space-separated list of Java options. If you are using IPv6, set
    `listeners` to `http://[::]:8088`.

The Docker network created by KSQL Server enables you to connect with a
dockerized KSQL CLI.

### Start KSQL CLI With a Provided Configuration File {#ksql-cli-config-file}

Set up a a KSQL CLI instance by using a configuration file, and run it
in a container:

``` {.sourceCode .bash}
# Assume KSQL Server is running.
# Ensure that the configuration file exists.
ls /path/on/host/ksql-cli.properties

docker run -it \
  -v /path/on/host/:/path/in/container  \
  confluentinc/cp-ksql-cli:{{ site.release }} http://10.0.0.11:8088 \
  --config-file /path/in/container/ksql-cli.properties
```

### Connect KSQL CLI to a KSQL Server Running on Another Host (Cloud) {#ksql-cli-connect-to-hosted-server}

Run a KSQL CLI instance in a container and connect to a remote KSQL
Server host:

``` {.sourceCode .bash}
docker run -it confluentinc/cp-ksql-cli:{{ site.release }} \
  http://ec2-blah.us-blah.compute.amazonaws.com:8080
```

Your output should resemble:

``` {.sourceCode .text}
... 
Copyright 2017-2018 Confluent Inc.

CLI v{{ site.release }}, Server v{{ site.release }} located at http://ec2-blah.us-blah.compute.amazonaws.com:8080

Having trouble? Type 'help' (case-insensitive) for a rundown of how things work!

ksql>
```

Interact With KSQL Running in a Docker Container {#ksql-interact-with-containerized-ksql}
------------------------------------------------

You can communicate with KSQL Server and the KSQL CLI when they run in
Docker containers. The following examples show common tasks with KSQL
processes that run in containers.

-   [ksql-wait-for-http-endpoint]{role="ref"}
-   [ksql-wait-for-message-in-container-log]{role="ref"}
-   [ksql-run-custom-code-before-launch]{role="ref"}
-   [ksql-execute-script-in-cli]{role="ref"}

### Wait for an HTTP Endpoint to Be Available {#ksql-wait-for-http-endpoint}

Sometimes, a container reports its state as `up` before it\'s actually
running. In this case, the docker-compose `depends_on` dependencies
aren\'t sufficient. For a service that exposes an HTTP endpoint, like
KSQL Server, you can force a script to wait before running a client that
requires the service to be ready and available.

Use the following bash commands to wait for KSQL Server to be available:

``` {.sourceCode .bash}
echo -e "\n\n⏳ Waiting for KSQL to be available before launching CLI\n"
while [ $(curl -s -o /dev/null -w %{http_code} http://<ksql-server-ip-address>:8088/) -eq 000 ]
do 
  echo -e $(date) "KSQL Server HTTP state: " $(curl -s -o /dev/null -w %{http_code} http://<ksql-server-ip-address>:8088/) " (waiting for 200)"
  sleep 5
done
```

This script pings the KSQL Server at `<ksql-server-ip-address>:8088`
every five seconds, until it receives an HTTP 200 response.

::: {.note}
::: {.admonition-title}
Note
:::

The previous script doesn\'t work with \"headless\" deployments of KSQL
Server, because headless deployments don\'t have a REST API server.
:::

To launch the KSQL CLI in a container only after KSQL Server is
available, use the following Docker Compose command:

``` {.sourceCode .bash}
docker-compose exec ksql-cli bash -c \
'echo -e "\n\n⏳ Waiting for KSQL to be available before launching CLI\n"; while [ $(curl -s -o /dev/null -w %{http_code} http://<ksql-server-ip-address>:8088/) -eq 000 ] ; do echo -e $(date) "KSQL Server HTTP state: " $(curl -s -o /dev/null -w %{http_code} http://<ksql-server-ip-address>:8088/) " (waiting for 200)" ; sleep 5 ; done; ksql http://<ksql-server-ip-address>:8088'
```

### Wait for a Particular Phrase in a Container's Log {#ksql-wait-for-message-in-container-log}

Use the `grep` command and [bash process
substitution](http://tldp.org/LDP/abs/html/process-sub.html) to wait
until the a specific phrase occurs in the Docker Compose log:

``` {.sourceCode .bash}
export CONNECT_HOST=<container-name>
echo -e "\n--\n\nWaiting for Kafka Connect to start on $CONNECT_HOST … ⏳"
grep -q "Kafka Connect started" <(docker-compose logs -f $CONNECT_HOST)
```

### Run Custom Code Before Launching a Container's Program {#ksql-run-custom-code-before-launch}

You can run custom code, like downloading a dependency or moving a file,
before a KSQL process starts in a container. Use Docker Compose to
overlay a change on an existing image.

#### Get the Container\'s Default Command

Discover the default command that the container runs when it launches,
which is either `Entrypoint` or `Cmd`:

``` {.sourceCode .bash}
docker inspect --format='{{.Config.Entrypoint}}' confluentinc/cp-ksql-server:{{ site.release }}
docker inspect --format='{{.Config.Cmd}}' confluentinc/cp-ksql-server:{{ site.release }}
```

Your output should resemble:

    []
    [/etc/confluent/docker/run]

In this example, the default command is `/etc/confluent/docker/run`.

#### Run Custom Commands Before the KSQL Process Starts

In a Docker Compose file, add the commands that you want to run before
the main process starts. Use the `command` option to override the
default command. In the following example, the `command` option creates
a directory and downloads a tar archive into it.

``` {.sourceCode .yaml}
ksql-server:
  image: confluentinc/cp-ksql-server:{{ site.release }}
  depends_on:
    - kafka
  environment:
    KSQL_BOOTSTRAP_SERVERS: <bootstrap-server-ip>:29092
    KSQL_LISTENERS: http://0.0.0.0:8088
  command: 
    - /bin/bash
    - -c 
    - |
      mkdir -p /data/maxmind
      cd /data/maxmind
      curl https://geolite.maxmind.com/download/geoip/database/GeoLite2-City.tar.gz | tar xz 
      /etc/confluent/docker/run
```

After the `mkdir`, `cd`, `curl`, and `tar` commands run, the
`/etc/confluent/docker/run` command starts the `cp-ksql-server` image
with the specified settings.

::: {.note}
::: {.admonition-title}
Note
:::

The literal block scalar, `- |`, enables passing multiple arguments to
`command`, by indicating that the following lines are all part of the
same entry.
:::

### Execute a KSQL script in the KSQL CLI {#ksql-execute-script-in-cli}

The following Docker Compose YAML runs KSQL CLI and passes it a KSQL
script for execution. The manual EXIT is required. The advantage of this
approach, compared with running KSQL Server headless with a queries
file, is that you can still interact with KSQL, and you can pre-build
the environment to a desired state.

``` {.sourceCode .yaml}
ksql-cli:
  image: confluentinc/cp-ksql-cli:{{ site.release }}
  depends_on:
    - ksql-server
  volumes:
    - $PWD/ksql-scripts/:/data/scripts/
  entrypoint: 
    - /bin/bash
    - -c
    - |
      echo -e "\n\n⏳ Waiting for KSQL to be available before launching CLI\n"
      while [ $$(curl -s -o /dev/null -w %{http_code} http://<ksql-server-ip>:8088/) -eq 000 ]
      do 
        echo -e $$(date) "KSQL Server HTTP state: " $$(curl -s -o /dev/null -w %{http_code} http://<ksql-server-ip>:8088/) " (waiting for 200)"
        sleep 5
      done
      echo -e "\n\n-> Running KSQL commands\n"
      cat /data/scripts/my-ksql-script.sql <(echo 'EXIT')| ksql http://<ksql-server-ip>:8088
      echo -e "\n\n-> Sleeping…\n"
      sleep infinity
```

Next Steps
----------

-   [ksql\_quickstart-docker]{role="ref"}
-   [ksql\_clickstream-docker]{role="ref"}
