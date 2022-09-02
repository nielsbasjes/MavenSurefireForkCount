# Maven Bugreport

Context:
- Ubuntu 20.04 LTS

- Maven 3.8.3
```bash
$ mvn -version
Apache Maven 3.8.3 (ff8e977a158738155dc465c6a97ffaf31982d739)
Maven home: /home/nbasjes/Tools/apache-maven-3.8.3
Java version: 11.0.16, vendor: Ubuntu, runtime: /usr/lib/jvm/java-11-openjdk-amd64
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "5.15.0-46-generic", arch: "amd64", family: "unix"
```
- Java 8 installed (not active)
- Java 17 installed (not active)
- Java 11 installed (ACTIVE):
```bash
$ java -version
openjdk version "11.0.16" 2022-07-19
OpenJDK Runtime Environment (build 11.0.16+8-post-Ubuntu-0ubuntu120.04)
OpenJDK 64-Bit Server VM (build 11.0.16+8-post-Ubuntu-0ubuntu120.04, mixed mode, sharing)
```

```bash
$ javac -version
javac 11.0.16
```

```bash
$ echo $JAVA_HOME
/usr/lib/jvm/java-11-openjdk-amd64/
```

Toolchains to make them all available to maven:
```xml
$ cat ~/.m2/toolchains.xml
<?xml version="1.0" encoding="UTF8"?>
<toolchains>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>8</version>
    </provides>
    <configuration>
      <jdkHome>/usr/lib/jvm/java-8-openjdk-amd64</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>11</version>
    </provides>
    <configuration>
      <jdkHome>/usr/lib/jvm/java-11-openjdk-amd64</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>17</version>
    </provides>
    <configuration>
      <jdkHome>/usr/lib/jvm/java-17-openjdk-amd64</jdkHome>
    </configuration>
  </toolchain>
</toolchains>
```

# The problem:
Somehow the maven-surefire-plugin behaves differently when running mutliple forks. It seems to not follow the defined toolchains in all cases.

## ForkCount = 1 
```bash
$ mvn clean test -DforkCount=1
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------< nl.basjes.bugreports:toolchainer >------------------
[INFO] Building toolchainer 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ toolchainer ---
[INFO] Deleting /home/nbasjes/workspace/Prive/BugReports/SurefireForks/target
[INFO] 
[INFO] --- maven-toolchains-plugin:3.1.0:toolchain (default) @ toolchainer ---
[INFO] Required toolchain: jdk [ version='17' ]
[INFO] Found matching toolchain for type jdk: JDK[/usr/lib/jvm/java-17-openjdk-amd64]
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ toolchainer ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /home/nbasjes/workspace/Prive/BugReports/SurefireForks/src/main/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.10.1:compile (default-compile) @ toolchainer ---
[INFO] Toolchain in maven-compiler-plugin: JDK[/usr/lib/jvm/java-17-openjdk-amd64]
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to /home/nbasjes/workspace/Prive/BugReports/SurefireForks/target/classes
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ toolchainer ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /home/nbasjes/workspace/Prive/BugReports/SurefireForks/src/test/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.10.1:testCompile (default-testCompile) @ toolchainer ---
[INFO] Toolchain in maven-compiler-plugin: JDK[/usr/lib/jvm/java-17-openjdk-amd64]
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to /home/nbasjes/workspace/Prive/BugReports/SurefireForks/target/test-classes
[INFO] 
[INFO] --- maven-surefire-plugin:3.0.0-M7:test (default-test) @ toolchainer ---
[INFO] Toolchain in maven-surefire-plugin: JDK[/usr/lib/jvm/java-17-openjdk-amd64]
[INFO] Using auto detected provider org.apache.maven.surefire.junitplatform.JUnitPlatformProvider
[INFO] 
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running nl.basjes.bugreport.TestApp
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.033 s - in nl.basjes.bugreport.TestApp
[INFO] 
[INFO] Results:
[INFO] 
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.082 s
[INFO] Finished at: 2022-09-02T17:15:55+02:00
[INFO] ------------------------------------------------------------------------
```

## ForkCount = 2
```bash
$ mvn clean test -DforkCount=2
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------< nl.basjes.bugreports:toolchainer >------------------
[INFO] Building toolchainer 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ toolchainer ---
[INFO] Deleting /home/nbasjes/workspace/Prive/BugReports/SurefireForks/target
[INFO] 
[INFO] --- maven-toolchains-plugin:3.1.0:toolchain (default) @ toolchainer ---
[INFO] Required toolchain: jdk [ version='17' ]
[INFO] Found matching toolchain for type jdk: JDK[/usr/lib/jvm/java-17-openjdk-amd64]
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ toolchainer ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /home/nbasjes/workspace/Prive/BugReports/SurefireForks/src/main/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.10.1:compile (default-compile) @ toolchainer ---
[INFO] Toolchain in maven-compiler-plugin: JDK[/usr/lib/jvm/java-17-openjdk-amd64]
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to /home/nbasjes/workspace/Prive/BugReports/SurefireForks/target/classes
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ toolchainer ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /home/nbasjes/workspace/Prive/BugReports/SurefireForks/src/test/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.10.1:testCompile (default-testCompile) @ toolchainer ---
[INFO] Toolchain in maven-compiler-plugin: JDK[/usr/lib/jvm/java-17-openjdk-amd64]
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to /home/nbasjes/workspace/Prive/BugReports/SurefireForks/target/test-classes
[INFO] 
[INFO] --- maven-surefire-plugin:3.0.0-M7:test (default-test) @ toolchainer ---
[INFO] Toolchain in maven-surefire-plugin: JDK[/usr/lib/jvm/java-17-openjdk-amd64]
[INFO] Using auto detected provider org.apache.maven.surefire.junitplatform.JUnitPlatformProvider
[INFO] 
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] 
[INFO] Results:
[INFO] 
[INFO] Tests run: 0, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.845 s
[INFO] Finished at: 2022-09-02T17:17:15+02:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:3.0.0-M7:test (default-test) on project toolchainer: Execution default-test of goal org.apache.maven.plugins:maven-surefire-plugin:3.0.0-M7:test failed: java.lang.UnsupportedClassVersionError: nl/basjes/bugreport/TestApp has been compiled by a more recent version of the Java Runtime (class file version 61.0), this version of the Java Runtime only recognizes class file versions up to 55.0 -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/PluginExecutionException
```


