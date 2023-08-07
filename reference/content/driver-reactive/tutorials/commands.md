+++
date = "2016-05-31T13:07:04-04:00"
title = "Run Commands"
[menu.main]
parent = "Reactive Tutorials"
identifier = "Reactive Run Commands"
weight = 90
pre = "<i class='fa'></i>"
+++

## Run Commands

Not all commands have a specific helper. However you can run any [MongoDB command]({{<docsref "reference/command" >}}) by using the MongoDatabase's [`runCommand()`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoDatabase.html#runCommand(org.bson.conversions.Bson,com.mongodb.ReadPreference)" >}}) method.

## Prerequisites

- The example below requires a `restaurants` collection in the `test` database. To create and populate the collection, follow the directions in [github](https://github.com/mongodb/docs-assets/tree/drivers).

- Include the following import statements:

     ```java
     import com.mongodb.reactivestreams.client.MongoClients;
     import com.mongodb.reactivestreams.client.MongoClient;
     import com.mongodb.reactivestreams.client.MongoDatabase;
     import org.bson.Document;
     ```

{{% note class="important" %}}
This guide uses the `Subscriber` implementations as covered in the [Quick Start Primer]({{< relref "driver-reactive/getting-started/quick-start-primer.md" >}}).
{{% /note %}}

## Connect to a MongoDB Deployment

Connect to a MongoDB deployment and declare and define a `MongoDatabase` instance.

For example, include the following code to connect to a standalone MongoDB deployment running on localhost on port `27017` and define `database` to refer to the `test` database:

```java
MongoClient mongoClient = MongoClients.create();
MongoDatabase database = mongoClient.getDatabase("test");
```

For additional information on connecting to MongoDB, see [Connect to MongoDB]({{< ref "connect-to-mongodb.md" >}}).

## Run the `buildInfo` Command

To run the `buildInfo` command, construct a [`Document`]({{< apiref "bson" "org/bson/Document.html" >}})
object that specifies the command and pass it to the `runCommand()` method.

The following sample code runs the [`buildInfo`]({{<docsref "reference/command/buildInfo" >}}) command and prints the results:

```java
database.runCommand(new Document("buildInfo", 1)).subscribe(new PrintDocumentSubscriber());
```

For a list of available MongoDB commands, see [MongoDB commands]({{<docsref "reference/command" >}}).
