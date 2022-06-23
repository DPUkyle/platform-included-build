platform-inclued-build
----------------------

A toy repository to show the ease of using Gradle's composite builds using a platform.

In this case, there are two independent projects. `platfom` is the upstream "producer" project, which publishes two libraries as well as a BOM.  The BOM is a "platform" in Gradle terms, providing a set of "blessed" dependencies, including version numbers.

`app` is the downstream "consumer" project.  It contains a single reference to the upstream platform's BOM, where it is imported using the version number.  Any other member library of the BOM can now be refereced by its "group:artifact" pair, omitting the version number.  The version will be inferred by matching the "group:artifact" pair from the platform.

When the `includeBuild` directive is used in the consuming project's `settings.gradle` file, Gradle will build the "live" code from the upstream platform project.  This allows "real time" changes to the upstream, and immediate visualization of their consequences on the consumer.

Steps to reproduce:

1. clone this project
2. `$ cd platform`
3. `$ ./gradlew publishToMavenLocal` -> publishes all jars and metadata to `~/.m2/repository/com/acme/platform/...`
4. `$ cd ../app`
5. `$ ./gradlew :app-war:build`

After step 5, the app builds successfully.  JARs from the upstream `platform` project are sources from `~/.m2/repository`.

Now to enable included builds, which builds the upstream `platform` code ad hoc.

1. Add/uncomment the line `includeBuild '../platform'` in `app/settings.gradle`
2. `$ ./gradlew :app-war:build`