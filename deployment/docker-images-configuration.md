# Docker images configuration

## Vyne Query Server

```text
   vyne:
      image: vyneco/vyne:${VYNE_VERSION}
      ports:
         - 5701-5721 # Hazelcast cluster ports
         - 9022:9022 # Vyne query api + Vyne UI
      environment:
         PROFILE: 
         OPTIONS: --eureka.uri=http://eureka:8761
         JVM_OPTS: -Xmx1024m
```

### Environment options

<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Vyne Version</th>
      <th style="text-align:left">Default</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">PROFILE</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Comma separated list of spring-boot profiles.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>embedded-discovery</code><b> </b>- (Optional) Starts embedded Eureka
        server.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>logstash</code> - (Optional) Exporting logs to ElasticSearch via
        Logstash</td>
    </tr>
    <tr>
      <td style="text-align:left">OPTIONS</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Space separated list of spring-boot application options.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--eureka.uri=http://eureka:8761</code> - not required for embedded-discovery
        profile.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--logstash.hostname=logstash:5044</code> - Logstash host.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--logging.level.io.vyne=INFO</code> 
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Since 0.4.2</td>
      <td style="text-align:left">LOCAL</td>
      <td style="text-align:left">
        <p><code>--vyne.schema.publicationMode=[LOCAL|DISTRIBUTED] </code>
        </p>
        <p>LOCAL - in-memory schema store exposed via REST Api.</p>
        <p>DISTRIBUTED - Hazelcast based distributed schema store.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Spring config server settings (by default Disabled)</p>
        <p><code>--spring.cloud.config.enabled=true <br />--spring.cloud.config.uri=http://config-server:8888</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">To load cont</td>
    </tr>
    <tr>
      <td style="text-align:left">JVM_OPTS</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Space separated list of JVM options</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>-Xmx1024m -XX:+PrintGCDetails -Xloggc:/tmp/gc.log</code>
      </td>
    </tr>
  </tbody>
</table>

## Schema Server

```text
   file-schema-server:
      image: vyneco/file-schema-server:${VYNE_VERSION}
      depends_on:
         - eureka
      ports:
         - 5701-5721 # Hazelcast cluster ports
      volumes:
         - ./schemas/:/var/lib/vyne/schemas
      environment:
         PROFILE: 
         OPTIONS: --eureka.uri=http://eureka:8761 --taxi.schema-local-storage=/var/lib/vyne/schemas
         JVM_OPTS: -Xmx1024m
         
```

### Environment options

<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Vyne version</th>
      <th style="text-align:left">Default</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">PROFILE</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Comma separated list of spring-boot profiles.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>logstash</code> - (Optional) Exporting logs to ElasticSearch via
        Logstash</td>
    </tr>
    <tr>
      <td style="text-align:left">OPTIONS</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Space separated list of spring-boot application options.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--eureka.uri=http://eureka:8761</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--logstash.hostname=logstash:5044</code> - Logstash host.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--taxi.schema-local-storage=/var/lib/vyne/schemas</code> - location
        of Taxi schemas. Folder will be searched recursively for presence of *.taxi
        files.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--logging.level.io.vyne=INFO</code> 
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Since 0.4.2</td>
      <td style="text-align:left">REMOTE</td>
      <td style="text-align:left">
        <p><code>--vyne.schema.publicationMode=[REMOTE|DISTRIBUTED] </code>
        </p>
        <p>REMOTE - Pushess/pulls schema from central Vyne schema store.</p>
        <p>DISTRIBUTED - Hazelcast based distributed schema store.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Since 0.4.2</td>
      <td style="text-align:left">5s</td>
      <td style="text-align:left">
        <p>Enabled when publicationMode=REMOTE</p>
        <p><code>--vyne.schema.pollInterval=5s</code> - schema store polling interval
          in seconds.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Since 0.4.2</td>
      <td style="text-align:left">3s</td>
      <td style="text-align:left">
        <p>Enabled when publicationMode=REMOTE</p>
        <p><code>--vyne.schema.publishRetryInterval=5s</code> - in case when schema
          store is down, this setting controls publication retry interval.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Spring config server settings (by default Disabled)</p>
        <p><code>--spring.cloud.config.enabled=true <br />--spring.cloud.config.uri=http://config-server:8888</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">JVM_OPTS</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Space separated list of JVM options</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>-Xmx1024m -XX:+PrintGCDetails -Xloggc:/tmp/gc.log</code>
      </td>
    </tr>
  </tbody>
