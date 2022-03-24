+++
date = "2015-03-19T14:27:51-04:00"
title = "Extended JSON"
[menu.main]
  parent = "BSON"
  weight = 50
  pre = "<i class='fa'></i>"
+++

## JSON

As discussed earlier, the Java driver supports reading and writing BSON documents represented as JSON values. The driver supports four
standard variants:

- Extended Mode: Canonical representation that avoids any loss of BSON type information. See the
[Extended JSON specification](https://github.com/mongodb/specifications/blob/master/source/extended-json.rst) for a description of this
mode.
- Relaxed Mode:  Relaxed representation that loses type information for BSON numeric types and uses a more human-readable representation
of BSON dates. See the
[Extended JSON specification](https://github.com/mongodb/specifications/blob/master/source/extended-json.rst) for a description of this
mode.
- Shell Mode: a superset of JSON that the
[MongoDB shell](https://www.mongodb.com/docs/manual/reference/program/mongo/) can parse.
- Strict Mode: Legacy representation.  Though now deprecated, this is still the default mode when writing JSON in order to avoid breaking
backwards compatibility.  This may change in a future major release of the driver.

Furthermore, the `Document`, `BsonDocument`, and `BasicDBObject` classes each provide two sets of convenience methods for this purpose:

- toJson(): a set of overloaded methods that convert an instance to a JSON string
- parse(): a set of overloaded static factory methods that convert a JSON string to an instance of the class

### Writing JSON

Consider the task of implementing a [mongoexport](https://www.mongodb.com/docs/database-tools/mongoexport/)-like tool using the
Java driver.

```java
String outputFilename;                 // initialize to the path of the file to write to
MongoCollection<Document> collection;  // initialize to the collection from which you want to query

BufferedWriter writer = new BufferedWriter(new FileWriter(outputFilename));

try {
    JsonWriterSettings settings = JsonWriterSettings.builder().outputMode(JsonMode.EXTENDED).build();
    for (Document doc : collection.find()) {
        writer.write(doc.toJson(settings));
        writer.newLine();
    } finally{
        writer.close();
    }
}

```

The `Document.toJson()` method constructs an instance of a `JsonWriter` with its default settings, which will write in strict mode with
no new lines or indentation.

You can override this default behavior by using one of the overloads of `toJson()`.  As an example, consider the task of writing a
 JSON string that can be copied and pasted into the MongoDB shell:

```java
SimpleDateFormat fmt = new SimpleDateFormat("dd/MM/yy");
Date first = fmt.parse("01/01/2014");
Date second = fmt.parse("01/01/2015");
Document doc = new Document("startDate", new Document("$gt", first).append("$lt", second));
System.out.println(doc.toJson(new JsonWriterSettings(JsonMode.SHELL)));
```

This code snippet will print out MongoDB shell-compatible JSON, which can then be pasted into the shell:

```javascript
{ "startDate" : { "$gt" : ISODate("2014-01-01T05:00:00.000Z"), "$lt" : ISODate("2015-01-01T05:00:00.000Z") } }
```

#### Customizing the output

Often applications have specific requirements on the structure of JSON that is generated from documents stored in MongoDB, where none of
the modes described above will suffice.   For those situations a `JsonWriterSettings` instance can be customized with an application-provided
[`Converter`]({{< apiref "bson" "org/bson/json/Converter" >}}) for each BSON type.

Consider a situation where an application wants to output the hex string representation of an ObjectId as a simple JSON string.  Simply
register a custom `Converter` with `JsonWriterSettings` for the ObjectId type:

```java
        JsonWriterSettings settings = JsonWriterSettings.builder()
                                              .outputMode(JsonMode.RELAXED)
                                              .objectIdConverter((value, writer) -> writer.writeString(value.toHexString()))
                                              .build();
```

A `JsonWriter` configured with these settings will use the given `JsonMode` as the default for all BSON types, but override the mode
with any registered converters.

### Reading JSON

Consider the task of implementing a [mongoimport](https://www.mongodb.com/docs/database-tools/mongoimport/)-like tool using the
Java driver.

```java
String inputFilename;                  // initialize to the path of the file to read from
MongoCollection<Document> collection;  // initialize to the collection to which you want to write

BufferedReader reader = new BufferedReader(new FileReader(inputFilename));

try {
    String json;

    while ((json = reader.readLine()) != null) {
        collection.insertOne(Document.parse(json));
    }
} finally {
    reader.close();
}
```

The `Document.parse()` static factory method constructs an instance of a `JsonReader` with the given string and returns an instance of an
equivalent Document. `JsonReader` automatically detects the JSON flavor in the string, so you do not need to specify it.

### Reading and Writing JSON Directly
If you do not need a document and only want to deal with JSON, you can use `JsonObject` to read and write JSON directly. `JsonObject`
is simply a wrapper class that takes in a `String` in the constructor and returns the `String` in the `getJson()` method.
Reading and writing JSON directly is more efficient than constructing a `Document` first and then calling `toJson()`, and it is also more efficient than calling `Document#parse`.
The codec responsible for reading/writing JSON (`JsonObjectCodec`) is part of the default registry, so doing this is very simple
and demonstrated by the following example:

```java
MongoDatabase database = mongoClient.getDatabase("mydb");
MongoCollection<JsonObject> collection = database.getCollection("json", JsonObject.class);
collection.insertOne(new JsonObject("{hello: 1}"));
JsonObject jsonObject = collection.find().first();
```

### Reading and Writing JSON with CustomSettings
You can also provide custom `JsonWriterSettings` to the `JsonObjectCodec`, by constructing the codec yourself and then creating your own registry:

```java
CodecRegistry codecRegistry = fromRegistries(fromCodecs(
        new JsonObjectCodec(JsonWriterSettings
                .builder()
                .outputMode(JsonMode.EXTENDED)
                .build())),
        getDefaultCodecRegistry());
MongoDatabase database = mongoClient.getDatabase("mydb").withCodecRegistry(codecRegistry);
MongoCollection<JsonObject> collection = database.getCollection("json", JsonObject.class);
collection.insertOne(new JsonObject("{hello: 1}"));
```

