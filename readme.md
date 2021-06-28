# Apache Maven 

Building a software project typically consists of such tasks as downloading dependencies, 
putting additional jars on a classpath, compiling source code into binary code, running tests, 
packaging compiled code into deployable artifacts such as JAR, WAR, and ZIP files, 
and deploying these artifacts to an application server or repository.

Apache Maven automates these tasks, minimizing the risk of humans making errors while 
building the software manually and separating the work of compiling and packaging our code 
from that of code construction.

In this tutorial, we're going to explore this powerful tool for describing, building, and managing 
Java software projects using a central piece of information — the Project Object Model (POM) — that is written in XML.

## Why Use Maven?
The key features of Maven are:
* **simple project setup that follows best practices:** Maven tries to avoid as much configuration as possible,
  by supplying project templates (named archetypes)
* **dependency management:** it includes automatic updating, downloading and validating the 
  compatibility, as well as reporting the dependency closures (known also as transitive dependencies)
* **isolation between project dependencies and plugins:** with Maven, project dependencies are 
  retrieved from the dependency repositories while any plugin's dependencies are retrieved from 
  the plugin repositories, resulting in fewer conflicts when plugins start to download additional dependencies
* **Central repository system:** project dependencies can be loaded from the local file system or
  public repositories, such as Maven Central

## Project Object Model
The configuration of a Maven project is done via a Project Object Model (POM), represented by a pom.xml file. 
The POM describes the project, manages dependencies, and configures plugins for building the software.

The POM also defines the relationships among modules of multi-module projects. Let's look at the basic 
structure of a typical POM file:

```xml
<project>
    
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.sudipcold</groupId>
    <artifactId>demo-artifact</artifactId>
    <packaging>jar</packaging>
    <version>1.0-SNAPSHOT</version>
    
    <name>Demo Artifact</name>
    
    <url>http://maven.apache.org</url>
    
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>           
            //...
            </plugin>
        </plugins>
    </build>
</project>
```

## Project Identifiers
Maven uses a set of identifiers, also called coordinates, to uniquely identify a project and specify 
how the project artifact should be packaged:

* **groupId** – a unique base name of the company or group that created the project
* **artifactId** – a unique name of the project
* **version** – a version of the project
* **packaging** – a packaging method (e.g. WAR/JAR/ZIP)

The first three of these (groupId:artifactId:version) combine to form the unique identifier and are the 
mechanism by which you specify which versions of external libraries (e.g. JARs) your project will use.

## Dependencies
These external libraries that a project uses are called dependencies. The dependency management feature 
in Maven ensures automatic download of those libraries from a central repository, so you don't 
have to store them locally.

This is a key feature of Maven and provides the following benefits:

* **Uses less storage by significantly reducing the number of downloads off remote repositories**
* **Makes checking out a project quicker**
* **Provides an effective platform for exchanging binary artifacts within your organization and 
  beyond without the need for building artifact from source every time**
  
In order to declare a dependency on an external library, you need to provide the groupId, artifactId, 
and the version of the library. Let's take a look at an example:
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>4.3.5.RELEASE</version>
</dependency>
```
As Maven processes the dependencies, it will download Spring Core library into your local Maven repository.

## Repositories
A repository in Maven is used to hold build artifacts and dependencies of varying types. The default local repository 
is located in the .m2/repository folder under the home directory of the user.

If an artifact or a plug-in is available in the local repository, Maven uses it. Otherwise, 
it is downloaded from a central repository and stored in the local repository. The default central 
repository is Maven Central.

Some libraries, such as JBoss server, are not available at the central repository but are available 
at an alternate repository. For those libraries, you need to provide the URL to the alternate 
repository inside `pom.xml` file:
```xml
<repositories>
    <repository>
        <id>JBoss repository</id>
        <url>http://repository.jboss.org/nexus/content/groups/public/</url>
    </repository>
</repositories>
```
**Please note that you can use multiple repositories in your projects.**

## Properties
Custom properties can help to make your pom.xml file easier to read and maintain. 
In the classic use case, you would use custom properties to define versions for your project's dependencies.

Maven properties are value-placeholders and are accessible anywhere within a `pom.xml` by 
using the notation `${name}`, where name is the property.

Let's see an example:
```xml
<properties>
    <spring.version>4.3.5.RELEASE</spring.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
    </dependency>
