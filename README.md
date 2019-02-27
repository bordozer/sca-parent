# My parent gradle script 

Parent gradle script contains static code analysis (SCA) tools' configuration and common dependencies

### Prerequisites

```
- Java 1.8.0 or later
- Gradle 4.6 or later
```

### Installing

Clone repo

```
git clone https://github.com/bordozer/sca-parent
```

Enter project directory

`````
cd sca-parent
`````
Install to local maven repo
`````
./gradlew clean build install
`````
Add to gradle build script in a project you would like to use SCA:

```
buildscript {
    repositories {
        mavenLocal()
        mavenCentral()
        maven {
            url "https://plugins.gradle.org/m2/"
        }
    }
    dependencies {
        classpath "gradle.plugin.com.github.spotbugs:spotbugs-gradle-plugin:1.6.9"
        classpath 'com.fizzpod:gradle-pater-build-plugin:2.0.0'
        classpath 'com.bordozer.commons:parent:3.02'
        classpath 'org.reflections:reflections:0.9.11'
        classpath 'org.apache.commons:commons-io:1.3.2'
    }
}
apply plugin: 'com.fizzpod.pater-build'

/* SCA configuration */
ext {
    /* Change this source to your src or particular package */
    scaSource = 'src/main/java/com/pru/aum'
}
checkstyleMain {
    source = scaSource
}
pmdMain {
    source = scaSource
}
// SpotBugs does not support source definition
```
This adds to a project SCA gradle tasks

Checkstyle
-----
Checkstyle (http://checkstyle.sourceforge.net/) is a development tool to help programmers write Java code that adheres to a coding standard. It automates the process of checking Java code to 
spare humans of this boring (but important) task. This makes it ideal for projects that want to enforce a coding standard.

Gradle tasks:
* **checkstyleMain** - runs Checkstyle against production code base
* **checkstyleTest** - runs Checkstyle against test code base

PMD
-----
PMD (https://pmd.github.io/) is a source code analyzer. It finds common programming flaws like unused variables, empty catch blocks, unnecessary object 
creation, copy-paste-detector and so forth.

Gradle tasks:
* **pmdMain** - runs PMD against production code base
* **pmdTest** - runs PMD against test code base

SpotBugs
-----
SpotBugs (https://spotbugs.github.io/) is a program which uses static analysis to look for bugs in Java code. Former FindBugs.

Gradle tasks:
* **spotbugsMain** - runs SpotBugs against production code base
* **spotbugsTest** - runs SpotBugs against test code base (*disabled in the project*)

All gradle SCA tacks depend on *check* task so are going to fail a build on a project where SCA violation are not fixed yet. 
So all SCA gradle tasks are disabled by default.
To enable add to build.gradle:

```
tasks.withType(Checkstyle) { task -> enabled = true }
```
```
tasks.withType(Pmd) { task -> enabled = true }
```
```
spotbugsMain.enabled = true
```
To prevent lombok violation place *lombok.config* file to the project src
 ```
lombok.log.fieldName=LOGGER
lombok.extern.findbugs.addSuppressFBWarnings = true
 ```

-----
Also common dependencies are included (lombok, JUnit5, mockito, assertj, etc):
```
dependencies {
    compile group: 'com.github.spotbugs', name: 'spotbugs', version: spotbugsVersion
    compileOnly group: 'com.github.spotbugs', name: 'spotbugs-annotations', version: spotbugsVersion
    compile group: 'com.google.code.findbugs', name: 'jsr305', version: '3.0.2'
    compileOnly  group: 'net.jcip', name: 'jcip-annotations', version: '1.0'

    compile group: 'org.projectlombok', name: 'lombok', version: lombockVersion

    testCompile group: 'org.junit.jupiter', name: 'junit-jupiter-api', version: junit5Version
    testCompile group: 'org.junit.jupiter', name: 'junit-jupiter-params', version: junit5Version
    testCompile group: 'org.junit.jupiter', name: 'junit-jupiter-engine', version: junit5Version
    testCompile group: 'org.junit.vintage', name: 'junit-vintage-engine', version: junit5Version
    testCompile group: 'org.junit.platform', name: 'junit-platform-launcher', version: junitPlatformLauncherVersion
    testCompile group: 'org.mockito', name: 'mockito-core', version: mockitoVersion
    testCompile group: 'org.mockito', name: 'mockito-junit-jupiter', version: mockitoVersion
    testCompile group: 'org.assertj', name: 'assertj-core', version: '3.11.1'
}
```

To enable JUnit5 test add to a your project:
```
test {
    useJUnitPlatform()
    testLogging {
        events 'PASSED', 'FAILED', 'SKIPPED'
    }
}
test.testLogging.showStandardStreams = true
```



## Authors

* **Borys Lukianov**

