# Day 10- Automated Deployment Pipeline using GitLab

Once the build pipeline is complete the next step is the release pipeline which involves the continuous delivery and continuous deployment pipeline. A sample project has been taken that contains a NodeJS application, which runs behind a Nginx server, a Docker has been used for containerization.

Therefore, whenever we push a code to the version control system like Git, the GitLab CI will detect the changes and reflect them in the GitLab repository as well. The pipeline is then triggered by GitLab runner, which eventually starts pulling the latest code, build it and finally will run a docker-compose process.

If any bugs or errors are found in the project, then the pipeline would stop, else the latest commut will be shown in the pipeline.

We would need a Cloud Hosting Provider which would serve as a server for the application.

After selecting the cloud provider, we install the GitLab runners, and set up the runner according to our requirements.

The GitLab runners pulls the latest commits that have been on starts building on the servers it has been installed and added to the GitLab repository.

GitLab runner is installed.

```shell
$ curl -L
https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh | sudo bash
$ sudo apt-get install gitlab-runner
```

Then, docker is installed:

```shell
$ sudo apt install docker.io
```



A yaml file is created to automate the process in GitLab, so that whenever changes are committed the yaml file runs and run the further processes automatically without any human intervention.

The .gitlab-ci.yml file is as follows:

```yaml
image: docker:latest
services:
  - docker:dind

stages:
  - test
  - deploy

step-develop:
  stage: test
  before_script:
    - export DYNAMIC_ENV_VAR=DEVELOP
  only:
    - develop
  tags:
    - develop
  script:
    - echo running tests in $DYNAMIC_ENV_VAR

step-uat:
  stage: deploy
  before_script:
    - export DYNAMIC_ENV_VAR=UAT
  only:
    - uat
  tags:
    - uat
  script:
    - echo setting up env $DYNAMIC_ENV_VAR
    - sudo apt-get install -y python-pip
    - sudo pip install docker-compose
    - sudo docker image prune -f
    - sudo docker-compose -f docker-compose.yml build --no-cache
    - sudo docker-compose -f docker-compose.yml up -d

step-deploy-staging:
  stage: deploy
  before_script:
    - export DYNAMIC_ENV_VAR=STAGING
  only:
    - staging
  tags:
    - staging
  script:
    - echo setting up env $DYNAMIC_ENV_VAR
    - sudo apt-get install -y python-pip
    - sudo pip install docker-compose
    - sudo docker image prune -f
    - sudo docker-compose -f docker-compose.yml build --no-cache
    - sudo docker-compose -f docker-compose.yml up -d

step-deploy-production:
  stage: deploy
  before_script:
    - export DYNAMIC_ENV_VAR=PRODUCTION
  only:
    - production
  tags:
    - production
  script:
    - echo setting up env $DYNAMIC_ENV_VAR
    - sudo apt-get install -y python-pip
    - sudo pip install docker-compose
    - sudo docker image prune -f
    - sudo docker-compose -f docker-compose.yml build --no-cache
    - sudo docker-compose -f docker-compose.yml up -d
  when: manual

```

This yaml file first pulls the latest images of docker. The <u>docker:dind</u> service has been added which will enable docker in docker service.

Two stages have been put in the file, text and deploy stage and jobs to each of them have been defined. The user acceptance has been added along with staging, production.



#### <u>Links referred:</u>

1. "https://medium.com/@sean_bradley/auto-devops-with-gitlab-ci-and-docker-compose-f931233f080f"
2. "https://github.com/Sean-Bradley/Seans-TypeScript-NodeJS-CRUD-REST-API-Boilerplate"
3. "https://about.gitlab.com/handbook/engineering/development/ci-cd/release/"
4. "https://www.digitalocean.com/community/tutorials/how-to-set-up-continuous-integration-pipelines-with-gitlab-ci-on-ubuntu-16-04"

 