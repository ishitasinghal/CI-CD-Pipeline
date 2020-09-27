# Day 5- Jenkins CI/CD Pipeline (Hands-on)

### <u>A brief  introduction:</u>

Jenkins is an open-source automation tool, to create CI/CD pipelines. A codified script is provided with the Jenkins pipeline. This script file clearly shows how the pipeline is built and different jobs are defined inside it to be executed. The jobs are executed sequentially and are structured inside multiple blocks to carry out different tasks.

Below is the script showing the details of the pipeline:

```script
pipeline {
	stages {
		stage("Build") {
			echo "Hello world"
			javac Hello.java
			java HelloWorld
			mvn clean package ./Package
			sh "ls -ltr"
		}
	}
}
```

The script executes each statement, first it displays the "Hello world" message on the console. Then a Java program names "Hello.java" is compiled and then executed. Next, a maven command is written which cleans and packages our application. This way, a build is made and finally a default shell is executed that lists the current files in the current directory.

Since, java, javac, and other commands are not available by default therefore we need to install and configure them in Jenkins. Therefore, Jenkins takes a lot of time in configuration.

### <u>Jenkins Hands-0n</u>

1) Download a jenkins.war file. Reach to that folder. Installed Java version is a prerequisite for Jenkins.

Now, to run up Jenkins, the command:

java -jar jenkins.war is run from the command and Jenkins opens up at port number 8080.

![](images/Screenshot%20(948).png)

![](images/Screenshot%20(949).png)

2) Now, we create a new Jenkins job by clicking on the new item icon in Jenkins and select a pipeline option.

![](images/Screenshot%20(950).png)

3) There are two ways to execute a Jenkins pipeline:

* With a codified script by making a jenkinsfile.
* With Source Code Management(SCM).

<u>Executing via a script:</u>

```
pipeline {
   agent any

   stages {
      stage('Build') {
        steps {
          echo 'Building...'
          echo "Running ${env.BUILD_ID} ${env.BUILD_DISPLAY_NAME} on ${env.NODE_NAME} and JOB ${env.JOB_NAME}"
        }
   }
   stage('Test') {
     steps {
        echo 'Testing...'
     }
   }
   stage('Deploy') {
     steps {
       echo 'Deploying...'
     }
   }
  }
}
```

In the above script, "agent any" instruction is the agent configuration. Here, it means that Jenkins can run the job on any of the available nodes. Thereafter, the three stages are defined- build, test, and deploy each having their tasks. The script is saved and the changes is applied.

![](images/Screenshot%(951).png)

![](images/Screenshot%(952).png)

Now, we need to start building the pipeline. For that we need to enter the build now option. The pipeline is built, below is the snapshot for the same.

![](images/Screenshot%(953).png)

<u>Executing via SCM</u>

For executing the script file from the SCM like Git, we just need to copy the URL of the file and put it in the execution from the SCM option. The script runs and the pipeline is built.

![](images/Screenshot%20(954).png)

#### That's how a simple basic pipeline is built using a script in Jenkins.



The concept of a serverless CI/CD pipeline is also being evolved as seen in blogs, which would require no servers or nodes to manage, saves costing. Although it has cons as well as slower boot time, no root access, and many more.

### <u>Problems with Jenkins</u>

* <u>Too many plugins:</u> Having an option of several plugins is a good thing, but the issue is for every task we need to perform, requires installing and configuring a plugin for each of them. For example want to develop a docker environment, we need to install and configure that plugin, if we want to pull something from Github, then we require a plugin, and likewise all the others. Therefore, much of the time is lost in installing and configuring plugins for performing a basic task.
* <u>Conflate CI/CD:</u> The biggest problem with Jenkins is that software teams sometimes mix up the CD things in CI, which requires more than a CI server.
* <u>Microservices not supported well:</u> Not much support is provided for testing and integration of multiple microservices at once.
* <u>Old-generation tool:</u> As CI tools were made long before, it doesn't takes full advantage of the new technology tools like Docker containers.

### <u>CI/CD with Jenkins and Docker</u>

The main tools required are:

* Jenkins- CI/CD
* GitHub — SCM
* Docker — Container
* JaCOCO — Code Coverage Tool for SCA (software code analysis)
* Gradle — Build tool
* Ansible — Configuration Management
* Ansible/Github/Docker/Cucumber

#### <u>Links referred:</u>

1. "https://opensource.com/article/19/9/intro-building-cicd-pipelines-jenkins"

2.  "https://github.com/bryantson/CICDPractice"

3.  "https://www.jenkins.io/solutions/pipeline/"

4.  "https://medium.com/@khandelwal12nidhi/ci-cd-with-jenkins-and-ansible-e9163d4a6e82"

5.  "https://www.jenkins.io/blog/2018/08/06/serverless-cicd-jenkins/"

6. "https://thenewstack.io/many-problems-jenkins-continuous-delivery/"

   

