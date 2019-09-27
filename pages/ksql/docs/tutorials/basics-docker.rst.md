::: {#ksql_quickstart-docker}
Writing Streaming Queries Against {{ site.ak-tm }} Using KSQL (Docker)
=============================================================
:::

This tutorial demonstrates a simple workflow using KSQL to write
streaming queries against messages in Kafka in a Docker environment.

To get started, you must start a Kafka cluster, including {{ site.zk }}
and a Kafka broker. KSQL will then query messages from this Kafka
cluster. KSQL is installed in the {{ site.cp }} by default.

Download the Tutorial and Start KSQL
====================================

1.  Clone the Confluent KSQL repository.

    ``` {.sourceCode .bash}
    git clone https://github.com/confluentinc/ksql.git
    cd ksql
    ```

2.  Switch to the correct {{ site.cp }} release branch:

    ::: {.codewithvars}
    bash

    git checkout {{ site.release\_post\_branch }}
    :::

3.  Navigate to the KSQL repository `docs/tutorials/` directory and
    launch the tutorial in Docker. Depending on your network speed, this
    may take up to 5-10 minutes.

    ``` {.sourceCode .bash}
    cd docs/tutorials/
    docker-compose up -d
    ```

4.  From two separate terminal windows, run the data generator tool to
    simulate \"user\" and \"pageview\" data:

    ::: {.codewithvars}
    bash

    docker run \--network tutorials\_default \--rm \--name
    datagen-pageviews confluentinc/ksql-examples:{{ site.release }}
    ksql-datagen bootstrap-server=kafka:39092 quickstart=pageviews
    format=delimited topic=pageviews maxInterval=500
    :::

    ::: {.codewithvars}
    bash

    docker run \--network tutorials\_default \--rm \--name datagen-users
    confluentinc/ksql-examples:{{ site.release }} ksql-datagen
    bootstrap-server=kafka:39092 quickstart=users format=json
    topic=users maxInterval=100
    :::

5.  From the host machine, start KSQL CLI

    ::: {.codewithvars}
    bash

    docker run \--network tutorials\_default \--rm \--interactive \--tty
    confluentinc/cp-ksql-cli:{{ site.release }}
    <http://ksql-server:8088>
    :::

::: {#struct-support-docker}
:::

::: {.codewithvars}
bash

docker run \--network tutorials\_default \--rm
confluentinc/ksql-examples:{{ site.release }} ksql-datagen
quickstart=orders format=avro topic=orders bootstrap-server=kafka:39092
schemaRegistryUrl=http://schema-registry:8081
:::

::: {#ss-joins-docker}
:::

``` {.sourceCode .bash}
docker run --interactive --rm --network tutorials_default \
  confluentinc/cp-kafkacat \
  kafkacat -b kafka:39092 \
          -t new_orders \
          -K: \
          -P <<EOF
1:{"order_id":1,"total_amount":10.50,"customer_name":"Bob Smith"}
2:{"order_id":2,"total_amount":3.32,"customer_name":"Sarah Black"}
3:{"order_id":3,"total_amount":21.00,"customer_name":"Emma Turner"}
EOF
```

``` {.sourceCode .bash}
docker run --interactive --rm --network tutorials_default \
  confluentinc/cp-kafkacat \
  kafkacat -b kafka:39092 \
          -t shipments \
          -K: \
          -P <<EOF
1:{"order_id":1,"shipment_id":42,"warehouse":"Nashville"}
3:{"order_id":3,"shipment_id":43,"warehouse":"Palo Alto"}
EOF
```

::: {#tt-joins-docker}
:::

``` {.sourceCode .bash}
docker run --interactive --rm --network tutorials_default \
  confluentinc/cp-kafkacat \
  kafkacat -b kafka:39092 \
          -t warehouse_location \
          -K: \
          -P <<EOF
1:{"warehouse_id":1,"city":"Leeds","country":"UK"}
2:{"warehouse_id":2,"city":"Sheffield","country":"UK"}
3:{"warehouse_id":3,"city":"Berlin","country":"Germany"}
EOF
```

``` {.sourceCode .bash}
docker run --interactive --rm --network tutorials_default \
  confluentinc/cp-kafkacat \
  kafkacat -b kafka:39092 \
          -t warehouse_size \
          -K: \
          -P <<EOF
1:{"warehouse_id":1,"square_footage":16000}
2:{"warehouse_id":2,"square_footage":42000}
3:{"warehouse_id":3,"square_footage":94000}
EOF
```

::: {#insert-into-docker}
:::

::: {.codewithvars}
bash

docker run \--network tutorials\_default \--rm \--name
datagen-orders-local confluentinc/ksql-examples:{{ site.release }}
ksql-datagen quickstart=orders format=avro topic=orders\_local
bootstrap-server=kafka:39092
schemaRegistryUrl=http://schema-registry:8081
:::

::: {.codewithvars}
bash

docker run \--network tutorials\_default \--rm \--name
datagen-orders\_3rdparty confluentinc/ksql-examples:{{ site.release }}
ksql-datagen quickstart=orders format=avro topic=orders\_3rdparty
bootstrap-server=kafka:39092
schemaRegistryUrl=http://schema-registry:8081
:::

::: {#terminate-docker}
:::

Docker
------

To stop all Data Generator containers, run the following:

``` {.sourceCode .bash}
docker ps|grep ksql-datagen|awk '{print $1}'|xargs -Ifoo docker stop foo
```

If you are running {{ site.cp }} using Docker Compose, you can stop it
and remove the containers and their data with this command.

``` {.sourceCode .bash}
cd docs/tutorials/
docker-compose down
```

Appendix
--------

The following instructions in the Appendix are not required to run the
quick start. They are optional steps to produce extra topic data and
verify the environment.

### Produce more topic data

The Compose file automatically runs a data generator that continuously
produces data to two Kafka topics `pageviews` and `users`. No further
action is required if you want to use just the data available. You can
return to the [main KSQL quick
start](README.md#create-a-stream-and-table) to start querying the data
in these two topics.

However, if you want to produce additional data, you can use any of the
following methods.

-   Produce Kafka data with the Kafka command line
    `kafka-console-producer`. The following example generates data with
    a value in DELIMITED format.

    ``` {.sourceCode .bash}
    docker-compose exec kafka kafka-console-producer \
                              --topic t1 \
                              --broker-list kafka:39092  \
                              --property parse.key=true \
                              --property key.separator=:
    ```

    Your data input should resemble this:

        key1:v1,v2,v3
        key2:v4,v5,v6
        key3:v7,v8,v9
        key1:v10,v11,v12

-   Produce Kafka data with the Kafka command line
    `kafka-console-producer`. The following example generates data with
    a value in JSON format.

    ``` {.sourceCode .bash}
    docker-compose exec kafka kafka-console-producer \
                              --topic t2 \
                              --broker-list kafka:39092  \
                              --property parse.key=true \
                              --property key.separator=:
    ```

    Your data input should resemble this:

        key1:{"id":"key1","col1":"v1","col2":"v2","col3":"v3"}
        key2:{"id":"key2","col1":"v4","col2":"v5","col3":"v6"}
        key3:{"id":"key3","col1":"v7","col2":"v8","col3":"v9"}
        key1:{"id":"key1","col1":"v10","col2":"v11","col3":"v12"}

You can also use kafkacat from Docker, as demonstrated in the earlier
examples.

### Verify your environment

The next three steps are optional verification steps to ensure your
environment is properly setup.

1.  Verify that six Docker containers were created.

    ``` {.sourceCode .bash}
    docker-compose ps
    ```

    Your output should resemble this. Take note of the `Up` state.

        Name                        Command            State                 Ports

    > \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--tutorials\_kafka\_1
    > /etc/confluent/docker/run Up 0.0.0.0:39092-\>39092/tcp, 9092/tcp
    > tutorials\_ksql-server\_1 /etc/confluent/docker/run Up 8088/tcp
    > tutorials\_schema-registry\_1 /etc/confluent/docker/run Up
    > 8081/tcp tutorials\_zookeeper\_1 /etc/confluent/docker/run Up
    > 2181/tcp, 2888/tcp, 3888/tcp

2.  Earlier steps in this quickstart started two data generators that
    pre-populate two Kafka topics `pageviews` and `users` with mock
    data. Verify that the data generator created two Kafka topics,
    including `pageviews` and `users`.

    ``` {.sourceCode .bash}
    docker-compose exec kafka kafka-topics --zookeeper zookeeper:32181 --list
    ```

    Your output should resemble this.

        _confluent-metrics
        _schemas
        pageviews
        users

3.  Use the `kafka-console-consumer` to view a few messages from each
    topic. The topic `pageviews` has a key that is a mock time stamp and
    a value that is in `DELIMITED` format. The topic `users` has a key
    that is the user ID and a value that is in `Json` format.

    ``` {.sourceCode .bash}
    docker-compose exec kafka kafka-console-consumer \
                              --topic pageviews \
                              --bootstrap-server kafka:39092 \
                              --from-beginning \
                              --max-messages 3 \
                              --property print.key=true
    ```

    Your output should resemble this.

        1491040409254    1491040409254,User_5,Page_70
        1488611895904    1488611895904,User_8,Page_76
        1504052725192    1504052725192,User_8,Page_92

    ``` {.sourceCode .bash}
    docker-compose exec kafka kafka-console-consumer \
                              --topic users \
                              --bootstrap-server kafka:39092 \
                              --from-beginning \
                              --max-messages 3 \
                              --property print.key=true
    ```

    Your output should resemble this:

        User_2   {"registertime":1509789307038,"gender":"FEMALE","regionid":"Region_1","userid":"User_2"}
        User_6   {"registertime":1498248577697,"gender":"OTHER","regionid":"Region_8","userid":"User_6"}
        User_8   {"registertime":1494834474504,"gender":"MALE","regionid":"Region_5","userid":"User_8"}

Next steps
==========

Try the end-to-end
[Clickstream Analysis demo \<ksql\_clickstream-docker\>]{role="ref"},
which shows how to build an application that performs real-time user
analytics.
