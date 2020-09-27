# Day 15- Docker Integration with GitLab CI

GitLab Runner can also use Docker engine to test and build any application. Docker hub contains many pre-built images which can be pulled as per the needs.

Docker run each job in the separate container, in the image which is already defined in the GitLab's yaml configuration file- .gitlab-ci.yml.

Benefit of using a docker engine is that all the commands can be tested in shell itself instead of running it on a separate CI server.

#### The "image" keyword: 

It has the name of the Docker image, that is used by the docker executor to perform the continuous integration tasks that have been assigned to it. It pulls the images from the docker hub or that can be configured by the gitlab configurations to allow the usage of local images.

#### The "service" keyword:

It can be called just another name for a docker image and it allows to access service image during the build time. For example running database service like mysql. This feature provides only network accessible services such as database.

Gitlab runner is configured accordingly to the installation of docker engine. The "dind", that is docker-in-docker image can also be another approach to run the job script in GitLab. In docker in docker image, each job runs in a separate container.

```
sudo gitlab-runner register -n \
  --url https://gitlab.com/ \
  --registration-token ****shgr76**** \
  --executor docker \
  --description "My Docker Runner" \
  --docker-image "docker:19.03.8" \
  --docker-privileged \
  --docker-volumes "/certs/client"
```

The above registers a new runner and use the docker image. 

Now, the docker can be used in the build script. It also requires addition of certain variables in the gitlab environment.

```yaml
image: docker:19.03.8

variables:
  DOCKER_TLS_CERTDIR: "/certs"

services:
  - docker:19.03.8-dind

before_script:
  - docker info
build:
  stage: build
  script:
    - docker build -t my-docker-image .
    - docker run my-docker-image /script/to/run/tests
```

In the above script, the docker image is pulled with the provided version number, including the service of the docker in docker.

Docker info is run before the script to display the information about the complete docker installation including the kernel versions, number of containers and images, where the data is stored, where is the metadata file and things like that.

The advantage of using docker-in-docker is that it helps builds the image faster as the existing image layers will not download it and will only work.

Whenever a docker build command is run, the instructions written in the Dockerfile runs in layers and are stored as cache. These layers can be reused if no changes are there. If one layer is changed, then all the other layers are affected.

The caching of such layers takes place in the following manner:

```yaml
image: docker:19.03.8

services:
  - docker:19.03.8-dind

variables:
  DOCKER_HOST: tcp://docker:2376
  DOCKER_TLS_CERTDIR: "/certs"

before_script:
  - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY

build:
  stage: build
  script:
    - docker pull $CI_REGISTRY_IMAGE:latest || true
    - docker build --cache-from $CI_REGISTRY_IMAGE:latest --tag $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA --tag $CI_REGISTRY_IMAGE:latest .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
    - docker push $CI_REGISTRY_IMAGE:latest
```

In the build stage, the docker uses the cache and pulls from the docker registry, and if the layer is available, it reuses it. Finally, if not in cache, then a new image is built and pushed on the container registry so that it may be used further as cache.

#### Links referred:

1. "https://docs.gitlab.com/ee/ci/docker/using_docker_images.html"
2. "https://about.gitlab.com/blog/2016/08/26/ci-deployment-and-environments/"
3. "https://docs.gitlab.com/ee/ci/docker/using_docker_images.html"
4. "https://docs.gitlab.com/ee/ci/docker/README.html"

