# Day 9- Maven with GitLab

The continuous integration pipeline mainly consists of the build, dependency management, automated unit tests, and static code analysis. 

#### <u>Dependency management</u> : 

Every project needs some dependencies to run and function. When a project is built, it requires different plugins and installations to run automated tests and other things. Many tools serves this purpose such as Maven, Gradle, Ant, etc.

In the following demonstration, maven has been worked upon. 

##### <u>Maven</u>:

Maven is a build tool, by providing a uniform build system. It makes the build process easy. It contains all the dependencies used by the project as in a "pom.xml file"(Project Object Model). Whenever a maven goal has to run, it looks into the pom.xml file of that directory and the execute the maven goal.

Execution in Maven takes place in the form of goals, and it has its own build cycle. The build cycle mainly consists of the following phases:

* validate: This goal check whether the project is valid or not.
* compile: This goal compiles the source code in the project.
* test: In this goal, unit testing of the code is done using any of the different unit testing frameworks such as JUnit, TestNg, depending on the type of project.
* package: After the code has been tested, it is packaged into a JAR file, or any other distributable format, to be delivered as an artifact. 
* verify: This ensures that the quality criteria of the project is met.
* install: The artifact is installed in the local maven repository to be used as dependency for other projects.
* deploy: The deployment part is done in the build environment, and the code is deployed to the remote repository to be shared between other developers.

Many third-party applications provides Maven plugin to provide integration with the build tool.

A maven project is used on GitLab which contains a basic Java program that has been tested using the Junit unit testing framework.

Below is the .gitlab-ci.yml file to carry out the different maven goals.

```yaml
image: maven:3.5-jdk-8

stages:
  - validate
  - compile
  - test

validate:
  stage: validate
  script:
    - mvn validate

checkstyle:
  stage: validate
  script:
    - mvn checkstyle:check
  allow_failure: true

compile:
  stage: compile
  script:
    - mvn compile

test:
  stage: test
  script:
    - mvn test -B

```

In the above yaml file, first the maven image has been pulled from docker to carry out the maven goals written in the file further. Different stages are written that performs different goal.

The checkstyle stage is bound to validate which implies that it will verify the code that is it valid or not before the test. It will parse different errors than the javac compiler.

![](images/Screenshot%20(982).png)

#### <u>Static Code Analysis</u>:

SCA helps us in improving the code quality and identify the errors and poor design patterns. A static analysis has been included in the maven build itself. This has been done using "checkstyle". Add the checkstyle plugin the pom.xml and we can have it running as a maven goal by editing the yaml script. We can save the reports by the command maven:site and the web reports would be saved in the target folder.

With the above code, if any errors were found by the chekstyle,only a warning is sent stating all the errors, whereas the overall pipeline passes.

#### <u>Links Referred:</u>

1. "https://medium.com/swlh/automating-java-projects-with-gitlab-7d81917aeb65"
2.  "https://docs.oracle.com/en/cloud/paas/developer-cloud/csdcs/create-and-configure-jobs-and-pipelines-using-yaml.html#GUID-021D7F74-2076-4033-AE49-48BFD7029D25"
3.  "https://docs.gitlab.com/ee/ci/yaml/README.html"
4.  "https://docs.gitlab.com/ee/ci/yaml/includes.html"