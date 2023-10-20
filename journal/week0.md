# Week 0 — Billing and Architecture

Table of Contents
- [Technical Tasks](#technical-tasks)
- [Cruddur Project Description](#cruddur-project-description)
- [AWS Well Architected Framework](#aws-well-architected-framework)
-	[AWS Setup](#aws-setup)
-	[Use AWS CloudShell](#use-aws-cloudshell)
-	[Set up GitPod extension on GitHub account](#set-up-gitpod-in-github-account)
-	[Install AWS CLI](#install-aws-cli)
-	[Assign Environment Variables to Security Credentials](#assign-environment-variables-to-security-credentials)
-	[Set Up AWS Billing Alarm](#set-up-aws-billing-alarm)
-	[Create A Budget](#create-a-budget)
-	[Lucid Charts App Architecture Diagrams](#lucid-charts-app-architecture-diagrams)
-	[Publish Files to GitHub Using CLI](#publish-files-to-github-using-cli)

&NewLine;
&NewLine;
&nbsp;

## Technical Tasks
Week-0 lays the foundation for the entire bootcamp by:
- Discussing the format of the bootcamp
- Going over the business use-case of our project
- Looking at an architectural diagram of what we plan to build
- Showcase how to use Lucid Charts to build architectures
- Talk about C4 Models
- Running through the cloud services we will utilize
- Testing that we can access our AWS accounts
- Settings up AWS free-tier and understand how to track spend in AWS: eg . AWS Budgets, AWS Cost Explorer, Billing Alarms
- Understanding how to look at monthly billing reports
- Launching AWS CloudShell and looking at AWS CLI
- Generating AWS credentials

&NewLine;
&NewLine;
&nbsp;

## Cruddur Project Description

Cruddur is an ephemeral social media micro-blogging platform that allows users to post updates, thoughts, and photos that automatically expire after a period of time, ensuring that personal information and conversations stay relevant and in the moment.
&NewLine;
&NewLine;
&nbsp;

[Cruddur Project Business Use Case](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/Cruddur%20Project%20Description.pdf)

&NewLine;
&NewLine;
&nbsp;

## AWS Well Architected Framework

[AWS Well-Architected Framework Documentation](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)

"The AWS Well-Architected Framework helps you understand the pros and cons of decisions you make while building systems on AWS. By using the Framework you will learn architectural best practices for designing and operating reliable, secure, efficient, cost-effective, and sustainable systems in the cloud."

Six pillars of the AWS well architected framework
1. Operational Excellence
2. Security
3. Reliability
4. Performance Efficiency
5. Cost Optimization
6. Sustainability

&NewLine;
&NewLine;
&nbsp;

## AWS Setup
-	Signed in as Root user (I already have an AWS account so I didn't need to create one)
-	Navigated to IAM Security in AWS console and set up multi-factor authentication for Root for added account security
### As Root create new IAM User with Admin Access:
-	From within IAM Users console I createa a new Admin Group and gave it “Administrator Access”.
-	From within IAM Users I created a new user called “awsbootcamp” with “Administrator Access” that I added to the Admin group.
-	Enabled AWS console access for the new IAM User
-	Created alias for new user “awsbootcamp” as “my-aws-bootcamp”.
-	Clicked into new account for User “awsbootcamp”
-	Clicked on Security Credentials and Created Access Keys
-	Chose AWS CLI Access and saved the CSV with the User credentials.
### Assign MFA to New User:
-	Logged out of Root user and logged back in under new IAM User “aswbootcamp”.
-	Assigned multi-factor authentication to newly created IAM User account.

&NewLine;
&NewLine;
&nbsp;

## Use AWS CloudShell
-	While logged into IAM User account “awsbootcamp” I launched AWS Cloudshell in the browser.
-	Checked that my newly created IAM User credentials for “awsbootcamp” are associated with AWS CLI by running the command:
```sh
aws –cli -auto-prompt
```
-	Made sure that AWS CLI via Cloudshell recognized my credentials by running the command “aws sts get-caller-identity” which displays the credentials in the terminal.

```sh
aws sts get-caller-identity
```
  
![IAM User Security Credentials Verified in Cloudshell](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/cloudshell-screenshot-new.png)

&NewLine;
&NewLine;
&nbsp;


## Set up Gitpod in GitHub account:
Gitpod is a browser of VSCode that can be configured to load automatically from a GitHub account with a modified ‘.gitpod.yml’ file.

1.	Added https://gitpod.io/# in front of my git hub account URL and confirmed the Workspace creation.

![Gitpod.io url addon]( https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/gitpod-io.png)


2.	Used Gitpod extension by pressing on green Gitpod button to open repository files on a new workspace that uses a VSCode browser.

&NewLine;
&NewLine;
&nbsp;
 
## Install AWS CLI 
[AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/)

[AWS CLI Install Instructions](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

### Install AWS CLI for GitPod 
-	Updated the `.gitpod.yml` file with the following task so AWS CLI runs in partial auto-prompt mode in GitPod upon launching of the GitPod environment.
```sh
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
```



### Install AWS CLI on Home/Local Computer
[AWS CLI Install Instructions for Various Operating Systems](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

&NewLine;
&NewLine;
&nbsp;


## Assign Environment Variables to Security Credentials
- I set the IAM User “awsbootcamp” security credentials for the Bash Terminal in GitPod.  

```sh
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
export AWS_DEFAULT_REGION=us-east-1
```

- Now I can view the AWS environment variables from the Gitpod CLI with the following command:
```sh
env | grep AWS_
```
- Next, I set the IAM User “awsbootcamp” security credentials environment variables so GitPod will remember the credentials every time I relaunch Workspaces.

```sh
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1
```

###  Verified that GitPod Terminal recognizes “awsbootcamp” as the User after Workspace relaunch with the command:
```sh
aws sts get-caller-identity
```

![IAM User Security Credentials verified in Cloudshell](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/security-credentials-awsbootcamp-new.png)

&NewLine;
&NewLine;
&nbsp;

## Set Up AWS Billing Alarm
### First, enable Billing Alerts on User account to be able to even receive alerts.
-	Logged into Root User 
-	Went to Billing console page
-	Clicked on  Billing Preferences > Receive Billing Alerts
-	Saved account preferences

### Second, create a User account Billing Alert

- Here is a snapshot of my User account Billing Alert Preferences I created in the "awsbootcamp" User console:

![Proof of working billing alert](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/billing-preferences.png)



#### Create SNS Topic to deliver an alert email when account is overbilled

[AWS SNS Create-Topic Documentation](https://docs.aws.amazon.com/cli/latest/reference/sns/create-topic.html)

- Used the following command in Gitpod Terminal to create topic:
```sh
aws sns create-topic --name billing-alarm
```
- This command returned a TopicARN.

![Topic Arn](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/correct-topic-arn-new.png)

- Created a subscription with the TopicARN and my Email.

```sh
aws sns subscribe \
    --topic-arn TopicARN \
    --protocol email \
    --notification-endpoint your@email.com
```

- Checked my email and confirmed the subscription exists.

![Proof of working billing alarm](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/subscription-confirmed-new.png)

- Proof of subscription in AWS console:

![Newly created subscription](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/subscriptions-console.png)

&NewLine;
&NewLine;
&nbsp;

### Create Billing Alarm for CloudWatch
[AWS Cloudwatch Put-Metric-Alarm Documentation](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html)

- Used the following command in GitPod Terminal to create Billing Alarm:

```sh
aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm_config.json
```

![My alarm-config.json file](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/aws/json/alarm-config.json)

![json code for setting up billing alarm via Command Prompt](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/alarm-code.png)

- Here is a snapshot of my User account Billing Alarms located in the AWS console under Cloudwatch > Alarms.
![Proof of working billing alarm](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/alarms.png)

&NewLine;
&NewLine;
&nbsp;

## Create A Budget

-	Used the following command to get my AWS Account ID for IAM User “awsbootcamp”
```sh
aws sts get-caller-identity --query Account --output text
```

-	Following AWS documentation, create two json files by copying and pasting the AWS code in the documentation into new files created in GitPod VSCode called `budget.json` and `budget-notifications-with-suscribers.json`

![Budget json file](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/aws/json/budget.json)

![budget json file](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/budget-json-file.png)

&NewLine;
&NewLine;
&nbsp;

![Budget with subscribers json file](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/aws/json/budget-notifications-with-subscribers.json)

![Budget with subscribers](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/budget-with-example2.png)

&NewLine;
&NewLine;
&nbsp;

- Created the environment variable ACCOUNT_ID to represent "awsbootcamp" User 12-digit ID.

![Account ID Env variable](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/create-budget-terminal.png)

- Use the following commands to create ACCOUNT_ID:

```sh
ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
export ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
env | grep ACCOUNT_
export AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
gp env AWS_ACCOUNT_ID=" <place AWS account ID number here between quotation marks> "
```


-	Used the following commands in GitPod VSCode terminal to create Budget and Notifications with new environment variable ACCOUNT_ID:
```sh
aws budgets create-budget \
    --account-id AccountID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```

- Proof I created Budgets in my AWS User account:

![Proof Budgets Created in User account](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/budget-example.png)

- If you want to check what you created, run this command:
```sh
aws budgets describe-budget --account-id 111122223333 --budget-name my-budget
```
&NewLine;
&NewLine;
&nbsp;


## Lucid Charts App Architecture Diagrams

The Lucid Charts web-based application provides a means to construct diagrams and flowcharts to understand organizational architecture and how processes interact.
&NewLine;
&NewLine;
&nbsp;
&NewLine;
&NewLine;
&nbsp;

Types of organizational architecture diagrams to model:
-	Conceptual: Basic diagram by stakeholders and architects. Organizes the architecture with concepts and rules. General service description without labels.
-	Logical: Diagram that describes the relationships between organizational system components.  High level overview and detailed information about individual components that the conceptual diagram does not provide. Maps out the software, hardware, and network components and how they interact without defining labels.
-	Physical: Diagram that goes beyond the logical diagram by providing granular details like IP addresses and names of cloud provider services, and so on.


[My Conceptual diagram for Cruddur architecture](https://lucid.app/lucidchart/1178e5b4-9c42-472d-aa73-79275784f24a/edit?viewport_loc=-857%2C3%2C3061%2C1573%2C0_0&invitationId=inv_9c09d566-cd67-41af-a5c3-2c21652d9526)
![Cruddur Conceptual Diagram](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/Cruddur%20Conceptual%20Diagram.png)



[My Logical diagram for Cruddur architecture](https://lucid.app/lucidchart/57bebfea-4b09-4a46-8b30-bc077baa98f1/edit?viewport_loc=137%2C-59%2C3072%2C1521%2C0_0&invitationId=inv_ce98a7c1-19bd-41c3-992f-e93a96736a0d)
![Cruddur Logical Diagram](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/Cruddur%20Logical%20Diagram.png)

&NewLine;
&NewLine;
&nbsp;

- My Napkin Conceptual Architecture Diagram

![Cruddur Conceptual Diagram](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/Napkin-Crudder%20Conceptual%20Architecture.jpg)




&NewLine;
&NewLine;
&nbsp;

## Publish Files to GitHub Using ClI

To publish files or folders to GitHub using CLI within Bash terminal:
1.	Make sure to be in your repository directory in the terminal:
```sh 
/workspace/repo-name
```
2.	Type:
```sh
git status
```
It will respond with a list of files that have been added since the last commit. 

3.	Add new file/folder: 
```sh
git add --all or git add *
```
This command stages the files for publishing.

4.	Confirm files were added: 
```sh
git status
```
5.	Commit changes: 
```sh
git commit -m "commit message"
``` 
This will commit changes and add a description to the commit version. 

6.	Push changes to GitHub: 
```sh
git push
```
7.	Check GitHub URL and see the file/folder just uploaded.

&NewLine;
&NewLine;
&nbsp;

### To create a new folder in your repository while in Github just click `create a new file`. Type your new folder name in the dirctory where you want it to be stored, and at the end of the name type a `/` to initilize it as a folder. Then you can create a new file in the newly created folder.










