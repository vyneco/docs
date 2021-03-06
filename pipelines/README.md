---
description: >-
  Pipelines allow you to fetch data from external datasources, optionally
  transform it using Vyne, and forward it to an output datasource
---

# Pipelines

Vyne pipelines provide streaming transformations and enrichment of data. It's also a convenient way to ingest data from across your organisation into a cask for querying later

## Pipeline definition

Pipeline definition describes the source of the data, the type and the target where the data should be pushed. 

### Example definition

Definition below would read crypto Orders from **kafka:9092 crypto-order-input-topic** and instruct Cask to map **json** message to demo.crypto.Order Type and store it.

```text
{
  "name": "crypto-orders-pipeline",
  "input": {
      "type": "demo.crypto.Order",
      "transport": {
          "topic": "crypto-order-input-topic",
          "type": "kafka-crypto",
          "direction": "INPUT",
          "targetType": "demo.crypto.Order",
          "props": {
              "group.id": "vyne-crypto-order-pipeline-group",
              "bootstrap.servers": "kafka:9092,",
              "heartbeat.interval.ms": "3000",
              "session.timeout.ms": "10000",
              "auto.offset.reset": "earliest"
          }
      }
  },
  "output": {
      "type": "demo.crypto.Order",
      "transport": {
          "targetType": "demo.crypto.Order",
          "type": "cask",
          "direction": "OUTPUT",
              "props": {
              "content-type": "json"
          }
      }
  }
}
```

## Pipeline deployment

Pipeline definitions are deployed via [Pipeline Orchestrator API](pipeline-orchestrator.md#pipeline-orchestrator-api).

