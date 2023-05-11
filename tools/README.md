# Docker Image for Hugo v0.25.x Legacy JVM Driver Documentation Builds

## Background

The legacy documentation for MongoDB JVM-based drivers (Java Sync, Java RS, and Scala)
were built with an older version of [``hugo``](https://github.com/gohugoio/hugo) and were
hosted on GitHub Pages. Since the Java RS and Scala driver documentation hasn't been
transitioned to MongoDB's Snooty platform yet, we need to continue to publish the new
documentation for each major and minor version release using the old tools.

The documentation is built with the static site generator, ``hugo``. However, the
pages were built long ago and did not keep up with the breaking changes in template syntax.
Rather than attempt to update the syntax, the teams continued to use v0.25.x.

When the company transitioned from Intel hardware to M1 Macs which use the "AArch64"
architecture, the ``hugo`` binaries stopped working, even under emulation. While 
the maintainers for ``hugo`` started building binaries for the architecture, the latest
one for v0.25.x binary is not compatible. 

However, it does run on a Linux environment on Docker. Therefore, we need to use
Docker to build the legacy reference documentation for these drivers for every
major and minor release until the team can transition the contents to the Snooty
platform.

## Setup Instructions

1. To build the Docker image, run the following command:

```sh
docker build -t ccho-mongodb/hugo-legacy-build .
```

2. To start an interactive shell in the Docker container built from the image, run the following command:

```sh
docker run -ti ccho-mongodb/hugo-legacy-build /bin/bash
```

3. Once in the container's shell, you can run the ``publish-docs`` script to generate the reference documentation for a new version of the drivers. See the [README in docs-java-other](https://github.com/mongodb/docs-java-other/blob/main/README.md) for more information.

:warning: **It's not recommended to attempt any other builds on the Docker container other than the reference documentation. Instead, use [``docker cp``](https://docs.docker.com/engine/reference/commandline/cp/) or similar mechanism to move files from your Docker container to the ``mongo-java-driver`` submodule of ``docs-java-other`` in your local environment.**

:warning: **While ``docker cp`` is recommended for managing changes, you can set up a [GitHub personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) to push and pull commits from the Docker container.**
