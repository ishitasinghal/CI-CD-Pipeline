# Day 13- Deploying artifacts from Maven to Nexus

As soon as a new code or a modified code is committed to the version control system, Git in this case, the .gitlab-ci.yml file runs, and executes the maven build pipeline. 

Storing artifacts in the some storehouse or a repository, is as important part in the release pipeline. Nexus have been used as an artifact repository in the following example. First, the code is built and tested by Maven, and then it is deployed to the Nexus server.

A sample project has been taken in which unit tests are written to perform unit testing. The code is committed to the GitLab.

This the POM file that has been added:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example.dep</groupId>
  <artifactId>simple-maven-dep</artifactId>
  <packaging>jar</packaging>
  <version>1.0</version>
  <name>simple-maven-dep</name>
  <url>http://maven.apache.org</url>
  <properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>
  <repositories>
    <repository>
      <id>gitlab-maven</id>
      <url>https://gitlab.com/api/v4/projects/3467553/packages/maven</url>
    </repository>
  </repositories>
  <distributionManagement>
    <repository>
      <id>gitlab-maven</id>
      <url>https://gitlab.com/api/v4/projects/3467553/packages/maven</url>
    </repository>
   <snapshotRepository>
      <id>ishita-nexus</id>
      <url>http://localhost:8081/repository/repodemo/</url>
   </snapshotRepository>
  
  </distributionManagement>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

<build>
<plugins>
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
<!--<plugin>-->
<!--   <groupId>org.apache.maven.plugins</groupId>-->
<!--   <artifactId>maven-deploy-plugin</artifactId>-->
<!--   <version>${maven-deploy-plugin.version}</version>-->
<!--   <configuration>-->
<!--      <skip>true</skip>-->
<!--   </configuration>-->
<!--</plugin>-->
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
</plugins>
</build>
</project>
```

In this pom.xml file, all the dependencies have been listed. It includes the plugins for sonarqube nexus, junit, and other dependencies necessary for the project. 

Here is the following .gitlba-ci.yml file:

```yaml
image: maven:latest

variables:
  MAVEN_CLI_OPTS: "-s .m2/settings.xml --batch-mode"
  MAVEN_OPTS: "-Dmaven.repo.local=.m2/repository"

cache:
  paths:
    - .m2/repository/
    - target/

build:
  stage: build
  script:
    - mvn $MAVEN_CLI_OPTS compile

test:
  stage: test
  script:
    - mvn $MAVEN_CLI_OPTS test

deploy:
  stage: deploy
  script:
    - mvn $MAVEN_CLI_OPTS deploy 
  only:
    - master
```

This is the yaml file which runs as soon as the code is committed in GitLab.

In the above code, even after trying multiple times, the deploy stage fails, and thus the whole pipeline fails. Multiple websites have been visited including "stackoverflow" platform, but the issue couldn't be resolved. 

Still, the solution is being worked upon. Here is the snapshots of the failed pipelines in the GitLab.

![](images/Screenshot%20(1001).png)

#### <u>Links referred:</u>

1. "https://help.sonatype.com/repomanager2/staging-releases/configuring-your-project-for-deployment#ConfiguringYourProjectforDeployment-DeploymentwiththeNexusStagingMavenPlugin"
2. "https://support.sonatype.com/hc/en-us/articles/213464108-maven-release-plugin-nexus-staging-plugin-Maven-2-2-1-Server-Credentials-with-ID-not-found"
3. "https://www.baeldung.com/maven-deploy-nexus"
4. "https://www.google.com/search?q=Failed+to+execute+goal+org.apache.maven.plugins%3Amaven-deploy-plugin%3A2.8.1%3A&rlz=1C1CHBF_enIN856IN856&oq=Failed+to+execute+goal+org.apache.maven.plugins%3Amaven-deploy-plugin%3A2.8.1%3A&aqs=chrome..69i57.15986j0j1&sourceid=chrome&ie=UTF-8"
5. "https://www.tfzx.net/article/667541.html"
6. "https://devops.com/integrating-maven-and-nexus-for-continuous-delivery-with-jenkins/"
7. "http://www.vineetmanohar.com/2010/06/getting-started-with-nexus-maven-repo-manager/"
8. "https://technology.amis.nl/2014/11/03/maven-assemble-release-nexus/"