</dependencies>
```
Now if you want to upgrade Spring to a newer version, you only have to change 
the value inside the `<spring.version>` property tag and all the dependencies using that property 
in their `<version>` tags will be updated.

Properties are also often used to define build path variables:
```xml
<properties>
    <project.build.folder>${project.build.directory}/tmp/</project.build.folder>
</properties>

<plugin>
    //...
    <outputDirectory>${project.resources.build.folder}</outputDirectory>
    //...
</plugin>
```

## Build
The build section is also a very important section of the Maven POM. It provides information about the default Maven goal, the directory for the compiled project, 
and the final name of the application. The default build section looks like this:
```xml
<build>
    <defaultGoal>install</defaultGoal>
    <directory>${basedir}/target</directory>
    <finalName>${artifactId}-${version}</finalName>
    <filters>
      <filter>filters/filter1.properties</filter>
    </filters>
    //...
</build>
```
The default output folder for compiled artifacts is named **target**, and the final name of the packaged artifact 
consists of the **artifactId and version**, but you can change it at any time.

## Using Profiles
Another important feature of Maven is its support for profiles. A profile is basically a set of configuration values. 
By using profiles, you can customize the build for different environments such as `Production/Test/Development`:
```xml
<profiles>
    
    <profile>
        <id>production</id>
        <build>
            <plugins>
                <plugin>
                //...
                </plugin>
            </plugins>
        </build>
    </profile>
    
    <profile>
        <id>development</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <build>
            <plugins>
                <plugin>
                //...
                </plugin>
            </plugins>
        </build>
     </profile>
     
 </profiles>
```
As you can see in the example above, the default profile is set to development. If you want to run the 
production profile, you can use the following Maven command: `mvn clean install -Pproduction`

## Generating a Simple Java Project
In order to build a simple Java project, let's run the following command:

```
 mvn archetype:generate
    -DgroupId=com.sudipcold
    -DartifactId=demo-test
    -DarchetypeArtifactId=maven-archetype-quickstart
    -DinteractiveMode=false
```
The groupId is a parameter indicating the group or individual that created a project, which is often a reversed 
company domain name. The artifactId is the base package name used in the project, and we use the standard archetype.

Since we didn't specify the version and the packaging type, these will be set to 
default values — the version will be set to 1.0-SNAPSHOT, and the packaging will be set to jar.

If you don't know which parameters to provide, you can always specify `interactiveMode=true`, so that Maven asks for all the required parameters.

After the command completes, we have a Java project containing an App.java class, which is just a simple “Hello World” program, in the src/main/java folder.

We also have an example test class in src/test/java. The pom.xml of this project will look similar to this:
```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.sudipcold</groupId>
    <artifactId>demo-test</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <packaging>jar</packaging>
    
    <name>Demo Test</name>
    
    <url>http://maven.apache.org</url>
    
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.1.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```
## Compiling and Packaging a Project
The next step is to compile the project:

`mvn compile`
Maven will run through all lifecycle phases needed by the compile phase to build the project's sources. 
If you want to run only the test phase, you can use:

`mvn test`
Now let's invoke the package phase, which will produce the compiled archive jar file:

`mvn package`

## Executing an Application
Finally, we are going to execute our Java project with the exec-maven-plugin.
Let's configure the necessary plugins in the pom.xml:
```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <plugins>
        <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.6.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>3.0.0</version>
            <configuration>
                <mainClass>org.baeldung.java.App</mainClass>
            </configuration>
        </plugin>
    </plugins>
</build>
```
The first plugin, maven-compiler-plugin, is responsible for compiling the source code using Java version 1.8. 
The exec-maven-plugin searches for the mainClass in our project.

To execute the application, we run the following command: `mvn exec:java`

---
## Multi-Module Projects
The mechanism in Maven that handles multi-module projects (also called aggregator projects) is called Reactor.

The Reactor collects all available modules to build, then sorts projects into the correct build order, 
and finally, builds them one by one.

Let's see how to create a multi-module parent project.

### 1. Create Parent Project
First of all, we need to create a parent project. In order to create a new project with the name parent-project, 
we use the following command:

`mvn archetype:generate -DgroupId=org.baeldung -DartifactId=parent-project`
Next, we update the packaging type inside the pom.xml file to indicate that this is a parent module:

`<packaging>pom</packaging>`
### 2. Create Submodule Projects
In the next step, we create submodule projects from the directory of parent-project:
``` 
cd parent-project
mvn archetype:generate -DgroupId=org.baeldung  -DartifactId=core
mvn archetype:generate -DgroupId=org.baeldung  -DartifactId=service
mvn archetype:generate -DgroupId=org.baeldung  -DartifactId=webapp
```
To verify if we created the submodules correctly, we look in the parent-project pom.xml file, 
where we should see three modules:

```xml
<modules>
    <module>core</module>
    <module>service</module>
    <module>webapp</module>
