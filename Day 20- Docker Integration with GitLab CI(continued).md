# Day 20- Docker Integration with GitLab CI(Hands-on)

The previous day, a thorough study from various blogs was done that included the deployment or release of docker images to the container registry. a hands-in session had been done for the same.

The container registry on the GitLab is enabled and a URL to that is achieved.

![](images/Screenshot(1046).png)

Secret variables containing the CI_build_Token is inputted.

![](images/Screenshot(1047).png)

Then, the GitLab runners are specified for the project under the settings tab.

![](images/Screenshot(1050).png)

The following commands are run in the command line to ensure that docker is logged in.

```
docker login registry.gitlab.com
```

![](images/Screenshot(1052).png)

Thereafter, a tag is attached to the image, that will be pushed to the container registry. The following command is used to do the purpose.

```
docker tag hello-centos6 registry.gitlab.com/gitschooldude/hello.centos6
```

![](images/Screenshot(1053).png)

Then we push our image following the command:

```
docker push registry.gitlab.com/gitschooldude/hello.centos6
```

![](images/Screenshot(1054).png)

![](images/Screenshot(1055).png)

After setting up the gitlab runners, and adding the secret variables for the project in the pipeline, the following script is used.

```yaml
# stages: 
#     - build
#     - test
#     - package
#     - deploy
    
# build:
#     stage: build
#     script: 
#         - echo " build"
    
# test:
#     stage: test
#     script: echo " unit tests"
    
# package:
#     stage: test
#     script: cat file.py file2.py | gzip > packaged.gz
#     artifacts:
#         paths:
#             - packaged.gz
    
# deploy:
#     stage: deploy
#     script: echo "Deployed"


image: docker:latest
services:
- docker:dind

stages:
- build
- test
- release
- deploy

variables:
  CONTAINER_TEST_IMAGE: registry.gitlab.com/ishitasinghal/newpro:$CI_BUILD_REF_NAME
  CONTAINER_RELEASE_IMAGE: registry.gitlab.com/ishitasinghal/newpro:latest

before_script:
  - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN registry.gitlab.com

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
 
# deploy:
#   stage: deploy
#   script:
#     - docker pull $CONTAINER_TEST_IMAGE
#     - docker tag $CONTAINER_TEST_IMAGEE $CONTAINER_RELEASE_IMAGE
#     - docker push $RELEASE_IMAGE
#   only:
#     - master
```

 The above script pops up with a build error, which says about the denied access.

![](images/Screenshot%20(1051).png)



#### Links referred:

1. "https://www.digitalocean.com/community/tutorials/how-to-build-docker-images-and-host-a-docker-image-repository-with-gitlab#prerequisites"
2. "https://gitlab.com/gitlab-org/gitlab-foss/-/issues/23058"
3. "https://docs.gitlab.com/ce/user/permissions.html#job-permissions"
4. "https://www.youtube.com/watch?v=wvhq082e2OY"