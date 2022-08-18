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


### Reference
1. https://templates.cloudonaut.io/en/stable/jenkins/

