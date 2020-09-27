# Day 25- Learning Circle CI .config.yml file

Circle CI works on one .config,yml under the .circleci folder. That requires a study of how to create a yaml file. The following demonstration deals with the basics of creating a configuration file for circle ci.

### Orbs:

Orbs are basically reusable parts of code, that is used to speed up the build process, and makes the configuration file simpler. Following steps are worked upon in order to create and publish an orb.

* **Setting up the project on Circle CI** : 

  Installing and configuring the CLI for Circle CI and updating it. The Circle CI is installable in Windows machine but is currently in beta production. To proceed with the Linux machine, the command is:

  ```
  curl -fLSs https://circle.ci/cli | bash
  ```

  The above command downloads the circle ci cli. For set-up, 	

  ```
  circleci setup
  ```

  New API token is created under the account settings and kept safe.

* **Verifying the validity and installation of the CLI**:  

  To confirm and verify that circleci has been successfully installed, a configuration file is created under the .circleci folder. The config.yml file would contain the following contents.

  ```yaml
  version: 2.0
  jobs:
    build:
      docker:
        - image: circleci/ruby:2.4.2-jessie-node
      steps:
        - checkout
        - run: echo "Hello World"
  ```

  To validate the file, the following command is punched in.

  ```
  circleci config validate
  ```

* **Bumping version property to compatible orbs**: 

  Once the configuration is done, the version number is incremented to a new, unique value (bumping version), such that it is compatible to be used with other orbs as well.

* **Creating a new orb using some inline templates**:

  Although there are many templates that could be used, we can use inline orbs to get a reference, which makes it handy to create an orb at the time of development. For writing an inline orb, the orb element is placed in the orbs declaration.

  ```yaml
  version: 2.1
  description: 
  
  orbs:
    my-orb:
      orbs:
        codecov: circleci/codecov-clojure@0.0.4
      executors:
        specialthingsexecutor:
          docker:
            - image: circleci/ruby:2.7.0
      commands:
        dospecialthings:
          steps:
            - run: echo "We will now do special things"
      jobs:
        myjob:
          executor: specialthingsexecutor
          steps:
            - dospecialthings
            - codecov/upload:
                path: ~/tmp/results.xml
  
  ```

  The above template can be used to create an inline orb and can be used. This provides a reference, and provides an easy approach to author orbs quickly and easily.

* **Designing the orb according to our requirements**:

  We can add any number of jobs, commands, and executors in the file independent of the inline template. 

  <u>Commands</u> can be used again that can be used with an existing job. It contains parameters, which can be passed and execute.

  ```yaml
  version: 2.1
  jobs:
    myjob:
      docker:
        - image: "circleci/node:9.6.1"
      steps:
        - myorb/sayhello:
            to: "Ishita"
  ```

  <u>Jobs</u> consists of two parts- steps, and environment in which the commands written in the steps are to be executed. 

  ```yaml
  jobs:
    my-job:
      executor: my-executor
      steps:
        - run: echo "this is a job"
  ```

  Executors tells about the environment in which the steps of a job are to be executed. Environments as in docker, macOS, windows, and several others. These include environments variables, in which shell to execute, and what size of the class resource are to be executed.

  When a single executor has to be used for multiple jobs, then the executor can be declared outside all the jobs.

  ```
  version: 2.1
  executors:
    executor1:
      docker:
        - image: circleci/ruby:2.4.0
  
  jobs:
    my-job:
      executor: executor1
      steps:
        - run: echo "executor defined outside"
  ```

  

* **Validating the created orb**:

  Once the orb has been created, a validate command is run through the CLI which provides several tools to validate the orb.

  ```
  circleci config validate
  ```

  

* **Finally publishing the created orb**:

  The orb is published using the command given below inside the circleci/orb-tools.

  ```
  orb-tools/publish
  ```

  

### Commands:

A command defines a series of steps that are to be executed in a job, and enable the reuse of a code snippet. An example for a simple command is given below:

```yaml
commands:
  command1:
    description: "Command 1 executed"
    parameters:
      to:
        type: string
        default: "Hello World"
    steps:
      - run: echo << parameters.to >>
```

### Parameters:

The parameters or the arguments required in the configuration file are declared using the "parameters" key at the top of the file. It supports string type, boolean type, integer type and enum type. The pipeline variables can only be identified in the configuration file, that is, where they are declared. Pipeline variables are not declared in orbs. Example:

```yaml
version: 2.1
parameters:
  image-tag:
    type: string
    default: "latest"
  workingdir:
    type: string
    default: "~/main"

jobs:
  build:
    docker:
      - image: circleci/node:<< pipeline.parameters.image-tag >>
    environment:
      IMAGETAG: << pipeline.parameters.image-tag >>
    working_directory: << pipeline.parameters.workingdir >>
    steps:
      - run: echo "Image tag used ${IMAGETAG}"
      - run: echo "$(pwd) == << pipeline.parameters.workingdir >>"
```

The above script has two pipeline parameters as "image-tag" which is of type string and "workingdir" which is also of type string and uses the default directly as main. The parameters are defined at the top of the configuration file so that they can be used subsequently in the following jobs, build in this case. 

### Executors:

Executors interprets the environment  in which the jobs are to be executed. One executor can be used to execute multiple jobs.

```yaml
version: 2.1
executors:
  executor1:
    docker:
      - image: circleci/ruby:2.4.0

jobs:
  my-job:
    executor: executor1
    steps:
      - run: echo "executor defined outside"
```



## Links Referred:

1. "https://circleci.com/docs/2.0/configuration-reference/"
2. "https://circleci.com/docs/2.0/creating-orbs/"
3. "https://circleci.com/docs/2.0/configuration-reference/#orbs-requires-version-21"
4. "https://circleci.com/docs/2.0/configuration-reference/#jobs"