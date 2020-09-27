# Day 18- Including SAST in pipeline 

In the previous days, the pipeline was able to transfer or deploy maven artifacts to S3 bucket. In the pipeline, a SAST(Static Application Security Testing) script has been included. 

SAST is a security testing that includes a set of technologies used to explore the source code, binaries or the byte code which are prone to security vulnerabilities. It is also known as white box testing, and helps to find the bugs in the software development lifecycle.

To configure the SAST script, the following command needs to be included in the script.

```yaml
include:
  - template: SAST.gitlab-ci.yml
```

Here, the include template creates a SAST job in our CI/CD pipeline and starts to scan for the vulnerabilities in the code.

The result is stored as an SAST artifact which can be downloaded and analyzed. The reports are uploaded to GitLab summarized in the merge requests section in the GitLab Ultimate or Gold version. It is not available to be downloaded from the web interface.

Below is attached the yaml file used for the SAST test.

```yaml
image: maven:latest

variables:
   MAVEN_CLI_OPTS: "-s .m2/settings.xml --batch-mode"
   MAVEN_OPTS: "-Dmaven.repo.local=.m2/repository"
   BUCKET_NAME: example-bucket4        
   
cache: 
    paths:
    - .m2/repository/
    - target/

stages:
   - build
   - test
   - package
   
include:
   - template: SAST.gitlab-ci.yml
   
build:
    stage: build 
    script:   
    - mvn compile
    artifacts: 
        paths: 
         - target/*.war
         
         
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
    stage: test 
    script: 
        - mvn $MAVEN_CLI_OPTS test 
        - echo "The code has been tested" 
    
package: 
    stage: package 
    script: 
        - mvn $MAVEN_CLI_OPTS package -Dmaven.test.skip=true 
        - echo "Packaging the code" 
    only: 
     - master 
```

As soon as the file is committed, the pipeline is triggered and runs successfully.

![](images/Screenshot%20(1041).png)

The reports are generated in the .json format.

Other tools such as Coverity Scan can also be used and integrated in the GitLab CI using the secret variables.

- COVERITY_SCAN_PROJECT_NAME - with value for whatever project we want to implement
- COVERITY_SCAN_TOKEN -  value of the token generated earlier.

This is the .gitlab-ci.yml script used by the coverity tool.

```yaml
Coverity:
  only:
    refs:
      - master
      - coverity
  script:
  - dnf update -y
  - dnf install -y git autoconf automake libtool make curl
  - curl -o /tmp/cov-analysis-linux64.tgz https://scan.coverity.com/download/linux64
    --form project=$COVERITY_SCAN_PROJECT_NAME --form token=$COVERITY_SCAN_TOKEN
  - tar xfz /tmp/cov-analysis-linux64.tgz
  - ./autogen.sh
  - ./configure
  - cov-analysis-linux64-*/bin/cov-build --dir cov-int make -j4
  - tar cfz cov-int.tar.gz cov-int
  - curl https://scan.coverity.com/builds?project=$COVERITY_SCAN_PROJECT_NAME
    --form token=$COVERITY_SCAN_TOKEN --form email=$GITLAB_USER_EMAIL
    --form file=@cov-int.tar.gz --form version="`git describe --tags`"
    --form description="`git describe --tags` / $CI_COMMIT_TITLE / $CI_COMMIT_REF_NAME:$CI_PIPELINE_ID "
```



#### <u>Links Referred:</u>

1. "https://www.softwaresecured.com/what-do-sast-dast-iast-and-rasp-mean-to-developers/"
2. "http://www.sast.gov.in/"
3. "https://docs.gitlab.com/ee/user/application_security/sast/"
4. "https://gitlab.com/gitlab-org/gitlab-foss/-/issues/32000"
5. "https://www.synopsys.com/blogs/software-security/integrating-coverity-scan-with-gitlab-ci/"
6. "https://forge.etsi.org/rep/help/ci/examples/code_quality.md"
7. "https://docs.gitlab.com/ee/ci/yaml/README.html"

