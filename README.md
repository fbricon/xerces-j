
# Xerces Java Build Instructions

This document contains instructions to build Xerces-J.

Before building Xerces-J, users need the source package and tools package 
available from the [Apache Xerces-J project's distribution web page](https://xerces.apache.org/xerces2-j/).

To build a particular released version, download both 
the Xerces-J-src.X.Y.Z.zip and Xerces-J-tools.X.Y.Z.zip 
files for that Xerces-J release (where "X.Y.Z" is the version
number) and extract them in the same directory. If you're using a Unix 
variant like Linux, download the equivalent .tar.gz files instead of 
the .zip files.

To build from head, clone this GitHub repository. 

You also need to have the Java Development Kit (JDK) version 1.8 or
higher installed on your system. The latest Xerces-J codebase on this GitHub 
repo has Java language maximum source/target level requirement of 1.8.
 
Before initiating any part of the build, set the JAVA_HOME environment 
variable to the installation directory of your JDK.

[Ant](https://ant.apache.org/) 1.10.2 or later is needed to build everything in Xerces-J, including
the documentation. This tool, and the others needed (besides the
pre-requisite JDK) are contained within the tools package. To
make building Xerces-J packages easier, a Windows batch file and a Linux 
shell script are included.

If you only want to compile the source code and make the JAR files,
run the following command on Windows:

```
build.bat jars
```

or from Linux (make sure that build.sh is executable):

```
./build.sh jars
```

This compiles all the source code and generates the JAR
files that are available as part of the binary package. After
these Xerces-J builds, the build results are located in the "build" directory.

If all results of Xerces-J build are needed, including the documentation,
run the build batch file or shell script specifying an Ant build "all" 
target instead of "jars".

## Building with Maven

A Maven build is available in addition to the Ant build. It produces the
same jars and uses the same compiler settings. The Maven wrapper downloads
a suitable Maven version automatically:

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

The file names in `target/` follow the Ant-style `finalName`. When the
artifacts are installed or deployed, Maven adds the project version and
classifier automatically, for example `xercesImpl-<version>.jar`,
`xercesImpl-<version>-samples.jar`, `xercesImpl-<version>-sources.jar`
and `xercesImpl-<version>-javadoc.jar`.

To compile and run the JUnit tests, use `./mvnw verify` (`./mvnw test`
skips packaging).

The Maven build targets Java 8 by default because current JDKs (20 and
later) can no longer target Java 7. On JDK 19 or earlier you can match the
Ant build's default target with:

```
./mvnw clean package -Djavac.source=1.7 -Djavac.target=1.7
```

### Optional Maven profiles

The optional Ant targets are available as Maven profiles. Profiles can be
combined, for example `-Pdocs,javadocs`.

| Ant target | Maven profile | Result |
| --- | --- | --- |
| `jars` | `-Pjars` | `target/xercesImpl.jar`, `xercesImpl-samples.jar` and the third-party jars |
| `dtdjars` | `-Pdtd` | `target/dtd-xercesImpl.jar` |
| `dvjar` | `-Pdv` | `target/xercesDV.jar` |
| `jars-schema11` | `-Pschema11` | `target/schema11-xercesImpl.jar` |
| `xjavac-jar` | `-Pxjavac` | `target/xjavac.jar` |
| `deprecatedjars` | `-Pdeprecated` | `target/xerces.jar` |
| `docs` | `-Pdocs` | `target/docs/` |
| `javadocs` | `-Pjavadocs` | `target/docs/javadocs/` |
| `pack-src` | `-Ppack-src` | `target/Xerces-J-src.<version>.{zip,tar.gz}` |
| `pack-tools` | `-Ppack-tools` | `target/Xerces-J-tools.<version>.{zip,tar.gz}` |
| `all` | `-Pdist` | source, binary and tools archives |
| `deprecatedall` | `-Pdeprecated-dist` | deprecated binary archive |

The distribution profiles resolve `xml-apis`, `xml-resolver` and Xalan
`serializer` from Maven Central and stage them under `target/lib`, rather
than copying the checked-in `tools/*.jar` files. Those checked-in jars are
still used by the Ant build, and `tools/xml-commons-external-src.zip` is
still used for the Schema 1.1 and javadoc source paths. The `dist` and
`deprecated-dist` profiles run at the `verify` phase, because they need the
jars produced by `package`:

```
./mvnw clean verify -Pdist
```

If the zip/tar archive did not preserve the executable bit on `mvnw`, run
`sh ./mvnw` instead.

Two Ant packaging bugs are fixed in the Maven distribution: the binary
archive includes `NOTICE` and the DOM license files, and it includes the
`xs` javadoc set. The deprecated binary archive also ships `xml-apis.jar`,
because the current `xerces.jar` no longer bundles the XML APIs.

The XML conformance suite targets (`get-conftests` and `run-conftests`) are
not ported: they depend on the retired SourceForge CVS service and on the
`xmlconf` suite being checked out under `tests/conformance`.

We use the [JProfiler](https://www.ej-technologies.com/jprofiler) tool for Java software run-time 
analysis and optimization of Xerces-J software.
