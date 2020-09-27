# Day 23- Getting Started with Circle CI

In the previous days, things were being implemented on GitLab CI, of building a pipeline to automate the release process ensuring a good quality software with code coverage updates. Below is the screenshot of the pipeline build from the project.

![](images/Screenshot%20(1096).png)

![](images/Screenshot%20(1094).png)

In this blog, we will be discussing about Circle CI. A brief discussion has also been done to show cast the differences between Jenkins, Circle CI.

#### Circle CI:

Circle CI is a cloud-based tool, to automate the build and release process that requires no dedicated server, that is, no need of manual maintenance. It provides multiple integrations, such as GitHub, AWS, Azure including the EC2 and several others.

It can be installed quickly, gives smart notifications, easy to begin, and caches the requirements and many third party dependencies.

![](images/Screenshot%20(1098).png)

#### Why Circle CI?

* VCS is directly from the user interface, unlike Jenkins in which we need to first install the VCS plugin. Repositories are imported as projects.
* Code snippets can be reused.
* It allocates more RAM and CPU to execute complex jobs.
* Automatically adds permissions for users at the time of VCS authentication, unlike Jenkins which requires manual setup for permissions.
* All jobs can be constructed within a single circle.yaml file unlike Jenkins, in which the information remains stored in Jenkins, and can't be shared. 
* The Web User Interface is a single page web application, and is changed frequently unlike Jenkins, the web interface is slower and less response because of more plugins.
* Every job is installed in a different environment in which all the dependencies are installed by default.
* No plugins are required as most of the core functionalities are built-in so we just need to add it in the shell script as required.
* The code can be debugged easily by the SSH feature.
* Built-in support for docker, services can be accessed by the yaml file whereas, Docker has no built-in Docker support.
* Parallel jobs can be achieved, unlike Jenkins, which is supported by multi-threading which can cause many issues related to file systems and database difficult to debug.

#### How Circle CI Works?

* The code is developed by the developers and checked including the initial compiling phase, ensuring no syntax errors, semantic errors or functional errors.
* The code is committed and the changes are taken care in the repository.
* A request is sent to the CI system of the circle ci.
* Jobs are run by the CI server, that includes tests, code coverage, syntax, dependencies, and other necessary things.
* The saved artifacts are released for further testing.
* A notification is provided to the team giving the build status whether the buils has passed or failed and the issue is then addressed by the team.

![](images/Screenshot%20(1097).png)



#### Cons of Circle CI:

* Problems may occur while customizing it for any third-party applications.
* Few out of the box languages are supported such as Go, Java, Python, Ruby on Rails, Haskell, Scala
* Only two versions of Ubuntu are supported for free and for MacOS, it has a paid part.

#### Links Referred:

1. "https://medium.com/hackernoon/continuous-integration-circleci-vs-travis-ci-vs-jenkins-41a1c2bd95f5"
2. "https://circleci.com/migrate-jenkins-to-circleci/"
3. "https://circleci.com/docs/2.0/migrating-from-jenkins/"
4. "https://www.educba.com/jenkins-vs-circleci/"
5. "https://www.javatpoint.com/jenkins-vs-circle-ci"