</modules> 
```
Moreover, a parent section will be added in each submodule’s pom.xml:
```xml
<parent>
    <groupId>org.baeldung</groupId>
    <artifactId>parent-project</artifactId>
    <version>1.0-SNAPSHOT</version>
</parent>
```
### 3. Enable Dependency Management in Parent Project
Dependency management is a mechanism for centralizing the dependency information for a muti-module 
parent project and its children.

When you have a set of projects or modules that inherit a common parent, you can put all the required 
information about the dependencies in the common pom.xml file. This will simplify the references to the 
artifacts in the child POMs.

Let's take a look at a sample parent's pom.xml:
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>4.3.5.RELEASE</version>
        </dependency>
        //...
    </dependencies>
</dependencyManagement>
```
By declaring the spring-core version in the parent, all submodules that depend on spring-core can declare 
the dependency using only the groupId and artifactId, and the version will be inherited:
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
    </dependency>
    //...
</dependencies>
```
Moreover, you can provide exclusions for dependency management in parent's pom.xml, so that specific 
libraries will not be inherited by child modules:
```xml
<exclusions>
    <exclusion>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
    </exclusion>
</exclusions>
```
Finally, if a child module needs to use a different version of a managed dependency, you can override 
the managed version in child's pom.xml file:
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>4.2.1.RELEASE</version>
</dependency>
```
**Please note that while child modules inherit from their parent project, a parent project does not 
necessarily have any modules that it aggregates. On the other hand, a parent project may 
also aggregate projects that do not inherit from it.**

### 4.Updating the Submodules and Building a Project
We can change the packaging type of each submodule. For example, let's change the packaging of the 
webapp module to WAR by updating the pom.xml file:

<packaging>war</packaging>
Now we can test the build of our project by using the mvn clean install command. 
The output of the Maven logs should be similar to this:
```
[INFO] Scanning for projects...
[INFO] Reactor build order:
[INFO]   parent-project
[INFO]   core
[INFO]   service
[INFO]   webapp
//.............
[INFO] -----------------------------------------
[INFO] Reactor Summary:
[INFO] -----------------------------------------
[INFO] parent-project .................. SUCCESS [2.041s]
[INFO] core ............................ SUCCESS [4.802s]
[INFO] service ......................... SUCCESS [3.065s]
[INFO] webapp .......................... SUCCESS [6.125s]
[INFO] -----------------------------------------
```

---
## Apache Maven Standard Directory Layout
Apache Maven is one of the most popular build tools for Java projects. Apart from just decentralizing dependencies 
and repositories, promoting a uniform directory structure across projects is also one of its important aspects.

### Directory Layout
```
└───maven-project
    ├───pom.xml
    ├───README.txt
    ├───NOTICE.txt
    ├───LICENSE.txt
    └───src
        ├───main
        │   ├───java
        │   ├───resources
        │   ├───filters
        │   └───webapp
        ├───test
        │   ├───java
        │   ├───resources
        │   └───filters
        ├───it
        ├───site
        └───assembly
```
The default directory layout can be overridden using project descriptors, but this is uncommon and discouraged.

### The Root Directory
* **maven-project/pom.xml** – defines dependencies and modules needed during the build lifecycle of a Maven project
* **maven-project/LICENSE.txt** – licensing information of the project
* **maven-project/README.txt** – summary of the project
* **maven-project/NOTICE.txt** – information about third-party libraries used in the project
* **maven-project/src/main** – contains source code and resources that become part of the artifact
* **maven-project/src/test** – holds all the test code and resources
* **maven-project/src/it** – usually reserved for integration tests used by the Maven Failsafe Plugin
* **maven-project/src/site** – site documentation created using the Maven Site Plugin
* **maven-project/src/assembly** – assembly configuration for packaging binaries

### The src/main Directory
As the name indicates, src/main is the most important directory of a Maven project. 
Anything that is supposed to be part of an artifact, be it a jar or war, should be present here.

Its subdirectories are:

* **src/main/java** – Java source code for the artifact
* **src/main/resources** – configuration files and others such as i18n files, per-environment configuration files, 
  and XML configurations
* **src/main/webapp** – for web applications, contains resources like JavaScript, CSS, HTML files, view templates, 
  and images
