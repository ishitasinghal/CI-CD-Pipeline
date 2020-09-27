# Day 26- Working on points discussed in meeting

### Unit tests in the pipeline not being shown:

To include the unit tests result on the pipeline page, a flag feature needs to be enabled in the settings of the project, which is disabled by default.

A few changes were required in the POM.xml, which is shown below. The changes were such as adding the jacoco plugin and other dependencies like surefire.

#### Recreated POM.xml

```yaml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.3.0.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.ishita</groupId>
	<artifactId>messageboard</artifactId>
	<version>1.0-SNAPSHOT</version>
	<packaging>war</packaging>
	<name>messageboard</name>
	<description>Demo project for a simple message board</description>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<jacoco.version>0.8.4</jacoco.version>
		<springfox-version>2.9.2</springfox-version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>io.jsonwebtoken</groupId>
			<artifactId>jjwt</artifactId>
			<version>0.9.1</version>
		</dependency>
		<dependency>
			<groupId>org.mapstruct</groupId>
			<artifactId>mapstruct-jdk8</artifactId>
			<version>1.3.0.Final</version>
		</dependency>
		<dependency>
			<groupId>org.mapstruct</groupId>
			<artifactId>mapstruct-processor</artifactId>
			<version>1.3.1.Final</version>
		</dependency>
		<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
			<version>${springfox-version}</version>
		</dependency>
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui</artifactId>
			<version>${springfox-version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>uk.co.jemos.podam</groupId>
			<artifactId>podam</artifactId>
			<version>7.2.1.RELEASE</version>
			<scope>test</scope>
		</dependency>
		<dependency>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.0.0-M4</version>
            <type>maven-plugin</type>
        </dependency>
	</dependencies>

	<build>
		<finalName>${project.name}</finalName>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
			
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
                <minimum>0%</minimum>
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

### Gitlab-ci.yml

The gitlab configuration also required a few changes. It required inclusion of reports artifacts to be generated in the xml format so that it can be viewed on the pipeline.

reports:
            junit:

​				- target/surefire-reports/TEST-*.xml

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
  - codecoverage
  - package
  - deploy
  
include:
   - template: SAST.gitlab-ci.yml
   - template: Code-Quality.gitlab-ci.yml
   
build:
    image: maven:3-jdk-8
    stage: build 
    script:   
        - mvn compile
        - ls target/generated-sources
    artifacts: 
        paths: 
         - target/*.war
        reports:
            junit:
                - target/surefire-reports/TEST-*.xml
         

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
 
codecoverage:
    image: maven:3-jdk-8
    stage: package
    script:
        - mvn clean verify
    artifacts:
        paths:
            - target/site/jacoco/index.html

docker-build:
  stage: package
  script:
    - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN registry.gitlab.com
    - docker build -t registry.gitlab.com/ishitasinghal/messageboard .
    - docker push registry.gitlab.com/ishitasinghal/messageboard
```

![](images/Screenshot%20(1110).png)

### Code Quality in the pipeline:

The code quality tests appear in the pipeline tab on having a MR or merge request on the code. Once the pull request is merged, all the code quality issues are displayed on the code quality tab on the pipeline page.

As soon as a new merge request is pulled and the changes are committed, the pipeline is triggered.

For example, in the given demonstration, a few changes are made and committed through a different branch.

![](images/Screenshot%20(1113).png)

![](images/Screenshot%20(1114).png)

The pipeline is triggered by the commit and is run.

![](images/Screenshot%20(1115).png)

![](images/Screenshot%20(1116).png)

The code quality tab now contains all the issues concerning about the code quality.

![](images/Screenshot%20(1117).png)

### Links Referred:

1. "https://gitlab.com/gitlab-org/gitlab-foss/-/issues/54346"

2. "https://gitlab.com/gitlab-com/www-gitlab-com/-/merge_requests/36148"

3. "https://gitlab.com/gitlab-org/gitlab/-/merge_requests/21527"

4. "https://www.youtube.com/watch?v=B32LxtJKo9M"

5. "https://gitlab.com/gitlab-org/gitlab-foss/-/issues/54067"

6. "https://docs.gitlab.com/ee/ci/junit_test_reports.html#how-it-works"

7. "https://gitlab.com/gitlab-com/www-gitlab-com/-/merge_requests/36148"

8. "https://gitlab.com/gitlab-org/gitlab/-/merge_requests/21527"

   

