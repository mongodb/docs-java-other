# MongoDB Java Driver Documentation

This repo contains build tools for the following driver documentation:

- Reactive Streams (Reference and API)
- Scala (Reference and API)
- Java Sync (legacy reference and current API)

The aforementioned documentation resides in the "gh-pages" branch of
the [MongoDB Java Driver](https://github.com/mongodb/mongo-java-driver).

For the current Java Sync driver reference documentation, see the
[Java Sync reference docs site](https://www.mongodb.com/docs/drivers/java/sync)
or the source in the [docs-java repository](https://github.com/mongodb/docs-java).

## Build Requirements

- Intel Mac or x64 PC (for hugo)
- [hugo v0.25.x](https://github.com/gohugoio/hugo/releases/tag/v0.25.1) (any other version may not generate the documentation correctly)
- The [https://github.com/mongodb/docs-java-other](docs-java-other) and [https://github.com/mongodb/mongo-java-driver](mongo-java-driver) repos cloned to your local machine (ensure you have appropriate SSH keys to access them) 

:warning: **If you are running on an Apple M1 CPU, you may not be able to find a working binary for Hugo v0.25.x.** and will need to set up an Ubuntu Docker Image for the linux/amd64 arch. See [docker-java-gh-docs](https://github.com/mongodb/docs-java-other/tree/main/tools/README.md) for information on setting this up.

## Build Instructions

:warning: **When building docs for a new version, update the following items:**
- The `<this repo>/reference/config.toml` file to point to the new base version
- The `<this repo>/landing/data/releases.toml` file to link to the docs for the new version
- Redirects such as the [Java sync docs redirect](https://github.com/mongodb/docs-java-other/pull/3/files#diff-0f1a8692163867f83ff7451f3018bae71d3d16dbee396abf03263784e5dda940)

After updating the documentation, submit a pull request for approval.

Once your PR is approved and merged, pull the latest changes.
Then run the `publish-docs` script with the version using the
format `<major>.<minor>`:

```sh
./publish-docs <version, e.g. 4.7>
```

This command updates the submodule that tracks the *gh-pages* branch
of the [mongo-java-driver](https://github.com/mongodb/mongo-java-driver).
Then it builds the documentation in a new directory that corresponds to the
new version name.

## Building API Documentation

Always build the API docs for any new major and minor releases.

To build the API docs, navigate to your `mongo-java-driver` repo 
(*Note: NOT the submodule in this repo*) and executing the appropriate `gradlew` command
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