* **src/main/filters** – contains files that inject values into configuration properties in the resources folder 
  during the build phase
  
### The src/test Directory
The directory src/test is the place where tests of each component in the application reside.

Note that none of these directories or files will become part of the artifact. Let's see its subdirectories:

* **src/test/java** – Java source code for tests
* **src/test/resources** – configuration files and others used by tests
* **src/test/filters** – contains files that inject values into configuration properties 
in the resources folder during the test phase
  
---

## Where is the Maven Local Repository?

This quick writeup will focus on where Maven stores all the local dependencies locally – which is in the Maven local repository.

Simply put, when we run a Maven build, all the dependencies of our project (jars, plugin jars, other artifacts) are all stored locally for later use.

Also keep in mind that, beyond just this type of local repository, Maven does support three types of repositories:

* **Local** – Folder location on the local Dev machine
* **Central** – Repository provided by Maven community
* **Remote** – Organization owned custom repository

Let's now focus on the local repository.

### The Local Repository
The local repository of Maven is a directory on the local machine, where all the project artifacts are stored.

When a Maven build is executed, Maven automatically downloads all the dependency jars into the local repository.

Usually, this directory is named .m2.

Here's where the default local repository is located based on OS:
```
Windows: C:\Users\<User_Name>\.m2

Linux: /home/<User_Name>/.m2

Mac: /Users/<user_name>/.m2

And of course, for Linux and Mac, we can write in the short form:

~/.m2
```

### Custom Local Repository in settings.xml
If the repo is not present in this default location, it's likely because of some pre-existing configuration.

That config file is located in the Maven installation directory – in a folder called conf – and is named settings.xml.

Here's the relevant configuration that determines the location of our missing local repo:

```xml
<settings>
    <localRepository>C:/maven_repository</localRepository>
    ...
```
That's essentially how we can change the location of the local repo – and of course, 
if that location is changed, we'll no longer find the repo at the default location.

**The files stored in the earlier location will not be moved automatically.**

### Passing Local Repository Location via Command Line
Apart from setting the custom local repository in Maven's settings.xml, the mvn command supports the 
maven.repo.local property, which allows us to pass the local repository location as a command-line parameter:

`mvn -Dmaven.repo.local=/my/local/repository/path clean install`

In this way, we don't have to change Maven's settings.xml

---

## Maven Goals and Phases

### Maven Build Lifecycle
The Maven build follows a specific life cycle to deploy and distribute the target project.

There are three built-in life cycles:
* `default` the main life cycle as it's responsible for project deployment
* `clean` to clean the project and remove all files generated by the previous build
* `site` to create the project's site documentation

Each life cycle consists of a sequence of phases. The **default build life cycle** 
consists of **23 phases** as it's the main build lifecycle.

On the other hand, **clean life cycle** consists of **3 phases**, while the site lifecycle is made up of 4 phases.

### Maven Phase
A Maven phase represents a stage in the Maven build lifecycle. Each phase is responsible for a specific task.

Here are some of the most important phases in the default build lifecycle:

#### Clean Lifecycle
Phase                   | Description
---                     | ---
pre-clean               | execute processes needed prior to the actual project cleaning
clean                   | remove all files generated by the previous build
post-clean              | execute processes needed to finalize the project cleaning

#### Default Lifecycle
Phase                   | Description
---                     | ---
validate                | validate the project is correct and all necessary information is available.
initialize              | initialize build state, e.g. set properties or create directories.
generate-sources        | generate any source code for inclusion in compilation.
process-sources	        | process the source code, for example to filter any values.
generate-resources      | generate resources for inclusion in the package.
process-resources       | copy and process the resources into the destination directory, ready for packaging.compile	compile the source code of the project.
process-classes	        | post-process the generated files from compilation, for example to do bytecode enhancement on Java classes.
generate-test-sources   | generate any test source code for inclusion in compilation.
process-test-sources    | process the test source code, for example to filter any values.
generate-test-resources | create resources for testing.
process-test-resources  | copy and process the resources into the test destination directory.
test-compile            | compile the test source code into the test destination directory
process-test-classes    | post-process the generated files from test compilation, for example to do bytecode enhancement on Java classes.
test                    | run tests using a suitable unit testing framework. These tests should not require the code be packaged or deployed.
prepare-package	        | perform any operations necessary to prepare a package before the actual packaging. This often results in an unpacked, processed version of the package. Package	take the compiled code and package it in its distributable format, such as a JAR.
pre-integration-test    | perform actions required before integration tests are executed. This may involve things such as setting up the required environment.
integration-test        | process and deploy the package if necessary into an environment where integration tests can be run.
post-integration-test   | perform actions required after integration tests have been executed. This may including cleaning up the environment.
verify                  | run any checks to verify the package is valid and meets quality criteria.
install                 | install the package into the local repository, for use as a dependency in other projects locally.
deploy                  | done in an integration or release environment, copies the final package to the remote repository for sharing with other developers and projects.

