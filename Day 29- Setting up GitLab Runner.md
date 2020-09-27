# Day 29- Setting up GitLab Runner

All the tasks in GitLab are carried out by <u>runners</u>, which are nothing but applications that process builds. After running the jobs, they send the results back to GitLab.

GitLab runner provides the following features:

1) It enables caching of Docker containers.

2) Jobs can be run in different modes, that is locally, using docker containers, over SSH, clouds, hypervisors.

3) Provides support for bash and windows.

4) It provides compatibility with GitLab versions.

### Installing GitLab Runner on Windows

Git should be pre-installed or should be installed first in order to work with gitlab runners. A binary file is downloaded and copied into a folder named GitLab-Runner.

The runner is installed and thereafter started. The commands used are:

```
gitlab-runner.exe install
```

To check the installation of the gtilab runner, the following command is run.

```
gitlab-runner --version
```

![](images/Screenshot%20(993).png)

We need to run these commands by having the admin or super user permissions for linux. To start the runner:

```
gitlab-runner.exe start
```

![](images/Screenshot%20(1012).png)

To start the service by Windows, a user account needs to be entered along with the credentials.

```
gitlab-runner.exe install --user ENTER-YOUR-USERNAME --password ENTER-YOUR-PASSWORD 
gitlab-runner.exe start
```

To stop the service:

```
gitlab-runner stop
```



### Registering a runner:



### Links Referred:

1. "https://about.gitlab.com/blog/2016/04/19/how-to-set-up-gitlab-runner-on-digitalocean/"
2. "https://docs.gitlab.com/runner/"
3. "https://docs.gitlab.com/runner/register/index.html"

