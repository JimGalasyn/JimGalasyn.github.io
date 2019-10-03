---
layout: page
title: KSQL Quick Start
tagline: Get Started with KSQL
description:  Get started fast with KSQL quick starts and tutorials
---

KSQL Quick Start {#ksql_quickstart}
================

{{ site.cp }} Quick Start
-------------------------

The [Confluent Platform Quick
Start](https://docs.confluent.io/current/quickstart/index.html) is
the easiest way to get you up and running with {{ site.cp }} and
KSQL. It will demonstrate a simple workflow with topic management,
monitoring, and using KSQL to write streaming queries against data
in {{ site.ak-tm }}.

KSQL Tutorials and Examples
---------------------------

The [KSQL tutorials and examples](ksql_tutorials){role="ref"}
page provides introductory and advanced KSQL usage scenarios in both
local and Docker-based versions.

- [Writing Streaming Queries Against Kafka Using KSQL](<ksql_tutorials>){role="ref"}.
This tutorial demonstrates a simple workflow using KSQL to write
streaming queries against messages in Kafka.
- [Clickstream Data Analysis Pipeline Using KSQL](ksql_clickstream-docker){role="ref"}.
Learn how to use KSQL, ElasticSearch, and Grafana to analyze
data feeds and build a real-time dashboard for reporting and
alerting.

You can configure Java streams applications to deserialize and
ingest data in multiple ways, including {{ site.ak }} console
producers, JDBC source connectors, and Java client producers. For
full code examples,
[connect-streams-pipeline](https://github.com/confluentinc/examples/tree/master/connect-streams-pipeline).
