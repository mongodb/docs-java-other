# MongoDB Java Driver Documentation

This repo contains build tools for the following driver documentation:

- Reactive Streams (legacy reference and API)
- Scala (legacy reference and API)
- Java Sync (legacy reference and current API)
- Kotlin (coroutine and sync)

The aforementioned documentation resides in the "gh-pages" branch of
the [MongoDB Java Driver](https://github.com/mongodb/mongo-java-driver).

For the current Java Sync driver reference documentation, see the
[Java Sync reference docs site](https://www.mongodb.com/docs/drivers/java/sync)
or the source in the [docs-java repository](https://github.com/mongodb/docs-java).

**Now that Java RS and Scala documentation is built on Snooty, you do not
have to update the legacy documentation site.**

## Install Java 17

You can install OpenJDK 17 by using Homebrew:

```sh
brew install openjdk@17
```
After you install it, follow the instructions to add it to $PATH and add any other needed links.

## Ensure Your Repos are Right

1. Clone this repo: `git clone git@github.com:mongodb/docs-java-other.git`
2. Clone the Java driver repo: `git clone git@github.com:mongodb/mongo-java-driver.git`
3. In `docs-java-other`, navigate to the `mongo-java-driver` submodule.
4. List all remote branches: `git branch -r`

If you see all branches from the `mongo-java-driver` repo, continue to building the docs. Otherwise, you'll need to re-add the submodule:

5. From `docs-java-other`, run the following commands:

```sh
git submodule deinit -f mongo-java-driver
rm -rf mongo-java-driver
git rm mongo-java-driver
git submodule add https://github.com/mongodb/mongo-java-driver.git mongo-java-driver && git submodule update --init --recursive  
```
6. List all remote branches again to confirm they all appear.

## Build API Documentation

Always build the API docs for any new major and minor releases.

1. Navigate to the `mongo-java-driver` repo (*not* the submodule in this repo).
2. Check out the tag that corresponds to this release. Example for v5.6:

```sh
git checkout r5.6.0
```
You will be in a detached HEAD state. This is fine.

3. Turn off Cloudflare WARP, then run this `gradlew` command:

```sh
./gradlew clean docs
```

If the build fails at :mongodb-crypt:verifyCryptLibs with a GPG error, skip GPG signature verification by running the following command:

```sh
./gradlew clean docs -PskipCryptVerify=true
```

4. Navigate to the `mongo-java-driver` submodule: `cd docs-java-other/mongo-java-driver`
5. Create the `<version>/apidocs` directory. Do *not* include the patch number in the folder name. Example for v5.6:
   
```sh
mkdir -p 5.6/apidocs
```

6. Copy the `build/docs` folder into the `apidocs` directory. For example,
if the `mongo-java-driver` repo is on a sibling level with this repo, run the following command:

```sh
cp -a ../mongo-java-driver/build/docs/ ./mongo-java-driver/<version>/apidocs
```

Your submodule directory should contain a directory structure that resembles the following:

```sh
<this repo>/<submodule directory>/<version>/apidocs/{bson,mongodb-driver-core,mongodb-driver-sync,mongodb-driver-legacy, mongodb-driver-reactivestreams/}
```

## Publishing

1. In your `mongo-java-driver` submodule, check out the `gh-pages` branch.
2. Run the following commands:

```sh
git add .
git commit -m <message>
git rebase -i --root (you can quit out of this)
```
3. If you see a warning, resolve it. For example, if the `specifications` directory couldn't be removed, remove it manually: `rm -rf driver-core/src/test/resources/specifications`
4. Push directly to the upstream repo: `git push origin gh-pages -f`. This will trigger a deploy.