#### Site Lifecycle
Phase                   | Description
---                     | ---
pre-site                | execute processes needed prior to the actual project site generation
site                    | generate the project's site documentation
post-site               | execute processes needed to finalize the site generation, and to prepare for site deployment
site-deploy             | deploy the generated site documentation to the specified web server

Phases are executed in a specific order. This means that if we run a specific phase using the command:
`mvn <PHASE>` **This won't only execute the specified phase but all the preceding phases as well.**

### Maven Goal 
Each phase is a sequence of goals, and each goal is responsible for a specific task.

When we run a phase – all goals bound to this phase are executed in order.

Here are some of the phases and default goals bound to them:

* compiler:compile – the compile goal from the compiler plugin is bound to the compile phase
* compiler:testCompile is bound to the test-compile phase
* surefire:test is bound to test phase
* install:install is bound to install phase
* jar:jar and war:war is bound to package phase

We can list all goals bound to a specific phase and their plugins using the command:
`mvn help:describe -Dcmd=PHASENAME
`
### Maven Plugin 
A Maven plugin is a group of goals. However, these goals aren't necessarily all bound to the same phase.

For example, here's a simple configuration of the Maven Failsafe plugin which is
responsible for running integration tests:
```xml
<build>
    <plugins>
        <plugin>
            <artifactId>maven-failsafe-plugin</artifactId>
            <version>${maven.failsafe.version}</version>
            <executions>
                <execution>
                    <goals>
                        <goal>integration-test</goal>
                        <goal>verify</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

As we can see, the Failsafe plugin has two main goals configured here:
* integration-test: run integration tests
* verify: verify all integration tests passed

We can use the following command to list all goals in a specific plugin: `mvn <PLUGIN>:help`

Example:
```
mvn failsafe:help

This plugin has 3 goals:

failsafe:help
  Display help information on maven-failsafe-plugin.
  Call mvn failsafe:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.

failsafe:integration-test
  Run integration tests using Surefire.

failsafe:verify
  Verify integration tests ran using Surefire.

```

**To run a specific goal, without executing its entire phase (and the preceding phases) we can use the command**
`mvn <PLUGIN>:<GOAL>`

For example, to run integration-test goal from Failsafe plugin, we need to run: `mvn failsafe:integration-test`

### Building a Maven Project
To build a Maven project, we need to execute one of the life cycles by running one of their phases:

`mvn deploy`
This will execute the entire default lifecycle. Alternatively, we can stop at the install phase:

`mvn install`
But usually we'll use the command:

`mvn clean install`
To clean the project first – by running the clean lifecycle – before the new build.

We can also run only a specific goal of the plugin:

`mvn compiler:compile`
Note that if we tried to build a Maven project without specifying a phase or a goal, that will cause the error:

`[ERROR] No goals have been specified for this build. You must specify a valid lifecycle phase or a goal`

---
## Install local jar with Maven

#### The Problem and the Options
Maven is a very versatile tool and its available public repositories are second to none. 
However there will always be an artifact that is either not hosted anywhere, or the repository
where it is hosted is risky to depend on, as it may not be up when you need it.

When that happens, there are a few choices:

bite the bullet and install a full fledged repository management solution such as Nexus
try to get the artifact uploaded into one of more reputable public repositories
install the artifact locally using a maven plugin
Nexus is of course the more mature solution, but it's also the more complex. 
Provisioning an instance to run Nexus, setting up Nexus itself, configuring and maintaining 
it may be overkill for such a simple problem as using a single jar. If this scenario – hosting custom artifacts – 
is a common one however, a repository manager makes a lot of sense.

Getting the artifact uploaded into a public repository or in Maven central directly is also a good solution,
but usually a lengthy one. In addition, the library may not be Maven enabled at all, which makes the process 
that much more difficult, so it's not a realistic solution to being able to use the artifact NOW.

That leaves the third option – adding the artifact in source control and using a maven plugin – in this case,
the `maven-install-plugin` to install it locally before the build process needs it. This is by far 
the easiest and most reliable option available.

#### Install Local Jar With the maven-install-plugin
Let's start with the full configuration necessary to install the artifact into our local repository:
```xml
<plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-install-plugin</artifactId>
   <version>2.5.1</version>
   <configuration>
      <groupId>org.somegroup</groupId>
      <artifactId>someartifact</artifactId>
      <version>1.0</version>
      <packaging>jar</packaging>
      <file>${basedir}/dependencies/someartifact-1.0.jar</file>
      <generatePom>true</generatePom>
   </configuration>
   <executions>
      <execution>
         <id>install-jar-lib</id>
         <goals>
            <goal>install-file</goal>
         </goals>
         <phase>validate</phase>
      </execution>
   </executions>
