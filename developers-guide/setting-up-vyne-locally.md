---
description: >-
  Getting Vyne running locally is simple, whether you're doing your first API
  integration, or building streaming data solutions
---

# Setting up Vyne locally

There are a few ways to get Vyne working on your local machine and which you choose depends on what you're interested in. 

This guide walks you through common use cases for running Vyne, and getting things going locally.  For all our examples, you'll need Docker installed - so i fyou haven't done that already, head over to the [Docker Docs](https://docs.docker.com/get-docker/) and get docker set up.

From here, getting started depends on what you'd like to achieve.  We have guides to help you get started locally for the following:

* [Exploring Vyne's automated API integrations](setting-up-vyne-locally.md#automating-api-integration)
* [Building a standalone taxonomy](setting-up-vyne-locally.md#building-a-standalone-taxonomy)
* [Building streaming data solutions](setting-up-vyne-locally.md#building-streaming-data-solutions)
* [Ingesting and querying flat-based data \(CSV, Json, Xml, etc\)](setting-up-vyne-locally.md#ingesting-and-querying-file-based-data)

## Automating API integration

Getting Vyne deployed locally to automate API integration is simple:

```bash
docker run -p 9022:9022 --env PROFILE=embedded-discovery,inmemory-query-history,eureka-schema vyneco/vyne
```

This will launch an instance of Vyne running on your local machine - just head over to [http://localhost:9022](http://localhost:9022) to see an empty instance of Vyne waiting for you.

![](../.gitbook/assets/documentation-images-4-.png)

This gives you a local developer envirnoment for Vyne, which is everything you need to start integrating APIs.  

The next step is to get Microservices to start and publish their schemas to Vyne.  We walk through this in detail in our Hello World tutorial - now's a good time to head over there.

{% page-ref page="employees-on-payroll-demo.md" %}

## Building a standalone Taxonomy

A common usecase for Vyne is to build and share an enterprise taxonomy, as part of a data governance strategy.  

Taxi provides an excellent way to build a shared taxonomy, and Vyne is a fantastic way to view and explore it.

![](../.gitbook/assets/documentation-images-6-.png)

To get started, you can use our Docker Compose file for this configuration, which is available [here](https://gitlab.com/vyne/vyne-taxonomy-environment/-/blob/master/docker-compose.yml).

Download the docker compose file locally, then simply run:

```bash
docker-compose up -d
```

Wait a bit, then head to http://localhost:9022.

We have a dedicated guide to building and publishing a taxonomy, which you can follow along with here:

{% page-ref page="../running-a-local-taxonomy-editor-environment/" %}

## Ingesting and querying file based data

Vyne doesn't just work with API's - we make any data discoverable and queryable.

For flat-file data that isn't served by an API \(such as CSV data, or just JSON / XML files\), we provide Casks -  a way of storing file data, applying a taxonomy, and then querying / enriching with Vyne.

![](../.gitbook/assets/documentation-images-7-.png)

To get started, you can use our Docker Compose file for this configuration, which is available [here](https://gitlab.com/vyne/vyne-taxonomy-environment/-/blob/master/docker-compose.yml).

Download the docker compose file locally, then simply run:

```bash
docker-compose up -d
```

Wait a bit, then head to http://localhost:9022.

We have a dedicated guide to storing and querying flat-file data, which you can follow along with here:

{% page-ref page="../casks/cask-server.md" %}

## Building streaming data solutions

With Vyne you can transform and enrich streaming data using our pipelines, either publishing data to another topic, or storing it in a cask to query later.

![](../.gitbook/assets/documentation-images-9-.png)

\[TODO - Provide docker compose file\]

## Sharing API schemas on your local machine

Vyne needs services to provide API schemas \(Taxi\) so that it can understand the data they provide, and the APIs that they publish.

There are different ways to configure services and Vyne to share schema data - Distributed or Centralised. - which we discuss in detail in [Publishing & sharing schemas](../overview/publishing-and-sharing-schemas.md).

{% page-ref page="../overview/publishing-and-sharing-schemas.md" %}

### Using HTTP polling to share API schemas

The examples so far have leveraged a centralised, polling mechanism, with the Vyne stack running inside docker containers, and developer services \(the one's you build\) running locally.

![Using HTTP Polling to distribute schema changes locally](../.gitbook/assets/documentation-images-10-.png)

This is the easiest way to get started, and it's how Vyne ships out-of-the-box.  However, the drawback of this approach is that as services start and stop, there can be a lag for everything to sync up, which can be frustrating.

### Using TCP multicasting to share API schemas

An alternative approach is to use TCP multicasting to share services. This makes updates instant between services as they start and stop, but requires a little extra config.

![](../.gitbook/assets/documentation-images-11-.png)

#### Getting Multicast running

First steps will be to update our local host IP. This step is required because we run Vyne in Docker. To update your IP:

* edit the .env file and set DOCKER\_HOST\_IP_=&lt;_your\_local\_host\_ip&gt;
* edit hazelcast.yml and replace member-list: _&lt;_your\_local\_host\_ip&gt; with your local IP

Once this is done navigate into your project directory and run:

`docker-compose up`

This will start the Vyne stack.

To view our service running in Vyne, navigate to [http://localhost:9022/schema-explorer](http://localhost:9022/schema-explorer). Here we can see if our service has successfully published schema types and operations to Vyne.

To stop the stack, run:

`docker-compose down` / `ctrl+c`

