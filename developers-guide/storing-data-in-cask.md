---
description: Casks provide lightweight data-as-a-service for static data.  Let's explore.
---

# Storing data in Cask

{% hint style="info" %}
Time required: 15 minutes
{% endhint %}

In this demo we are going to take a look at making simple CSV data queryable.

We'll write a couple of CSV files, store them in a service called a "Cask", and then write some queries to get our data back, linking our CSV data.

For this example, we're going to use two csv reports from a pretend shop - one on Customer information, and another on Purchase history.

## What is a Cask?

Casks are a lightweight way of storing any data \(csv, json, xml\), and exposing it via a REST service.  

When data is stored in a cask, it's stored with a type, making it discoverable using Vyne's data discovery mechanisms.  

This makes casks a great way to get started rapidly with storing and querying semantic data

## Starting Vyne

For this guide, we'll be running a few components in Vyne - Vyne itself, our Cask server, and a local schema publisher.  

| Component | Usage |
| :--- | :--- |
| Vyne | The server that we'll be running queries with to explore our stored data |
| Cask | Stores simple data \(CSV, Json, Xml, etc\), exposes it via simple REST endpoints, and makes it queryable using Vyne's semantic query language |
| Schema Service | A simple service that publishes Taxi schemas up to Vyne.  In our demo, we'll be editing schemas locally on our machine, but in production a schema server can be used to expose schemas directly from Git.\` |

![](../.gitbook/assets/documentation-images-15-.png)

