# Week 3 — Decentralized Authentication

Table of Contents
-	[Week-3 Technical Tasks Overview](#week-3-technical-tasks-overview)
-	[Decentralized Authentication in AWS Cloud](#decentralized-authentication-in-aws-cloud)
-	[Amazon Cognito](#amazon-cognito)
-	[Amazon Cognito in Identity Access Management](#amazon-cognito-in-identity-access-management)
-	[Amazon Cognito Security Best Practices](#amazon-cognito-security-best-practices)
-	[Provision An Amazon Cognito User Pool](#provision-an-amazon-cognito-user-pool)
-	[Install and Configure Amplify Client-Side Library for Amazon Congito](#install-and-configure-amplify-client-side-library-for-amazon-cognito)
- [Cruddur App Signin Page](#cruddur-app-signin-page)	
- [Create a User](#create-a-user)
-	[Signup Page](#signup-page)
-	[Confirmation Page](#confirmation-page)
-	[Account Recovery Page](#account-recovery-page)
-	[References](#references)




## Week-3 Technical Tasks Overview
Gain practical knowledge of implementing a decentralized authentication service into a web-application with custom login and signup pages in a react application.

- Provision via ClickOps an Amazon Cognito User Pool
- Install and configure Amplify client-side library for Amazon Congito
- Implement API calls to Amazon Coginto for custom login, signup, recovery and forgot password page
- Show conditional elements and data based on logged in or logged out
- Verify JWT Token server side to serve authenticated API endpoints in Flask Application

## Decentralized Authentication in AWS Cloud
Decentralized authentication is a security mechanism that allows for the secure and decentralized authentication of users on the web, meaning there is no central authority verifying an individual’s identity.   It is used for online marketplaces, social media platforms, and other online communities that require user authentication. Decentralized Identifiers (DIDs) are unique identifiers that allow for verified digital identification.  DIDs, unlike traditional federated identifiers, are designed to be independent of centralized registries, identity providers, and certificate authorities. For organizations, decentralized authentication limits risk, makes audits easier, and checks credentials for faster remote hiring. 

### Types of Decentralized Authentication
-	`SAML/Single Sign ON & Identity Provider`:   SAML is an acronym for Security Assertion Markup Language.   One credential is used to log into and authenticate with an application with face id.   It allows a single point of entry into any application.  Tells an application like Facebook, for example, to use face id for username and password, so you don’t have to remember them moving forward.
&NewLine;
&NewLine;
&nbsp;
-	`OpenID Connect`:  Allows you to use your social credentials to authenticate any service.  You don’t have to select OpenID Connect each time you want to use it.  But you must be working with an application that uses OpenID Connect.
&NewLine;
&NewLine;
&nbsp;
-	`OAuth`:  Provides Authentication and Authorization for consumer social media applications. It uses the credentials of one consumer application to log into another consumer application. 



## Amazon Cognito
Amazon Cognito is your service that allows authentication with users that it stores locally in the Amazon account.  It provides users with a username and password to access an application. Basically, Cognito is a user directory  to store credentials along with the context of the Amazon web service.  
&NewLine;
&NewLine;
&nbsp;

There are two types of Amazon Cognito:
1)	`Cognito User Pool`:  Adds user directories to your application. Does authentication with OAuth. Talks to social media sites to get authorization from user to use their credentials to establish a trust relationship between that social media site and your new application in order to off load the need to store username and password locally.
 
2)	`Cognito Identity Pool`:  Grants access to AWS services. Allows an application to request temporary credentials and it acts as an access broker.  It says to a user that they already have credentials, and that they seem to know their Amazon credentials, as well,  so the user is given temporary credentials so they can access different Amazon services using temporary credentials. AWS credentials are tied to your IAM Role.

### Why Use Cognito
-	It’s a user directory for consumer customers and a username authentication which can attach to anything that is an application.
-	Can make it possible for users to easily access AWS resources tied to an application, such as Amazon S3 or Amazon DynamoDb..
-	Acts as an identity broker for AWS resources with temporary credentials.
  
![why use cognito](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/why-use-cognito1.png)
Source: [Cloud Security Podcast. (2023, March 8)](https://www.youtube.com/watch?v=tEJIeII66pY)

&NewLine;
&NewLine;
&nbsp;

## Amazon Cognito in Identity Access Management
1)	Cognito User and Identity pools integrated into User Lifecycle Management through the user provisioning process from onboarding to offboarding. Provisioning is the creation, update, and removal of access to a resource in an IT environment.
   
&NewLine;
&NewLine;
&nbsp;
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
Source: [Cloud Security Podcast. (2023, March 8)](https://www.youtube.com/watch?v=tEJIeII66pY)

&NewLine;
&NewLine;
&nbsp;

## Amazon Cognito Security Best Practices
Source: [Cloud Security Podcast. (2023, March 8)](https://www.youtube.com/watch?v=tEJIeII66pY)
&NewLine;
&NewLine;
&nbsp;

Best security practices for Amazon server side and client application side:
&NewLine;
&NewLine;
&nbsp;

![cognito security 1](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/cognito-security-1.png)

![cognito security 2](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/cognito-security-2.png)


## Provision An Amazon Cognito User Pool
[Amazon Cognito Documentation](https://docs.aws.amazon.com/cognito/)
Amazon Cognito is
-  From the AWS console navigate to Cognito.
-  Click on the hamburger like icon in the upper left corner.  Cognito offers two Provider types: `User Pools` or `Federated Identities`.
- Choose `User Pools` when you want to integrate application with Cognito and you have a pool of users that can be used that need login and sign-up and you want that managed externally. Federated identity is when you want to login using an identity from another provider.
- For the Cruddur app I selected `User Pools`.

![user pool](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/user-pool.png)

- Click “ Create user pool`

![create user pool](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/create-user-pool.png)

### Configure Sign-In Experience
-	For the Cruddur app we’ll configure sign-in experience with `User name` and `Email`.   Click ‘Next’.

![configure signin](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/configure-signin.png)

### Configure Security Requirements
-	Configure security requirements with Password Policy as `Cognito Defaults`, Multi-factor authentication as `No MFA` to not incur any costs, User Account Recovery with `Enable self-service account recovery’ so users can reset their own passwords, and Delivery Method for User Account Recovery Messages as `Email only` because it doesn’t cost anything to use email. We’re not using SMS because it will cost money.

![configure security requirements](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/configure-security-requirements.png)

### Configure Sign-Up Experience
-	Configure sign-up experience as shown in the following image and then click `next`:

![configure sign up experience](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/configure-signup-experience.png)

NOTE:  
-	For sign-up experience we are choosing self registration so anyone on the internet can sign up for a Cruddur account.  
-	We are allowing Cognito to send a verification message with a code that the User must enter.  For new users, this will verify the attribute and confirm their account.
-	Cognito will send users an email message to verify their email addresses  for sign-in.
-	The  Email address attribute will remain active when an update is pending.
-	Choose the `name` attribute as an attribute required when a new Cruddur User is created.
-	There are only two attributes for the Cruddur application with Cognito: name and email.

### Configure Message Delivery
-	Configure how your user pool sends messages to Users.
-	In order to use Amazon SES it is necessary to set-up a custom domain.  We’re not doing custom domains this point in the bootcamp.  So, that will be revisited in another week.
-	For now, we will use `Send email with Cognito` , which allows for up to 50 emails a day while our Cruddur application is in development.
-	We’ll us the default `no-reply@verificationemail.com` for the FROM email address.
-	Click `next`

![configure message delivery](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/configure-message-delivery.png)

### Integrate Your App
-	Create User pool name as `cruddur-user-pool`
-	Do not use the Cognito Hosted UI due to its deficits
-	Choose `Public client` for the initial app client.  This will automatically populate common default settings. 
-	Make app client name `cruddur`
-	Don’t generate a client secret for the app client for this project.
-	No advanced app client settings used
-	Except all default Attribute Read and Write Permissions.
-	Click `next`

![Integrate your app](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/integrate-your-app.png)

### Review and Create User Pool
-	Review User pool selections
-	Once a User pool has been created it cannot be altered.  If needed, a User pool can be torn done and a new one created with a different configuration.
-	Click `Create user pool`

![user pool success](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/user-pool-success-new.png)


## Install and Configure Amplify Client-Side Library for Amazon Congito

[AWS Amplify]( https://aws.amazon.com/amplify/?trk=9eb02e4d-80e0-4f27-a621-b90b3c870bf3&sc_channel=ps&ef_id=EAIaIQobChMI-qGi0djvgQMVJkVyCh0hgQ7ZEAAYASAAEgIOCfD_BwE:G:s&s_kwcid=AL!4422!3!651751060764!e!!g!!aws%20amplify!19852662236!145019201417)

[AWS Amplify Documentation for Javascript]( https://docs.amplify.aws/start/getting-started/installation/q/integration/js/)
-	Amplify is a SDK
-	Used to bring Cognito into the Cruddur application
-	AWS Cognito used to have its own javascript SDK, but AWS now wants Cognito is used with Amplify for the javascript SDK.

### Install AWS Amplify
-	Start in the frontend directory `frontend-react-js`
-	Install amplify by command line:
```sh
npm i aws-amplify –save
```
-	Now we can see that AWS Amplify has been installed by looking under the `dependencies` section of the `package.json` file.

![amplify install](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/amplify-install.png)

-	Hook up the Cognito Pool to the code in the file `App.js` by placing the following code anywhere in the file.
```
import { Amplify } from 'aws-amplify';
```
-	Configure amplify with the following code block placed in `App.js` below the code from the previous step.
```js
Amplify.configure({
  "AWS_PROJECT_REGION": process.env.REACT_AWS_PROJECT_REGION,
  "aws_cognito_identity_pool_id": process.env.REACT_APP_AWS_COGNITO_IDENTITY_POOL_ID,
  "aws_cognito_region": process.env.REACT_APP_AWS_COGNITO_REGION,
  "aws_user_pools_id": process.env.REACT_APP_AWS_USER_POOLS_ID,
  "aws_user_pools_web_client_id": process.env.REACT_APP_CLIENT_ID,
  "oauth": {},
  Auth: {
    // We are not using an Identity Pool
    // identityPoolId: process.env.REACT_APP_IDENTITY_POOL_ID, // REQUIRED - Amazon Cognito Identity Pool ID
    region: process.env.REACT_AWS_PROJECT_REGION,           // REQUIRED - Amazon Cognito Region
    userPoolId: process.env.REACT_APP_AWS_USER_POOLS_ID,         // OPTIONAL - Amazon Cognito User Pool ID
    userPoolWebClientId: process.env.REACT_APP_AWS_USER_POOLS_WEB_CLIENT_ID,   // OPTIONAL - Amazon Cognito Web Client ID (26-char alphanumeric string)
  }
});
```
-	Set the following environment variables in the `App.js` and `docker-compose.yml` files
  
![app.js variables](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/appjs-variables.png)

-	Use `${AWS_DEFAULT_REGION}` for both the REACT_AWS_PROJECT_REGION and REACT_APP_AWS_COGNITO_REGION.
-	Get the AWS User Pool ID from Amazon Cognito in the Amazon account
-	Get the App Client ID by clicking into `cruddur-user-pool` in AWS Cognito User pools. Then click on the `App integration` tab.  Find the `Client ID` in the `App clients and analytics` section at the bottom of the page.
-	Delete the User `aws_cognito_identity_pool_id` line of code from both the `App.js` and `docker-compose.yml` files because we are not working with identity pool.


### Show Conditional Elements and Data Based On Logged In Or Logged Out
-	Open the `HomeFeedPage.js` file
-	Add the import statement:
```js
import { Auth } from 'aws-amplify';
```

-	Set a react state.  Put the following code in `HomeFeedPage.js`:
```js
const [user, setUser] = React.useState(null);

```
-	Check if the current user is authenticated.  
-	First, remove the `const checkAuth = async () => {` block of code from `HomeFeedPage.js` (10 lines of code).

![authenticated](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/authenticated.png)

-	Replace the deleted code with:
```js
const checkAuth = async () => {
  Auth.currentAuthenticatedUser({
    // Optional, By default is false. 
    // If set to true, this call will send a 
    // request to Cognito to get the latest user data
    bypassCache: false 
  })
  .then((user) => {
    console.log('user',user);
    return Auth.currentAuthenticatedUser()
  }).then((cognito_user) => {
      setUser({
        display_name: cognito_user.attributes.name,
        handle: cognito_user.attributes.preferred_username
      })
  })
  .catch((err) => console.log(err));
};
```
-	Check when the page when the page loads if we are authenticated. Add the code to the file.
```js
React.useEffect(()=>{
 loadData();
 checkAuth();
}, [])
```

![Home Feed](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/home-feed.png)

-	pass user to the Cruddur app components `Desktop Navigation` and `Desktop Sidebar`:
```js
<DesktopNavigation user={user} active={'home'} setPopped={setPopped} />
<DesktopSidebar user={user} />
```
-	Update `ProfileInfo.js` with the following code:
```js
import { Auth } from 'aws-amplify';
```
-	Delete the `import Cookies from js-cooke` because we are not using cookies.
-	Delete the block of code that starts with `const signOut = async () => {` because it contains code about cookies, which we are not using. And replace the entire block of code with:
```js
const signOut = async () => {
  try {
      await Auth.signOut({ global: true });
      window.location.href = "/"
  } catch (error) {
      console.log('error signing out: ', error);
  }
}
```
-	Ran Docker `Compose Up` while in the `react-js: bash` terminal to check that the Cruddur app is working. And it is.


## Cruddur App Signin Page
-	Open the `SigninPage.js`. It is in ` /frontend-react-js/src/pages/`
-	Remove the import statement for cookies and replace with:
```js
import { Auth } from 'aws-amplify';
```
-	Deleted the block of code starting with `const onsubmit = async (event) => {`
-	Replace that block of code with:
```js
const onsubmit = async (event) => {
  setCognitoErrors('')
  event.preventDefault();
  try {
    Auth.signIn(username, password)
      .then(user => {
        localStorage.setItem("access_token", user.signInUserSession.accessToken.jwtToken)
        window.location.href = "/"
      })
      .catch(err => { console.log('Error!', err) });
  } catch (error) {
    if (error.code == 'UserNotConfirmedException') {
      window.location.href = "/confirm"
    }
    setCognitoErrors(error.message)
  }
  return false
}
```

-	Replace the line `setCognitoErrors('')` with `setErrors('')`

-	Replace the line `setCognitoErrors(error.message)` with `setErrors('')`

-	I replaced `username` with `email` to be consistent with the line of code in the file `const[email, setEmail] = React.useState(‘’)`


![const onsubmit](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/const-onsubmit.png)


NOTE: In the block of code `username` means the User email. If you look up the API, it will refer to email as username.


-	Made changes to the block of code one more time because Andrew found that when he started up the frontend and backend containers, that the Sign In page on app did not throw the `User Not Confirmed` error when a User put in the wrong or unknown username and/or password.
-	Added `console.log(‘onsubmit’)` to output a message to the web console to track any errors that may occur while signing into the app.
-	
NOTE:  Adding console.log into a file will allow you to use a web browser’s INSPECT tool to look at console events.  To access INSPECT go to the front-end URL for the app and right click to select INSPECT.

-	Here is the correct code to replace the existing `const onsubmit = async (event) =>{` code block:
```js
Errors('')
    console.log('onsubmit')
    event.preventDefault();
    Auth.signIn(email, password)
    .then(user => {
      localStorage.setItem("access_token", user.signInUserSession.accessToken.jwtToken)
      window.location.href = "/"
    })
    .catch (error => {
      if (error.code == 'UserNotConfirmedException') {
        window.location.href = "/confirm"
      }
      setErrors(error.message)
    });
    return false
  }
```
Note:  Before I continued to “Create a User”,  Andrew mentioned that when we create our cruddur-user-pool we didn’t add the attribute “preferred username” in addition to the “name” attribute. Per Andrew’s recommendation I deleted my user pool and recreated a new cruddur-user-pool with the added preferred username attribute.  

![preferred username](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/preferred-username.png)


-	Then, I went into `app.js` and `docker-compose.yml ` to put in my new `user_pool_id` and `user_pool_client_id`.  
-	I can now create a user.

## Create a User
-	Went to my Amazon Cognito User pool `cruddur-user-pool` and click on it’s name.
-	In the `Users` section click `Create User` 
-	In the `Create user` section I made the following selections and clicked `create user`:

![Create User me](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/create-user-me-new.png)


![user created](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/user-created-new.png)

### Stop AWS Forced Password Change for New User
-	Confirmed that the aws-cli bash terminal recognizes my awsbootcamp UserID, Account Number, and ARN.
-	While in the frontend-react-js directory run this command in the CLI. Replace X’s your password:
```sh
aws cognito-idp admin-set-user-password - -username suzettebecraft - -password XXXXXXX - -user-pool-id us-east-1_JJOgfD6jG - -permanent
```

![force password undone](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/force-password-undone2.png)

-	If you go to the app frontend URL and sign into the Cruddur app  you will no longer get the “Cannot read properties of null ( reading ‘accessToken’)” error because we got rid of the AWS force-password-change feature.
-	Here is my signin to the Cruddur app with my email and password to access a Cruddur session.

![sign in with my email](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/signin-with-myemail-new.png)

- I am able to sign in successfully.  Here is my Cruddur account.

![me in app as user](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/me-in-app-as-user.png)

-	Now try to sign out of the Cruddur app.  `Sign Out` is accessed by clicking on `My Name`.

![Sign Out](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/sign-out.png)

-	Put the following `console.log` command in the Sign In block of code in the `SigninPage.js` file to track any possible errors.  
```js
Console.log(‘user’, user)
```

![inspect logs](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/console-log.png)

-	Sign in to the Cruddur app and then right click webpage and choose INSPECT to see console log for any errors, if they arise.

### Add Preferred Username to User Account 

![my name](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/my-name.png)

-	The `My Name` and `@handle` are fillers for the fields Username and Preferred Username, respectively.  
-	To add the values to those attributes for my User account I went to AWS Cognito User Pool in my awsbootcamp AWS account and clicked on the username,suzettebecraft.
-	In `User Attributes` click on `Edit`. In `Edit User` I added my name , Suzette Becraft, and my preferred name, suzettebecraft. Then, `SAVE CHANGES`.
-	Run Docker `Compose Up` to reset the app environment.   I now see the name and preferred name/handle after signing into the app.

![suzette handle](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/suzette-handle.png)


## Signup Page
-	In the `SignupPage.js` file found in frontend-react, replace `import Cookies from ‘js-cookie’ with:
```js
import { Auth } from 'aws-amplify';
```
-	Replace the `const onsubmit = async (event) => {`  block of code with:
```js
const onsubmit = async (event) => {
  event.preventDefault();
  setCognitoErrors('')
  try {
      const { user } = await Auth.signUp({
        username: email,
        password: password,
        attributes: {
            name: name,
            email: email,
            preferred_username: username,
        },
        autoSignIn: { // optional - enables auto sign in after user is confirmed
            enabled: true,
        }
      });
      console.log(user);
      window.location.href = `/confirm?email=${email}`
  } catch (error) {
      console.log(error);
      setCognitoErrors(error.message)
  }
  return false
}
```

![signup-const](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/signup-const.png)

-	Change both `setCognitoErrors(‘’)` and `setCognitoErrors(error.message)` in the above code to:
```js
setErrors(‘’)
```
```js
setErrors(error.message)
```
-  Realized the `const onsubmit` code block was indented properly.  I shifted the block of lines under `try {`:

![shift code](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/shift-code.png)

## Confirmation Page
-	In the `ConfirmationPage.js` file found in frontend-react, replace `import Cookies from ‘js-cookie’ with:
```js
import { Auth } from 'aws-amplify';
```
-	Delete the `const resend-code` block of code in the file and replace with:
```js
const resend_code = async (event) => {
  setCognitoErrors('')
  try {
    await Auth.resendSignUp(email);
    console.log('code resent successfully');
    setCodeSent(true)
  } catch (err) {
    // does not return a code
    // does cognito always return english
    // for this to be an okay match?
    console.log(err)
    if (err.message == 'Username cannot be empty'){
      setCognitoErrors("You need to provide an email in order to send Resend Activiation Code")   
    } else if (err.message == "Username/client id combination not found."){
      setCognitoErrors("Email is invalid or cannot be found.")   
    }
  }
}
```
-	Change `setCognitoErrors(‘’)` in the above code to:
```js
setErrors(‘’)
```

![resend code](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/resend-code.png)

-	Now replace the block of code under `const onsubmit = async (event) => {` with:
```js
const onsubmit = async (event) => {
  event.preventDefault();
  setCognitoErrors('')
  try {
    await Auth.confirmSignUp(email, code);
    window.location.href = "/"
  } catch (error) {
    setCognitoErrors(error.message)
  }
  return false
}
```
-	Change both `setCognitoErrors(‘’)` and `setCognitoErrors(error.message)` in the above code to:
```js
setErrors(‘’)
```
```js
setErrors(error.message)
```
-	Docker `Compose Up` to open port 3000 to reach Cruddur URL (frontend of app), if not already up.
-	On the homepage of app, signed up with a valid email  that I can access.
-	App won’t complete sign up because it sees the preferred username as an email.
-	Added the following three console.log() statements in the `SignupPage.js` file to help determine origin of error.
-	I did some investigation into my awsbootcamp account in AWS and found that I had Username and Email as Cognito sign-in options when it was supposed to be just Email.
-	Now, I need to delete my `cruddur-user-pool` and recreate it.
-	Click on `Create New User Pool`
-	Configure ` Cognito user pool sign-in options` as Email only.

![redo user pool](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/redo-user-pool.png)

-	After I configured the new `cruddur-user-pool` I restarted Docker to refresh the app.  
-	I do not have any users in my Cognito user pool, so I will sign up with the email that I used before.
-	Click on `Join Now!`

![join now](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/join-now.png)

-	Sign up to create a Cruddur account.

![Suzette create account](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/suzette-create-account-new.png)

-	I now see the `Confirm your Email` page.

![confirm email](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/confirm-email.png)

-	I checked my email account and see there is an email from no-reply@ verificationemail.com with a code for me to submit when I confirm my email on the Cruddur app.

![received code](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/received-code.png)

-	Next, I entered my email and the confirmation code on the `Confirm your Email` app page.
-	 In AWS Cognito, under cruddur-user-pool, you can see that I created a user account for myself.

![new user me](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/new-user-me-new.png)

-	I can login to the Cruddur app successfully.

![Success login again](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/success-login-again.png)

-	Sign out of app.

![sign out with Suzette handle](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/sign-out-with-suzette-handle.png)

## Account Recovery Page
-	I set up a recovery page for when users forget their password.
-	At the `Sign into your Cruddur account` page you can click on `Forgot Password?` to set up a new password.

![app sign in web page](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/app-signin-webpage.png)

![recover password](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/recover-password.png)


-	In the `RecoveryPage.js` file add:
```js
import { Auth } from 'aws-amplify';

```
-	Delete the `const onsubmit_send_code` code block and replace with the following:
```js
const onsubmit_send_code = async (event) => {
  event.preventDefault();
  setCognitoErrors('')
  Auth.forgotPassword(username)
  .then((data) => setFormState('confirm_code') )
  .catch((err) => setCognitoErrors(err.message) );
  return false
}
```
-	Replace `setCognitoErrors(‘’)` with:
```js
setErrors(‘’)
```
-Replace `setCognitoErrors(err.message)` with:
```js
setErrors(err.message)
```
-	Delete the `const onsubmit_cinfirm_code` code block and replace with the following:
```js
const onsubmit_confirm_code = async (event) => {
  event.preventDefault();
  setCognitoErrors('')
  if (password == passwordAgain){
    Auth.forgotPasswordSubmit(username, code, password)
    .then((data) => setFormState('success'))
    .catch((err) => setCognitoErrors(err.message) );
  } else {
    setCognitoErrors('Passwords do not match')
  }
  return false
}
```
-	Replace `setCognitoErrors(‘’)` with:
```js
setErrors(‘’)
```
-Replace `setCognitoErrors(err.message)` with:
```js
setErrors(err.message)
```
-	Refreshed Cruddur `Recover your Password` webpage and entered my email.
-	Now I can reset my password with a verification code I received in an email.

![reset my password two](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/reset-my-password.png)

-	I can sign in with my new password successfully.

![me in app as user](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-3-assets/me-in-app-as-user.png)


## References
- Cloud Security Podcast. (2023, March 8). Amazon Cognito Security Best Practices. YouTube. https://www.youtube.com/watch?v=tEJIeII66pY
