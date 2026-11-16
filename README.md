
# Xerces Java Build Instructions

This document contains instructions to build Xerces-J.

To build a particular released version, download the
Xerces-J-src.X.Y.Z.zip file for that Xerces-J release (where "X.Y.Z" is the
version number). If you're using a Unix variant like Linux, download the
equivalent .tar.gz file instead of the .zip file.

To build from head, clone this GitHub repository.

You need to have the Java Development Kit (JDK) version 1.8 or higher installed
on your system. Before initiating any part of the build, set the JAVA_HOME
environment variable to the installation directory of your JDK.

## Building with Maven

The Maven wrapper downloads a suitable Maven version automatically, so no
separate Maven installation is required:

```
./mvnw clean package
```

The results are:

```
target/xercesImpl.jar
target/xercesImpl-samples.jar
target/xercesImpl-sources.jar
target/xercesImpl-javadoc.jar
```

The file names in `target/` follow the `finalName` configured in the POM. When
the artifacts are installed or deployed, Maven adds the project version and
classifier automatically, for example `xercesImpl-<version>.jar`,
`xercesImpl-<version>-samples.jar`, `xercesImpl-<version>-sources.jar` and
`xercesImpl-<version>-javadoc.jar`.

To compile and run the JUnit tests, use `./mvnw verify` (`./mvnw test` skips
packaging).

The Maven build targets Java 8 by default because current JDKs (20 and
later) can no longer target Java 7. On JDK 19 or earlier you can target
Java 7 with:

```
./mvnw clean package -Djavac.source=1.7 -Djavac.target=1.7
```

### Source layout

The build uses the standard Maven layout:

| Directory | Content |
| --- | --- |
| `src/main/java` | parser sources |
| `src/main/resources` | message bundles, service registrations and other resources |
| `src/main/java-templates` | source templates filtered at build time |
| `src/main/manifest` | jar manifest template |
| `src/main/services` | alternative `META-INF/services` registrations used by the `dtd` profile |
| `src/test/java` | JUnit tests |
| `src/test/resources` | test data |
| `samples` | sample applications, packaged as the `samples` classifier |

### Optional Maven profiles

Optional build outputs are available as Maven profiles. Profiles can be
combined, for example `-Pdocs,javadocs`.

| Profile | Result |
| --- | --- |
| `-Pjars` | `target/xercesImpl.jar`, `xercesImpl-samples.jar` and the third-party jars |
| `-Pdtd` | `target/dtd-xercesImpl.jar` |
| `-Pdv` | `target/xercesDV.jar` |
| `-Pschema11` | `target/schema11-xercesImpl.jar` |
| `-Pxjavac` | `target/xjavac.jar` |
| `-Pdeprecated` | `target/xerces.jar` |
| `-Pdocs` | `target/docs/` |
| `-Pjavadocs` | `target/docs/javadocs/` |
| `-Ppack-src` | `target/Xerces-J-src.<version>.{zip,tar.gz}` |
| `-Ppack-tools` | `target/Xerces-J-tools.<version>.{zip,tar.gz}` |
| `-Pdist` | source, binary and tools archives |
| `-Pdeprecated-dist` | deprecated binary archive |

The distribution profiles resolve `xml-apis`, `xml-resolver` and Xalan
`serializer` from Maven Central and stage them under `target/lib`, rather
than copying the checked-in `tools/*.jar` files. The `dist` and
`deprecated-dist` profiles run at the `verify` phase, because they need the
jars produced by `package`:

```
./mvnw clean verify -Pdist
```

If the zip/tar archive did not preserve the executable bit on `mvnw`, run
`sh ./mvnw` instead.

The `javadocs` and `xjavac` profiles still read their inputs from the tools
package (`tools/xml-commons-external-src.zip`, `tools/bin/xerces2taglets.jar`
and `tools/src/XJavac.java`). Download the Xerces-J-tools.X.Y.Z.zip package and
extract it next to the source package if you need those profiles.

The distribution fixes two bugs from the historical Ant packaging: the binary
archive includes `NOTICE` and the DOM license files, and it includes the
`xs` javadoc set. The deprecated binary archive also ships `xml-apis.jar`,
because the current `xerces.jar` no longer bundles the XML APIs.

The XML conformance suite is not part of the build: it depends on the retired
SourceForge CVS service and on an `xmlconf` checkout under `tests/conformance`.

We use the [JProfiler](https://www.ej-technologies.com/jprofiler) tool for Java software run-time 
analysis and optimization of Xerces-J software.