</plugin>
```
Now, let's break down and analyze the details of this configuration.

#### The Artifact Information
The Artifact Information is defined as part of the `<configuration>` element. The actual syntax is very similar 
to declaring the dependency – a groupId, artifactId and version elements.

Next part of the configuration requires defining the packaging of the artifact – this is specified as `jar`.

Next, we need to provide the `location of the actual jar file to be installed` – this can be an absolute 
file path or it can be relative, using the properties available in Maven. In this case, 
the `${basedir}` property represents the `root` of the project, namely the location where the `pom.xml` file exists. 
This means that the `someartifact-1.0.jar` file needs to be placed in a `/dependencies/ directory` under the `root`.

Finally, there are several other optional details that can be configured as well.

#### The Execution
The execution of the `install-file` goal is bound to the `validate` phase from the standard Maven build lifecycle. 
As such, before attempting to compile – you will need to run the `validation phas`e explicitly:

`mvn validate`
After this step, the standard compilation will work:

`mvn clean install`
Once the compile phase does execute, our `someartifact-1.0.jar` is correctly installed in our local repository, 
just as any other artifact that may have been retrieved from Maven central itself.

#### Generating a POM vs Supplying the POM
The question of whether we need to supply a pom.xml file for the artifact or not depends on mainly on the 
runtime dependencies of the artifact itself. Simply put, if the artifact has runtime dependencies on other jars, 
these jars will need to be present on the classpath at runtime as well. With a simple artifact that should not be a 
problem, as it will likely have no dependencies at runtime (a leaf in the dependency graph).

The `generatePom` option in the `install-file` goal should suffice for these kinds of artifacts:
```xml
<generatePom>true</generatePom>
```
However, if the artifact is more complex and does have non-trivial dependencies, then, if these dependencies 
are not already in the classpath, they must be added. One way to do that is by defining these new dependencies
manually in the pom file of the project. A better solution is to provide a custom pom.xml file along 
with the installed artifact:

```xml
<generatePom>false</generatePom>
<pomFile>${basedir}/dependencies/someartifact-1.0.pom</pomFile>
```

This will allow Maven to resolve all dependencies of the artifact defined in this custom pom.xml, 
without having to define them manually in the main pom file of the project.

---
## Maven Deploy to Nexus
Maven project can install locally a third party jar that has not yet been deployed on Maven Central 
(or on any of the other large and publicly hosted repositories).

That solution should only be applied in small projects where installing, running and maintaining a 
full Nexus server may be overkill. However, as a project grows,

Nexus quickly becomes the only real and mature option for hosting third party artifacts, as well as 
for reusing internal artifacts across development streams.

### Nexus Requirements in the *pom.xml*
n order for Maven to be able to deploy the artifacts it creates in the package phase of the build, 
it needs to define the repository information where the packaged artifacts will be deployed, 
via the `distributionManagement` element:
```xml
<distributionManagement>
   <snapshotRepository>
      <id>nexus-snapshots</id>
      <url>http://localhost:8081/nexus/content/repositories/snapshots</url>
   </snapshotRepository>
</distributionManagement>
```

A hosted, public Snapshots repository comes out of the box on Nexus, so there's no need to create or configure 
anything further. Nexus makes it easy to determine the URLs of its hosted repositories – each repository displays 
the exact entry to be added in the `<distributionManagement>` of the project pom, under the Summary tab.

### Plugins
By default, Maven handles the deployment mechanism via the maven-deploy-plugin 
– this mapped to the deployment phase of the default Maven lifecycle:
```xml
<plugin>
   <artifactId>maven-deploy-plugin</artifactId>
   <version>2.8.1</version>
   <executions>
      <execution>
         <id>default-deploy</id>
         <phase>deploy</phase>
         <goals>
            <goal>deploy</goal>
         </goals>
      </execution>
   </executions>
