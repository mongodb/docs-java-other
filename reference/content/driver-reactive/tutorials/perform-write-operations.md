+++
date = "2016-06-09T13:21:16-04:00"
title = "Write Operations"
[menu.main]
  parent = "Reactive Tutorials"
  identifier = "Reactive Perform Write Operations"
  weight = 20
  pre = "<i class='fa'></i>"
+++

## Write Operations (Insert, Update, Replace, Delete)

Perform write operations to insert new documents into a collection, update existing document or documents in a collection, replace an existing document in a collection, or delete existing document or documents from a collection.

## Prerequisites

- The example below requires a ``restaurants`` collection in the ``test`` database. To create and populate the collection, follow the directions in [github](https://github.com/mongodb/docs-assets/tree/drivers).

- Include the following import statements:

     ```java
     import com.mongodb.*;
     import com.mongodb.client.MongoClients;
     import com.mongodb.client.MongoClient;
     import com.mongodb.client.MongoCollection;
     import com.mongodb.client.MongoDatabase;
     import com.mongodb.client.model.Filters;
     import static com.mongodb.client.model.Filters.*;
     import static com.mongodb.client.model.Updates.*;
     import com.mongodb.client.model.UpdateOptions;
     import com.mongodb.client.result.*;
     import org.bson.Document;
     import org.bson.types.ObjectId;

     import java.util.List;
     import java.util.Arrays;
     import java.util.ArrayList;
     ```

{{% note class="important" %}}
This guide uses the `Subscriber` implementations as covered in the [Quick Start Primer]({{< relref "driver-reactive/getting-started/quick-start-primer.md" >}}).
{{% /note %}}

## Connect to a MongoDB Deployment

Connect to a MongoDB deployment and declare and define a `MongoDatabase` instance.

For example, include the following code to connect to a standalone MongoDB deployment running on localhost on port `27017` and define `database` to refer to the `test` database and `collection` to refer to the `restaurants` collection:

```java
MongoClient mongoClient = MongoClients.create();
MongoDatabase database = mongoClient.getDatabase("test");
MongoCollection<Document> collection = database.getCollection("restaurants");
```

For additional information on connecting to MongoDB, see [Connect to MongoDB]({{< ref "connect-to-mongodb.md" >}}).

## Insert New Document

To insert a single document into the collection, you can use the collection's [`insertOne()`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#insertOne(TDocument)" >}}) method.

```java
Document document = new Document("name", "Café Con Leche")
               .append("contact", new Document("phone", "228-555-0149")
                                       .append("email", "cafeconleche@example.com")
                                       .append("location",Arrays.asList(-73.92502, 40.8279556)))
               .append("stars", 3)
               .append("categories", Arrays.asList("Bakery", "Coffee", "Pastries"));

collection.insertOne(document).subscribe(new ObservableSubscriber<Void>());
```

{{% note %}}
If no top-level `_id` field is specified in the document, the Java driver automatically adds the `_id` field to the inserted document.
{{% /note %}}

## Insert Multiple Documents

To add multiple documents, you can use the collection's [`insertMany()`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#insertMany(java.util.List)" >}}) method, which takes a list of documents to insert.

The following example inserts two documents to the collection:

```java
Document doc1 = new Document("name", "Amarcord Pizzeria")
               .append("contact", new Document("phone", "264-555-0193")
                                       .append("email", "amarcord.pizzeria@example.net")
                                       .append("location",Arrays.asList(-73.88502, 40.749556)))
               .append("stars", 2)
               .append("categories", Arrays.asList("Pizzeria", "Italian", "Pasta"));


Document doc2 = new Document("name", "Blue Coffee Bar")
               .append("contact", new Document("phone", "604-555-0102")
                                       .append("email", "bluecoffeebar@example.com")
                                       .append("location",Arrays.asList(-73.97902, 40.8479556)))
               .append("stars", 5)
               .append("categories", Arrays.asList("Coffee", "Pastries"));

List<Document> documents = new ArrayList<Document>();
documents.add(doc1);
documents.add(doc2);

collection.insertMany(documents).subscribe(new ObservableSubscriber<Void>());;
```
{{% note %}}
If no top-level `_id` field is specified in the documents, the Java driver automatically adds the `_id` field to the inserted documents.
{{% /note %}}

## Update Existing Documents

To update existing documents in a collection, you can use the collection's [`updateOne()`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#updateOne(org.bson.conversions.Bson,org.bson.conversions.Bson)" >}}) or [`updateMany`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#updateMany(org.bson.conversions.Bson,org.bson.conversions.Bson)" >}}) methods.

### Filters

You can pass in a filter document to the methods to specify which documents to update. The filter document specification is the same as for [read operations]({{< relref "driver-reactive/tutorials/perform-read-operations.md" >}}). To facilitate creating filter objects, the Java driver provides the [`Filters`]({{< apiref "mongodb-driver-core" "com/mongodb/client/model/Filters.html" >}}) helper.

To specify an empty filter (i.e. match all documents in a collection), use an empty [`Document`]({{< apiref "bson" "org/bson/Document.html" >}}) object.

### Update Operators

To change a field in a document, MongoDB provides [update operators]({{<docsref "reference/operator/update" >}}).  To specify the modification to perform using the update operators, use an updates document.

To facilitate the creation of updates documents, the Java driver provides the [`Updates`]({{< apiref "mongodb-driver-core" "com/mongodb/client/model/Updates.html" >}}) class.

{{% note class="important" %}}
The `_id` field is immutable; i.e. you cannot change the value of the `_id` field.
{{% /note %}}

### Update a Single Document

The [`updateOne()`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#updateOne(org.bson.conversions.Bson,org.bson.conversions.Bson)" >}}) method updates at most a single document, even if the filter condition matches multiple documents in the collection.

The following operation on the `restaurants` collection updates a document whose `_id` field equals `ObjectId("57506d62f57802807471dd41")`.

```java
collection.updateOne(
                eq("_id", new ObjectId("57506d62f57802807471dd41")),
                combine(set("stars", 1), set("contact.phone", "228-555-9999"), currentDate("lastModified")))
          .subscribe(new ObservableSubscriber<UpdateResult>());
```

Specifically, the operation uses:

- [`Updates.set`]({{< apiref "mongodb-driver-core" "com/mongodb/client/model/Updates.html#set(java.lang.String,TItem)" >}}) to set the value of the `stars` field to `1` and the `contact.phone` field to `"228-555-9999"`, and

- [`Updates.currentDate`]({{< apiref "mongodb-driver-core" "com/mongodb/client/model/Updates.html#currentDate(java.lang.String)" >}}) to modify the `lastModified` field to the current date. If the `lastModified` field does not exist, the operator adds the field to the document.  

### Update Multiple Documents

The [`updateMany`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#updateMany(org.bson.conversions.Bson,org.bson.conversions.Bson)" >}}) method updates all documents that match the filter condition.

The following operation on the `restaurants` collection updates all documents whose `stars` field equals `2`.

```java
collection.updateMany(
              eq("stars", 2),
              combine(set("stars", 0), currentDate("lastModified")))
          .subscribe(new ObservableSubscriber<UpdateResult>());
```

Specifically, the operation uses:

- [`Updates.set`]({{< apiref "mongodb-driver-core" "com/mongodb/client/model/Updates.html#set(java.lang.String,TItem)" >}}) to set the value of the `stars` field to `0` , and

- [`Updates.currentDate`]({{< apiref "mongodb-driver-core" "com/mongodb/client/model/Updates.html#currentDate(java.lang.String)" >}}) to modify the `lastModified` field to the current date. If the `lastModified` field does not exist, the operator adds the field to the document.  

### Update Options

With the [`updateOne()`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#updateOne(org.bson.conversions.Bson,org.bson.conversions.Bson)" >}}) and [`updateMany`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#updateMany(org.bson.conversions.Bson,org.bson.conversions.Bson)" >}}) methods, you can include an [`UpdateOptions`]({{< apiref "mongodb-driver-core" "com/mongodb/client/model/UpdateOptions.html" >}}) document to specify the [`upsert`]({{<docsref "reference/method/db.collection.update/#upsert(option" >}}) option or the [`bypassDocumentationValidation`]({{<docsref "core/document-validation/#bypass-document-validation" >}}) option.

```java
collection.updateOne(
                eq("_id", 1),
                combine(set("name", "Fresh Breads and Tulips"), currentDate("lastModified")),
                new UpdateOptions().upsert(true).bypassDocumentValidation(true))
          .subscribe(new ObservableSubscriber<UpdateResult>());
```
## Replace an Existing Document

To replace an existing document in a collection, you can use the collection's [`replaceOne`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#replaceOne(org.bson.conversions.Bson,TDocument)" >}}) method.

{{% note class="important" %}}
The `_id` field is immutable; i.e. you cannot replace the `_id` field value.
{{% /note %}}

### Filters

You can pass in a filter document to the method to specify which document to replace. The filter document specification is the same as for [read operations]({{< relref "driver-reactive/tutorials/perform-read-operations.md" >}}). To facilitate creating filter objects, the Java driver provides the [`Filters`]({{< apiref "mongodb-driver-core" "com/mongodb/client/model/Filters.html" >}}) helper.

To specify an empty filter (i.e. match all documents in a collection), use an empty [`Document`]({{< apiref "bson" "org/bson/Document.html" >}}) object.

The [`replaceOne`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#replaceOne(org.bson.conversions.Bson,TDocument)" >}}) method replaces at most a single document, even if the filter condition matches multiple documents in the collection.

### Replace a Document

To replace a document, pass a new document to the [`replaceOne`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#replaceOne(org.bson.conversions.Bson,TDocument)" >}}) method.

{{% note class="important" %}}
The replacement document can have different fields from the original document. In the replacement document, you can omit the `_id` field since the `_id` field is immutable; however, if you do include the `_id` field, you cannot specify a different value for the `_id` field.
{{% /note %}}

The following operation on the `restaurants` collection replaces the document whose `_id` field equals `ObjectId("57506d62f57802807471dd41")`.

```java
collection.replaceOne(
                eq("_id", new ObjectId("57506d62f57802807471dd41")),
                new Document("name", "Green Salads Buffet")
                        .append("contact", "TBD")
                        .append("categories", Arrays.asList("Salads", "Health Foods", "Buffet")))
         .subscribe(new ObservableSubscriber<UpdateResult>());
```

See also [Update a Document](#update-a-single-document).

### Update Options

With the [`replaceOne`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#replaceOne(org.bson.conversions.Bson,TDocument)" >}}), you can include an [`UpdateOptions`]({{< apiref "mongodb-driver-core" "com/mongodb/client/model/UpdateOptions.html" >}}) document to specify the [`upsert`]({{<docsref "reference/method/db.collection.update/#upsert(option" >}}) option or the [`bypassDocumentationValidation`]({{<docsref "core/document-validation/#bypass-document-validation" >}}) option.

```java
collection.replaceOne(
                eq("name", "Orange Patisserie and Gelateria"),
                new Document("stars", 5)
                        .append("contact", "TBD")
                        .append("categories", Arrays.asList("Cafe", "Pastries", "Ice Cream")),
                new UpdateOptions().upsert(true).bypassDocumentValidation(true))
          .subscribe(new ObservableSubscriber<UpdateResult>());
```

## Delete Documents

To delete documents in a collection, you can use the
[`deleteOne`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#deleteOne(org.bson.conversions.Bson)" >}}) and [`deleteMany`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#deleteMany(org.bson.conversions.Bson)" >}}) methods.

### Filters

You can pass in a filter document to the methods to specify which documents to delete. The filter document specification is the same as for [read operations]({{< relref "driver-reactive/tutorials/perform-read-operations.md" >}}). To facilitate creating filter objects, the Java driver provides the [`Filters`]({{< apiref "mongodb-driver-core" "com/mongodb/client/model/Filters.html" >}}) helper.

To specify an empty filter (i.e. match all documents in a collection), use an empty [`Document`]({{< apiref "bson" "org/bson/Document.html" >}}) object.

### Delete a Single Document

The [`deleteOne`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#deleteOne(org.bson.conversions.Bson)" >}}) method deletes at most a single document, even if the filter condition matches multiple documents in the collection.

The following operation on the `restaurants` collection deletes a document whose `_id` field equals `ObjectId("57506d62f57802807471dd41")`.

```java
collection.deleteOne(eq("_id", new ObjectId("57506d62f57802807471dd41"))).subscribe(new ObservableSubscriber<DeleteResult>());
```

### Delete Multiple Documents

The [`deleteMany`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#deleteMany(org.bson.conversions.Bson)" >}}) method deletes all documents that match the filter condition.

The following operation on the `restaurants` collection deletes all documents whose `stars` field equals `4`.

```java
collection.deleteMany(eq("stars", 4)).subscribe(new ObservableSubscriber<DeleteResult>());
```

See also [Drop a Collection]({{< relref  "driver/tutorials/databases-collections.md" >}}).

## Write Concern

[Write concern]({{<docsref "reference/write-concern" >}}) describes the level of acknowledgement requested from MongoDB for write operations.

Applications can configure [write concern]({{<docsref "reference/write-concern" >}}) at three levels:

- In a [`MongoClient()`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoClient.html" >}})

  - Via [`MongoClientSettings`]({{< apiref "mongodb-driver-core" "com/mongodb/MongoClientSettings.html" >}}):

      ```java
      MongoClient mongoClient = MongoClients.create(MongoClientSettings.builder()
                                                    .applyConnectionString(new ConnectionString("mongodb://host1,host2"))
                                                    .writeConcern(WriteConcern.MAJORITY)
                                                    .build());
      ```

  - Via [`ConnectionString`]({{< apiref "mongodb-driver-core" "com/mongodb/ConnectionString.html" >}}), as in the following example:

      ```java
      MongoClient mongoClient = MongoClients.create("mongodb://host1:27017,host2:27017/?w=majority");
      ```

- In a [`MongoDatabase`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoDatabase.html" >}}) via its [`withWriteConcern`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoDatabase.html#withWriteConcern(com.mongodb.WriteConcern)" >}}) method, as in the following example:

    ```java
     MongoDatabase database = mongoClient.getDatabase("test").withWriteConcern(WriteConcern.MAJORITY);
    ```

- In a [`MongoCollection`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html" >}}) via its [`withWriteConcern`]({{< apiref "mongodb-driver-reactivestreams" "com/mongodb/reactivestreams/client/MongoCollection.html#withWriteConcern(com.mongodb.WriteConcern)" >}}) method, as in the following example:

    ```java
     MongoCollection<Document> collection = database.getCollection("restaurants").withWriteConcern(WriteConcern.MAJORITY);
    ```

`MongoDatabase` and `MongoCollection` instances are immutable. Calling `.withWriteConcern()` on an existing `MongoDatabase` or `MongoCollection` instance returns a new instance and does not affect the instance on which the method is called.

For example, in the following, the `collWithWriteConcern` instance has the write concern of majority whereas the write concern of the `collection` is unaffected.

```java
MongoCollection<Document> collWithWriteConcern = collection
                                                  .withWriteConcern(WriteConcern.MAJORITY);
```

You can build `MongoClientSettings`, `MongoDatabase`, or `MongoCollection` to include a combination of write concern, [read concern]({{<docsref "reference/read-concern" >}}), and [read preference]({{<docsref "reference/read-preference" >}}).

For example, the following sets all three at the collection level:

```java
collection = database.getCollection("restaurants")
                .withReadPreference(ReadPreference.primary())
                .withReadConcern(ReadConcern.MAJORITY)
                .withWriteConcern(WriteConcern.MAJORITY);
```
