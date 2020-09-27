

# Day 24- Circle CI(Hands-on)

In the following demonstration, a Java Spring Boot Application is taken, in which the circle ci configuration file is written. Circle CI works on one configuration file, that is written inside the .circleci folder. The hierarchy of the configuration file in the root of the project is:

".circleci/config.yml"

![](images/Screenshot%20(1101).png)

The configuration file is added automatically for the we project we want to build, or it can also be added manually. A configuration file has been written in the project through the version control system like GitHub. Circle CI identifies the template, or we can set it according to the language our code has been written in.

![](images/Screenshot%20(1102).png)

Below is the configuration file written for the project.

```yaml
version: 2
jobs:
  build:
    environment:
      # Configure the JVM and Gradle to avoid OOM errors
      _JAVA_OPTIONS: "-Xmx3g"
      GRADLE_OPTS: "-Dorg.gradle.daemon=false -Dorg.gradle.workers.max=2"
    docker:
      - image: circleci/openjdk:11.0.3-jdk-stretch
    steps:
      - checkout
      - restore_cache:
          key: v1-gradle-wrapper-{{ checksum "gradle/wrapper/gradle-wrapper.properties" }}
      - restore_cache:
          key: v1-gradle-cache-{{ checksum "build.gradle" }}
      - run:
          name: Install dependencies
          command: ./gradlew build -x test
      - save_cache:
          paths:
            - ~/.gradle/wrapper
          key: v1-gradle-wrapper-{{ checksum "gradle/wrapper/gradle-wrapper.properties" }}
      - save_cache:
          paths:
            - ~/.gradle/caches
          key: v1-gradle-cache-{{ checksum "build.gradle" }}
      - persist_to_workspace:
          root: .
          paths:
            - build
  test:
    # Remove if parallelism is not desired
    parallelism: 2
    environment:
      # Configure the JVM and Gradle to avoid OOM errors
      _JAVA_OPTIONS: "-Xmx3g"
      GRADLE_OPTS: "-Dorg.gradle.daemon=false -Dorg.gradle.workers.max=2"
    docker:
      - image: circleci/openjdk:11.0.3-jdk-stretch
      - image: circleci/postgres:12-alpine
        environment:
          POSTGRES_USER: postgres
          POSTGRES_DB: circle_test
    steps:
      - checkout
      - attach_workspace:
          at: .
      - run:
          name: Run tests in parallel 	
          command: |
            cd src/test/java
            # Get list of classnames of tests that should run on this node
            CLASSNAMES=$(circleci tests glob "**/*.java" \
              | cut -c 1- | sed 's@/@.@g' \
              | sed 's/.\{5\}$//' \
              | circleci tests split --split-by=timings --timings-type=classname)
            cd ../../..
            # Format the arguments to "./gradlew test"
            GRADLE_ARGS=$(echo $CLASSNAMES | awk '{for (i=1; i<=NF; i++) print "--tests",$i}')
            echo "Prepared arguments for Gradle: $GRADLE_ARGS"
            ./gradlew test $GRADLE_ARGS
      - run:
          name: Generate code coverage report
          command:
            ./gradlew jacocoTestReport
      - store_test_results:
          path: build/test-results/test
      - store_artifacts:
          path: build/test-results/test
          when: always
      - store_artifacts:
          path: build/reports/jacoco/test/html
          when: always
      - run:
          name: Assemble JAR
          command: |
            # Skip this for other nodes
            if [ "$CIRCLE_NODE_INDEX" == 0 ]; then
              ./gradlew assemble
            fi
      # This will be empty for all nodes except the first one
      - store_artifacts:
          path: build/libs

workflows:
  version: 2
  workflow:
    jobs:
    - build
    - test:
        requires:
          - build
```

![](images/Screenshot%20(1105).png)

To stop the build and edit the configuration file, we can go to the VCS, GitHub in this case, which allows to edit the file there, and then commit the code and start the build process again. 

"https://github.com/{username}/{repo}/edit/circleci-project-setup/.circleci/config.yml"

![](images/Screenshot%20(1106).png)

The pipeline is build and successful.

## Digging into the concepts:

#### Building a new project:

Providing a GitHub or a BitBucket integration, all the repositories are added to the Circle CI dashboard with an option of setting up a project.

![](images/Screenshot%20(1107).png)

The user for any particular project is the one who logs into the circle ci, and sets up the project. Usually the user is the owner of the VCS and the administrator is the onw who is logged into the Circle CI.

#### Circle CI Orbs:

