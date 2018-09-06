# Serverless DevOps Workshop

In this workshop you'll deploy a RESTful API that enables users to manage the Wild Rydes Unicorn Stable.  You will use the [Serverless Application Model (SAM)](https://github.com/awslabs/serverless-application-model) to deploy the API interfaces, business logic, and database into your AWS account.  The RESTful API will allow a user to list, create, view, update, and delete the unicorns in the Wild Rydes stable.

The application architecture uses [AWS Lambda](https://aws.amazon.com/lambda/), [Amazon API Gateway](https://aws.amazon.com/api-gateway/), and [Amazon DynamoDB](https://aws.amazon.com/dynamodb/).  The API is built using Lambda and API Gateway, using DynamoDB as a persistent data store for unicorn data.

See the diagram below for a depiction of the API architecture.

![Wild Rydes DevOps RESTful API Application Architecture](images/wildrydes-devops-api-architecture.png)

The DevOps Continuous Delivery Pipeline uses [AWS CodePipeline](https://aws.amazon.com/codepipeline/), [AWS CodeBuild](https://aws.amazon.com/codebuild/), and [Amazon S3](https://aws.amazon.com/s3/).  CodePipeline orchestrates the steps to build, test, and deploy your code changes.  CodeBuild compiles source code, runs tests, and produces software packages that are ready to deploy to environments.

See the screenshot below for a depiction of the continuous delivery pipeline that you will build at the completion of Module 4.

![Wild Rydes Unicorn API Continuous Delivery Pipeline](images/codepipeline-final.png)

If you'd like to jump in and get started please visit the [CodeStar Project](0_CodeStar) module page to begin the workshop.

## Prerequisites

### AWS Account

In order to complete this workshop you'll need an AWS Account with access to create AWS IAM, S3, DynamoDB, Lambda, API Gateway, CodePipeline, and CodeBuild resources. The code and instructions in this workshop assume only one student is using a given AWS account at a time. If you try sharing an account with another student, you'll run into naming conflicts for certain resources. You can work around these by appending a unique suffix to the resources that fail to create due to conflicts, but the instructions do not provide details on the changes required to make this work.

All of the resources you will launch as part of this workshop are eligible for the AWS free tier if your account is less than 12 months old. See the [AWS Free Tier page](https://aws.amazon.com/free/) for more details.

### AWS Command Line Interface

To complete the first module of this workshop you'll need the AWS Command Line Interface (CLI) installed on your local machine. You'll use the CLI to copy objects into your S3 website bucket.

Follow the [AWS CLI Getting Started](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) guide to install and configure the CLI on your machine.

### VPC and Subnet

In your AWS account please set up a VPC\Subnet so you can launch EC2 instances which you can further use in this lab to install required tools

### SAM Local setup

Running Serverless projects and functions locally with SAM Local requires Docker to be installed and running. SAM Local will use the `DOCKER_HOST` environment variable to contact the docker daemon.


[SAM Local](http://docs.aws.amazon.com/lambda/latest/dg/test-sam-local.html) is the AWS CLI tool for managing Serverless applications written with [Serverless Application Model (SAM)](https://github.com/awslabs/serverless-application-model).  SAM Local can be used to test functions locally, start a local API Gateway from a SAM template, validate a SAM template, and generate sample payloads for various event sources

 - macOS: [Docker for Mac](https://store.docker.com/editions/community/docker-ce-desktop-mac)
 - Windows: [Docker Toolbox](https://docs.docker.com/toolbox/toolbox_install_windows/,https://download.docker.com/win/stable/DockerToolbox.exe)
 - Linux: Check your distro's package manager (e.g. yum install docker)

For macOS and Windows users: SAM local requires that the project directory (or any parent directory) is listed in Docker file sharing options.

Verify that docker is working, and that you can run docker commands from the CLI (e.g. `docker ps`). You do not need to install/fetch/pull any containers - SAM Local will do it automatically as required.


#### Windows, Linux, macOS with NPM [Recommended]

The easiest way to install **`sam`** is to use [NPM](https://www.npmjs.com).

```bash
npm install -g aws-sam-local
```

If you get a permission error when using npm (such as `EACCES: permission denied`), please see the instructions on this page of the NPM documentation: [https://docs.npmjs.com/getting-started/fixing-npm-permissions](https://docs.npmjs.com/getting-started/fixing-npm-permissions).

Verify the installation worked:
```bash
sam --version
```

#### Binary release

We also release the CLI as binaries that you can download and instantly use. You can find them under [Releases](https://github.com/awslabs/aws-sam-local/releases) in the SAM Local repo.


#### Alternative Installation: Amazon Linux (EC2)

If you're unable to install SAM Local on your workstation, you may find it easier to use SAM Local on an Amazon Linux EC2 instance.   In this case, you will not be performing work locally on your laptop, instead you will connect remotely into an EC2 instance to perform editing and testing.

1. [Create a keypair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html), if you do not have one already.

1. To launch an EC2 instance with the required dependencies, click on the **Launch Stack** button for your region below.  You will need to select the keypair that you created in the previous step, as well as a VPC and Subnet for your EC2 instance, please ensure you have a VPC and subnet set up which is accessible from your network so you can connect to EC2 instance:

    Region| Launch
    ------|-----
    US East (N. Virginia) | [![Launch Dev Instance in us-east-1](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/images/cloudformation-launch-stack-button.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=DeveloperInstance&templateURL=https://s3.amazonaws.com/fsd-aws-wildrydes-us-east-1/developer-instance.yml)
    US West (N. California) | [![Launch Dev Instance in us-west-1](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/images/cloudformation-launch-stack-button.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/create/review?stackName=DeveloperInstance&templateURL=https://s3.amazonaws.com/fsd-aws-wildrydes-us-west-1/developer-instance.yml)
    US West (Oregon) | [![Launch Dev Instance in us-west-2](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/images/cloudformation-launch-stack-button.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?stackName=DeveloperInstance&templateURL=https://s3.amazonaws.com/fsd-aws-wildrydes-us-west-2/developer-instance.yml)
    EU (Ireland) | [![Launch Dev Instance in eu-west-1](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/images/cloudformation-launch-stack-button.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/create/review?stackName=DeveloperInstance&templateURL=https://s3.amazonaws.com/fsd-aws-wildrydes-eu-west-1/developer-instance.yml)
    EU (Frankfurt) | [![Launch Dev Instance in eu-central-1](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/images/cloudformation-launch-stack-button.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/create/review?stackName=DeveloperInstance&templateURL=https://s3.amazonaws.com/fsd-aws-wildrydes-eu-central-1/developer-instance.yml)
    Asia Pacific (Sydney) | [![Launch Dev Instance in ap-southeast-2](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/images/cloudformation-launch-stack-button.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/create/review?stackName=DeveloperInstance&templateURL=https://s3.amazonaws.com/fsd-aws-wildrydes-ap-southeast-2/developer-instance.yml)


1. Once the CloudFormation stack creation has completed, find the EC2 instance public DNS name by selecting the checkbox to the left of the **DeveloperInstance** Stack, and clicking the **Outputs** tab below the list of Stacks.  Find the output key labeled `PublicDnsName` and use the corresponding value in order to access the EC2 instance.
 
1. Use SSH client to connect to the instance.  If you are using Windows, use a client such as Putty or Bitvise (you can find instructions for connecting from Windows using Putty here: [Connecting to Your Linux Instance from Windows Using PuTTY](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html))

1. The Git repository has been cloned to the home directory on launch, `/home/ec2-user/uni-api`.

###### Setup Port Forwarding Configuration

SAM Local can start an HTTP server locally on EC2 instance on port 3000.  In order to view content on that HTTP server through the browser on your laptop, you need to configure port forwarding.

###### Port Forwarding on MacOS

On your workstation, open a new terminal and run the following command.  In the command, replace ***~/mykeypair.pem*** with the location and file name of your .pem file and replace ***ec2-###-##-##-###.compute-1.amazonaws.com*** with the public DNS name of your EC2 developer instance:

   ```
   ssh -i ~/mykeypair.pem -N -L 3000:ec2-###-##-##-###.compute-1.amazonaws.com:3000 ec2-user@ec2-###-##-##-###.compute-1.amazonaws.com
   ```

###### Port Forwarding with Putty on Windows

1. In your putty configuration, select **Connection** , **SSH** , **Tunnels** and add a mapping:

   ```
   Source port: 3000
   
   Destination: 127.0.0.1:3000
   ```

   The configuration should look like this:

   ![Putty Tunnel Config](images/putty-tunnel-config.png)


###### Port Forwarding with Bitvise SSH Client on Windows

1. In **Profile** window, select **C2S** tab, create an entry with this configuration:

   ```
   Listen Interface: 127.0.0.1
   
   List. Port: 3000
   
   Destination Host: localhost
   
   Dest. Port: 3000
   ```

   C2S configuration should look similar to this:

   ![Bitvise Tunnel Config](images/bitvise-tunnel-config.png)

### Browser

We recommend you use the latest version of Chrome or Firefox when testing the web application UI.

### Text Editor

You will need a local text editor for making minor updates to configuration files.

## Modules

This workshop is broken up into multiple modules. You must complete each module before proceeding to the next.

0. [CodeStar Project](0_CodeStar)
1. [Serverless Application Model (SAM)](1_ServerlessApplicationModel)
2. [Continuous Delivery Pipeline](2_ContinuousDeliveryPipeline)
3. [AWS X-Ray Integration](3_XRay)
4. [Multiple Environment CI/CD Pipeline](4_MultipleEnvironments)


After you have completed the workshop you can delete all of the resources that were created by following the [cleanup guide](9_CleanUp).
