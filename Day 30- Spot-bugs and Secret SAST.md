# Day 30- Spot-bugs and Secret SAST

Static Application testing helps us realize the application source code, and the binaries, that reveals any security vulnerabilities in a non-running state.

Security testing is done to because at the time of building software, less concern is given to the security issues that could be left in the code. This is because of the meet the deadlines faster, and work on the functionality and working of the code, which could result in attended security issues in the code.

There are two ways of performing the security testing, Static Application Security Testing(SAST), which detects risks within the system for example, buffer overflow, SQL injection, and Dynamic Application Security Testing(DAST), detecting risks outside the system when the application is running by making use of penetration testing, both being used in different stages accordingly. 

### SAST In GitLab CI

GitLab CI provides the SAST template [GitLab CI SAST Template](https://docs.gitlab.com/ee/user/application_security/sast/#configuration) which can be used in the GitLab configuration yaml file.

The SAST reports can be viewed in the merge requests page or even in the security tab on the pipelines page.

![](images/Screenshot%20(1128).png)

![](images/Screenshot%20(1127).png)

The result are shown in different categories, sorted from critical to other security issues. The sorted list is as follows:

1. Critical
2. High
3. Medium
4. Low
5. Unknown
6. Everything else

Multiple support of packages and frameworks  has been provided supporting different languages. In the project being worked upon, in written in Java, a Spring Boot Application, using Maven  for which [SpotBugs](https://spotbugs.github.io/) program can be used, along with the plugin [find-sec-bugs](https://find-sec-bugs.github.io/).

### SAST Configuration

To make use of the GitLab SAST template, we need to include the template in out gitlab-ci.yml file.

```yaml
include:
	- template: SAST.gitlab-ci.yml
```

The result is stored as an artifact which can be easily downloaded or browsed on the web itself.

### SpotBugs

SpotBugs is a program that uses static analysis for finding bugs in a Java Program. It provides integrations with many tools, such as Ant, Maven, Gradle, and Eclipse.

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

This is the code for spotbugs used, through the SAST template provided by GitLab CI.

### SAST Analyzers

SAST includes third-party tools for static code analysis, different for different languages. Those tools are wrapped in analyzers. An analyzer converts the output in a common format, handles the execution of the code.

### Links Referred:

1. "https://docs.gitlab.com/ee/user/application_security/sast/#secret-detection"
2. "https://spotbugs.github.io/"
3. "https://find-sec-bugs.github.io/"
4. "https://github.com/find-sec-bugs/find-sec-bugs/"
5. "https://www.castsoftware.com/glossary/static-application-security-testing"