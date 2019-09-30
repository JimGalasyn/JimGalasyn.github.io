---
---
KSQL Tutorials and Examples {#ksql_tutorials}
===========================

::: {.toctree}
basics-docker basics-local basics-control-center clickstream-docker
generate-custom-test-data connect-integration examples
:::

KSQL Basics
-----------

This tutorial demonstrates a simple workflow using KSQL to write
streaming queries against messages in {{ site.ak-tm }}.

### Write Streaming Queries with the KSQL CLI

``` {.sourceCode .text}
===========================================
=        _  __ _____  ____  _             =
=       | |/ // ____|/ __ \| |            =
=       | ' /| (___ | |  | | |            =
=       |  <  \___ \| |  | | |            =
=       | . \ ____) | |__| | |____        =
=       |_|\_\_____/ \___\_\______|       =
=                                         =
=  Streaming SQL Engine for Apache Kafka® =
===========================================
```

> Copyright 2018 Confluent Inc.
>
> CLI v{{ site.release }}, Server v{{ site.release }} located at
> <http://localhost:8088>
>
> Having trouble? Type \'help\' (case-insensitive) for a rundown of how
> things work!
>
> ksql\>

Get started with the KSQL CLI:

-   [ksql\_quickstart-docker]{role="ref"}
-   [ksql\_quickstart-local]{role="ref"}

Write Streaming Queries with KSQL and {{ site.c3 }}
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

![image](../../../images/ksql-interface-create-stream.png){width="600px"}

Get started with KSQL and {{ site.c3 }}:

-   {{ site.c3-short }}
    [deployed with Docker \<ce-docker-ksql-quickstart\>]{role="ref"}
-   {{ site.c3-short }}
    [deployed locally \<ce-ksql-quickstart\>]{role="ref"}

Stream Processing Cookbook
--------------------------

The [Stream Processing
Cookbook](https://www.confluent.io/product/ksql/stream-processing-cookbook)
contains KSQL recipes that provide in-depth tutorials and recommended
deployment scenarios.

Clickstream Data Analysis Pipeline
----------------------------------

Clickstream analysis is the process of collecting, analyzing, and
reporting aggregate data about which pages a website visitor visits and
in what order. The path the visitor takes though a website is called the
clickstream.

This tutorial focuses on building real-time analytics of users to
determine:

-   General website analytics, such as hit count and visitors
-   Bandwidth use
-   Mapping user-IP addresses to actual users and their location
-   Detection of high-bandwidth user sessions
-   Error-code occurrence and enrichment
-   Sessionization to track user-sessions and understand behavior (such
    as per-user-session-bandwidth, per-user-session-hits etc)

The tutorial uses standard streaming functions (i.e., min, max, etc) and
enrichment using child tables, stream-table join, and different types of
windowing functionality.

Get started now with these instructions:

-   [ksql\_clickstream-docker]{role="ref"}

If you do not have Docker, you can also run an [automated
version](https://github.com/confluentinc/examples/tree/master/clickstream)
of the Clickstream tutorial designed for local Confluent Platform
installs. Running the Clickstream demo locally without Docker requires
that you have Confluent Platform installed locally, along with
Elasticsearch and Grafana.

KSQL Examples
-------------

[These examples \<ksql\_examples\>]{role="ref"} provide common KSQL
usage operations.

KSQL in a Kafka Streaming ETL
-----------------------------

To learn how to deploy a Kafka streaming ETL using KSQL for stream
processing, you can run the [Confluent Platform
demo](https://docs.confluent.io/current/tutorials/cp-demo/docs/index.html).
All components in the {{ site.cp }} demo have encryption,
authentication, and authorization configured end-to-end.

Level Up Your KSQL Videos
-------------------------

  --------------------------------------------------------------------------------------------
  Video                                                      Description
  ---------------------------------------------------------- ---------------------------------
  [KSQL                                                      Intro to Kafka stream processing,
  Introduction](https://www.youtube.com/embed/C-rUyWmRJSQ)   with a focus on KSQL.

  [KSQL Use                                                  Describes several KSQL uses
  Cases](https://www.youtube.com/embed/euz0isNG1SQ)          cases, like data exploration,
                                                             arbitrary filtering, streaming
                                                             ETL, anomaly detection, and
                                                             real-time monitoring.

  [KSQL and Core                                             Describes KSQL dependency on core
  Kafka](https://www.youtube.com/embed/-GpbMAK3Uow)          Kafka, relating KSQL to clients,
                                                             and describes how KSQL uses Kafka
                                                             topics.

  [Installing and Running                                    How to get KSQL, configure and
  KSQL](https://www.youtube.com/embed/icwHpPm-TCA)           start the KSQL server, and syntax
                                                             basics.

  [KSQL Streams and                                          Explains the difference between a
  Tables](https://www.youtube.com/embed/DPGn-j7yD68)         STREAM and TABLE, shows a
                                                             detailed example, and explains
                                                             how streaming queries are
                                                             unbounded.

  [Reading Kafka Data from                                   How to explore Kafka topic data,
  KSQL](https://www.youtube.com/embed/EzVZOUt9JsU)           create a STREAM or TABLE from a
                                                             Kafka topic, identify fields.
                                                             Also explains metadata like
                                                             ROWTIME and TIMESTAMP, and covers
                                                             different formats like Avro,
                                                             JSON, and Delimited.

  [Streaming and Unbounded Data in                           More detail on streaming queries,
  KSQL](https://www.youtube.com/embed/4ccg1AFeNB0)           how to read topics from the
                                                             beginning, the differences
                                                             between persistent and
                                                             non-persistent queries, how do
                                                             streaming queries end.

  [Enriching data with                                       Scalar functions, changing field
  KSQL](https://www.youtube.com/embed/9_Gwe6qJrjI)           types, filtering data, merging
                                                             data with JOIN, and rekeying
                                                             streams.

  [Aggregations in                                           How to aggregate data with KSQL,
  KSQL](https://www.youtube.com/embed/db5SsmNvej4)           different types of aggregate
                                                             functions like COUNT, SUM, MAX,
                                                             MIN, TOPK, etc, and windowing and
                                                             late-arriving data.

  [Taking KSQL to                                            How to use KSQL in streaming ETL
  Production](https://www.youtube.com/embed/f3wV8W_zjwE)     pipelines, scale query
                                                             processing, isolate workloads,
                                                             and secure your entire
                                                             deployment.

  [Insert Into](https://www.youtube.com/watch?v=z508VDdtp_M) A brief tutorial on how to use
                                                             INSERT INTO in KSQL by Confluent.

  [Struct (Nested                                            A brief tutorial on how to use
  Data)](https://www.youtube.com/watch?v=TQd5rfFmbhw)        STRUCT in KSQL by Confluent.

  [Stream-Stream                                             A short tutorial on stream-stream
  Joins](https://www.youtube.com/watch?v=51yLu5FnPYo)        joins in KSQL by Confluent.

  [Table-Table                                               A short tutorial on table-table
  Joins](https://www.youtube.com/watch?v=-eMXWeBfK7U)        joins in KSQL by Confluent.

  [Monitoring KSQL in Confluent Control                      Monitor performance and
  Center](https://www.youtube.com/watch?v=3o7MzCri4e4)       end-to-end message delivery of
                                                             your KSQL queries.
  --------------------------------------------------------------------------------------------
