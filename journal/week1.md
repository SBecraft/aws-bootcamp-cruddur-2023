# Week 1 — App Containerization

Table of Contents
-	[Week-1 Technical Tasks Overview](#week-1-technical-tasks-overview)
-	[VSCode Docker Extension](#vscode-docker-extension)
-	[Containerize Backend](#containerize-backend)
-	[Containerize Frontend](#containerize-frontend)
-	[Container Security and why](#container-security-and-why)
-	[Docker Architecture and Security Components](#docker-architecture-and-security-components)
-	[Top 10 Container Security Best Practices](#top-10-container-security-best-practices)
-	[Snyk OpenSource Security](#snyk-opensource-security)
-	[AWS Secret Manager](#aws-secret-manager)
-	[AWS Inspector/Clair](#aws-inspector-clair)
-	[Value of Managed Container Services](#value-of-managed-container-services)
-	[References](#references)



## WEEK-1 Technical Tasks Overview:

-	Create a new GitHub repo
-	Launch the repo within a Gitpod workspace
-	Configure Gitpod.yml configuration
-	Clone the frontend and backend repo
-	Explore the codebases
-	Ensure we can get the apps running locally
-	Write a Dockerfile for each app
-	Ensure we get the apps running via individual container
-	Create a docker-compose file
-	Ensure we can orchestrate multiple containers to run side by side
-	Mount directories so we can make changes while we code

&NewLine;
&NewLine;
&nbsp;


## VSCode Docker Extension
[VSCode with Containers Overview] https://code.visualstudio.com/docs/containers/overview
&NewLine;
&NewLine;
&nbsp;

Usually Gitpod is pre-installed with the Docker extension.
&NewLine;
&NewLine;
&nbsp;

I did not have the Docker extension, so I added it to my Gitpod VSCode.
&NewLine;
&NewLine;
&nbsp;

## Containerize Backend
Note:  `workdir` container command is referring to /backend-flask within the container and the container shares the host operating system kernel.
&NewLine;
&NewLine;
&nbsp;
Backend Containerized Dockerfile based off the following image file:
&NewLine;
&NewLine;
&nbsp;

[Add Dockerfile Instructions]https://github.com/omenking/aws-bootcamp-cruddur-2023/blob/week-1/journal/week1.md

&NewLine;
&NewLine;
&nbsp;

```sh
FROM python:3.10-slim-buster
WORKDIR /backend-flask
COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt
COPY . .
ENV FLASK_ENV=development
EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

&NewLine;
&NewLine;
&nbsp;

### Add Dockerfile
Working from inside Container
&NewLine;
&NewLine;
&nbsp;

Make a new folder inside container
&NewLine;
&NewLine;
&nbsp;

WORKDIR /backend-flask
&NewLine;
&NewLine;
&nbsp;

![Show backend-flask folder](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/shows%20docker%20image%20built%20successfully.png)

&NewLine;
&NewLine;
&nbsp;


Created new file within the backend-flask directory:
&NewLine;
&NewLine;
&nbsp;

![Backend-flask Container with code](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/backend-flask-container-code.png)

&NewLine;
&NewLine;
&nbsp;

Used command `ls` to list files/directories to see backend-flask  directory on local host.
&NewLine;
&NewLine;
&nbsp;

Used command `cd` backend-flask/ to change into the backend-flask directory
&NewLine;
&NewLine;
&nbsp;

Used command `ls` to see that the file called Dockerfile is listed in my backend-flask directory
&NewLine;
&NewLine;
&nbsp;

![Show backend-flask dir and Dockerfile](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/change-into-Dockerfile-before-pip.png)

&NewLine;
&NewLine;
&nbsp;


FROM python:3.10-slim-buster
&NewLine;
&NewLine;
&nbsp;

Outside Container -> Inside Container
&NewLine;
&NewLine;
&nbsp;

This contains the libraries to install to run the Cruddur app
&NewLine;
&NewLine;
&nbsp;

Install the python libraries used for the app
&NewLine;
&NewLine;
&nbsp;

RUN pip3 install -r requirements.txt
```sh
Pip3 install -r requirements.txt
```

&NewLine;
&NewLine;
&nbsp;


Proof of successful Python install:
&NewLine;
&NewLine;
&nbsp;

![successful python install](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/successful%20python%20install.png)

&NewLine;
&NewLine;
&nbsp;


Next I ran the python3 flask module with local host and port 4567
&NewLine;
&NewLine;
&nbsp;

cmd (command), -m for module (flask)
&NewLine;
&NewLine;
&nbsp;

CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```sh
 python3 -m flask run –host=0.0.0.0  --port 4567
```

&NewLine;
&NewLine;
&nbsp;

![Python3 flask](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/python3-flask.png)

&NewLine;
&NewLine;
&nbsp;

EXPOSE ${PORT}
&NewLine;
&NewLine;
&nbsp;

After running the flask module I unlocked port 4567 from PORTS tab in Gitpod Terminal.
&NewLine;
&NewLine;
&nbsp;

Next, click on URL listed for port 4567 in PORTS tab.
&NewLine;
&NewLine;
&nbsp;

I received a 404 error which is good because it means the server is getting requests and is running, but it is saying nothing is showing up.  I needed to set up environment variables and some other things before it completely works. I need to set frontend and backend variables for the endpoints.


&NewLine;
&NewLine;
&nbsp;

![404 Errors](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/404-errors.png)

&NewLine;
&NewLine;
&nbsp;

![404 Not Found Need Env Vars](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/404-URL-Running-Need-Env-Vars.png)

&NewLine;
&NewLine;
&nbsp;

Set frontend and backend environment variable for the endpoints which will remain set when the container is running.
&NewLine;
&NewLine;
&nbsp;


```sh
export FRONTEND_URL="*"
```
```sh
export BACKEND_URL="*"
```

Clicking on the URL under PORTs tab should’ve taken me to webpage with data, but I did not see that. 
&NewLine;
&NewLine;
&nbsp;

I then copied the URL next to open port 4567, pasted it into a browser, and added `/api/activities/home` and got the following output:

&NewLine;
&NewLine;
&nbsp;

![Env Vars python3](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/env-vars-python3.png)

&NewLine;
&NewLine;
&nbsp;

I got back JSON which is the output I should see.
&NewLine;
&NewLine;
&nbsp;

Note: Difference between RUN and CMD.
&NewLine;
&NewLine;
&nbsp;

RUN is used to create a layer in an image file.  CMD essentially is the command the container is going to run when it starts up. RUN as set of processes need to put in the container image and build the image.

&NewLine;
&NewLine;
&nbsp;

To stop the server press `CTRL + C` to quit, which sends a kill command to the server.
&NewLine;
&NewLine;
&nbsp;

Two other ways to stop server is to click on the trashcan icon at top right of terminal screen.  Or click on 3 bars in upper left of Gitpod and click on terminal and run new terminal.
&NewLine;
&NewLine;
&nbsp;

Unset the backend and frontend environment variables in case they cause issue when I run a new container file.
```sh
Unset FRONTEND
Unset BACKEND
```
&NewLine;
&NewLine;
&nbsp;

Check that the backend and frontend variables are gone.
```sh
env | grep _URL
```


&NewLine;
&NewLine;
&nbsp;

### Build Container
Next step is to build a docker container.
&NewLine;
&NewLine;
&nbsp;

But first we need to make sure we are in are app and not in the backend-flask folder.
&NewLine;
&NewLine;
&nbsp;

Ran the `pwd` command to see my working directory. Then, ran `cd .. ` to move up one directory.
&NewLine;
&NewLine;
&nbsp;

I am now working in the main directory.
```sh
Pwd
cd ..
```
&NewLine;
&NewLine;
&nbsp;

![Change into Dockerfile](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/change-into-Dockerfile2.png)


&NewLine;
&NewLine;
&nbsp;

Run the command to build the container (-t is for tag image)
```sh
docker build -t  backend-flask ./backend-flask
```
&NewLine;
&NewLine;
&nbsp;

![Build backend container](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/build-backend-container.png)

&NewLine;
&NewLine;
&nbsp;

Proof that docker image was created:
&NewLine;
&NewLine;
&nbsp;


![Proof of docker image](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/proof-of-docker-image.png)

&NewLine;
&NewLine;
&nbsp;

To get HELP for docker build:
```sh
Docker build –help
```
&NewLine;
&NewLine;
&nbsp;

Be sure to tag version for an image. Ex. `docker build -t backend-flask:version 1.0.0 ./backend-flask`



&NewLine;
&NewLine;
&nbsp;


### Run Docker Container
Ran the following command to make sure that I can run the container:
```sh
docker run --rm -p 4567:4567 -it backend-flask
```
&NewLine;
&NewLine;
&nbsp;

![Port 4567 unlocked](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/port4567-unlocked.png)

&NewLine;
&NewLine;
&nbsp;

![Image after running container](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/image-after-running-container.png)


&NewLine;
&NewLine;
&nbsp

I got a 404-ERROR as expected because I still need to reset the environment variables.
&NewLine;
&NewLine;
&nbsp;

Stop the container either by `CTRL + C`.  OR click on the container file under CONTAINERS in the docker extension, right click on STOP.
&NewLine;
&NewLine;
&nbsp;


Set the frontend and backend environment variables locally.
```sh
export FRONTEND_URL="*"
```
```sh
export BACKEND_URL="*"
```
&NewLine;
&NewLine;
&nbsp;

Run the docker container image with the environment variables.
```sh
docker run --rm -p 4567:4567 -it -e FRONTEND_URL=’*’ -e BACKEND_URL=’*’ backend-flask
```
&NewLine;
&NewLine;
&nbsp;

I got a 404-ERROR after running the container.
&NewLine;
&NewLine;
&nbsp;

Then, I added  `/app/activites/home` to the end of the URL in order to access data on server.
&NewLine;
&NewLine;
&nbsp;

Go into new Bash terminal  and run command `docker ps` to see running containers.
```sh
docker ps
```
&NewLine;
&NewLine;
&nbsp;

Need to kill container to remove it. Can use `--rm` at end of RUN command to kill container right after is run.
&NewLine;
&NewLine;
&nbsp;

## Containerize Frontend
Change directory to ‘frontend-react-js/`
```sh
cd frontend-react-js/
```
&NewLine;
&NewLine;
&nbsp;

Next, I ran an NPM install before building the frontend container because it needs to copy the contents of node modules
```sh
npm i
```

&NewLine;
&NewLine;
&nbsp;

Change back to the main directory (aws-bootcamp-cruddur-2023)
```sh
cd ..
```
&NewLine;
&NewLine;
&nbsp;

Now create a docker file at `front-end-react-js/Dockerfile`.
&NewLine;
&NewLine;
&nbsp;

Click on `frontend-react-js` directory in Gitpod, right click and choose NEW FILE.
&NewLine;
&NewLine;
&nbsp;

Name the new file `Dockerfile`.
&NewLine;
&NewLine;
&nbsp;

Copy and paste the following code into the newly created ‘Dockerfile`:
```sh
FROM node:16.18
ENV PORT=3000
COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```

&NewLine;
&NewLine;
&nbsp;

![Front-end react js and Dockerfile](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/backend-dockerfile-image.png)

&NewLine;
&NewLine;
&nbsp;

Created a `docker-compose.yml` file at root of my project (main).
&NewLine;
&NewLine;
&nbsp;

This .yml file allows for the running of multiple containers at the same time.
&NewLine;
&NewLine;
&nbsp;

Docker Compose is a multi-container deployment with Infrastructure As Code (IAC) that makes it much easier to run containers that need to run together logically.

&NewLine;
&NewLine;
&nbsp;

The standard way of using Docker is with a Docker file, but it is not good for running multiple parts of an application.
&NewLine;
&NewLine;
&nbsp;

Docker came up with Docker Compose as a solution to running multiple parts of a project.

&NewLine;
&NewLine;
&nbsp;

`docker-compose.yml` file:
```sh
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
```

&NewLine;
&NewLine;
&nbsp;

Run `docker-compose up`
```sh
Docker-compose up
```
&NewLine;
&NewLine;
&nbsp;

The frontend container(s) is on port 3000
&NewLine;
&NewLine;
&nbsp;

The backend container(s) is on port 4567
&NewLine;
&NewLine;
&nbsp;

Make sure both are unlocked.

&NewLine;
&NewLine;
&nbsp;

![Image of ports 3000 and 4567](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/both-ports-open.png)

&NewLine;
&NewLine;
&nbsp;

Click on the frontend URL and you will see the Cruddur application.
&NewLine;
&NewLine;
&nbsp;

We now have access to the web application.
&NewLine;
&NewLine;
&nbsp;

The frontend and backend can communicate with each other.
&NewLine;
&NewLine;
&nbsp;


![Cruddur app homepage](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/cruddur-app.png)


&NewLine;
&NewLine;
&nbsp;

# Container Security and Why
What is Container Security?
&NewLine;
&NewLine;
&nbsp;

It is the practice of protecting your applications hosted on compute services like Containers.
&NewLine;
&NewLine;
&nbsp;

Examples include Single Page Applications (SPAs), Microservices, and APIs.

&NewLine;
&NewLine;
&nbsp;


NOTE:  Containers can also run outside of the cloud.
&NewLine;
&NewLine;
&nbsp;

### Why care about Container Security?
-	Most applications are being developed with Containers and Cloud Native
-	Container Security reduces the impact of breach through the segregation of applications and related services
-	Managed Container services means your security responsibility is focused on few things like AWS ECS and AWS ECR, for example.
-	Automation can reduce recovery times to a known good state easily

&NewLine;
&NewLine;
&nbsp;

### Why Container Security requires practice to implement effectively
-	Containers are complex
-	If you rely on a Managed Cloud Service Provider (CSP) for features you may be limited to the services they provide and will need to learn those specific features.  The CSP provides the management of the Container(s).
-	Using UnManaged Container services (not CSP, rather on-prem) requires a lot more hours of work then Managed but would require you to keep updated on everything containers.

&NewLine;
&NewLine;
&nbsp;

## Docker Architecture and Security Components
###Docker Components
Docker Client is the laptop that Docker is installed as a service to run that has the Build feature, Pull feature, and the Run feature.
-	Rest API calls are made to the Docker Host/Server
-	Registry is like an address book on the internet but specifically for images that you can use on Docker. 
-	Registry of Docker images can be a public registry or a private registry.  A lot of enterprises like to have a private registry that they can control and manage themselves and make sure their images are not vulnerable like they could be if placed in a public registry.

&NewLine;
&NewLine;
&nbsp;

![Docker Components](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/docker-components.png)

&NewLine;
&NewLine;
&nbsp;

![Docker components 2](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/docker-components%20(2).png)


&NewLine;
&NewLine;
&nbsp;

### Container Security Components

![Container Security Components](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/container-security-components.png)


&NewLine;
&NewLine;
&nbsp;

## Top 10 Container Security Best Practices

![Top 10 Container Security Best Practices](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/top-ten-security-best-practices.png)


&NewLine;
&NewLine;
&nbsp;

## Snyk OpenSource Seccurity
-	Added to provide Container Security by finding opensource vulnerabilities in your Dockerfile itself before using it..
-	Snyk OpenSource and Snyk Container Security are available for personal projects.
-	Used to find vulnerabilities in docker files
  
&NewLine;
&NewLine;
&nbsp;

Went to www.CloudSecurityBootcamp.com to connect to Snyk open source libraries for this AWS bootcamp.
&NewLine;
&NewLine;
&nbsp;

![Snyk Login](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/bootcamp-snyk-libraries-login.png)


&NewLine;
&NewLine;
&nbsp;



### Authorize Snyk login with GitHub

![Authorize Snyk](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/authorize-snyk.png)


&NewLine;
&NewLine;
&nbsp;

Choose GitHUb, Public Repository, and skip automation settings.

![github-successfully-connected-snyk](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/github-successfully-connected-snyk.png)


&NewLine;
&NewLine;
&nbsp;

There are no images in the my aws-bootcamp-cruddur-2023 yet.
&NewLine;
&NewLine;
&nbsp;

[Get repository at](https://github.com/snyk-labs/docker-goof)

&NewLine;
&NewLine;
&nbsp;

Create a new fork that is `SBecraft/docker-goof`

&NewLine;
&NewLine;
&nbsp;

In Description wrote “Vulnerable Docker Repository for Testing”

&NewLine;
&NewLine;
&nbsp;

Click `Create fork`

&NewLine;
&NewLine;
&nbsp;

Now I have a `docker-goof` repository under my GitHub account

&NewLine;
&NewLine;
&nbsp;

![Docker-goof repository in my GitHub](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/docker-goof-repository-suzette.png)


&NewLine;
&NewLine;
&nbsp;

S/Becraft/docker-goof is now in Snyk app as a repository.

&NewLine;
&NewLine;
&nbsp;

Click on `S/Becraft/docker-goof` 

&NewLine;
&NewLine;
&nbsp;

Click on `Import and Scan`

&NewLine;
&NewLine;
&nbsp;

Snyk reported that my `SBecraft/docker-goof` repository had 289 vulnerabilities across 6 projects.

&NewLine;
&NewLine;
&nbsp;

![Snyk reported vulnerabilities](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/snyk-reported-vulnerabilities.png)


&NewLine;
&NewLine;
&nbsp;

Click onto the `Docker` project file to see what is causing vulnerabilities in it specifically.
&NewLine;
&NewLine;
&nbsp;

Note:  Snyk is looking at files within the `SBeraft/docker-goof` repository and not image files.

&NewLine;
&NewLine;
&nbsp;




## AWS Secret Manager
Stores Secrets in EC2 in the AWS Cloud Computing platform.
&NewLine;
&NewLine;
&nbsp;

Another option is Hashicorp Vault to keep Secrets without having to keep secrets in a dockerfile permanently.  It is good practice not to keep secrets indefinitely in those docker files.

&NewLine;
&NewLine;
&nbsp;

Next, I logged into my AWS User account “awsbootcamp”.  This is not my root account.  It is good practice not to use Root for daily tasks.

&NewLine;
&NewLine;
&nbsp;

![AWS Secrets Manager Console](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/aws-secrets-manager-homepage.png)


&NewLine;
&NewLine;
&nbsp;


### Create a secret 
I clicked on `Store a new secret` on AWS Secrets Manager webpage.
&NewLine;
&NewLine;
&nbsp;

I chose `Other type of event` for an API

&NewLine;
&NewLine;
&nbsp;

Next, I chose `Key/value` under Key/Value Pairs and put `Password` and `DockerFile` as key-value pair.
&NewLine;
&NewLine;
&nbsp;

Kept `aws/secretsmanager` under Encryption Key option. Then, clicked “next".

&NewLine;
&NewLine;
&nbsp;

![Create Secret](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/create-secret-type.png)


&NewLine;
&NewLine;
&nbsp;


Configure secret with Secret name `docker/password/dev` and Tag as key value pair `Purpose` and `Docker-test`
&NewLine;
&NewLine;
&nbsp;

![Configure Secret](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/configure-secret.png)

&NewLine;
&NewLine;
&nbsp;


### Configure rotation of Secret
Turn on `Automatic rotation` and click “next” if you like, but lamda will have to be configured.
&NewLine;
&NewLine;
&nbsp;

This is ok to do because the Secret is not stored in a Docker file.  It is used temporarily for an API call, so it is ok if Secrets Manager rotates Secrets.

&NewLine;
&NewLine;
&nbsp;

I did not use `Automatic rotation” per the bootcamp instructor so that we could avoid setting up Lamda at this time and move on to AWS Inspector.  I created the Secret without automatic rotation.
&NewLine;
&NewLine;
&nbsp;

Proof I created a Secret in AWS Secret Manager:
&NewLine;
&NewLine;
&nbsp;


![Saved Secret](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/saved-secret-secretmanager.png)


&NewLine;
&NewLine;
&nbsp;

## AWS Inspector/Clair
Can use AWS Inspector or Clair to check if a docker image is vulnerable, but it is good to know that Clair runs in the background of AWS Inspector.  
&NewLine;
&NewLine;
&nbsp;

Clair is open-source Container Image Seccurity Analyzer by CoreOS.
&NewLine;
&NewLine;
&nbsp;

Amazon Inspector has integration with ECS, EC2 and ECR (registry) to scan Container images.

&NewLine;
&NewLine;
&nbsp;

![Amazon Inspector Console](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/amazon-inspector-console.png)


&NewLine;
&NewLine;
&nbsp;

Click on `Get Started`

&NewLine;
&NewLine;
&nbsp;

On Activate Inspector page click on “Activate Inspector` in lower right-hand corner of webpage.
&NewLine;
&NewLine;
&nbsp;

![Inspector Successful Setup](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/inspector-successful-setup.png)


&NewLine;
&NewLine;
&nbsp;

### Image Scan with Snyk
Can run scan of all containers with Snyk.
&NewLine;
&NewLine;
&nbsp;

Install Docker desktop if CLI is challenging.
&NewLine;
&NewLine;
&nbsp;

Refer to the following webpage for instructions on using Docker Compose to scan Containers.
&NewLine;
&NewLine;
&nbsp;

[Docker Compose Overview]https://docs.docker.com/compose/

&NewLine;
&NewLine;
&nbsp;

## Value of Managed Container Services 
Problem with Docker Container or Docker Compose is every time you want to make a change you have to stop the machine. The problem with this you can only build one application.  It cannot build more than one machine from one file.  And if you want to make any security updates you have to shut down the machine, make the update, and turn the machine back on.  This may upset the customer.

&NewLine;
&NewLine;
&nbsp;

It is better to use Managed Container services like AWS services like AWS ECS, AWS, EKS, AWS Fargate, AWS APP Runner, or AWS CoPilot. 
&NewLine;
&NewLine;
&nbsp;

![Running Containers in AWS](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/running-containers-in-aws.png)


&NewLine;
&NewLine;
&nbsp;

## References
- [Docker Compose Overview] https://docs.docker.com/compose/
&NewLine;
&NewLine;
&nbsp;
- [Debugging Connection Refused] https://pythonspeed.com/articles/docker-connection-refused/
