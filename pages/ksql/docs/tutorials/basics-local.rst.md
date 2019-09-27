---
---
::: {#ksql_quickstart-local}
Writing Streaming Queries Against {{ site.ak-tm }} Using KSQL (Local)
============================================================
:::

This tutorial demonstrates a simple workflow using KSQL to write
streaming queries against messages in Kafka.

To get started, you must start a Kafka cluster, including {{ site.zk }}
and a Kafka broker. KSQL will then query messages from this Kafka
cluster. KSQL is installed in the {{ site.cp }} by default.

Watch the [screencast of Reading Kafka Data from
KSQL](https://www.youtube.com/embed/EzVZOUt9JsU) on YouTube.

**Prerequisites:**

-   [Confluent Platform \<installation\>]{role="ref"} is installed and
    running. This installation includes a Kafka broker, KSQL, {{
    site.c3-short }}, {{ site.zk }}, {{ site.sr }}, {{ site.crest }},
    and {{ site.kconnect }}.
-   If you installed {{ site.cp }} via TAR or ZIP, navigate into the
    installation directory. The paths and commands used throughout this
    tutorial assume that you are in this installation directory.
-   Consider [installing \<cli-install\>]{role="ref"} the {{
    site.confluent-cli }} to start a local installation of {{ site.cp
    }}.
-   Java: Minimum version 1.8. Install Oracle Java JRE or JDK \>= 1.8 on
    your local machine

::: {#ksql-create-a-stream-and-table}
:::

::: {#struct-support-local}
:::

``` {.sourceCode .bash}
$ <path-to-confluent>/bin/ksql-datagen  \
     quickstart=orders \
     format=avro \
     topic=orders 
```

::: {#ss-joins-local}
:::

``` {.sourceCode .bash}
$ <path-to-confluent>/bin/kafka-console-producer \
  --broker-list localhost:9092 \
  --topic new_orders \
  --property "parse.key=true" \
  --property "key.separator=:"<<EOF
1:{"order_id":1,"total_amount":10.50,"customer_name":"Bob Smith"}
2:{"order_id":2,"total_amount":3.32,"customer_name":"Sarah Black"}
3:{"order_id":3,"total_amount":21.00,"customer_name":"Emma Turner"}
EOF

$ <path-to-confluent>/bin/kafka-console-producer \
  --broker-list localhost:9092 \
  --topic shipments \
  --property "parse.key=true" \
  --property "key.separator=:"<<EOF
1:{"order_id":1,"shipment_id":42,"warehouse":"Nashville"}
3:{"order_id":3,"shipment_id":43,"warehouse":"Palo Alto"}
EOF
```

::: {.tip}
::: {.admonition-title}
Tip
:::

Note that you may see the following warning message when running the
above statements---it can be safely ignored:

``` {.sourceCode .bash}
Error while fetching metadata with correlation id 1 : {new_orders=LEADER_NOT_AVAILABLE} (org.apache.kafka.clients.NetworkClient)
Error while fetching metadata with correlation id 1 : {shipments=LEADER_NOT_AVAILABLE} (org.apache.kafka.clients.NetworkClient)
```
:::

::: {#tt-joins-local}
:::

``` {.sourceCode .bash}
$ <path-to-confluent>/bin/kafka-console-producer \
  --broker-list localhost:9092 \
  --topic warehouse_location \
  --property "parse.key=true" \
  --property "key.separator=:"<<EOF
```

Your output should resemble:

    1:{"warehouse_id":1,"city":"Leeds","country":"UK"}
    2:{"warehouse_id":2,"city":"Sheffield","country":"UK"}
    3:{"warehouse_id":3,"city":"Berlin","country":"Germany"}
    EOF

``` {.sourceCode .bash}
$ <path-to-confluent>/bin/kafka-console-producer \
  --broker-list localhost:9092 \
  --topic warehouse_size \
  --property "parse.key=true" \
  --property "key.separator=:"<<EOF
```

Your output should resemble:

    1:{"warehouse_id":1,"square_footage":16000}
    2:{"warehouse_id":2,"square_footage":42000}
    3:{"warehouse_id":3,"square_footage":94000}
    EOF

::: {#insert-into-local}
:::

::: {.tip}
::: {.admonition-title}
Tip
:::

Each of these commands should be run in a separate window. When the
exercise is finished, exit them by pressing Ctrl-C.
:::

``` {.sourceCode .bash}
$ <path-to-confluent>/bin/ksql-datagen \ 
     quickstart=orders \
     format=avro \
     topic=orders_local 

$ <path-to-confluent>/bin/ksql-datagen \ 
     quickstart=orders \
     format=avro \
     topic=orders_3rdparty 
```

::: {#terminate-local}
:::

Confluent CLI
=============

If you are running {{ site.cp }} using the CLI, you can stop it with
this command.

``` {.sourceCode .bash}
$ <path-to-confluent>/bin/confluent local stop
```
