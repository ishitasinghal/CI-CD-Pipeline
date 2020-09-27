# Day 7- CI Pipeline with GitlabCI

This blog will brief everything about Gitlab, for how the pipeline is triggered right from the time the code is committed to a VCS.

#### <u>Creating a simple maven dependency</u>

To create an artifact, GitLab account needs to be logged in and the URL of the location wherever the project is located, is imported to the GitLab repository. A normal Maven project has been used for this demonstration.

Status of importing is running.

![](images/Screenshot%20(957).png)

The project is imported successfully.

![](images/Screenshot%20(958).png)

![](images/Screenshot%20(959).png)

#### <u>Configuring the artifact deployment:</u>

We are using Nexus for the repository of the build artifacts. For going through the same, install the Sonarqube-7.7 version, which is a zipped file, extract the files, and check for the successful installation. 

Start the nexus server by the command:

"nexus.exe /run"

![](images/Screenshot%20(960).png)

The Sonatype Nexus repository has started and can we viewed on port number 8081.

![](images/Screenshot%20(961).png)

![](images/Screenshot%20(962).png)

In the maven repository, we need to install the deployment plugin in pom.xml file.

```xml
<pluginManagement>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
</pluginManagement>
```

Then we need to configure Nexus and add the username and password for the nexus repository.

```xml
 <distributionManagement>
    <snapshotRepository>
      <id>nexus-snapshots</id>
      <url>http://localhost:8081/repository/maven-snapshots/</url>
    </snapshotRepository>
 </distributionManagement>
```

Performing maven goals, installing first:

![](images/Screenshot%20(968).png)

Compiling:

![](images/Screenshot%20(969).png)

Test-compile:

![](images/Screenshot%20(970).png)

When the artifact is deployed to nexus through maven, the build fails. This failure is due to a third-party plugin whixh is Nexus in this case.

![](images/Screenshot%20(971).png)



#### <u>Links referred:</u>

1. "https://docs.gitlab.com/ee/ci/yaml/README.html#introduction"

2. "https://www.youtube.com/watch?v=qM5DiWUqJ84"

3. "https://docs.gitlab.com/ee/ci/examples/artifactory_and_gitlab/"

4. "https://help.sonatype.com/learning/repository-manager-3/first-time-installation-and-setup/lesson-1%3A--installing-and-starting-nexus-repository-manager"

5.  "https://mincong.io/2018/08/04/maven-deploy-artifacts-to-nexus/"

   

