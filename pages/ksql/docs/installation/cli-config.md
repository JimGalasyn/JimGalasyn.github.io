---
layout: page
title: Configure KSQL CLI
tagline: Connect the KSQL CLI to KSQL Server
description: Learn how to connect the KSQL CLI to a KSQL Server cluster
keywords: ksql, cli, console, terminal
---

Configure KSQL CLI {#install_cli-config}
==================

You can connect the KSQL CLI to one KSQL server per cluster.

::: {.important}
::: {.admonition-title}
Important
:::

>There is no automatic failover of your CLI session to another KSQL
server if the original server that the CLI is connected to becomes
unavailable. Any persistent queries you executed will continue to run in
the KSQL cluster.
:::

To connect the KSQL CLI to a cluster, run this command with your KSQL
server URL specified (default is `http://localhost:8088`):

```bash
<path-to-confluent>/bin/ksql <ksql-server-URL>
```

Configuring Per-session Properties
----------------------------------

You can set the properties by using the KSQL CLI startup script argument
`/bin/ksql <server> --config-file <path/to/file>` or by using the SET
statement from within the KSQL CLI session. For more information, see
[install_ksql-cli]{role="ref"}.

Here are some common KSQL CLI properties that you can customize:

-   [ksql.streams.auto.offset.reset](#ksql-auto-offset-reset){role="ref"}
-   [ksql.streams.cache.max.bytes.buffering ](#ksql-cache-max-bytes-buffering){role="ref"}
-   [ksql.streams.num.stream.threads](#ksql-streams-num-streams-threads){role="ref"}
