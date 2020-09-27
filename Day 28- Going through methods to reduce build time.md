# Day 28- Going through methods to reduce build time

### Reasons for increasing build time:

**1)** GitLab runner used in the project are shared runners. It's not the CPU and RAM that is affecting the build time, but the network congestion. Network is important in the pipeline as the build and the deploy stages require downloading dependencies and uploading artifacts to different servers. 

The network becomes too crowded on the shared runners provided by GitLab.

**2)** Downloading dependencies again and again can increase time unnecessarily. Docker images ca be used that can be pulled and the dependencies listed can be used, with pre-installed dependencies.

**3)** Pulling heavy images just to execute minimal commands, which even a light-weight image could do, for example pulling alpine image instead of a different heavy Linux distribution that could be much larger used for carrying out very simple tasks.

**4)** Even the irrelevant jobs run when a change in the source code is committed. For example, if some changes are done in a module in the front-end application, then the other tests for the other modules that haven't changed need not to be run again.

### Few methods to reduce time:

* Hosting own GitLab runner.

* Pre-installing dependencies.

* Using minimum amount of images that just fulfill the needs.

* Using an overlay Docker storage driver which creates a distributed network among multiple hosts. This can be done by adding an overlay variable at the top of the .gitlab-ci.yml file.

  ```yaml
  variables:
    DOCKER_DRIVER: overlay2
  ```

  Or, by adding an environment in the config.toml file while hosting our own runner.

  ```yaml
  environment = ["DOCKER_DRIVER=overlay2"]
  ```

* Using a cached docker image, as docker uses images in cache to build only the layers that have changes, thus speeding up the process of building. This can be done by using a cache option. 

  `-cache-from`:   Images to consider as cache sources

  The below mentioned script builds the image by using the inline-cache metadata, pushes it to a registry and use the image as cache source on another machine.

  ```dockerfile
  $ docker build -t myname/myapp --build-arg BUILDKIT_INLINE_CACHE=1 .
  $ docker push myname/myapp
  ```

  Pushed image can be used as cache from another machine on the following manner.

  ```dockerfile
  $ docker build --cache-from myname/myapp .
  ```

* Running jobs only when relevant files are changed. This reduces a lot of time wasted in re-running tests on irrelevant files. This can be done by assigning the tag in the yaml file which is only executed only when some changes are made. The "only:changes" key is paired with the merge request, the dependencies that may affect the job execution are kept included. 

  ```yaml
  job1:
    script:
      - yarn --cwd apps/job1example/ test
    only:
      changes:
        - apps/job1example/**/*
        - shared-dependencies/**/*
  job2:
    script:
      - yarn --cwd apps/job2example/ test
    only:
      changes:
        - apps/job2example/**/*
        - shared-dependencies/**/*
  ```

* The concept of caching also helps a lot, when the modules are specified so no time is wasted in looking for them anywhere else. The following script shows an example.

  ```yaml
  
  test:
    stage: test
    image: scala-sbt:8-jdk-alpine
    cache:
      key: "$CI_COMMIT_REF_NAME"
      untracked: true
      paths:
        - "sbt-cache/ivy/cache"
        - "sbt-cache/boot"
        - "sbt-cache/sbtboot" 
        - "sbt-cache/target"
    script:
      - sbt core/test api/test
    tags:
      - runner
  ```

* Try and reduce parallel jobs, as a runner can run only a limited number of parallel jobs, after 4 jobs, the others are paused and need to wait a lot.

### Autoscaling Runners

Such runners provide a flexible and dynamic way of utilizing the resources. In such a way, the runner can be configured my creating machines on demand. After the task has been completed, can be removed automatically. This way we don't need to pay more for machines. GitLab provides this feature, we just need to install the GitLab runners.

### An example for caching dependencies

```yaml
variables:
  MY_IMAGE: docker.mycompany.org/sso
stages:
- dockerize
- deploy
dockerize:
  stage: dockerize
  script:
  # Pull the cache image => populate the cache
  - docker pull $MY_IMAGE:uat_latest  || true
  # Build docker image from cache:
  - docker build --target runtime-image --cache-from $MY_IMAGE:uat_latest -t $MY_IMAGE:$VERSION -t $MY_IMAGE:uat_latest .
  # Push the version
  - docker push $MY_IMAGE:$VERSION
  - docker push $MY_IMAGE:uat_latest
  tags:
  - shell
  only:
  - uat
```

### Links Referred:

1. "https://blog.sparksuite.com/7-ways-to-speed-up-gitlab-ci-cd-times-29f60aab69f9"

2. "https://about.gitlab.com/blog/2019/08/21/making-builds-faster-autoscaling-runners/"

3. "https://insight.full360.com/how-to-speed-up-your-gitlab-ci-pipeline-7af593eb0f4c"

4. "https://medium.com/faun/ci-cd-boosting-your-pipeline-speed-up-to-x3-times-86a6c6ad46c9"

5. "https://stackoverflow.com/questions/56285802/how-to-reduce-docker-pull-time-during-ci-build"

   