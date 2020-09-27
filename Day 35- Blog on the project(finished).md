The `build` stage:

```yaml
build:
    image: maven:3.5-jdk-8-alpine
    stage: build 
    script:   
        - mvn compile
        - ls target/generated-sources
    artifacts: 
        paths: 
         - target/*.war
```

- In this stage, we are simply pulling a Maven image locally for this job, to execute the `mvn` (maven) commands, such as `mvn compile` . 
- By writing the `stage: build` tag, it defines that this job belongs to the build stage.
- Any command we want to run is written under the `script` tag. For this job, we have specified to run the `mvn compile` command, which compiles code residing in the repository, and fails in case of any error.
- `ls` command is just listing the contents of the folder on the console.
- All the artifacts generated in the process, such as the war files, are stored in the target folder. 
- We pulled the `maven:3.5-jdk-8-alpine` as it is a light-weight image, that includes basic commands only that we want to run.



- The `test` stage:

  ```yaml
  unittests:
      image: maven:3.5-jdk-8-alpine
      stage: test 
      script: 
          - mvn $MAVEN_CLI_OPTS test 
      artifacts:
          reports:
              junit:
                  - target/surefire-reports/TEST-*.xml
  
  ```

  - After the code has been compiled, the test cases written by the developer, which are run in this stage. 
  
  - This is known as unit testing.
  
  - `mvn $MAVEN_CLI_OPTS test` command is written to test all the unit test cases that has been written.
  
  - For unit testing, JUnit framework has been used in the project. 

  - All the JUnit reports generated are stored as an artifact in the target folder.

  - The `surefire-reports/TEST-*.xml` implies that a surefire dependency, which is a testing report dependency that generates an xml report, and can be viewed on the tests tab in the pipeline section.
  
  - The surefire dependency has been added to the `po.xml` file in the repository.
  
    
  
    ![](images/Screenshot%20(1149).png)
  
    ![](images/Screenshot%20(1150).png)
  
  

- Coming to the `codecoverage` stage:

  Code coverage includes the number of lines of code covered, or the percentage of code covered. More the test coverage, lesser is the chance of the code containing unidentified bugs left in the code.

  ```yaml
  codecoverage:
      image: maven:3.5-jdk-8-alpine
      stage: codecoverage
      script:
          - mvn clean verify
      artifacts:
          paths:
              - target/site/jacoco/index.html
  
  ```

  - In this project, we have used Jacoco as a code coverage tool. To include the Jacoco plugin, put the following plugin inside the POM file.

    ```xml
    <plugin>
      <groupId>org.jacoco</groupId>
      <artifactId>jacoco-maven-plugin</artifactId>
      <version>0.8.6-SNAPSHOT</version>
    </plugin>
    ```

    To avoid the generation of redundant reports with the `maven-site-plugin` the below mentioned plugins are used.

    ```xml
    <project>
      <reporting>
        <plugins>
          <plugin>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <reportSets>
              <reportSet>
                <reports>
                  <!-- select non-aggregate reports -->
                  <report>report</report>
                </reports>
              </reportSet>
            </reportSets>
          </plugin>
        </plugins>
      </reporting>
    </project>
    ```

  - Jacoco is a free library for Java for code coverage.

  - It provides a complete code coverage report in a `.html` format or which can be stored as an artifact and can be downloaded.

    ![](images/Screenshot%20(1151).png)

    ![](images/Screenshot%20(1155).png)

    ![](images/Screenshot%20(1154).png)

    ![](images/Screenshot%20(1153).png)



- The `codeQuality` stage:

  GitLab provides an in-built template that can simply be used to check the quality of code. To include that template, the following lines are added in the script:

  ```yaml
  include:
     - template: Code-Quality.gitlab-ci.yml
  ```

  And thereafter, the following script is added to perform the needful tasks.

  ```yaml
  code_quality:
     stage: codeQuality
     artifacts:
       reports:
         codequality: gl-code-quality-report.json
     after_script:
       - cat gl-code-quality-report.json
  ```

  - Code Quality makes sure that the project is easy to read, simple and other team members can understand and contribute easily. 

  - GitLab uses free, open-source, code climate engines, which are run in the pipeline.

  - In pipelines, it runs using a pre-built Docker image built in GitLab's code quality project.

  - A template can also be used provided by GitHub, as done in this project.

  - `SonarJava` code analyzer can be used to extend climate check, for climate checks in Java Projects.

  - It is included in the Code quality tab on the pipeline page.

    ![](images/Screenshot (1156).png)

  

