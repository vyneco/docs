# Monitoring

Cask exposes metrics relating to the status and performance of loaded Casks to a range of supported monitoring systems.

## Configure a monitoring system

Run cask providing an additional configuration file as follows.

```shell
java -jar cask.jar --spring.config.additional-location=file:/cask.yml
```

Ensure the custom configuration file contains a management configuration specific to the chosen monitoring system.  Configurations for
supported monitoring systems are shown below:

###datadog

Config
```yaml
management:
   metrics:
      export:
        datadog:
           enabled: true
           api-key:
           uri: https://api.datadoghq.com
```

###Dynatrace

Config
```yaml
management:
   metrics:
      export:
        dynatrace:
           enabled: true
           technology-type: java
           api-token:
           group: vyne
```

###ElasticSearch

Config
```yaml
management:
   metrics:
      export:
        elastic:
           enabled: true
           host: http://127.0.0.1:9200
           user-name: vyne
           password: vyne
           index: vyne-metrics
```

###Graphite

Config
```yaml
management:
   metrics:
      export:
        graphite:
           enabled: true
           port: 2004
           host: localhost
```

###InfluxDB

Config
```yaml
management:
   metrics:
      export:
        influx:
           enabled: true
           db: vyne
           uri: http://127.0.0.1:8086
           user-name: vyne
           password: vyne
```

###JMX

Config
```yaml
management:
   metrics:
      export:
        jmx:
           enabled: true
           domain: vyne-metrics
```

###Prometheus

Vyne Query service

http://localhost:9022/api/actuator/prometheus

Cask

http://localhost:8800/api/actuator/prometheus

Config
```yaml
management:
   metrics:
      export:
        prometheus:
           enabled: true

   endpoints:
      web:
         exposure:
            include: prometheus

   endpoint:
      prometheus:
         enabled: true
```

# Metrics

The following metrics are exported to the configured monitoring system in addition to JVM and HTTP performance metrics

##Counters

Counters

- cask.import.success
- cask.import.rejected

##Gauges

Gauge

- schema.compiled.count
- cask.count
- cask.row.counts (multi gauge tagged by cask name)