To get started locally, [this guide](setting-up-vyne-locally.md#ingesting-and-querying-file-based-data) has all the steps you'll need.

## Writing our csv

We are going to have two files, one containing customer data and the other containing purchase records. The files we are producing are downloaded below:

{% file src="../.gitbook/assets/customers.csv" caption="customers.csv" %}

{% file src="../.gitbook/assets/purchases.csv" caption="purchases.csv" %}

Inside the csv files, the data will look like this:

{% tabs %}
{% tab title="customers.csv" %}
```text
customerId,firstName,lastName
01,steve,darcy
02,dave,jasper
03,mark,spencer
```
{% endtab %}

{% tab title="purchases.csv" %}
```text
customerId,itemName,itemPrice
01,macbook,2500
02,windows,1200
03,linux,1900
```
{% endtab %}
{% endtabs %}

## Writing our Taxi

Taxi allows for modelling any type of data - not just data returned from services.  So, it's a great way of describing the contract of our CSV files.  Below, we've created two models - `CustomerRecord` and `PurchaseRecord`. 

You'll note that we're using Semantic types \(`CustomerId`, `FirstName`, `LastName`\), rather than primitives. Semantic types are big part of building with Vyne.  By providing rich type information, Vyne is able to build links between data and services, and automate the integration of our services.

Read more about semantic types here:

{% hint style="info" %}
If you're typing this out, we recommend using [Visual Studio Code](https://code.visualstudio.com/), along with the great Taxi plugin \([taxi-language-server](https://marketplace.visualstudio.com/items?itemName=taxi-lang.taxi-language-server)\).  It provides as-you-type compilation, auto completion, and a bunch of other handy dev tools.
{% endhint %}

{% tabs %}
{% tab title="CustomerRecord.taxi" %}
```text
type CustomerId inherits Int
type FirstName inherits String
type LastName inherits String

model CustomerRecord {
    customerId: CustomerId by column("customerId")
    firstName : FirstName by column("firstName")
    lastName : LastName by column("lastName")
}
```
{% endtab %}

{% tab title="PurchaseRecord.taxi" %}
```text
type CustomerId inherits Int
type ItemName inherits String
type ItemPrice inherits String

model PurchaseRecord {
    customerId : CustomerId by column("customerId")
    itemName : ItemName by column("itemName")
    itemPrice: ItemPrice by column("itemPrice")
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
When saving these taxi files, ensure they are named ending with `.taxi` and that they are saved inside `/schemas` which can be found in `vyne-taxonomy-environment`.  Or, ensure that you have set `TAXONOMY_HOME` in the `.env` file to the path to your taxi files. To clone this repo go [here](https://gitlab.com/vyne/vyne-taxonomy-environment).
{% endhint %}

The `.env` file will look something like this:

{% tabs %}
{% tab title="/schemas" %}
```text
TAXONOMY_HOME=/schemas
```
{% endtab %}

{% tab title="/custom-path" %}
```text
TAXONOMY_HOME=path/to/taxi/files
```
{% endtab %}
{% endtabs %}

To learn more about starting Vyne and using Visual Studio Code follow the link below:

{% page-ref page="../running-a-local-taxonomy-editor-environment/starting-vyne.md" %}

## Loading csv data into Vyne

Once [Vyne is running](storing-data-in-cask.md#starting-vyne) we can go to [http://localhost:9022/data-explorer](http://localhost:9022/data-explorer) and load in our csv file\(s\). The page will look like this:

![](../.gitbook/assets/storing-cask-upload-csv.png)

To add a file, select browse and choose the file you wish to upload. We are going to upload our `customers.csv` first.

![](../.gitbook/assets/storing-cask-uploaded-csv%20%281%29.png)

Here we can see a basic table containing the contents of our CSV file.  We're going to apply a schema to the file, so that the Cask understands it's contents, and can make it queryable.

Simply start typing `CustomerRecord` in the type name area and it should autocomplete.

![](../.gitbook/assets/storing-cask-saving-as-custom-record.png)

This applies the schema to our csv and record.  You should now see two additional tabs - the schema we selected, and the result of parsing our CSV file against the schema. Let's take another look at this data that Vyne offers us:

![](../.gitbook/assets/storing-data-profiler-tab.png)

Above we can see another view of this data once we add a model to it. If we click on individual cells Vyne will provide more information around the selected cell. In the image the `customerId` cell has been selected and we can see on the righthand side more information around this `customerId`.

We want to be able to store this data and now query it, in order to do this we are going to use a cask. The process of publishing this data into a cask is simple. In the image above you can see there is a 'Store this data in a cask' drop down menu, if you select that you should see this:

![](../.gitbook/assets/storing-cask-posting.png)

Anyone can post data into a cask by publishing it to a URL. All we need to do is press 'Send' and the data will be published into a cask. You should see a `SUCCESS` message pop up.

{% hint style="warning" %}
You may come across this error:

```text
Error: com.netflix.client.ClientException: Load balancer does not have available server for client: cask
```

This is just the Eureka load balancer taking time to sync everything up, just wait a couple of seconds and retry. Read more about this error [here](https://cloud.spring.io/spring-cloud-static/Greenwich.RELEASE/multi/multi__service_discovery_eureka_clients.html#_why_is_it_so_slow_to_register_a_service).
{% endhint %}

Now we can go ahead and add our other csv file `purchases.csv` and publish it to a cask. Once both our csv files are loaded into Vyne we can head over to the query builder and start querying our data.

### Editing taxi and syncing with Vyne

Vyne's Schema server is watching the directory we're saving our taxi scripts to, so changes to our schemas are automatically populated through to Vyne.

When we edit a `.taxi` file and save that, Vyne will detect a change and refresh the schema. This is a powerful tool as it allows us to make changes to our model in real time.

Let's take a look at an example. Our `CustomerRecord` currently holds a `FirstName` and `LastName`, we are now going to add a `FullName`.

We need to add this to our `CustomerRecord` model:

```text
fullName: FirstName by concat(this.firstName, " ", this.lastName)
```

Our model will now look like this:

```text
type FullName inherits String

model CustomerRecord {
    customerId: CustomerId by column("customerId")
    firstName : FirstName by column("firstName")
    lastName : LastName by column("lastName")
    fullName: FullName by concat(this.firstName, " ", this.lastName)
}
```

Once we save our taxi file, Vyne will update the schema to look like this:

![](../.gitbook/assets/storing-cask-schema-explorer-fullname.png)

## Exploring a Cask

In here, you will see there is a cask for `CustomerRecord` and `PurchaseRecord`. This page displays data on our cask, data such as:

* Cask ID
* Number of records in our cask
* Date of creation

![](../.gitbook/assets/storing-cask-cask-inventory.png)

### Exploring cask data through REST API's

In addition to storing the data, Cask has built a number of RESTful API's for us to query and access the data. 

Ultimately, we'll let Vyne do the work to fetch data from these REST API's, but it's useful to take a look at what's been generated to help understand what's going on.

Let's start by finding our new service in the Type Explorer. Head over to the Type Explorer, and filter to see our services, we should see our new `CustomerRecordCaskService` present.

![](../.gitbook/assets/storing-cask-showing-service.png)

Select the `CustomerRecordCaskService` to see all the different calls we can make. 

![](../.gitbook/assets/storing-cask-type-explorer%20%282%29%20%281%29%20%281%29.png)

We are now able to select any of the above operations and we can run them through Vyne's UI. We are going to try out the `findAll` as it is the simplest one. After selecting `findAll` we will see this:

![](../.gitbook/assets/find-all-customer-records.png)

Let's invoke this service - all we need to do is select 'Try it out', and then select 'Submit'. Vyne will return all our customer data that is stored in our cask. We should be greeted with a table displaying our data:

![](../.gitbook/assets/find-all-customer-data.png)

Now that we've seen how the data is accessed through REST API's, let's write some queries, and get Vyne doing the work for us.

## Query builder

The query builder allows us to write queries about our data, decoupled from the services that provide them.  Our queries stay focussed on the information we want to discover, rather than the API's that service them.  

This is really powerful, as it means that as service API's change, or are replaced, our query code does not need to change.  

The query builder can be found at [http://localhost:9022/query-wizard](http://localhost:9022/query-wizard).

Let's write the following query:

```text
findAll { CustomerRecords[] }
```

{% page-ref page="../querying-with-vyne/" %}

This will return all our customers from our csv that are stored in cask. It will look something like this:

![](../.gitbook/assets/storing-cask-find-all-table.png)

This data is also available in json format by going into the 'Profiler' tab and selecting the call made to retrieve the data. This json data can be downloaded by click the 'Download' button.

You can find out more about our query syntax at [Querying with Vyne](../querying-with-vyne/).

## Discovering links between our data

Now, let's try and write a query that leverages information from both sets of data - `customers.csv` \( as `CustomerRecord` \) and `purchases.csv` \(as `PurchaseRecord`\).

Our query will pull back data from the `PurchaseRecord`, and enrich it with additional customer information, made available from our `CustomerRecord`.

As both sets of data expose `CustomerId` attributes, Vyne will be able to automatically link between the two datasets. We need to make a minor change to our `CustomerRecord` taxonomy, adding an `@Id` attribute, so that Vyne understands that the `customerId` attribute is safe to use for lookups.

The [taxi](https://docs.taxilang.org/taxi-language) will now look like this:

```text
type CustomerId inherits Int
type FirstName inherits String
type LastName inherits String
type FullName inherits String

model CustomerRecord {
    @Id
    customerId: CustomerId by column("customerId")
    firstName : FirstName by column("firstName")
    lastName : LastName by column("lastName")
    fullName: FullName by concat(this.firstName, " ", this.lastName)
}
```

Save this, and Vyne will automatically the taxonomy.  Looking in the Type explorer for our Customer cask, your You should also see that there are new additional RESTful endpoints that have been exposed in our Cask, allowing lookups using the `customerId` attribute.

Finally, let's issue our query. Taking the query below, paste it into your query builder and see the full power of Vyne.

```text
findAll { PurchaseRecord[] } as {
    id: CustomerId
    product: ItemName
    price: ItemPrice
    customerName: FullName
}[]
```

As before, our query doesn't refer to the services used to fetch the data from, it simply describes the data that we're interested in.  One of the attributes - `customerName` has been fetched from the customer cask.

This will pull back data from `customers.csv` and `purchases.csv` using the `CustomerId` as a link. This is powerful because `purchases.csv` doesn't store any data on our customer, this data is being gathered all through Vyne creating links between services. The output will look like this:

![](../.gitbook/assets/storing-cask-custom-search.png)

Let's take a look at some of the calls made by Vyne in the profiler tab.

![](../.gitbook/assets/storing-cask-custom-search-profiler-tab.png)

In this view we can see the process Vyne goes through to get the expected output. We can see all the calls made and if we click on those calls we get more information around them. If we click on the first call we can see the json body returned from it and other data:

![](../.gitbook/assets/storing-cask-custom-search-json.png)

Above we can see the time taken to make this call, the response from the call and the actual request itself.

## Summary

Let's take a look at what has happened in the last 15 minutes:

* We have taken csv data and loaded it into a cask
* Written taxi schemas that represents our data
* Created restful services that sit over our cask
* Queried our data and seen how Vyne links the services together

