# Day 11- Using Nexus with GitLab

For solving the above purpose, GitLab runner is required as it helps running the jobs efficiently.

#### <u>GitLab Runners:</u>

* GitLab runner is used to run jobs in GitLab CI, and sends results back to it. 
* It is a part of GitLab CI that helps in running the jobs.
* Although a default GitLab runner is provided by GitLab, but it is preferred to get one for private project.
* It allows running multiple jobs together.

![](images/Screenshot%20(993).png)

Till now, we had build a project using maven having maven, so we will now be deploying our code to the Nexus artifact repository for maven.

![](images/Screenshot%20(994).png)

Nexus is installed, or even a docker container can be used for the purpose and a nexus container is launched and a repository is made. 

The nexus variables are added to the GitLab variables.

Finally, the jobs are described in the .gitlab-ci.yml file and pushed to the repository. Below is the yaml file for the purpose.

```yaml
image: maven

variables:
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
  - deploy

codebuild:
  tags:
    - build      
  stage: build
  script: 
    - mvn compile

codetest:
  tags:
    - build
  stage: test
  script:
    - mvn $MAVEN_CLI_OPTS test
    - echo "The code has been tested"

Codepackage:
  tags:
    - build
  stage: package
  script:
    - mvn $MAVEN_CLI_OPTS package -Dmaven.test.skip=true
    - echo "Packaging the code"
  artifacts:
    paths:
      - target/*.war
  only:
    - master  

Codedeploy:
  tags:
    - build
  stage: deploy
  script:
    - mvn $MAVEN_CLI_OPTS deploy -Dmaven.test.skip=true
    - echo "installing the package in local repository"
  only:
    - master
```

The above script explains the jobs that have to be performed by GitLab using GitLab runners. 

First of all, the script pulls the maven image and stores the maven paths in the cache to reduce the access time as they would be used frequently. 

Four stages are defined in the yaml file that is the build stage, test stage, packaging and deploying to a repository. The maven goals are used to carry out the stages, compiling the code, then performing UT(unit tests), and then deploying it on the nexus server. 

