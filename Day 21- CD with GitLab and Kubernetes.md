# Day 21- CD with GitLab and Kubernetes

Continuous Delivery and Continuous Deployment has benefits in releasing a good quality code more efficiently and faster by the help of automated pipelines. 

In the following demonstration, a Spring Boot Application is taken which is built and deployed on a Kubernetes cluster.

#### A brief about Kubernetes:

Kubernetes is basically a tool used to orchestrate or manage containerized applications such as docker containerized application. Kubernetes clusters are made that helps to make the application highly available, scalable and easy deployments for frequent changes. Containers are bundled together including all the dependencies and libraries required, which helps run the application on any infrastructure.

#### Creating Kubernetes Cluster on Google Cloud Platform(GCP):

To deploy our application to the GCP Kubernetes cluster, a GCP account is required. After logging to our GCP, first of all a project is created, "my-project-ishita" as in this demonstration.

![](images/Screenshot%20(1065).png)

![](images/Screenshot%20(1066).png)

Service accounts for an IAM user is created accordingly, and roles and permissions are provided. 

![](images/Screenshot%20(1069).png)

A private key is created and downloaded in the .json format.

![](images/Screenshot%20(1071).png)

Then, a cluster is created, by the name, cluster-1-ishita.

![](images/Screenshot%20(1074).png)

![](images/Screenshot%20(1077).png)

We need to create a kubernetes deploy job, to deploy our application to the Kubernetes engine. 

#### Continuous Delivery Pipeline with GitLab:

As soon as a code is committed, the pipeline is triggered and .gitlba-ci.yml file starts to execute according to the instructions written in them. The following is the deploy job.

```yaml
k8s-deploy-staging:
  image: google/cloud-sdk
  stage: deploy
  script:
  - echo "$GOOGLE_KEY" > key.json
  - gcloud auth activate-service-account ishita-singhal@my-project-ishita.iam.gserviceaccount.com --key-file key.json
  - gcloud container clusters get-credentials cluster-1-ishita --zone us-central1-c --project my-project-ishita
  - kubectl create secret docker-registry registry.gitlab.com --docker-server=https://registry.gitlab.com --docker-username=ishita31 --docker-password=docker@ish --docker-email=500060649@stu.upes.ac.in
  - kubectl apply -f deployment.yml --namespace=staging
  environment:
    name: staging
    url: https://example.staging.com
  only:
  - master

```

In the above job, the google/cloud-sdk image is used as it is pre-loaded with dependencies. For the deployment part, we push images to the registry using the commands provided in the script.

We connect to the cluster created on GCP.

![](images/Screenshot%20(1080).png)

We package the Spring Boot Application by creating the Dockerfile in the root directory of the repository.

```dockerfile
FROM openjdk:8u111-jdk-alpine
VOLUME /tmp
ADD /target/actuator-sample-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

This docker file first pulls a base docker image, java image and then mounts a volume and adds the created jar file to the volume created. The ENTRYPOINT command executes whenever container is started.

#### Defining the Kubernetes Deployment:

To define the Kubernetes deployment, we need to create a deployment file. By this file, replicas are defined for the number of pods.

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: actuator-sample
spec:
  replicas: 2
  template:
    metadata:
      labels:
        app: actuator-sample
    spec:
      containers:
      - name: actuator-sample
        image: registry.gitlab.com/ishitasinghal/actuator-sample
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
      imagePullSecrets:
        - name: registry.gitlab.com
```

By the following script, 2 replicas of the pods are created. the imagePullSecrets field authenticates with the GitLab container registry.

#### .gitlab-ci.yml file:

