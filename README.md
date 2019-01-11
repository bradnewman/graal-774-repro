# Reproducer for GraalVM #774 #

Reproduces [oracle/graal#774](https://github.com/oracle/graal/issues/774) with GraalVM RC9 and RC10; produces a working binary with GraalVM RC8.

Based on the Kraal example project.

## Building ##

With GraalVM installed locally:

    ./gradlew build
    native-image \
        --static \
        --report-unsupported-elements-at-runtime \
        -jar build/fatjar/example.jar
    ./example

Or, using a Docker container with GraalVM and building the example webapp into a 26MB Docker image:

    ./gradlew build
    docker build -f Dockerfile -t kraal-example build/fatjar
    docker run --rm -P kraal-example