</plugin>
```
The maven-deploy-plugin is a viable option to handle the task of deploying to artifacts of a project to Nexus, 
but it was not built to take full advantage of what Nexus has to offer. Because of that fact, Sonatype built 
a Nexus specific plugin – the `nexus-staging-maven-plugin` – that is actually designed to take full advantage 
of the more advanced functionality that Nexus has to offer – functionality such as staging.

Although for a simple deployment process we do not require staging functionality, we will go forward with 
this custom Nexus plugin since it was built with the clear purpose to talk to Nexus well.

The only reason to use the `maven-deploy-plugin` is to keep open the option of using an 
alternative to Nexus in the future – for example, an `Artifactory repository`. However, 
unlike other components that may actually change throughout the lifecycle of a project, 
the Maven Repository Manager is highly unlikely to change, so that flexibility is not required.

So, the first step in using another deployment plugin in the deploy phase is to disable the existing, default mapping:

```xml
<plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-deploy-plugin</artifactId>
   <version>${maven-deploy-plugin.version}</version>
   <configuration>
      <skip>true</skip>
   </configuration>
</plugin>
```
Now, we can define:
```xml
<plugin>
   <groupId>org.sonatype.plugins</groupId>
   <artifactId>nexus-staging-maven-plugin</artifactId>
   <version>1.5.1</version>
   <executions>
      <execution>
         <id>default-deploy</id>
         <phase>deploy</phase>
         <goals>
            <goal>deploy</goal>
         </goals>
      </execution>
   </executions>
   <configuration>
      <serverId>nexus</serverId>
      <nexusUrl>http://localhost:8081/nexus/</nexusUrl>
      <skipStaging>true</skipStaging>
   </configuration>
</plugin>
```
The deploy goal of the plugin is mapped to the deploy phase of the Maven build.

Also notice that, as discussed, we do not need staging functionality in 
a simple deployment of `-SNAPSHOT` artifacts to Nexus, so that is fully disabled via the `<skipStaging>` element.

By default, the deploy goal includes the staging workflow, which is recommended for release builds.

### The Global *settings.xml*
Deployment to Nexus is a **secured operation** – and a deployment user exists for this purpose 
out of the box on any Nexus instance.

Configuring Maven with the credentials of this deployment user, so that it can interact correctly with Nexus, 
cannot be done in the pom.xml of the project. This is because the syntax of the pom doesn't allow it, not to mention the fact that the pom may be a public artifact, so not well suited to hold credential information.

The credentials of the server have to be defined in the global Maven setting.xml:
```xml
<servers>
   <server>
      <id>nexus-snapshots</id>
      <username>deployment</username>
      <password>the_pass_for_the_deployment_user</password>
   </server>
</servers>
```
The server can also be configured to use key based security instead of raw and plaintext credentials.

### The Deployment Process
Performing the deployment process is a simple task:
`mvn clean deploy -Dmaven.test.skip=true`

Skipping tests is OK in the context of a deployment job because this job should be the last job 
from a deployment pipeline for the project.

A common example of such a deployment pipeline would be a succession of Jenkins jobs, each triggering 
the next only if it completes successfully. As such, it is the responsibility of the 
previous jobs in the pipeline to run all tests suites from the project – by the time the deployment job runs, 
all tests should already pass.

If ran a single command, then tests can be kept active to run before the deployment phase executes: `mvn clean deploy`

---
## Maven Release to Nexus

### Repository in the pom
In order for Maven to be able to release to a Nexus Repository Server, we need to define the repository 
information via the `distributionManagement` element:
```xml
<distributionManagement>
   <repository>
      <id>nexus-releases</id>
      <url>http://localhost:8081/nexus/content/repositories/releases</url>
   </repository>
</distributionManagement>
```
The hosted Releases Repository comes out of the box on Nexus, so there is no need to create it explicitly.

### SCM in the Maven pom
he Release process will interact with the Source Control of the project – this means
we first need to define the <scm> element in our `pom.xml`:

```xml
<scm>
   <connection>scm:git:https://github.com/user/project.git</connection>
   <url>http://github.com/user/project</url>
   <developerConnection>scm:git:https://github.com/user/project.git</developerConnection>
</scm>
```
Or, using the git protocol:
```xml
<scm>
   <connection>scm:git:git@github.com:user/project.git</connection>
   <url>scm:git:git@github.com:user/project.git</url>
   <developerConnection>scm:git:git@github.com:user/project.git</developerConnection>
