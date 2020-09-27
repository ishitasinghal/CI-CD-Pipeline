# Day 2- Deploying through Pipeline 

The previous day the pipeline reached the code analysis stage. Today, how docker is used for containerization, how the application is deployed further is learned.

1) Installation on Docker on Windows. Since only Pro and Enterprise versions were not compatible with Docker, therefore an alternative to this was "Docker Toolbox".

To check the proper installation of docker, a docker hello-world image is run. When it is not found locally, it pulls that image form the Docker hub and returns with a message depicting the proper installation of docker.

Next, the project is cloned from Github, to avoid any missing files, and a Dockerfile is made inside the root directory of the project. A docker file basically consists of the commands and arguments which we want to run.

Dockerfile:

```dockerfile
FROM alpine:3.5
RUN apk add --update python py-pip
COPY requirements.txt /src/requirements.txt
RUN pip install -r /src/requirements.txt
COPY app.py /src
COPY buzz /src/buzz
CMD python /src/app.py
```

This Dockerfile pulls an alpine image from Dockerhub when not found locally in the system.

Then it runs updates to ensure that python and pip installer is up to date.

It takes all the requirements from the project using the "requirements.txt file".

Then the pip command is run to install and run the updates.

It then copies to the web application and the main code to the src folder, and finally runs the application.

This dockerfile is build to get an image according to the requirements and dependencies to run our application. It turns into an image after executing all the commands written in the dockerfile.

When the image is run, the application runs successfully.

We keep pushing our code to Github, to ensure continuous integration, and the process is done in TravisCI whenever any changes occur or something is pushed in the source code repository.

2) a .travis file is made along with a script file, so that whenever a new build is made, at the end of the pipeline, a new image is pushed on docker.

Below is the script attached:

```shell
#!/bin/sh
docker login -u $DOCKER_USER -p $DOCKER_PASS
if [ "$TRAVIS_BRANCH" = "master" ]; then
    TAG="latest"
else
    TAG="$TRAVIS_BRANCH"
fi
docker build -f Dockerfile -t $TRAVIS_REPO_SLUG:$TAG .
docker push $TRAVIS_REPO_SLUG:$TAG
```

The image is pushed to the Dockerhub.

The next build goes through all the stages through the TravisCI, and gives the build status, that is pass or fail.

Now, when the image is pulled and launched again from the dockerhub, the application works successfully.

3) Deploying this small web application to a cloud platform, Heroku is used.

Finally, our application is deployed to that platform.



##### So that's how I was able to make out how actually a pipeline works and how different tools are integrated together to fulfill a specific purpose. From the next day onwards , working would start on which tool suits our project the best and start to explore those.

