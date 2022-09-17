# building-a-multibranch-pipeline-project

This repository is for 
[Build a multibranch Pipeline project](https://jenkins.io/doc/tutorials/build-a-multibranch-pipeline-project/)
tutorial in the [Jenkins User Documentation](https://jenkins.io/doc/).

This tutorial uses the same application that the [Build a Node.js and React app
with
npm](https://jenkins.io/doc/tutorials/build-a-node-js-and-react-app-with-npm/)
tutorial. Therefore, you'll build and test the same
application but this time, the delivery will be different depending on the Git
branch that Jenkins pipeline builds from. That is, the built branches determines which delivery stage of the pipeline.

The `jenkins` directory contains an example of the `Jenkinsfile` (i.e. Pipeline)
you'll be creating yourself during the tutorial and the `scripts` subdirectory
contains shell scripts with commands that are executed when Jenkins processes
either the "Deliver for development" or "Deploy for production" stages of your
Pipeline (depending on the branch that Jenkins builds from).

## Modify the docker working directory
To fix `npm ERR! Please try running this command again as root/Administrator.` we can add `HOME= '.'` under `environment` to run under current directory rather than build as `root`

The finished code looks like this:
```
'pipeline {
    agent {
        docker {
            image 'node:6-alpine'
            args '-p 3000:3000 -p 5000:5000'
        }
    }
    environment {
        HOME = '.'
        CI = 'true'
    }
```
![](img/2022-05-27-12-05-36.png)

## Following [this](https://tempora-mutantur.github.io/jenkins.io/github_pages_test/doc/tutorials/build-a-multibranch-pipeline-project/#pull-your-updated-jenkinsfile-into-the-other-repository-branches) guide to run docker locally

Click on proceed to and visit http://localhost:3000 to view the page

![](/img/2022-05-27-12-02-35.png)
![](img/2022-05-27-11-56-49.png)
![](/img/2022-05-27-12-02-08.png)
## Running Jenkins on EC2 / Azure VM

Modify the security group of the Jenkins instance to allow inbound traffic on Port 3000 from your IP address (DO NOT allow traffic from 0.0.0.0/0 for best security practices)

## Optional - Deploy a highly available master node via Cloudformation
1. Login to AWS console
2. navigate to `cfn-template-for-jenkins-master-node`
3. Installation guide
    -   This template depends on one of our vpc-*azs.yaml templates. Launch Stack `vpc-2azs.yaml`
    -   Launch Stack via `jenkins2-ha.yaml`
    -   Click Next to proceed with the next step of the wizard.
    -   Specify a name and all parameters for the stack.
    -   Click Next to proceed with the next step of the wizard.
    -   Click Next to skip the Options step of the wizard.
    -   Check the I acknowledge that this template might cause AWS CloudFormation to create IAM resources. checkbox.
    -   Click Create to start the creation of the stack.
    -   Wait until the stack reaches the state CREATE_COMPLETE
    -   Grab the URL of the Jenkins master from the Outputs tab of your stack.


### [Reference](https://templates.cloudonaut.io/en/stable/jenkins/)

## Managing Plugins via CLI

```
java -jar jenkins-cli.jar -s http://localhost:8080/ install-plugin SOURCE ... [-deploy] [-name VAL] [-restart]

Installs a plugin either from a file, an URL, or from update center.

 SOURCE    : If this points to a local file, that file will be installed. If
             this is an URL, Jenkins downloads the URL and installs that as a
             plugin.Otherwise the name is assumed to be the short name of the
             plugin in the existing update center (like "findbugs"),and the
             plugin will be installed from the update center.
 -deploy   : Deploy plugins right away without postponing them until the reboot.
 -name VAL : If specified, the plugin will be installed as this short name
             (whereas normally the name is inferred from the source name
             automatically).
 -restart  : Restart Jenkins upon successful installation.
 ```
## Steps


1. Generate API token
    - click on `user/admin -> configure -> API Token -> Add new Token -> copy`

    - ![](./img/20220916180509.png)  
    - in current directory create a credential file to avoid leaking secrets in logs
    - vi `creds` -> save admin:apitoken in file

2. Navigate to  `Manage jenkins -> Jenkins CLI`
3. Download jenkins-cli.jar or `wget http://jenkins:8080/jnlpJars/jenkins-cli.jar in terminal`
4. run blow

```
java -jar jenkins-cli.jar -s http://localhost:8080 -auth @creds install-plugin docker-plugin:1.2.9
    
java -jar jenkins-cli.jar -s https://jenkins-controller.devopsguru.link/ -auth @creds list-plugins > plugins.txt

curl -u username:apitoken http://localhost:8080/pluginManager/api/xml\?depth\=1\&xpath\=/\*/\*/shortName\|/\*/\*/version\&wrapper\=plugins | perl -pe 's/.*?<shortName>([\w-]+).*?<version>([^<]+)()(<\/\w+>)+/\1 \2\n/g'|sed 's/ /:/'
    - auth option uses username:API token to authenticate to Jenkins
    - add safe-restart to install the plugin 
```
 ### [Reference](https://www.jenkins.io/doc/book/managing/plugins/)
 
