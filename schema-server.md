# Schema Server

## Overview

The Schema Server is responsible for reading Taxi files and distributing them to all the connected vyne components.

The server may be configured to read files from a local file system, or from a remote git repository.

## Local files mode

In this mode you specify folder where you keep all the schemas. Any modifications in that folder will be automatically detected and propagated between Vyne components. Example docker-compose environment can be found [here](https://gitlab.com/vyne/vyne-taxonomy-environment).

Sample docker-compose file

```text
version: "3.4"
services:
   vyne:
      image: vyneco/vyne:latest-snapshot
      ports:
         - 5701-5705
         - 9022:9022
      environment:
         PROFILE: embedded-discovery,inmemory-query-history

   file-schema-server:
      image: vyneco/file-schema-server:latest-snapshot
      ports:
         - 5701-5705
      volumes:
         - /path/to/local/vyne/schemas:/var/lib/vyne/schemas
      environment:
         OPTIONS: \
            --taxi.schema-local-storage=/var/lib/vyne/schemas \
            --eureka.uri=http://vyne:9022

   cask:
      image: vyneco/cask:latest-snapshot
      depends_on:
         - vyne
      ports:
         - 5701-5705
         - 15432:5432
         - 8800:8800
      environment:
         PROFILE: local
         OPTIONS: --eureka.uri=http://vyne:9022
```

Save the file to your desired location e.g. **/dev/vyne/demo/**docker-compose.yml.

To start schema server environment: `docker-compose up`

To stop: `ctrl+c or docker-compose down`

To add test schema, create a new file e.g. **/dev/vyne/demo/schemas/**order.taxi with this content:

```text
type alias OrderId as String
type alias Symbol as String
type alias Price as Decimal
type alias Side as String
type alias OrderDate as Instant

type Order {
    id: OrderId by column(1)
    symbol : Symbol by column(2)
    price : Price by column(3)
    side: Side by column(4)
    @Between
    date: OrderDate by column(5)
}
```

Save it and navigate to [http://localhost:9022/schema-explorer](http://localhost:9022/schema-explorer), you should see your newly defined schema there.

### Configuring change detection mode

There are two different types of change detection available for detecting changes to source files made in the local file system.

The mode is defined by setting `--taxi.change-detection-method` as a startup parameter.

#### Watch mode \(Default\)

```text
--taxi.change-detection-method=watch
```

When watch mode is enable, file system events trigger changes.  This provides the fastest feedback, and is a sensible default.

{% hint style="warning" %}
Watch mode cannot be used when running the schema server within a docker container with a host mounted volume for the taxi sources.  This is due to limitations in Java's FileWatcher API.  In this scenario, Poll mode should be enabled
{% endhint %}

#### Poll mode

```text
--taxi.change-detection-method=poll --taxi.schema-poll-interval-seconds=2
```

Poll mode will periodically poll the directory for changes, using a configurable poll interval.

## Remote git repository

The file schema server may be configured to periodically pull a predefined git branch to the local folder.

Sample docker-compose configuration:

```text
version: "3.4"
services:
   vyne:
      image: vyneco/vyne:latest-snapshot
      ports:
         - 5701-5705
         - 9022:9022
      environment:
         PROFILE: embedded-discovery,inmemory-query-history

   file-schema-server:
      image: vyneco/file-schema-server:latest-snapshot
      ports:
         - 5701-5705
      volumes:
         - /path/to/local/vyne/schemas:/var/lib/vyne/schemas
      environment:
         OPTIONS: \
            --eureka.uri=http://vyne:9022 \
            --taxi.schema-local-storage=/var/lib/vyne/schemas \
            --taxi.gitCloningJobEnabled=true \
            --taxi.gitCloningJobPeriodMs=5000 \ 
            --taxi.gitSchemaRepos[0].name=taxonomy \
            --taxi.gitSchemaRepos[0].uri=git@gitlab.com:notional/taxonomy.git \
            --taxi.gitSchemaRepos[0].branch=master \
            --taxi.gitSchemaRepos[0].sshPrivateKeyPath=/home/ubuntu/.ssh/id_rsa

   cask:
      image: vyneco/cask:latest-snapshot
      depends_on:
         - vyne
      ports:
         - 5701-5705
         - 15432:5432
         - 8800:8800
      environment:
         PROFILE: local
         OPTIONS: --eureka.uri=http://vyne:9022
```

## Schema server parameters

<table>
  <thead>
    <tr>
      <th style="text-align:left">Option name</th>
      <th style="text-align:left">Default</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">taxi.schema-local-storage</td>
      <td style="text-align:left">--</td>
      <td style="text-align:left">Location where all the Taxi schema files are stored</td>
    </tr>
    <tr>
      <td style="text-align:left">taxi.change-detection-method</td>
      <td style="text-align:left">watch</td>
      <td style="text-align:left">Possible options: <code>watch | poll</code>. See<a href="schema-server.md#configuring-change-detection-mode"> configuring change detection</a> for
        more detail.</td>
    </tr>
    <tr>
      <td style="text-align:left">taxi.gitCloningJobEnabled</td>
      <td style="text-align:left">false</td>
      <td style="text-align:left">Set to true to enable synchronization of schema changes from Git.</td>
    </tr>
    <tr>
      <td style="text-align:left">taxi.gitCloningJobPeriodMS</td>
      <td style="text-align:left">
        <p>300000</p>
        <p>(5 minutes)</p>
      </td>
      <td style="text-align:left">Schema refresh interval in milliseconds.</td>
    </tr>
    <tr>
      <td style="text-align:left">taxi.gitSchemaRepos[n].*</td>
      <td style="text-align:left">--</td>
      <td style="text-align:left">
        <p>An array of repositories to pull data from.</p>
        <p>Possible options here</p>
        <ul>
          <li>taxi.gitSchemaRepos[0].name - unique name of respository,</li>
          <li>taxi.gitSchemaRepos[0].uri - git uri,</li>
          <li>taxi.gitSchemaRepos[0].branch - branch name, e.g. master,</li>
          <li>taxi.gitSchemaRepos[0].sshPrivateKeyPath - path to ssh key with read access
            to the repository.</li>
        </ul>
        <p>There can be multiple repositories added here.</p>
        <p>Constraints: each repository name, should be unique!</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
  </tbody>
</table>