</table>

## Cask

```text
   cask:
      image: vyneco/cask:${VYNE_VERSION}
      depends_on:
         - eureka
      ports:
         - 5701-5721 # Hazelcast cluster ports
         - 8800:8800 # Cask ingestion/query API
      environment:
         PROFILE: local
         OPTIONS: --eureka.uri=http://eureka:8761
         JAVA_OPTS: -Xmx1024m
```

### Environment options

<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Vyne version</th>
      <th style="text-align:left">Default</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">PROFILE</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Comma separated list of spring-boot profiles.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>local </code>- (Required) Cask running in local mode.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>logstash</code> - (Optional) Exporting logs to ElasticSearch via
        Logstash</td>
    </tr>
    <tr>
      <td style="text-align:left">OPTIONS</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Space separated list of spring-boot application options.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--eureka.uri=http://eureka:8761</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--logstash.hostname=logstash:5044</code> - Logstash host.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--logging.level.io.vyne=INFO</code> 
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Spring config server settings (by default Disabled)</p>
        <p><code>--spring.cloud.config.enabled=true <br />--spring.cloud.config.uri=http://config-server:8888</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Since 0.4.2</td>
      <td style="text-align:left">REMOTE</td>
      <td style="text-align:left">
        <p><code>--vyne.schema.publicationMode=[REMOTE|DISTRIBUTED] </code>
        </p>
        <p>REMOTE - Pushess/pulls schema from Vyne schema store.</p>
        <p>DISTRIBUTED - Hazelcast based distributed schema store.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">JVM_OPTS</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Space separated list of JVM options</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>-Xmx1024m -XX:+PrintGCDetails -Xloggc:/tmp/gc.log</code>
      </td>
    </tr>
  </tbody>
</table>

## Pipeline Orchestrator

```text
   pipelines-orchestrator:
      image: vyneco/pipelines-orchestrator:${VYNE_VERSION}
      depends_on:
         - eureka
      ports:
         - 5701-5721
         - 9600:9600
      environment:
         PROFILE: local
         OPTIONS: --eureka.uri=http://eureka:8761
         JVM_OPTS: -Xmx1024m
```

### Environment options

<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">PROFILE</td>
      <td style="text-align:left">Comma separated list of spring-boot profiles.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>local </code>- (Required) Orchestrator running in local mode.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>logstash</code> - (Optional) Exporting logs to ElasticSearch via
        Logstash</td>
    </tr>
    <tr>
      <td style="text-align:left">OPTIONS</td>
      <td style="text-align:left">Space separated list of spring-boot application options.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--eureka.uri=http://eureka:8761</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--logstash.hostname=logstash:5044</code> - Logstash host.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>--logging.level.io.vyne=INFO</code> 
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Spring config server settings (by default Disabled)</p>
        <p><code>--spring.cloud.config.enabled=true <br />--spring.cloud.config.uri=http://config-server:8888</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">JVM_OPTS</td>
      <td style="text-align:left">Space separated list of JVM options</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"><code>-Xmx1024m -XX:+PrintGCDetails -Xloggc:/tmp/gc.log </code>
      </td>
    </tr>
  </tbody>
</table>

## Eureka Server \(optional\)

Don't have your own Eureka Server running? Don't worry, we provide sample docker image that will get you going quickly.

```text
   eureka:
      image: vyneco/eureka:${VYNE_VERSION}
      ports:
         - 8761:8761
```

## Spring Config Server \(optional\)

```text
   eureka:
      image: vyneco/config-server:${VYNE_VERSION}
      ports:
         - 8761:8761
```







