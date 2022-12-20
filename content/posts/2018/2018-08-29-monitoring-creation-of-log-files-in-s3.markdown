---
author: dschaaff
categories:
- datadog
- monitoring
- techology
comments: true
date: "2018-08-29T03:45:14Z"
link: http://danielschaaff.com/monitoring-creation-of-log-files-in-s3/
slug: monitoring-creation-of-log-files-in-s3
tags:
- aws
- boto3
- devops
- python
- s3
title: Monitoring Creation of Log Files in s3
wordpress_id: 882
---

I manage several apps that write various pieces of data to the local file system and rely on Fluentd to ship them to s3. There is solid monitoring around the fluentd aggregator process, but I wanted better visibility and alerting when things aren’t written to s3 as expected.

The solution I came up with was a [custom Datadog check](https://github.com/dschaaff/datadog-checks). The files I am monitoring are written into a bucket named something like `example-logs/data/event-files/year/month/day`. A new path is set up in the s3 bucket for the current day’s date, e.g. `logs/data/example-log/2018/08/15` each day. The Datadog check sends the count of objects in the current date’s directory as a gauge. You can then monitor that objects are created each day as expected and at a normal rate.

![]({{ site.url }}/assets/img/screen-shot-2018-08-28-at-8-41-07-pm.png)

Here is an example config

```yaml
init_config:

instances:
# this will monitor s3://example-logs/data/production/event-log/<year>/<month>/<day>
  - bucket: example-logs
    prefix: data/production/event-log
    tags:
      - 'env:production'
      - 'log:event-log'
  - bucket: example-logs
    prefix: data/staging/event-log
    tags:
      - 'env:staging'
      - 'log:event-log'
```

The check will add the current date path to the prefix automatically.

## How to Set it Up for Yourself

- Install boto3 via the Datadog embedded pip

```bash
/opt/datadog-agent/embedded/bin/pip install boto3
```

- add s3_object_count.py to /etc/datadog-agent/checks.d
- add your config file to /etc/datadog-agent/conf.d/s3_object_count.d

The code for the check is pretty simple.

<script src="https://gist.github.com/dschaaff/c8328f1c6846eaa18980ed988b062d71.js"></script>

See [https://github.com/dschaaff/datadog-checks](https://github.com/dschaaff/datadog-checks) for the full source.
