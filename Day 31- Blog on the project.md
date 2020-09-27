# Day 31- Blog on the project



## Continuous Integration

Continuous Integration is a practice followed by developers, that involves integrating code changes and commit frequently in a shared repository. Version Control Systems like GitHub, BitBucket can be used for the purpose. 

This involves automated builds and tests whenever a code change is committed to the VCS. Earlier the developers used to work in isolation, and when it was time to integrate the code, it became very difficult to resolve the bugs and the conflicts, which would consequently waste time as well as efforts. Such problems can be avoided by the help of CI. The branches may keep the work isolated for a short span just to make sure no that there is no conflict on merging and the master branch is able to meet the desirable results.

Continuous Integration also involves automated tests on the application so that any bug in the code is caught early in the development process and fixed. These automated tests are run for every build. This doesn't make the code bug free, but it just takes care that the codes are caught and fixed beforehand, rather than at a later stage when it becomes too complicated to fix them.

Therefore, the practice of CI is being adopted by developers to have faster builds, automating the process of builds and tests to increase efficiency and do it at a faster rate.



## Continuous Deployment

Continuous Deployment is a part of release pipeline, a plan to release software only after it has passed all the tests successfully and is automatically released into the production environment.

It just deploys the feature to the production environment but not actually running the code until the team makes the decision to release the feature. 



## Continuous Delivery

Continuous Delivery is similar to continuous deployment, what is different is juts the last manual approval of whether or not to release the product into the production. In the delivery stage, developers review the code changes, merge them and then package them into an artifact. The package waits for an approval, in which the package is opened and reviewed again with system of automated checks. When it passes all the set of tests, at that time, the code is deployed automatically.



## About GitLab

GitLab is an open-source project, having over 2000 contributors, made to allow different teams to work together and make the software development better and faster. It is a single application, or a DevOps platform as a single application, to carry out the complete DevOps life cycle. It makes the lifecycle much shorter. The lifecycle includes the general stages such as **<u>managing</u>** applications, **<u>planning</u>**, **<u>creating</u>**, **<u>verifying</u>**, **<u>packaging</u>**, **<u>configuring</u>**, **monitoring**, and making our application more **<u>secured</u>**.



### Managing the application:

GitLab provides a feature to manage teams which lets them be aware of how the business is being performed. It helps the teams to optimize the software delivery life cycle by making use of metrics. 

- New groups and sub-groups can be made by the owners or the administrators.
- The custom templates for groups provided by GitLab can be used which makes all the public projects available for that group.

### Planning:

GitLab provides planning tools to make sure that the teams are working on the right track. It makes use of the certain tools to feature planning.

-  <u>Issue tracking</u>- Any issue in the project can be created that would provide a medium to collaborate on a new idea, implementation on new code, tracking status.
- <u>Kanban Boards</u> to list out the task status, what all has been done and what is left.
- <u>Time tracking</u> to find out the approximate time spent on issues.
- <u>Quality Management</u> to track the quality of the project.

There are many more features under planning which can be explored and utilized.

### Create the code:

We can create, edit, view, and manage code, can create branches by GitLab repositories. Teams can follow the workflow without disruption.

- It provides source code management, enabling collaboration, merging branches, tracking changes, and rolling back in case of failures.
- Code reviews to discuss changes, identify defects and automate them.
- Web IDE to edit the code and make changes online and contribute.
- Snippets can be used to share small pieces of code with other users.

### Verifying the code:

GitLab also helps us to verify to the code maintaining code quality, and coding standards necessary for production. These includes-

- Continuous Integration to automate the builds, tests, security checks, and verify each commit.
- Code quality to check whether on some commit the quality of the code has been degraded or improved.
- Unit testing along with the code coverage to ensure that modules perform functionality as expected.
- Load Testing to ensure that changes are validated with the real world scenarios.

### Packaging the code:

GitLab enables to package applications and dependencies together and build artifacts so that they work in the supply chain. The feature provides the following things for package management:

- Package registry, where the project's packages and dependencies are stored.
- Container registry which is used as a private registry to store the docker images. The images can be created, pushed and retrieved.

### Configuring the application:

Application environments can be configured and managed through GitLab. Secret variables can be used to limit access to only authorized access and processes. Kubernetes integration reduces effort to define the infrastructure required to run the application.

- Auto DevOps
- Kubernetes Management

### Monitoring the application:

GitLab monitors metrics automatically to track how the changes made in the code impacted the production environment. Monitoring provides feedbacks which can be responded quickly.

- GitLab displays information for the projects.
- Error tracking to discover errors that the application might be throwing, increasing awareness.

### Security:

Security issues are left unaddressed at the time of development of any application. GitLab provides built-in templates for security checks.

- SAST to check the security issues within the system.
- DAST for security checks outside the system when the application is running.
- Security tests.

### Releasing the application:

GitLab automates the release pipeline and the delivery of applications. The applications can be delivered with zero-touch. The makes the process quite short and faster. 

- Deliver changes to production with zero-touch and allows release through the path defined for production.
- GitLab pages use static site generator that creates websites and are deployed by GitHub.

### Defending the application:

GitLab also takes care of the security issues of an application during the runtime, such as threat detection, data security, and application infrastructure security.

- In the Auto DevOps feature provided by GitLab, a Web Application Firewall(WAF) feature is enabled which examines the traffic being sent to the application and detects malicious content before it reaches the application and cause harm. 
- Container Network Security blocks the unauthorized network between the pods and the internet while implementing Kubernetes.



## Why GitLab?

- GitLab has a shorter life cycle. A shorter feedback life cycle also has some more benefits like:
  - Reducing risks such as:
    - It is easy to estimate smaller iterations.
    - Every minute changes in the code gets attention, which results in better code quality.
  - The applications are more secured as many security defects can be rectified during the development time only .

- The Silos and Stages are connected together, as it is a single platform, where every team is aware of everything, GitLab links all the information together.
- Through GitLab, every stage involved in the DevOps lifecycle can be automated.

In a nutshell, GitLab gives the following key advantages:

- Even minute changes are taken care of, fully tested, secured and are not ignored, resulting in better code quality.
- A feedback loop is created through GitLab's monitoring tools.
- Common single platform, so each team member is on the same page sharing the common things.
- Feedbacks are at the same place where they the code are written and rectified in case of any fault.
- There is complete visibility and control over the project, where it is leading to.