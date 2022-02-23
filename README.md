# MongoDB Java Driver Documentation

This repo contains the documentation for the Reactive Streams and Scala variants of the
[MongoDB Java Driver](https://github.com/mongodb/mongo-java-driver).

Documentation for the synchronous Java driver is available at
https://docs.mongodb.com/drivers/java/sync and the source is available at
https://github.com/mongodb/docs-java

## Build Instructions

:warning: **When building docs for a new version, update the following items:**
- The `<this repo>/reference/config.toml` file to point to the new base version
- The `<this repo>/landing/data/releases.toml` file to link to the docs for the new version
- Redirects such as the [Java sync docs redirect](https://github.com/mongodb/docs-java-other/pull/3/files#diff-0f1a8692163867f83ff7451f3018bae71d3d16dbee396abf03263784e5dda940)

After making required changes, submit a pull request. Once your PR is approved and merged, run the `publish-docs` script with the version:

```sh
./publish-docs <version>
```

This will initiate a submodule (if necessary) that tracks the *gh-pages* branch
of the [mongo-java-driver](https://github.com/mongodb/mongo-java-driver). It then
builds the documents in a new directory in the submodule that corresponds to the
new version name.

## Building API Documentation

For major and minor releases, it is required to build the API docs. This
can be done by navigating to your `mongo-java-driver` repo (*Note: NOT the submodule in this repo*) and executing the appropriate `gradlew` command
after checking out the correct tag. Ensure you installed the Java version specified in the `:bson:compileJava` task in your development environment prior to building.

For example, to build the API docs for the 4.4 release of driver:

```sh
git checkout r4.4.0
./gradlew clean docs
```

Then copy the `build/docs` folder into the `apidocs` directory. For example,
if the `mongo-java-driver` repo is on a sibling level with this repo:

```sh
cp -a ../mongo-java-driver/build/docs ./mongo-java-driver/<version>/apidocs
```

Your submodule directory should contain a directory structure that resembles the following:

```sh
<this repo>/<submodule directory>/<version>/apidocs/{bson,mongodb-driver-core,mongodb-driver-sync,mongodb-driver-legacy, mongodb-driver-reactivestreams/}
```


## Publishing

To publish the documentation, you can execute the following commands in your shell from the `docs-java-other` repository location:

```js
cd mongo-java-driver
git add .
git commit -m <message>
git rebase -i --root
git push origin gh-pages -f
```
