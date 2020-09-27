# Day 19- Learning about GitSwarm (Docker Integration)

The images and services feature in GitLab CI allows us to use the docker images that may be required at the time of build execution in our CI/CD pipeline. 

#### <u>Using Docker images:</u>

The <u>image</u> keyword is used to use any docker image existing in the local docker engine or the docker hub that will be used to create a container while building the application form the pipeline.

The <u>service</u> keyword is used to link another docker image with one docker image to allow access to the service image during the build time. Example- mysql service. Whenever a mysql service is executed, that image is accessed and linked together to complete the build execution. Any image can be that is found on the docker hub can be used as a service, all it needs is just a configuration.

To register a GitLab runner for docker, the following commands need to be punched after starting the docker container.

```
gitlab-ci-multi-runner register \
  --url "https://gitlab.com/" \
  --registration-token "****************" \
  --description "docker-ruby-2.1" \
  --executor "docker" \
  --docker-image ruby:2.1 \
  --docker-postgres latest \
  --docker-mysql latest
```

The above command uses the ruby 2.1 image from docker hub and runs two latest services, postgres and mysql, which can be used at the time of execution of build.

The docker engine can be used to test and build our applications by using the GitLab runner service. When docker is used with GitLab CI, then each of the build is run in a separate container that is isolated, each having a predefined image as described in the .gitlab-ci.yml file.

To define an image or services that would be used for all the jobs listed in the gitlab yaml file, one image is listed at the top of the script.

```yaml
image: ruby:2.2

services:
  - postgres:9.3

before_script:
  - bundle install

test:
  script:
  - bundle exec rake spec
```

To define different images for different jobs the following type of syntax is used in the script.

```yaml
before_script:
  - bundle install

test:2.1:
  image: ruby:2.1
  services:
  - postgres:9.3
  script:
  - bundle exec rake spec

test:2.2:
  image: ruby:2.2
  services:
  - postgres:9.4
  script:
  - bundle exec rake spec
```

In the above script, test 2.1 pulls the ruby 2.1 image from docker hub, and a different version of the postgres service, whereas, test 2.2 pulls a ruby 2.2 image and a different version of postgres.

The docker integration works in the following manner:

* It creates any service container that is listed in the script file such as redis, musql or mongodb.
* Creates the build container and starts the container.
* Checkouts the code under the project name folder.
* Runs the steps defined in the script.
* Exits the scripts and removes all the services created in the container.

Many docker based projects can be tested and build using the docker engine through gitlab ci. 

#### GitSwarm Container Registry:

With this registry, every project has its own storage space to store docker images. In GitLab, it is auto-enabled for all the projects, but can also be enabled by having the administration permissions. 

We need to login to our registry, and build the docker images within the project we want to use it for.

```dockerfile
docker build -t registry1.gitlabeg.com/group1/newpro .
docker push registry1.gitlabeg.com/group1/newpro
```

#### Using GitSwarm Registry:

Once, we have created a registry, we can use it in the yaml file using the docker-in-docker image.

```yaml
build:
   image: docker:latest
   services:
   - docker:dind
   stage: build
   script:
     - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN registry.gitlabeg.com
     - docker build -t registry1.gitlabeg.com/group1/newpro:latest .
     - docker push registry1.gitlabeg.com/group1/newpro:latest
```

The variable $CI_BUILD_TOKEN  has been given value in the secret variables of the project. The above script pushes the images build on the docker hub.

The below script shows the complete execution of the pipeline using the yaml file.

```yaml
image: docker:latest
services:
- docker:dind

stages:
- build
- test
- release
- deploy

variables:
  CONTAINER_TEST_IMAGE: registry.example.com/my-group/my-project:$CI_BUILD_REF_NAME
  CONTAINER_RELEASE_IMAGE: registry.example.com/my-group/my-project:latest

before_script:
  - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN registry.example.com

build:
  stage: build
  script:
    - docker build --pull -t $CONTAINER_TEST_IMAGE .
    - docker push $CONTAINER_TEST_IMAGE

test1:
  stage: test
  script:
    - docker pull $CONTAINER_TEST_IMAGE
    - docker run $CONTAINER_TEST_IMAGE /script/to/run/tests

test2:
  stage: test
  script:
    - docker pull $CONTAINER_TEST_IMAGE
    - docker run $CONTAINER_TEST_IMAGE /script/to/run/another/test
release-image:
  stage: release
  script:
    - docker pull $CONTAINER_TEST_IMAGE
    - docker tag $CONTAINER_TEST_IMAGE $CONTAINER_RELEASE_IMAGE
    - docker push $CONTAINER_RELEASE_IMAGE
  only:
    - master

deploy:
  stage: deploy
  script:
    - ./deploy.sh
  only:
    - master

```



#### Links Referred:

1. "https://www.perforce.com/manuals/gitswarm/ci/yaml/README.html#image-and-services"

2. "https://www.perforce.com/manuals/gitswarm/ci/docker/README.html"

3. "https://www.perforce.com/manuals/gitswarm/ci/docker/using_docker_images.html"

4. "https://www.perforce.com/manuals/gitswarm/ci/docker/using_docker_build.html"

5. "https://www.perforce.com/manuals/gitswarm/container_registry/README.html"

   