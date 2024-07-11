# MongoDB Java Driver Documentation

This repo contains build tools for the following driver documentation:

- Reactive Streams (legacy reference and API)
- Scala (legacy reference and API)
- Java Sync (legacy reference and current API)

The aforementioned documentation resides in the "gh-pages" branch of
the [MongoDB Java Driver](https://github.com/mongodb/mongo-java-driver).

For the current Java Sync driver reference documentation, see the
[Java Sync reference docs site](https://www.mongodb.com/docs/drivers/java/sync)
or the source in the [docs-java repository](https://github.com/mongodb/docs-java).

**Now that Java RS and Scala documentation is built on Snooty, you do not
have to update the legacy documentation site.**

## Building API Documentation

Always build the API docs for any new major and minor releases.

To build the API docs, navigate to your `mongo-java-driver` repo
(_Note: NOT the submodule in this repo_) and execute the appropriate `gradlew` command
after checking out the correct tag. Ensure you installed the Java
version specified in the `:bson:compileJava` task in your development
environment prior to building. In the most recent (5/2024) deploy, this
was Java 17.

:warning: **Use the GitHub "release" that corresponds to the version of the driver rather than a branch**

For example, to build the API docs for the 4.4 release of the driver run the following commands:

```sh
git checkout r4.4.0
./gradlew clean docs
```

You need to create the `<version>/apidocs` directory yourself before
copying the generated API docs files.

Run the following commands to create the `apidocs` directory:

```sh
cd docs-java-other/mongo-java-driver
mkdir -p <version>/apidocs
```

Then copy the `build/docs` folder into the `apidocs` directory. For example,
if the `mongo-java-driver` repo is on a sibling level with this repo, run the following command:

```sh
cp -a ../mongo-java-driver/build/docs ./mongo-java-driver/<version>/apidocs
```

Your submodule directory should contain a directory structure that resembles the following:

```sh
<this repo>/<submodule directory>/<version>/apidocs/{bson,mongodb-driver-core,mongodb-driver-sync,mongodb-driver-legacy, mongodb-driver-reactivestreams/}
```

## Publishing

To publish the documentation, run the following commands in your shell from the `docs-java-other` repository location:

```sh
cd mongo-java-driver
git add .
git commit -m <message>
git rebase -i --root
git push origin gh-pages -f
```

:warning: Make sure you are starting from the latest gh-pages branch commit in the mongo-java-driver submodule. This may not be set if you did not run the publish-docs script from docs-java-other on your local machine (e.g. if the task only involved generating reference docs on Docker and copying them over).
