# Day 34- Blog on the project(continued)

## Configuring .gitlab-ci.yml for project

Now that we have learnt about all the basic things, let's begin with our actual configuration file. Below is the configuration file, you can replace the contents of the `.gitlab-ci.yml` file with this file. 

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
  - codeQuality
  - package
  - deploy


include:
   - template: SAST.gitlab-ci.yml
   - template: Code-Quality.gitlab-ci.yml


build:
    image: maven:3.5-jdk-8-alpine
    stage: build 
    script:   
        - mvn compile
        - ls target/generated-sources
    artifacts: 
        paths: 
         - target/*.war
        

unittests:
    image: maven:3.5-jdk-8-alpine
    stage: test 
    script: 
        - mvn $MAVEN_CLI_OPTS test 
    artifacts:
        reports:
            junit:
                - target/surefire-reports/TEST-*.xml


code_quality:
   stage: codeQuality
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


codecoverage:
    image: maven:3.5-jdk-8-alpine
    stage: codecoverage
    script:
        - mvn clean verify
    artifacts:
        paths:
            - target/site/jacoco/index.html


docker-build:
  stage: package
  script:
    - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN registry.gitlab.com
    - docker build -t registry.gitlab.com/ishitasinghal/messageboard . --build-arg BUILDKIT_INLINE_CACHE=1 
    - docker push registry.gitlab.com/ishitasinghal/messageboard


k8s-deploy:
 image: google/cloud-sdk:latest
 stage: deploy
 script:
 - echo "$GOOGLE_KEY" > key.json
 - gcloud auth activate-service-account ishita-singhal@my-project-ishita.iam.gserviceaccount.com --key-file key.json
 - gcloud container clusters get-credentials cluster-1-ishita --zone us-central1-c --project my-project-ishita
 - kubectl delete secret $DPRK_SECRET_KEY_NAME 2>/dev/null || echo "secret does not exist"
 - kubectl create secret docker-registry $DPRK_SECRET_KEY_NAME --docker-username="$DPRK_DOCKERHUB_INTEGRATION_USERNAME" --docker-password="$DPRK_DOCKERHUB_INTEGRATION_PASSWORD" --docker-email="$DPRK_DOCKERHUB_INTEGRATION_EMAIL" --docker-server="$DPRK_DOCKERHUB_INTEGRATION_URL"/
 - kubectl apply -f deployment.yml


```

On running the script, the following pipeline is obtained.

**Note:** The secret variables used in the file are added in the secret variables in the GitLab CI/CD settings. We will be discussing about them in the upcoming stages.

![](images/Screenshot%20(1147).png)

![](images/Screenshot%20(1148).png)

Now, we will be discussing the content written in the file.



## Understanding the configuration file

- Starting from the first three lines written in the file, it pulls an image to run and execute the commands written in the file.

  ```yaml
  image: docker:latest
  services:
    - docker:dind
  ```

  - The `image` is a keyword, which pulls a Docker image present in the local Docker engine or Docker hub ,that will be used to create a container to build and test our application. In GitLab pipelines, each job is run in a separate container which is isolated. 

  - The `services` is also a keyword, that takes another docker image, and  defines what services are to be included during the build time. It can use any type of services has to be involved. 

    - For example, including a `MYSQL` service that runs a database container. Using images makes it easier and faster to use an already existing image instead of installing `mysql` at each build time.

  - In this project, the `dind` service is used, known as `docker-in-docker` image. This service enables docker build within the pipeline. 

  - `docker:latest` image contains all the necessary thing that are required to connect to docker daemon and the docker daemon itself, such as `docker build`, `docker run`, but the docker daemon is not started as an [entrypoint]([https://goinbigdata.com/docker-run-vs-cmd-vs-entrypoint/#:~:text=ENTRYPOINT%20instruction%20allows%20you%20to,runs%20with%20command%20line%20parameters.](https://goinbigdata.com/docker-run-vs-cmd-vs-entrypoint/#:~:text=ENTRYPOINT instruction allows you to,runs with command line parameters.)). Whereas, `docker:dind` is build on the `docker:latest` image and starts the docker daemon as an entrypoint.

    **Note:** Using both `docker:dind`  and `dind` isn't necessary, only one of them can be used, all we need to do is just start `dockerd` before docker build and run.

    

- Coming to the next few lines, which uses the variables tag.

  ```yaml
  variables:
    DOCKER_DRIVER: overlay
    SPRING_PROFILES_ACTIVE: gitlab-ci
    MAVEN_CLI_OPTS: "-s .m2/settings.xml --batch-mode"
    MAVEN_OPTS: "-Dmaven.repo.local=.m2/repository"
  ```

  Variables, similar to their use with any other language to store a specific value. Similar is their use in GitLab. Variables can be passed to the jobs listed in the file. They can be created either locally for each job or globally. In this pipeline, they have been used globally, once at the top.

  - The first variable is `DOCKER_DRIVER: overlay`. It is acts as a storage driver. Since, while executing jobs on shared runners, increases traffic which makes the pipeline execution time more. Therefore, overlay value, creates a distributed network among multiple docker daemon hosts, allowing containers to communicate securely over the network. 
  - The next variable is `SPRING_PROFILES_ACTIVE`. Since, the project we are using is a Spring Boot Application, therefore we need to activate spring profile to separate parts of application to make them work in certain environments only. For example, if we are running the application on the developer machine, `localhost` and for GitLab CI, `mongo` can be used. Different URL's can be used for different environments.
  - `MAVEN_CLI_OPTS`  This launches a Maven command line option, and the value `"-s .m2/settings.xml --batch-mode"` is passed to take the contents from maven's local repository .m2 the settings.xml file automatically in a batch mode, that is, this command won't stop to accept input from the user.
  - Next, the `MAVEN_OPTS` is used for launching the Java Virtual Machine(JVM) that runs Maven. The value `"-Dmaven.repo.local=.m2/repository"` is passed as an option to the JVM, to have a maven environment and set the local repository as .m2/repository.

  

- The `cache` part of the yaml file.

  

  ```yaml
  cache: 
      paths:
      - .m2/repository/
      - target/
  ```

  Cache is basically used to store all the project's dependencies to save time while running jobs. 

  - Here we are keeping Maven's .m2 repository in cache, as it contains the necessary files, such as `settings.xml` and the target folder, which is Maven's default output whenever a build command is run and contains the jar and the war files.

  - The contents of the m2 folder and target folder is required many times in the folder such as testing, packaging, deployment which is reused many time. So saving it in cache saves fetching time.

    

- Now, the one of the main part of the pipeline comes, **stages**.

  ```yaml
  stages:
    - build
    - test
    - codecoverage
    - codeQuality
    - package
    - deploy
  ```

  - In this script, six stages have been defined. These stages determines the lifecycle of our build. In total, 8 jobs are written in all the stages. 
  - Few jobs run in parallel with more than one job depending on one stage.
  - Stages are run sequentially in the order they are defined.
  - If any of the stage fails, the pipeline is stopped midway.

  Now, let's understand each stage in detail.