# Day 33- Blog on the project(continued)

## Creating a GitLab Project 

We are going to create a CI/CD pipeline that would build an artifact, perform unit tests, package into war files, perform some more tests and finally deploys it to the Kubernetes cluster on GCP. We will be learning about every stage in the pipeline. The project is built in Java, using Maven, in which test cases have been written by JUnit framework. So let's get started!

Firstly, an account is required on GitLab, which is free to create. It allows GitHub integration as well, so we can login to GitLab through GitHub also. You can create the account from [here](https://gitlab.com/users/sign_in).

After creating an account, click on the "New project" button.

![](images/Screenshot%20(1137).png)

For this demonstration, we would be cloning a project from GitLab itself. To do that, follow with the steps:

After clicking on the new project option, select the "Import project" tab and select the "git Repo by URL" option.

After doing this, insert "https://gitlab.com/petermyren/messageboard" in the URL tab.

![](images/Screenshot%20(1138).png)

And click on "Create" button. This will clone the repository into your GitLab account.

![](images/Screenshot%20(1139).png)

Everything is set now, and we are ready to set up our CI/CD pipeline.

## Creating a sample .gitlab-ci.yml file

Pipelines in GitLab are configured using a YAML file, the ".gitlab-ci.yml" file for each project in the root of the directory. It defines the structure of the pipeline, and the order in which the jobs are to be executed. Jobs are executed using GitLab runners. 

We can configure our own GitLab runners, although for this project, we will be using the GitLab shared runners. 

A pipeline consists of jobs and stages. Try out first a very basic pipeline, that will give the overview for how things are going to work and how the jobs are actually carried.

Create a new file named ".gitlab-ci.yml", in the root of the repository and put the following script into it and commit the changes done in the repository.

![](images/Screenshot%20(1143).png)

```yaml
stages: 
    - build
    - test
    - package
    - deploy
    
build:
    stage: build
    script: 
        - echo " build"
    
test:
    stage: test
    script: echo " unit tests"
    
package:
    stage: test
    script: cat file.py file2.py | gzip > packaged.gz
    artifacts:
        paths:
            - packaged.gz
    
deploy:
    stage: deploy
    script: echo "Deployed"
```

![](images/Screenshot%20(1144).png)

![](images/Screenshot%20(1145).png)

The pipeline is automatically triggered and the GitLab runners start to execute the jobs written in .gitlab-ci.yml file.

Navigate to the CI/CD tab in the side bar and select pipelines to view the built pipeline.

![](images/Screenshot%20(1146).png)

So that's how you built a sample pipeline. Now let's proceed to actually configure pipeline for our project.



