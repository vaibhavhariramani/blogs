---
title: "The ELK Stack: Elasticsearch, Logstash, and Kibana Explained"
excerpt: "How three open-source tools combine into one pipeline that turns scattered logs into searchable, visualized data."
header:
  image: /assets/images/unsplash-gallery-image-4.jpg
  teaser: images/cloud_computing/cc_teaser2.png
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

Once you're running more than one or two services — especially containerized ones managed by [Kubernetes](/CloudComputing/kubernetes/) — logs stop being something you can `tail -f` on a single server. Pods get rescheduled, containers get replaced, and the logs they wrote disappear with them. The ELK Stack exists to solve exactly this problem: collect logs from everywhere, store them centrally, and make them searchable.

## What ELK Actually Stands For

ELK is an acronym for three separate open-source projects, all originally built or stewarded by Elastic, that are commonly deployed together:

- **E — Elasticsearch**: a distributed search and analytics engine, built on top of Apache Lucene. This is where the data actually lives, indexed for fast full-text search and aggregation.
- **L — Logstash**: a server-side data processing pipeline that ingests data from many sources simultaneously, transforms/enriches it, and ships it to a destination (usually Elasticsearch).
- **K — Kibana**: a web UI that sits on top of Elasticsearch, letting humans search, filter, and visualize the data with dashboards and charts.

You'll also hear the term **"Elastic Stack"** — that's the modern rebrand, which adds **Beats** (lightweight shippers like Filebeat and Metricbeat that forward data with a much smaller footprint than Logstash) into the mix.

## How Logs Actually Flow Through the Pipeline

A typical ELK pipeline looks like this:

```
[App / Server / Container logs]
        |
        v
   Filebeat (lightweight shipper, runs as an agent)
        |
        v
     Logstash (parse, filter, enrich, transform)
        |
        v
   Elasticsearch (index and store)
        |
        v
      Kibana (search, visualize, alert)
```

1. **Collection** — Filebeat (or another Beat) tails log files on each host or container and forwards new lines as they're written, with minimal CPU/memory overhead.
2. **Processing** — Logstash receives that raw data and applies filters. A classic example is the `grok` filter, which uses pattern matching to turn an unstructured line like:

   ```
   2026-07-20T10:15:32 ERROR OrderService - Failed to charge card for order #48213
   ```

   into structured fields: `timestamp`, `log_level`, `service`, `message`, `order_id`. Structured data is what makes the next steps possible — you can't filter or chart on a blob of raw text nearly as well as you can on named fields.
3. **Storage & Indexing** — Logstash sends the structured event to Elasticsearch, which indexes it so it's searchable within roughly a second.
4. **Search & Visualization** — Kibana queries Elasticsearch and renders it: a search bar for ad-hoc queries, dashboards for trends, and alerting rules for anomalies.

## A Simple Example Use Case: Centralized Log Search

Imagine you run 15 microservices across a Kubernetes cluster. A customer reports "checkout failed around 10:15 AM" and you need to find out why — but the failure could be in any of five different services.

Without centralized logging, you'd be running `kubectl logs` against pod after pod, hoping the right Pod hasn't already been rescheduled and lost its log history.

With ELK in place, you'd instead go to Kibana and run something like:

```
log_level:ERROR AND @timestamp:[2026-07-20T10:10:00 TO 2026-07-20T10:20:00]
```

Because every service ships its logs through Filebeat → Logstash → Elasticsearch, this one query searches across *all* services at once, and returns matching entries with full context — timestamps, service names, request IDs, stack traces — regardless of which Pod originally produced them or whether that Pod still exists. You can pivot straight into a dashboard showing error-rate-by-service over that window, spot that `PaymentService` had a spike, and drill into just its logs.

## Where It Fits in a Real Stack

ELK typically sits alongside your metrics stack (like Prometheus/Grafana) as the "logs" pillar of observability. In a containerized environment, it plugs in like this: applications running as [Kubernetes](/CloudComputing/kubernetes/) Pods write logs to stdout, a Filebeat DaemonSet collects those logs from every node, Logstash enriches them, and Elasticsearch/Kibana make them queryable for debugging and alerting.

The main trade-off with ELK is operational weight: Elasticsearch clusters need real capacity planning (heap sizing, shard counts, index lifecycle policies), and Logstash pipelines can become a maintenance burden of their own at scale. That operational cost is exactly why some teams choose [Graylog](/CloudComputing/graylog/) instead — a lighter-weight alternative built on the same underlying idea of centralizing and searching logs, but with a different set of trade-offs, covered in the next post.

# Made with ❤️ by Vaibhav Hariramani
