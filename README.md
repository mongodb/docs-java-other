# MongoDB Java Driver Documentation

This repo contains the documentation for the Reactive Streams and Scala variants of the
[MongoDB Java Driver](https://github.com/mongodb/mongo-java-driver).

Documentation for the synchronous Java driver is available at
https://docs.mongodb.com/drivers/java/sync and the source is available at
https://github.com/mongodb/docs-java

## Build Instructions

After making required changes, run the `publish-docs` script with the version:

```sh
./publish-docs <version>
```

This will initiate a submodule (if necessary) that tracks the *gh-pages* branch
of the [mongo-java-driver](https://github.com/mongodb/mongo-java-driver). It will
then build the documents and copy items over as necessary (using rsync).

## Publishing

To publish the documentation, you can execute the following commands in your shell:

```js
cd mongo-java-driver
git add .
git ci -a -m fixup!
git rebase -i --root
git push origin gh-pages -f
```