Orbs are basically, reusable code snippets that speeds up the project setup and helps integrate third-party faster. There is a registry called Circle CI Orbs Registry, which contains many orbs or small units of code that makes the configuration file simpler and the existing code can be reused.

![](images/Screenshot%20(1108).png)

#### Configuration File:

The whole pipeline is executed through one yaml configuration file, contained inside the .circleci folder under the root of the project. The main components of the configuration consists of the following things:

* **Steps**: These are actions or the instructions that are needed to be performed to complete the job.
* **Jobs**: These are the building blocks of the configuration file a series of steps that runs the scripts as required or instructed by the mentioned command. It includes a default image if not specified.

* **Workflows**: To manage the list of steps, the order in which they are to be executed. Jobs may be executed sequentially or parallelly, either manually or on a scheduled script.
* **Executor and images**: Each job defined in the configuration file runs in a unique executor which could be any virtual machine or a docker container.
*  **Caches**: It is kind of an object storage, such that the dependencies required for the next build may be accessed faster, and speed up the build. Therefore, the dependencies are cached in each job. 
* **Workspaces**: Workspace is basically a mechanism which keeps a track of workflow, where each workflow is associated with a temporary workspace.
* **Artifacts**: These are the outputs of the build process of workflow, and can be used for long-term storage.

![](images/Screenshot%20(1109).png)

## Shifting from GitLab to Circle CI:

The code that was being built on GitLab, should lie on GitHub or BitBucket, so that it could be integrated. Next step is to refactor the .gitlab-ci.yml file in GitLab into the .config.yml file in the .circleci folder that needs to be created in the root of the repository.

Some of the rules have been discussed below:

* **For cache dependencies**

###### .gitlab-ci.yml:

```yaml
image: node:latest
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - modules/

before_script:
  - npm install

test:
  script:
    - node ./specs/start.js ./specs/async.spec.js
```

In the above code, jasmine has been used for unit testing in the node.js application.



###### To convert it into .config.yml,

```yaml
jobs:
  job1:
    steps:
      - restore_cache:
          key: source-v1-< .Revision >

      - checkout

      - run: npm install

      - save_cache:
          key: source-v1-< .Revision >
          paths:
            - "modules"
```



* **For specifying a docker image to use for a particular job**

###### .gitlab-ci.yml:

```yaml
job1:
  image: node:10
```

###### Converting to .config.yml

```yaml
jobs:
  job1:
    docker:
      - image: node:10
```

The above job is pulling a node 10 image through docker.



* **For defining a job**

###### .gitlab-ci.yml:

```yaml
job1:
  script: "any script"
```

###### Converting to .config,yml:

```yaml
jobs:
  job1:
    steps:
      - checkout
      - run: "any script"
```



* **To execute the build on multiple platforms**

###### .gitlab-ci.yml:

```yaml
ubuntu job:
  tags:
    - ubuntu
  script:
    - echo "Hello, $USER!"

osx job:
  tags:
    - osx
  script:
    - echo "Hello, $USER!"
```

GitLab CI makes use of GitLab runners, to exceute any script.

###### Converting to .config.yml:

```yaml
jobs:
  ubuntuJob:
    machine:
      image: ubuntu-1604:201903-01
    steps:
      - checkout
      - run: echo "Hello, $USER!"
  osxJob:
    macos:
      xcode: 11.3.0
    steps:
      - checkout
      - run: echo "Hello, $USER!"
```

Circle CI makes use of executors to run on multiple platforms.

* **Building a multi-stage pipeline**

###### .gitlab-ci.yml:

```yaml
stages:
  - build
  - test
  - deploy
  
job1:
  stage: build
  script: echo "job1"

job2:
  stage: build
  script: echo "job2"

job3:
  stage: test
  script: echo "job3"

job4:
  stage: deploy
  script: echo "job4"
```

###### Converting to .config.yml:

```yaml
version: 2
jobs:
  job1:
    steps:
      - checkout
      - run: echo "job1"
  job2:
    steps:
      - run: echo "job2"
  job3:
    steps:
      - run: echo "job3"
  job4:
    steps:
      - run: echo "job4"

workflows:
  version: 2
  jobs:
    - job1
    - job2
    - job3:
        requires:
          - job1
          - job2
    - job4:
        requires:
          - job3
```



## Links Referred:

1. "https://circleci.com/docs/2.0/getting-started/#digging-into-your-first-pipeline"
2. "https://circleci.com/docs/2.0/migrating-from-gitlab/#section=getting-started"
3. "https://circleci.com/docs/2.0/concepts/#section=getting-started"
4. "https://circleci.com/blog/a-step-into-open-source/"