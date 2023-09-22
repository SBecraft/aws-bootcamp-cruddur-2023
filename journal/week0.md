# Week 0 — Billing and Architecture

Table of Contents
-	[AWS Setup](#aws-setup)
-	[Use AWS CloudShell](#use-aws-cloudshell)
-	[Set up GitPod extension on GitHub account](#set-up-gitpod-in-github-account)
-	[Install AWS CLI](#install-aws-cli)
-	[Assign Environment Variables to Security Credentials](#assign-environment-variables-to-security-credentials)
-	[Set Up AWS Billing Alarm](#set-up-aws-billing-alarm)
-	[Create A Budget](#create-a-budget)
-	[Lucid Charts App Architecture Diagrams](#lucid-charts-app-architecture-diagrams)
-	[Publish Files to GitHub](#publish-files-to-github)


## AWS Setup
-	Created AWS account and signed in as the Root user.
-	Navigated to IAM Security in AWS console and set up multi-factor authentication for Root for added account security
### As Root create new IAM User with Admin Access:
-	From within IAM Users console I createa a new Admin Group and gave it “Administrator Access”.
-	From within IAM Users I created a new user called “awsbootcamp” with “Administrator Access” that I added to the Admin group.
-	Enabled AWS console access for the new IAM User
-	Created alias for new user “awsbootcamp” as “my-aws-bootcamp”.
-	Clicked into new User “awsbootcamp”, clicked on Security Credentials and Create Access Keys
-	Chose AWS CLI Access and saved the CSV with the User credentials.
### Assign MFA to New User:
-	Logged out of Root user and logged back in under new IAM User “aswbootcamp”.
-	Assigned multi-factor authentication to newly created IAM User account.

## Use AWS CloudShell
-	While logged into IAM User account “awsbootcamp” I launched AWS Cloudshell in the browser.
-	Checked that my newly created IAM User credentials for “awsbootcamp” are associated with AWS CLI by running the command “aws –cli -auto-prompt”
-	Made sure that AWS CLI via Cloudshell recognized my credentials by running the command “aws sts get-caller-identity” which displays the credentials in the terminal.

  ```sh
aws sts get-caller-identity
```

  
![IAM User Security Credentials verified in Cloudshell](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/security-credentials-awsbootcamp.png)


## Set up Gitpod in GitHub account:
Gitpod is a browser of VSCode that can be configured to load automatically from a GitHub account with a modified ‘.gitpod.yml’ file.

1.	Added https://gitpod.io/# in front of my git hub account URL and confirmed the Workspace creation.

![IAM User Security Credentials verified in Cloudshell](        )
2.	Used Gitpod extension by pressing on green Gitpod button to open repository files on a new workspace that uses a VSCode browser.
 
## Install AWS CLI 
[AWS CLI Reference]  https://docs.aws.amazon.com/cli/latest/
[AWS CLI Install Instructions]https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
### Install AWS CLI for GitPod 
-	Updated the ‘.gitpod.yml” file with the following task so AWS CLI runs in partial auto-prompt mode in GitPod upon launching of the GitPod environment.
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
Manually run these commands to perform the AWS CLI install on home computer:
```sh
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
```

## Assign Environment Variables to Security Credentials
I set the IAM User security credentials for “awsbootcamp”.  This can be done from either the GitPod terminal or the Command Prompt terminal on home computer.

```sh
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
export AWS_DEFAULT_REGION=us-east-1
```
Next, I set the IAM User security credentials environment variables for “awsbootcamp”. 

```sh
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1
```
###  Verified that GitPod Terminal or the home computer terminal recognize “awsbootcamp” as the user of the terminal with the command:
```sh
aws sts get-caller-identity
```
Place image here

## Set Up AWS Billing Alarm
### First, enable Billing Alerts on User account to be able to even receive alerts.
-	Logged into Root User 
-	Went to Billing console page
-	Clicked on  Billing Preferences > Receive Billing Alerts
-	Saved account preferences

### Second, create a User account Billing Alarm
Here is a snapshot of my User account Billing Alarm:
![Proof of working billing alarm](assets/week00-proof-of-billing-alarm.png)

#### Create SNS Topic to deliver an alert email when account is overbilled
[aws sns create-topic](https://docs.aws.amazon.com/cli/latest/reference/sns/create-topic.html)
Used the following command in Gitpod Terminal to create topic:
```sh
aws sns create-topic --name billing-alarm
```
This command returned a TopicARN

![Topic Arn](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-0-assets/correct-topic-arn.png)

Created a subscription with the TopicARN and my Email

```sh
aws sns subscribe \
    --topic-arn TopicARN \
    --protocol email \
    --notification-endpoint your@email.com
```

Checked my email and confirmed the subscription exists.
![Proof of working billing alarm](assets/week00-proof-of-billing-alarm.png)


### Create Billing Alarm for CloudWatch
[aws cloudwatch put-metric-alarm](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html)
Used the following command in GitPod Terminal to create Billing Alarm:

```sh
aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm_config.json
```
![Proof of working billing alarm](assets/week00-proof-of-billing-alarm.png)
Instructions for how to create the alarm:
[Create an Alarm via AWS CLI](https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-estimatedcharges-alarm/)

## Create A Budget
[aws budgets create-budget](https://docs.aws.amazon.com/cli/latest/reference/budgets/create-budget.html)
-	Used the following command to get my AWS Account ID for IAM User “awsbootcamp”
```sh
aws sts get-caller-identity --query Account --output text
```

-	Following AWS documentation, create two json files by copying and pasting the AWS code in the documentation into new files created in GitPod VSCode called ‘budget.json’ and ‘budget-notifications-with-suscribers.json’
-	Used the following commands in GitPod VSCode terminal to create Budget and Notifications:
```sh
aws budgets create-budget \
    --account-id AccountID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```
![Proof of working billing alarm](assets/week00-proof-of-billing-alarm.png)

## Lucid Charts App Architecture Diagrams
[Conceptual diagram for Cruddur architecture](https://lucid.app/lucidchart/1178e5b4-9c42-472d-aa73-79275784f24a/edit?viewport_loc=-857%2C3%2C3061%2C1573%2C0_0&invitationId=inv_9c09d566-cd67-41af-a5c3-2c21652d9526)
![Cruddur Conceptual Diagram](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/Cruddur%20Conceptual%20Diagram.png)

[Logical diagram for Cruddur architecture](https://lucid.app/lucidchart/57bebfea-4b09-4a46-8b30-bc077baa98f1/edit?viewport_loc=137%2C-59%2C3072%2C1521%2C0_0&invitationId=inv_ce98a7c1-19bd-41c3-992f-e93a96736a0d)
![Cruddur Logical Diagram](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/Cruddur%20Logical%20Diagram.png)

## Publish Files to GitHub

To publish files or folders to GitHub from Terminal:
1.	Terminal should read: 
/workspace/repo-name
2.	Type: 
git status
It will respond with a list of files that have been added since the last commit. 
3.	Add new file/folder: 
git add --all or git add *
This command stages the files for publishing.
4.	Confirm files were added: 
git status 
5.	Commit changes: 
git commit -m "commit message" 
This will commit changes and add a description to the commit version. 
6.	Push changes to GitHub: 
git push 
7.	Check GitHub URL and see the file/folder just uploaded.



To create a new folder in a repository click “create a new file.” Type your new folder’s name in the area where you would write the file name, and at the end of the file name type a “/” to initilize it as a folder. After this you can create a new file in the folder.


If you want to check what you created, run this command:
```sh
aws budgets describe-budget --account-id 111122223333 --budget-name my-budget
```





