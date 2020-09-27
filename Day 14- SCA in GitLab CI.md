#  Day 14- SCA in GitLab

Static Code Analysis(SCA) is performed as a part of code review or white-box testing. It identifies the vulnerabilities in the non-running or static code using different techniques such as Data Flow Analysis, Control Flow Graphs, Taint Analysis, Lexical Analysis.

Ensuring code quality helps to be simple, and easy to understand and contribute. We can check the code quality of the code pushed on the GitLab, using the GitLab Code Quality.

The GitLab code quality uses the open source code climate engines, and runs in pipeline using a docker image using the default configurations. It can make use of <u>templates</u>, and can be extended to any other plugin or tool.

A very basic implementation of Code Quality in GitLab has been described in this demonstration.

A java project is taken that is consists a "Hello World" program. In the .gitlab-ci.yml file, the GitLab's code quality is included. Below is the yaml file shown:

```yaml
image: java:latest

stages:
  - build
  - test
  - execute

include:
  - template: Code-Quality.gitlab-ci.yml

build:
  stage: build
  script: /usr/lib/jvm/java-8-openjdk-amd64/bin/javac HelloWorld.java
  artifacts:
    paths:
     - HelloWorld.*

code_quality:
  stage: test
  artifacts:
    expose_as: 'Code Quality Report'
    paths: [gl-code-quality-report.json]

execute:
  stage: execute
  script: /usr/lib/jvm/java-8-openjdk-amd64/bin/java HelloWorld
```

The above script first pulls the java image to run the java commands in our project. Thereafter, three stages are defined.

```yaml
include:
	- template: Code-Quality.gitlab-ci.yml
```

* It creates a code_quality job in the CI/CD pipeline, which will scan the code for testing quality.
* The code quality report is saved as an artifact which can be downloaded later to analyze.
* To download the artifacts on the job details page, the below mentioned code is added.

```yaml
include:
  - template: Code-Quality.gitlab-ci.yml

code_quality:
  artifacts:
    paths: [gl-code-quality-report.json]
```

The code quality stage runs in the test stage, therefore the test stage also needs to be included in the yaml file.

A few changes are made in the java code to check if our pipeline is running well along with the code quality that is added.

![](images/Screenshot%20(1006).png)

After doing the changes, the code is committed and merged to the main branch and the pipeline starts.

![](images/Screenshot%20(1007).png)

The  pipeline runs and as described in the script, the code quality report is shown on the page.

![](images/Screenshot%20(1008).png)

The code quality report is available to be downloaded as a json file.

#### Links Referred:

1. "https://owasp.org/www-community/controls/Static_Code_Analysis"
2. "https://docs.gitlab.com/ee/user/project/merge_requests/code_quality.html"
3. "https://www.youtube.com/watch?v=B32LxtJKo9M"



