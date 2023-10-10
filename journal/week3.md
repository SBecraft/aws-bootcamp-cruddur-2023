# Week 3 — Decentralized Authentication

Table of Contents
-	[Week-3 Technical Tasks Overview](#week-3-technical-tasks-overview)
-	[Decentralized Authentication in AWS Cloud](#decentralized-authentication-in-aws-cloud)
-	[Amazon Cognito](#amazon-cognito)
-	[Cognito in Identity Access Management](#cognito-in-identity-access-management)
-	[Amazon Cognito Security Best Practices](#amazon-cognito-security-best-practices)

-	[References](#references)



## Week-3 Technical Tasks Overview
Gain practical knowledge of implementing a decentralized authentication service into a web-application with custom login and signup pages in a react application.

- Provision via ClickOps an Amazon Cognito User Pool
- Install and configure Amplify client-side library for Amazon Congito
- Implement API calls to Amazon Coginto for custom login, signup, recovery and forgot password page
- Show conditional elements and data based on logged in or logged out
- Verify JWT Token server side to serve authenticated API endpoints in Flask Application

## Decentralized Authentication in AWS Cloud
Decentralized authentication is a security mechanism that allows for the secure and decentralized authentication of users on the web, meaning there is no central authority verifying an individual’s identity.   It is used for online  for this technology include online marketplaces, social media platforms, and other online communities that require user authentication. Decentralized Identifiers (DIDs) are unique identifiers that allow for verified digital identification.  DIDs, unlike traditional federated identifiers, are designed to be independent of centralized registries, identity providers, and certificate authorities. For organizations, decentralized authentication limits risk, makes audits easier, and checks credentials for faster remote hiring. 

### Types of Decentralized Authentication
-	SAML/Single Sign ON & Identity Provider:   SAML is an acronym for Security Assertion Markup Language.   One credential is used to log into and authenticate with an application with face id.   It allows a single point of entry into any application.  Tells an application like Facebook, for example, to use face id for username and password, so you don’t have to remember them moving forward.
-	OpenID Connect:  Allows you to use your social credentials to authenticate any service.  You don’t have to select OpenID Connect each time you want to use it.  But you must be working with an application that uses OpenID Connect.
-	OAuth:  Provides Authentication and Authorization for consumer social media applications. It uses the credentials of one consumer application to log into another consumer application. 



## Amazon Cognito
Amazon Cognito is your service that allows authentication with users that it stores locally in the Amazon account.  It provides users with a username and password to access an application. Basically, Cognito is a user directory  to store credentials along with the context of the Amazon web service.  
&NewLine;
&NewLine;
&nbsp;

There are two types of Amazon Cognito:
1)	Cognito User Pool:  Adds user directories to your application. Does authentication with OAuth. Talks to social media sites to get authorization from user to use their credentials to establish a trust relationship between that social media site and your new application in order to off load the need to store username and password locally.
 
2)	Cognito Identity Pool:  Grants access to AWS services. Allows an application to request temporary credentials and it acts as an access broker.  It says to a user that they already have credentials, and that they seem to know their Amazon credentials, as well,  so the user is given temporary credentials so they can access different Amazon services using temporary credentials. AWS credentials are tied to your IAM Role.

### Why Use Cognito
-	It’s a user directory for consumer customers.  And a username authentication which can attach to anything that is an application.
-	Can make it possible for users to easily access AWS resources tied to an application, such as Amazon S3 or Amazon DynamoDb..
-	Acts as an identity broker for AWS resources with temporary credentials.
  
![why use cognito](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/why-use-cognito1.png)


## Cognito in Identity Access Management
1)	Cognito User and Identity pools integrated into User Lifecycle Management through the user provisioning process from onboarding to offboarding. .  Provisioning is the creation, update, and removal of access to a resource in an IT environment.
`User Lifecycle Management`
-	Onboarding new employee 
-	Provision
-	Enforce
-	Update
-	Offboarding

2)	`User Provisioning Process`:
-	Employee joins
-	Create employee IT profile
-	Assign birthright Apps
-	Onboard employee
-	Additional app requirements
-	Changes
-	Employee departs
-	Offboard

3)	`Token Lifecycle Management`:
-	A key feature of credential token management
-	Allows for tokens to be created, updated, and revoked as needed
-	Credential tokens are updated as changes are made to users or employee accounts in terms of roles and responsibilities.
-	Really important for managing access to applications and APIs.
  
![token lifecycle](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/token-lifecycle.png)

## Amazon Cognito Best Security Practices
Source cited: [`Cloud Security Podcast. (2023, March 8)](Amazon Cognito Security Best Practices - YouTube)
&NewLine;
&NewLine;
&nbsp;

Best security practices for Amazon server side and client application side:
&NewLine;
&NewLine;
&nbsp;

![cognito security 1](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/cognito-security-1.png)

![cognito security 2](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/cognito-security-2.png)




## References
- Cloud Security Podcast. (2023, March 8). Amazon Cognito Security Best Practices. YouTube. https://www.youtube.com/watch?v=tEJIeII66pY
