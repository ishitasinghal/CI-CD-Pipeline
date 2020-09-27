# Day 16-  AWS S3 Bucket with GitLab

#### Amazon S3:

Amazon S3 stands for Simple Storage Service. It is a basically a storage for the internet, that can be used to store any amount of data, and retrieve it at any time, from anywhere on the web. It is reliable and faster. 

#### Setting up AWS S3  for the deployment :

In the below demonstration, a static website is used to be hosted. It can't host a dynamic website as it is just a storage container. So let's get started!

For setting up S3, we need to navigate to the management console and go to the storage section and choose S3. The bucket name is entered, say, example-bucket3.

![](images/Screenshot%20(1013).png)

![](images/Screenshot%20(1014).png)

Since we need the AWS S3 bucket for hosting a static website, therefore we need to set the property accordingly. 

![](images/Screenshot%20(1015).png)

![](images/Screenshot%20(1016).png)

To grant the read permissions to everybody using the bucket, we need to set up a policy written in  .json format. 

```json
{
  "Version":"2012-10-17",
  "Statement":[{
    "Sid":"PublicReadGetObject",
    "Effect":"Allow",
	  "Principal": "*",
    "Action":["s3:GetObject"],
    "Resource":["arn:aws:s3:::example-bucket3/*"]
  }]
}
```

![](images/Screenshot%20(1024).png)

Now we need to create an IAM user to push the data. For that we need to apply one more policy to that IAM user.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::example-bucket3/*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": "*"
        }
    ]
}
```

![](images/Screenshot%20(1022).png)

![](images/Screenshot%20(1023).png)

The aws requirements for S3 bucket are all set, now we need to configure the variables in GitLab CI/CD. The two variables are:

- *AWS_ACCESS_KEY_ID* with the new user’s access key on aws 
- *AWS_SECRET_ACCESS_KEY* with the new user’s access secret key on aws

Accordingly the GitLab yaml file is configured that will deploy the artifacts to the AWS S3 bucket.

```yaml
image: "registry.gitlab.com/rpadovani/rpadovani.com:latest"
stages:
  - build
  - deploy

variables:
  AWS_DEFAULT_REGION: eu-central-1 
  BUCKET_NAME: example-bucket3         

cache:
  paths:
    - vendor

buildJekyll: methods
  stage: build
  script:
    - bundle install --path=vendor/
    - bundle exec jekyll build --future 
  artifacts:
    paths:
      - _site/  

deploys3:
  image: "python:latest" 
  stage: deploy
  dependencies:
    - buildJekyll     
  before_script:
    - pip install awscli
  script:
    - aws s3 cp _site s3://${BUCKET_NAME}/${CI_COMMIT_REF_SLUG} --recursive 
  environment:
    name: ${CI_COMMIT_REF_SLUG}
    url: http://${BUCKET_NAME}.s3-website.${AWS_DEFAULT_REGION}.amazonaws.com/${CI_COMMIT_REF_SLUG}  
    on_stop: clean_s3

clean_s3:
  image: "python:latest"
  stage: deploy
  before_script:
    - pip install awscli
  script:
    - aws s3 rm s3://${BUCKET_NAME}/${CI_COMMIT_REF_SLUG} --recursive 
  environment:
    name: ${CI_COMMIT_REF_SLUG}
    action: stop
  when: manual
```



#### Links Referred:

1. "https://rpadovani.com/aws-s3-gitlab"
2. "https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html"
3. "https://supsystic.com/documentation/id-secret-access-key-amazon-s3/"
4. "https://www.youtube.com/watch?v=nIyhr4vCGuc"