- Security checks, the SAST stage:

  Security testing is done to spot the security issues, because at the time of building software, less concern is given to the security issues that could be left in the code. This is because of meeting the deadlines faster, focusing on the functionality and working of the code, which could result in unattended security issues in the code.

  Similar to the code quality template, GitLab also provides the security template, which needs to be included.
  
  ```yaml
  include:
     - template: SAST.gitlab-ci.yml

  ```

  - Since our project is Java based, along with Maven, so the SAST makes use of the [SpotBugs](https://spotbugs.github.io/) along with [find-sec-bugs](https://find-sec-bugs.github.io/) plugin as a scan tool.
  
  ```yaml
  spotbugs-sast:
    dependencies:
      - build
    script:
      - /analyzer run -compile=false
    variables:
    MAVEN_REPO_PATH: ./.m2/repository
    artifacts:
      reports:
        sast: gl-sast-report.json
  ```
  
  - It has dependency on the build stage, as all the artifacts are passed to this stage. 
  
  - The script `- /analyzer run -compile=false` is used to verify whether all the artifacts have been included in the working directory.
  
  - The path to the maven repository is also provided for the `SpotBugs` analyzer setting to Maven's local repository.
  
  - The reports generated are stored as an artifact which are in the `.json` format.
  
- The results are viewed in the GitLab pipeline page under the security tab.
  
  ![](images/Screenshot%20(1157).png)
  

  
- The `package` stage involves the docker-build job.

  ```yaml
  docker-build:
    stage: package
    script:
      - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN registry.gitlab.com
      - docker build -t registry.gitlab.com/ishitasinghal/messageboard . --build-arg BUILDKIT_INLINE_CACHE=1 
      - docker push registry.gitlab.com/ishitasinghal/messageboard
  ```

  - In this stage, the application is being packaged into a Docker container.

  - Through the docker commands written in the script, the image are being built, and pushed into the GitLab container registry provided by GitLab.

    ![](images/Screenshot%20(1158).png)

  - `docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN`

    To login into the GitLab's container registry, the `$CI_BUILD_TOKEN ` is an enviro  nment variable that is generated automatically and enables us to login to the GitLab container registry.

  - `docker build -t registry.gitlab.com/ishitasinghal/messageboard . --build-arg BUILDKIT_INLINE_CACHE=1 ` 

    The above command builds  an image. BuildKit is used as a build backend for caching mechanism, which is more faster and reliable. 

  - Finally, the image is pushed into the GitLab container registry. by `docker push`.



- The `deploy` stage:

  This stage consists the Kubernetes deploy job, where the application is deployed to the Google Kubernetes Engine.

  For this, we need to create a GCP account, which can be created from [here]([https://cloud.google.com/free/?utm_source=google&utm_medium=cpc&utm_campaign=japac-IN-all-en-dr-bkws-all-super-trial-b-dr-1008074&utm_content=text-ad-none-none-DEV_c-CRE_435278944951-ADGP_Hybrid+%7C+AW+SEM+%7C+BKWS+~+T1+%7C+BMM+%7C+General+%7C+1:1+%7C+IN+%7C+en+%7C+Cloud+%7C+gcp-KWID_43700037085435511-kwd-20903505266&userloc_9040168&utm_term=KW_%2Bgcp&gclid=EAIaIQobChMIta3B88jo6QIVmX8rCh1MwwzJEAAYASABEgKVY_D_BwE](https://cloud.google.com/free/?utm_source=google&utm_medium=cpc&utm_campaign=japac-IN-all-en-dr-bkws-all-super-trial-b-dr-1008074&utm_content=text-ad-none-none-DEV_c-CRE_435278944951-ADGP_Hybrid+|+AW+SEM+|+BKWS+~+T1+|+BMM+|+General+|+1:1+|+IN+|+en+|+Cloud+|+gcp-KWID_43700037085435511-kwd-20903505266&userloc_9040168&utm_term=KW_%2Bgcp&gclid=EAIaIQobChMIta3B88jo6QIVmX8rCh1MwwzJEAAYASABEgKVY_D_BwE)). After going to the GCP dashboard and create a service account, thereafter a cluster under the Kubernetes engine.

  ![](images/Screenshot%20(1065).png)

  ![](images/Screenshot%20(1066).png)

  And then create a cluster by any name.

  ![](images/Screenshot%20(1074).png)

  ![](images/Screenshot%20(1077).png)

  After all the prerequisites have been done on GCP, we will write the deployment script in our `.gitlab-ci.yml`. 

  ```yaml
  k8s-deploy:
   image: google/cloud-sdk:latest
   stage: deploy
   script:
   - echo "$GOOGLE_KEY" > key.json
   - gcloud auth activate-service-account ishita-singhal@my-project-ishita.iam.gserviceaccount.com --key-file key.json
   - gcloud container clusters get-credentials cluster-1-ishita --zone us-central1-c --project my-project-ishita
   - kubectl delete secret $DPRK_SECRET_KEY_NAME 2>/dev/null || echo "secret does not exist"
   - kubectl create secret docker-registry $DPRK_SECRET_KEY_NAME --docker-username="$DPRK_DOCKERHUB_INTEGRATION_USERNAME" --docker-password="$DPRK_DOCKERHUB_INTEGRATION_PASSWORD" --docker-email="$DPRK_DOCKERHUB_INTEGRATION_EMAIL" --docker-server="$DPRK_DOCKERHUB_INTEGRATION_URL"/
   - kubectl apply -f deployment.yml
  ```

  - `google/cloud-sdk:latest` is used as it contains all the basic dependencies and components of the google software development kit.

  - `$GOOGLE_KEY` is a secret variable along with `$DPRK_DOCKERHUB_INTEGRATION_USERNAME`, `$DPRK_DOCKERHUB_INTEGRATION_PASSWORD`, `$DPRK_DOCKERHUB_INTEGRATION_EMAIL`, `$DPRK_DOCKERHUB_INTEGRATION_URL`, whose values have been put in GitLab secret variables, as it contains secret details of a user to connect to the services. 

    For that, Go to project's Settings>CI/CD, and then expand the Variables tab and add all the secret variables there that includes the user details.

    ![](images/Screenshot%20(1159).png)

  - The `$GOOGLE_KEY` puts the json key value of the service account on GCP. 

  - ` gcloud auth activate-service-account ishita-singhal@my-project-ishita.iam.gserviceaccount.com --key-file key.json` command performs the authentication process of the service account. If the value mismatches, the authentication fails, and it throws an error.

  - `gcloud container clusters get-credentials cluster-1-ishita --zone us-central1-c --project my-project-ishita` command we download the `kubectl` configuration file to run the further script and connects to the cluster made on GCP.

  - `kubectl delete secret $DPRK_SECRET_KEY_NAME 2>/dev/null || echo "secret does not exist"`  command is required because Kubernetes API doesn't have a replace option for docker-registry, therefore if this statement is not used, it shows an error that a registry already exists.

  - `kubectl create secret docker-registry $DPRK_SECRET_KEY_NAME --docker-username="$DPRK_DOCKERHUB_INTEGRATION_USERNAME" --docker-password="$DPRK_DOCKERHUB_INTEGRATION_PASSWORD" --docker-email="$DPRK_DOCKERHUB_INTEGRATION_EMAIL" --docker-server="$DPRK_DOCKERHUB_INTEGRATION_URL"/` 

    This command authenticates with our private GitLab container registry, and downloads the images pushed in the registry.

  - `kubectl apply -f deployment.yml` finally uses the deployment file defined, and deploys the images to the GCP Kubernetes cluster.

    

    

      

  