```
image: docker:latest
services:
  - docker:dind

cache:
  paths:
    - ./.m2/repository
  key: "$CI_BUILD_REF_NAME"  

variables:
  MAVEN_OPTS: "-Dhttps.protocols=TLSv1.2 -Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=WARN -Dorg.slf4j.simpleLogger.showDateTime=true -Djava.awt.headless=true"
  MAVEN_CLI_OPTS: "--batch-mode --errors --fail-at-end --show-version -DinstallAtEnd=true -DdeployAtEnd=true"
  DOCKER_DRIVER: overlay
  SPRING_PROFILES_ACTIVE: gitlab-ci

cache:
  paths:
    - .m2/repository



stages:
  - build
  - package
  - deploy

maven-build:
  image: maven:3.6.0-jdk-8-alpine
  stage: build
  script: "mvn package -B $MAVEN_CLI_OPTS"
  artifacts:
    paths:
      - target/*.jar

docker-build:
  stage: package
  script:
  - docker build -t registry.gitlab.com/ishitasinghal/actuator-sample .
  - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN registry.gitlab.com
  - docker push registry.gitlab.com/ishitasinghal/actuator-sample

k8s-deploy-staging:
  image: google/cloud-sdk
  stage: deploy
  script:
  - echo "$GOOGLE_KEY" > key.json
  - gcloud auth activate-service-account ishita-singhal@my-project-ishita.iam.gserviceaccount.com --key-file key.json
  - gcloud container clusters get-credentials cluster-1-ishita --zone us-central1-c --project my-project-ishita
  - kubectl create secret docker-registry registry.gitlab.com --docker-server=https://registry.gitlab.com --docker-username=ishita31 --docker-password=docker@ish --docker-email=500060649@stu.upes.ac.in
  - kubectl apply -f deployment.yml --namespace=staging
  environment:
    name: staging
    url: https://example.staging.com
  only:
  - master

k8s-deploy-production:
  image: google/cloud-sdk
  stage: deploy
  script:
  - echo "$GOOGLE_KEY" > key.json
  - gcloud auth activate-service-account ishita-singhal@my-project-ishita.iam.gserviceaccount.com --key-file key.json
  - gcloud config set compute/zone us-central1-c
  - gcloud config set project my-project-ishita
  - gcloud config set container/use_client_certificate True
  - gcloud container clusters get-credentials cluster-1-ishita --project my-project-ishita
  - kubectl delete secret registry.gitlab.com
  - kubectl create secret docker-registry registry.gitlab.com --docker-server=https://registry.gitlab.com --docker-username=ishitasinghal --docker-password=registry@ish --docker-email=500060649@stu.upes.ac.in
  - kubectl create namespace production
  - kubectl apply -f deployment.yml --namespace=production
  environment:
    name: production
    url: https://example.production.com
  when: manual
  only:
  - production
```

This is the yaml file that would execute the pipeline. The images and services defines the image name that is docker, which is used. The docker-in-docker service is used which acts as a linked container for our pipeline. 

The variables are set on the build environment, which provides a signal to tell which storage needs to be used.

Thereafter, three stages in our pipeline are defined, which are build, package, and deploy which defines the build lifecycle of our pipeline. These are executed sequentially in the order they are defined.

The maven build job pulls the maven image, and packages the build by the mvn package -B command. This includes the validate, compile and test phases in our project. The unit tests are also run in the project. The package phase finally creates an executable jar file.

In the docker build job, our application is packaged in a container, and the image is pushed into the GitLab container registry.

Finally, two environments are defined in the file, which has the staging environment and the production environment through which we can roll back the changes in the latest versions in case of any bugs or discrepancies.

![](images/Screenshot%20(1081).png)

![](images/Screenshot%20(1082).png)

#### Links Referred:

1. "https://about.gitlab.com/blog/2016/12/14/continuous-delivery-of-a-spring-boot-application-with-gitlab-ci-and-kubernetes/"
2. "https://docs.gitlab.com/ce/user/packages/container_registry/index.html"
3. "https://www.digitalocean.com/community/tutorials/how-to-automate-deployments-to-digitalocean-kubernetes-with-circleci"
4. "https://docs.gitlab.com/ce/user/packages/container_registry/index.html"