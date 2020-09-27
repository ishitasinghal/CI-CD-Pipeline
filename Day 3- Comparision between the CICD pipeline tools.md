# Day 3- Comparing various CI/CD tools

Although, one can only judge any tool by trying and experiencing them once. A brief study taken from the theoretical concepts has been put in the table. 

The sources from which I have gathered information are listed below:

1. "https://about.gitlab.com/devops-tools/jenkins-vs-gitlab.html"
2. "https://github.com/features/actions"
3. "https://help.github.com/en/actions/migrating-to-github-actions/migrating-from-jenkins-to-github-actions"
4. "https://about.gitlab.com/devops-tools/jenkins-vs-gitlab.html"
5. "https://www.guru99.com/jenkins-vs-travis.html"
6. "https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions"

These are some of the points made out of the study:

| Feature        | Jenkins                                                      | Github actions                                               | TravisCI                                                     | Gitlabs                                                      |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Cost           | Free, open-source, but requires a dedicated server which can be included as an extra expense. | API that supports CI/CD, free for 2000  minutes of usage per month. Not available for private repositories in legacy. | Free for open-source projects, but needed to be purchased for private projects. Provides free hosting, no need to provide a dedicated server. | Provides source code management, along with built-in CI/CD.  |
| Set up Time    | Takes a lot of time for set up according to the complete level of our customization. | No as such set up needed. We can directly create a workflow from Github. | Just needs a configuration file to get started and we can integrate other tools along with this, just a yml file. | No additional time in setup, we can simply create a workflow. |
| Type of Tool   | Open source, self-contained, Java based tool.                | Web-based, freely available for open source projects, and pricing for private repos depends on the way we go. | It is a commercial CI tool.                                  | Freely available                                             |
| Performance    | Unlimited customization options, along with several  of plugins for kubernetes, docker, maven, etc. | Uses matrix workflows, that makes the workflow powerful quickly. | Best for open source projects, have lesser customization options when compared. | Is currently on top, provides integration with Github as well. |
| Usage          | Jenkins is easy to use.                                      | Just a workflow required.                                    | This is quite flexible to use.                               | Easy to use tool.                                            |
| Github         | Works well with Github.                                      | Works well with Github.                                      | Works well with Github.                                      | Works well with Github.                                      |
| Server machine | Server-based.                                                | Web-based.                                                   | Cloud-based.                                                 | Web-based.                                                   |



## 