</scm>
```
### The Release Plugin
The standard Maven plugin used by a Release Process is the `maven-release-plugin` – 
the configuration for this plugin is minimal:
```xml
<plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-release-plugin</artifactId>
   <version>2.4.2</version>
   <configuration>
      <tagNameFormat>v@{project.version}</tagNameFormat>
      <autoVersionSubmodules>true</autoVersionSubmodules>
      <releaseProfiles>releases</releaseProfiles>
   </configuration>
</plugin>
```
What is important here is that the `releaseProfiles` configuration will actually force a Maven profile
– the releases profile – to become `active` during the `Release process`.

It is in this process that the `nexus-staging-maven-plugin` is used to perform a 
deploy to the `nexus-releases` Nexus repository:

```xml
<profiles>
   <profile>
      <id>releases</id>
      <build>
         <plugins>
            <plugin>
               <groupId>org.sonatype.plugins</groupId>
               <artifactId>nexus-staging-maven-plugin</artifactId>
               <version>1.5.1</version>
               <executions>
                  <execution>
                     <id>default-deploy</id>
                     <phase>deploy</phase>
                     <goals>
                        <goal>deploy</goal>
                     </goals>
                  </execution>
               </executions>
               <configuration>
                  <serverId>nexus-releases</serverId>
                  <nexusUrl>http://localhost:8081/nexus/</nexusUrl>
                  <skipStaging>true</skipStaging>
               </configuration>
            </plugin>
         </plugins>
      </build>
   </profile>
</profiles>
```
The plugin is configured to perform the Release process without the staging mechanism, same as previously,
for the Deployment process `(skipStaging=true)`.
And also similar to the Deployment process, Releasing to Nexus is a secured operation
– so we're going to use the Out of the Box deployment user form Nexus again.

We also need to configure the credentials for the nexus-releases server 
in the global `settings.xml` located at `(%USER_HOME%/.m2/settings.xml)`:

```xml
<servers>
   <server>
      <id>nexus-releases</id>
      <username>deployment</username>
      <password>the_pass_for_the_deployment_user</password>
   </server>
</servers>
```

### The Release Process
#### 1. Release:Clean
Cleaning a Release will:
* delete the release descriptor (release.properties)
* delete any backup POM files

#### 2.release:prepare
Next part of the Release process is **Preparing the Release**; this will:

* perform some checks – there should be no uncommitted changes and the project should depend on no SNAPSHOT dependencies
* change the version of the project in the pom file to a full release number (remove SNAPSHOT suffix) 
  – in our example – 0.1
* run the project **test** suites
* commit and push the changes
* create the tag out of this non-SNAPSHOT versioned code
* **increase the version** of the project in the pom – in our example – 0.2-SNAPSHOT
* commit and push the changes

#### 3. release:perform
The latter part of the Release process is **Performing the Release**; this will:

* checkout release tag from SCM
* build and deploy released code

This second step of the process relies on the output of the Prepare step – the release.properties.

### On Jenkins
Jenkins can perform the release process in one of two ways – it can either use 
it's own release plugins, or it can simply run perform the release with a 
standard maven job running the correct release steps.

The existing Jenkins plugins focused on the release process are:
* [Release Plugin](https://plugins.jenkins.io/release/)
* [M2 Release Plugin](https://plugins.jenkins.io/m2release/)
  
However, since the Maven command for performing the release is simple enough, we can simply define
a standard Jenkins job to perform the operation – no plugins necessary.

So, for a new Jenkins job (Build a maven2/3 project) – we”ll define 2 String parameters: 
* releaseVersion=0.1
* developmentVersion=0.2-SNAPSHOT.

At the Build configuration section, we can simply configure the following Maven command to run:

```
Release:Clean 
release:prepare 
release:perform -DreleaseVersion=${releaseVersion} -DdevelopmentVersion=${developmentVersion}
```
When running a parametrized job, Jenkins will prompt the user to specify values for these parameters – so each time 
we run the job we need to fill in the right values for `releaseVersion` and `developmentVersion`.

Also, it's worth using the `Workspace Cleanup Plugin` and check the `Delete workspace` before build starts option for 
this build. However keep in mind that the perform step of the Release should necessarily be run by the 
same command as the `preparestep` – this is because the latter perform step will use the `release.properties`
file created by `prepare`. 

**This means that we cannot have a Jenkins Job running prepare and another running perform.**

---

















