# Day 22- Code Coverage with GitLab CI

#### Code Coverage:

Code coverage is basically a method, to detect how much of the code has been covered during the execution of test cases run on the application. Code coverage lets us know which parts of the code are not covered. It helps us to improve the test suites containing the test cases, and making the software more efficient and better.

There are various tools to carry out code coverage for different languages. Some of the techniques are discussed below in the demonstration.

#### Using Jacoco plugin for maven:

Jacoco is a code coverage tool, that is used to measure the lines of code that are tested. It is 

The jacoco plugin is included in the pom file of the project for jacoco test coverage to work. The complete pom.xml is given below.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>actuator-sample</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>actuator-sample</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.3.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
			
		
            <!--<plugin>-->
            <!--    <groupId>org.jacoco</groupId>-->
            <!--    <artifactId>jacoco-maven-plugin</artifactId>-->
            <!--    <version>0.8.6-SNAPSHOT</version>-->
            <!--</plugin>-->
        <plugin>
            <groupId>org.jacoco</groupId>
  <artifactId>jacoco-maven-plugin</artifactId>
  <version>0.8.3</version>
  <executions>
    <execution>
      <id>coverage-initialize</id>
      <goals>
        <goal>prepare-agent</goal>
      </goals>
    </execution>
    <execution>
      <id>coverage-report</id>
      <phase>post-integration-test</phase>
      <goals>
        <goal>report</goal>
      </goals>
    </execution>
    <!-- Threshold -->
    <execution>
      <id>coverage-check</id>
      <goals>
        <goal>check</goal>
      </goals>
      <configuration>
        <rules>
          <rule>
            <element>CLASS</element>
            <excludes>
              <exclude>com.asimio.demo.Application</exclude>
            </excludes>
            <limits>
              <limit>
                <counter>LINE</counter>
                <value>COVEREDRATIO</value>
                <minimum>80%</minimum>
              </limit>
            </limits>
          </rule>
        </rules>
      </configuration>
    </execution>
  </executions>
  </plugin>
		</plugins>
	</build>


</project>
```

Along with this, a few configurations are required in the settings.xml in the .m2 repository as well.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <profiles>
    <profile>
      <id>jboss-public</id>
      <repositories>
        <repository>
          <id>jboss-public-repository</id>
          <name>JBoss Public Maven Repository Group</name>
          <url>http://repository.jboss.org/nexus/content/groups/public/</url>
        </repository>
      </repositories>
    </profile>
  </profiles>

<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-surefire-plugin</artifactId>
  <configuration>
    <argLine>@{argLine} -more -arguments</argLine>
...
  </configuration>
</plugin>

  <activeProfiles>
    <activeProfile>jboss-public</activeProfile>
  </activeProfiles>
</settings>
```

This plugin creates a new task which is dependent on the test, and generates code coverage reports. An html report is generated containing the code coverage reports. It is stored in the form of artifacts which can be downloaded. The folder in which it is contained is: 

```
artifacts:
        paths:
        - target/site/jacoco/index.html
```

Configuring it in the .gitlab-ci.yml file, it is added in the test section of the script.

```yaml
image: docker:latest
services:
  - docker:dind

variables:
  DOCKER_DRIVER: overlay
  SPRING_PROFILES_ACTIVE: gitlab-ci
  MAVEN_CLI_OPTS: "-s .m2/settings.xml --batch-mode"
  MAVEN_OPTS: "-Dmaven.repo.local=.m2/repository"

  
cache: 
    paths:
    - .m2/repository/
    - target/

stages:
  - build
  - test
  - package
#   - deploy
  
include:
   - template: SAST.gitlab-ci.yml
   - template: Code-Quality.gitlab-ci.yml
   
build:
    image: maven:3-jdk-8
    stage: build 
    script:   
        - mvn compile
    artifacts: 
        paths: 
         - target/*.jar
         

code_quality:
   artifacts:
     reports:
       codequality: gl-code-quality-report.json
   after_script:
     - cat gl-code-quality-report.json

spotbugs-sast:
   dependencies:
     - build
   script:
     - /analyzer run -compile=false
   variables:
     MAVEN_REPO_PATH: ./.m2/repository
   artifacts:
    reports:
      sast: gl-sast-report.json

test:
    image: maven:3-jdk-8
    stage: test 
    script: 
        - mvn $MAVEN_CLI_OPTS test 
        - echo "The code has been tested" 
    artifacts:
        paths:
        - target/site/jacoco/index.html


docker-build:
  stage: package
  script:
    - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN registry.gitlab.com
    - docker build -t registry.gitlab.com/ishitasinghal/actuator-sample .
    - docker push registry.gitlab.com/ishitasinghal/actuator-sample
```

The conditions can be specified accordingly, such that the build should only pass if the minimum code coverage is 0.7%.  This report can be viewed on the gitlab pages, and then the reports can be viewed on the accessed, "http://group-path.gitlab.io/project-path/index.html."

We need to add the following figure in our project settings, to make sure that the coverage result is printed to the console.

```
Total.*?([0-9]{1,3})%
```

![](images/Screenshot%20(1088).png)

#### Links referred:

1. "https://blog.gojekengineering.com/code-coverage-from-failing-the-build-to-publishing-the-report-with-gitlab-pages-a1d9a6a414c0"
2. "https://about.gitlab.com/blog/2016/11/03/publish-code-coverage-report-with-gitlab-pages/"
3. "https://about.gitlab.com/2016/11/03/publish-code-coverage-report-with-gitlab-pages/"
4. "https://stackoverflow.com/questions/48032798/code-coverage-report-using-gitlab-ci-yml-file"
5. "https://medium.com/@kaiwinter/javafx-and-code-coverage-on-gitlab-ci-29c690e03fd6"
6. "https://blog.exxeta.com/en/2018/08/12/display-the-test-coverage-with-gitlab/"
7. "https://dzone.com/articles/code-coverage-for-javafx-with-gitlab-ci"
8. "https://dzone.com/articles/reporting-code-coverage-using-maven-and-jacoco-plu"