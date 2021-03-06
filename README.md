### CD Tools Demo

This [repository](https://github.com/amesken/cd-tool-stack) contains Dockerfiles for running a set of Continuous Delivery Tools with a single command (docker-compose). [Click for details](https://github.com/amesken/cd-tool-stack/blob/master/docker-compose.yml)
The diagram contains all tools used in the Docker containers.

![Docker CD Tools](https://raw.githubusercontent.com/amesken/cd-tool-stack/master/screenshots/docker-cd-tools.png)

The setup is blatantly copied from https://blog.codecentric.de/en/2015/10/continuous-integration-platform-using-docker-container-jenkins-sonarqube-nexus-gitlab  , so all credits go to Marcel Birkner.

#### Prerequisites (Windows)

You should have Docker Toolbox installed, see https://www.docker.com/toolbox

I am using docker-compose to start several docker container at once.
Since all containers run in a single VM (virtualbox), this VM needs enough memory.

#### Step 0 - List Docker Machine

```
~/github/cd-tool-stack$ docker-machine ls

NAME      ACTIVE   DRIVER       STATE     URL                         SWARM
default   *        virtualbox   Running   tcp://192.168.99.100:2376
```

#### Step 1 - Stop your docker VM

```
docker-machine stop default
```

#### Step 2 - Increase Memory via VirtualBox UI

I am using 6000MB for my VM.

![VirtualBox](https://raw.githubusercontent.com/amesken/cd-tool-stack/master/screenshots/virtualbox.png)

#### Step 3 - Start VM

```
docker-machine start default
```

### Getting started

Create the folder structure used to mount the data volumes of the images:

```
<USER_HOME> = c:\Users\<USER_NAME>
<DOCKER_VOLUMES> = <USER_HOME>\docker\data
<DOCKER_VOLUMES>\artifactory\data
<DOCKER_VOLUMES>\artifactory\logs
<DOCKER_VOLUMES>\artifactory\backup
<DOCKER_VOLUMES>\jenkins
<DOCKER_VOLUMES>\sonarqube\data
<DOCKER_VOLUMES>\sonarqube\logs
<DOCKER_VOLUMES>\sonarqube\extensions
```

To get all docker containers up and running use:

```
git clone git@github.com:amesken/cd-tool-stack.git
cd cd-tool-stack
docker-compose up -d
```

When all containers have been build, check their status:

```
$ docker-compose ps
          Name                         Command               State                           Ports
--------------------------------------------------------------------------------------------------------------------------
cdtoolstack_artifactory_1   catalina.sh run                  Up      0.0.0.0:18081->8080/tcp, 8081/tcp
cdtoolstack_gitlab_1        /sbin/entrypoint.sh app:start    Up      0.0.0.0:10022->22/tcp, 443/tcp, 0.0.0.0:10080->80/tcp
cdtoolstack_jenkins_1       /bin/tini -- /usr/local/bi ...   Up      50000/tcp, 0.0.0.0:18080->8080/tcp
cdtoolstack_nodechrome_1    /opt/bin/entry_point.sh          Up      0.0.0.0:32769->5900/tcp
cdtoolstack_nodefirefox_1   /opt/bin/entry_point.sh          Up      0.0.0.0:32768->5900/tcp
cdtoolstack_postgresql_1    /start                           Up      5432/tcp
cdtoolstack_redis_1         /sbin/entrypoint.sh              Up      6379/tcp
cdtoolstack_selhub_1        /opt/bin/entry_point.sh          Up      0.0.0.0:4444->4444/tcp
cdtoolstack_sonar_1         ./bin/run.sh                     Up      0.0.0.0:19000->9000/tcp
```

### Access Tools

| *Tool* | *Link* | *Credentials* |
| ------------- | ------------- | ------------- |
| Jenkins | http://${docker-machine ip default}:18080/ | no login required |
| SonarQube | http://${docker-machine ip default}:19000/ | admin/admin |
| Artifactory | http://${docker-machine ip default}:18081/artifactory | admin/password |
| GitLab | http://${docker-machine ip default}:10080/ | root/5iveL!fe |
| Selenium Grid | http://${docker-machine ip default}:4444/grid/console | no login required |

### Screenshots

Here is an overview of all tools:

- GitLab is used for storing the Sourcecode
- Jenkins contains build job and is triggered once projects in GitLab are updated
- As part of the CI build, Jenkins triggers a static code analysis and the results are stored in SonarQube
- The Maven build uses Artifactory as a Proxy Repository for all 3rd party libs. The build artefacts are deployed to the Artifactory Release Repository
- The Selenium Grid contains Docker containers running Chrome and Firefox and is used for UI tests


#### Gitlab

![Gitlab interface](https://raw.githubusercontent.com/amesken/cd-tool-stack/master/screenshots/gitlab.png)

#### Jenkins Jobs

There are several jobs preconfigured in Jenkins.
The Jobs cover the following tasks:

- Continuous Integration Build with Maven
- Unit Tests
- Static Source Analysis results are stored in SonarQube
- JaCoCo Test Coverage
- Deployment to Artifactory
- Jenkins Job DSL examples
- Selenium UI Test

![Conference App Jobs](https://raw.githubusercontent.com/amesken/cd-tool-stack/master/screenshots/jenkins-jobs-1.png)

![Conference App CI Job](https://raw.githubusercontent.com/amesken/cd-tool-stack/master/screenshots/jenkins-jobs-2-conference-app-ci.png)

#### SonarQube Dashboard

![Jenkins Jobs](https://raw.githubusercontent.com/amesken/cd-tool-stack/master/screenshots/sonar-analysis-conference-app.png)

#### Artifactory Repository

![Artifactory Proxy Repository](https://raw.githubusercontent.com/amesken/cd-tool-stack/master/screenshots/artifactory.png)

#### Selenium Grid

![Selenium Grid](https://raw.githubusercontent.com/amesken/cd-tool-stack/master/screenshots/selenium-grid.png)