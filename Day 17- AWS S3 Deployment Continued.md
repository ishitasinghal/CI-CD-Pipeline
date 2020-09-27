# Day 17- AWS S3 Deployment Continued.

On the previous day, all the policies had been added for the IAM user who had been granted access to put or deploy maven artifacts to the S3 bucket, after going through the build, test and the package stages, when it finally goes to the deploy stage.

The IAM user by the name "userish" is created and the programmatic access is provided to the created user.

![](images/Screenshot%20(1028).png)

The additional policies are added to the user so that the user could get a full access to the bucket.

![](images/Screenshot%20(1029).png)

The access key ID and the secret key to the user is downloaded and kept safe for the user as that would be required while working with the GitLab CI pipeline.

![](images/Screenshot%20(1031).png)

Then, the secret variables are added to the project  for the project we want to implement, in the variables section.

![](images/Screenshot%20(1033).png)

The GitLab script that is the .gitlab-ci.yml file has been shown below:

```yaml
image: maven:latest

variables:
   MAVEN_CLI_OPTS: "-s .m2/settings.xml --batch-mode"
   MAVEN_OPTS: "-Dmaven.repo.local=.m2/repository"
   BUCKET_NAME: example-bucket4        
   
cache: 
    paths:
    - .m2/repository/
    - target/

stages:
   - build
   - test
   - package
   - deploy
   
build:
    stage: build 
    script:   
    - mvn compile
    artifacts: 
        paths: 
         - target/*.war

test: 
    stage: test 
    script: 
        - mvn $MAVEN_CLI_OPTS test 
        - echo "The code has been tested" 
    
package: 
    stage: package 
    script: 
        - mvn $MAVEN_CLI_OPTS package -Dmaven.test.skip=true 
        - echo "Packaging the code" 
    only: 
     - master 
     
     
deploy:
    stage: deploy
    image:
        name: banst/awscli
        entrypoint: [""]
    script:
        - aws configure set region us-east-1
        - aws s3 cp target/ s3://${BUCKET_NAME}/artifact1 --recursive
```

Here, in this the artifact will be deployed by the name of "artifact1" to the created S3 bucket named, "example-bucket4".

The pipeline is triggered as soon as changes in any code is made, and it passes all the stages successfully.

The pipeline with the latest tag is the successful pipeline with all stages passed and our artifact deployed.

![](images/Screenshot%20(1035).png)

![](images/Screenshot%20(1038).png)

![](images/Screenshot%20(1039).png)

When the AWS console is visited, we see that the artifact by the name "artifact1" has been deployed successfully in the S3 bucket.

![](images/Screenshot%20(1036).png)

![](images/Screenshot%20(1037).png)



