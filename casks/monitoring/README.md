# Monitoring

Cask exposes metrics relating to the status and performance of loaded Casks to a range of supported monitoring systems.

## Configure a monitoring system

Run cask providing an additional configuration file as follows.

```shell
java -jar cask.jar --spring.config.additional-location=file:/cask.yml
```

Ensure the custom configuration file contains a management configuration specific to the chosen monitoring system.  Configurations for 
supported monitoring systems are shown below

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

###dynatrace

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

###elastic

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

###graphite

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

###influx

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

###jmx

Config
```yaml
management:
   metrics:
      export:
        jmx:
           enabled: true
           domain: vyne-metrics
```

###prometheus

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