# Day 32- Blog on the project(continued)

## Understanding GitLab functioning

The continuous integration and continuous deployment service is provided by GitLab. GitLab functions on one configuration file which is the gitlab-ci.yml file. For each and every commit that is pushed to the GitLab repository, the added gitlab-ci.yml file is triggered which is run by the help of GitLab runners. The yaml file basically tells the runner what all jobs need to be carried out. 

The backbone for any project to involve CI/CD are pipelines. 

### GitLab CI/CD Pipelines

- In GitLab CI, pipelines are made up of mainly stages and jobs. 
  - Jobs define the task that has been carried out, and 
  - the stages define the order in which the jobs are to be executed. 
- Jobs are executed by [GitLab Runners](https://docs.gitlab.com/ee/ci/runners/README.html).
- Parallel jobs can be executed, this usually result in longer build time, if there are more than four parallel jobs, or the GitLab runners can be configured accordingly inspite of using the GitLab shared runners. We will the covering this concept later in the blog.
- If any of the stages fails in the pipeline, execution of the other further stages is stopped and the pipeline is said to have failed.

A general pipeline consists of mainly four stages, namely:

- The build stage that mainly carried out the compilation of the source code or the compile job.
- The test stage, in which unit testing on the source code is done, or the test job is carried out.
- The staging stage, where the source code is deployed to the staging environment.
- The production stage, where the source code is deployed to the production environment.

Other things about the pipeline, including the types of pipelines, can be referenced from [here](https://docs.gitlab.com/ee/ci/pipelines/index.html#types-of-pipelines), as it would deviate the main topic.

This is somewhat how the pipeline we are going to develop in the project looks like.

![](images/Screenshot%20(1133).png)

Usually a pipeline is automatically triggered when any changes are made in the files lying in the repository, only if the GitLab's configuration file, that is, the gitlab-ci.yml is present in the root of the repository.

GitLab keeps the whole metrics of the pipeline, the duration it ran, the number of success, which can be viewed in the Analytics Section on the GitLab project's page.

![](images/Screenshot%20(1134).png)

This is the pipeline analytics that has been shown below in the picture.

![](images/Screenshot%20(1136).png)

### GitLab Runners

The jobs in the pipelines are executed by GitLab runners. GitLab provides some runners by default, which are known as shared runners. Although, we can also configure runners according to our needs, which would eventually result in longer pipeline execution time. Below are mentioned a few features of runners:

- Installation of runners is quite easy on many platforms like Windows, macOS, Linux.
- It enables caching of docker containers.
- Jobs can run in docker containers, or locally, along with the parallel execution of multiple jobs.
- Makes use of token for each project, and the number of jobs per token can also be limited.

### GitLab configuration file

GitLab uses gitlab-ci.yml file to configure the things that are required to be done in the project. As soon as a change is committed, this file starts to run the job using runners for that commit.

