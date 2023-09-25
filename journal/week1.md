# Week 1 — App Containerization

Table of Contents
•	[Week-1 Technical Tasks Overview](#week-1-technical-tasks-overview)
•	[VSCode Docker Extension](#vscode-docker-extension)
•	[Containerize Backend](#containerize-backend)
•	[Containerize Frontend](#containerize-frontend)
•	[Container Security and why](#container-security-and-why)
•	[Docker Architecture and Security Components](#docker-architecture-and-security-components)
•	[Top 10 Container Security Best Practices](#top-10-container-security-best-practices)
•	[Snyk OpenSource Security](#snyk-opensource-security)
•	[AWS Secret Manager](#aws-secret-manager)
•	[AWS Inspector/Clair](#aws-inspector-clair)
•	[Value of Managed Container Services](#value-of-managed-container-services)
•	[References](#references)


## WEEK-1 Technical Tasks Overview:

-	Create a new GitHub repo
-	Launch the repo within a Gitpod workspace
-	Configure Gitpod.yml configuration, eg. I’m VSCode Extensions
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
[Add Dockerfile Instructions] https://github.com/omenking/aws-bootcamp-cruddur-2023/blob/week-1/journal/week1.md
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
Make a new folder inside container
WORKDIR /backend-flask

Used command “ls” to list files/directories to see backend-flask  directory on local host.
Used command “cd backend-flask/ to change into the backend-flask directory
Used command “ls” to see that the file called Dockerfile is listed in my backend-flask directory


&NewLine;
&NewLine;
&nbsp;

FROM python:3.10-slim-buster
Outside Container -> Inside Container
This contains the libraries to install to run the Cruddur app
Install the python libraries used for the app
RUN pip3 install -r requirements.txt
```sh
Pip3 install -r requirements.txt
```

&NewLine;
&NewLine;
&nbsp;


Next I ran the python3 flask module with local host and port 4567
cmd (command), -m for module (flask)
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```sh
 python3 -m flask run –host=0.0.0.0  --port 4567
```

&NewLine;
&NewLine;
&nbsp;

EXPOSE ${PORT}
After running the flask module I unlocked port 4567 from PORTS tab in Gitpod Terminal
Next, click on URL listed for port 4567 in PORTS tab.
I received a 404 error which is good because it means the server is getting requests and is running, but it is saying nothing is showing up.  I needed to set up environment variables and some other things before it completely works. I need to set front end and back end variables for the endpoints.


&NewLine;
&NewLine;
&nbsp;


Set frontend and backend environment variable for the endpoints which will remain set when the container is running
ENV FLASK_ENV=development
```sh
export FRONTEND_URL="*"
```
```sh
export BACKEND_URL="*"
```

Clicking on the URL under PORTs tab should’ve taken me to webpage with data, but I did not see that. 
I then copied the URL next to open port 4567, pasted it into a browser, and added /api/activities/home and got the following output:
I got back JSON which is the output I should see. 
Note: Difference between RUN and CMD. 
RUN is used to create a layer in an image file.  CMD essentially is the command the container is going to run when it starts up. RUN as set of processes need to put in the container image and build the image.

To stop the server press CTRL + C to quit, which sends a kill command to the server
Two other ways to stop server is to click on the trashcan icon at top right of terminal screen.  Or click on 3 bars in upper left of Gitpod to the left of EXPLORER, click on terminal and run new terminal.
&NewLine;
&NewLine;
&nbsp;

Unset the backend and frontend environment variables in case they cause issue when I run a new container file.
```sh
Unset FRONTEND
Unset BACKEND
```
Check that the backend and frontend variables are gone.
```sh
env | grep _URL
```


&NewLine;
&NewLine;
&nbsp;

### Build Container
Next step is to build a docker container.
But first we need to make sure we are in are app and not in the backend-flask folder.
Ran the `pwd` command to see my working directory. Then, ran `cd .. ` to move up one directory.
I am now working in the main directory.
```sh
Pwd
cd ..
```
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
To get HELP for docker build:
```sh
Docker build –help
```
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
I got a 404-ERROR as expected because I still need to reset the environment variables.

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

I got a 404-ERROR after running the container
Then, I added  `/app/activites/home` to the end of the URL in order to access data on server
Go into new Bash terminal  and run command `docker ps` to see running containers.
```sh
docker ps
```
&NewLine;
&NewLine;
&nbsp;

Need to kill container to remove it. Can use - -rm at end of RUN command to kill container right after is run.
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

I ran an NPM install before building the frontend container because it needs to copy the contents of node modules
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

Now create a docker file at `front-end-react-js/Dockerfile` 
Click on `frontend-react-js` directory in Gitpod, right click and choose NEW FILE
Name the new file `Dockerfile`
Copy and paste the following code into the newly created ‘Dockerfile`:
```sh
FROM node:16.18
ENV PORT=3000
COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]

&NewLine;
&NewLine;
&nbsp;

Created a `docker-compose.yml` file at root of my project (main).
This .yml file allows for the running of multiple containers at the same time.
Docker Compose is a multi-container deployment with Infrastructure As Code (IAC) that makes it much easier to run containers that need to run together logically.

The standard way of using Docker is with a Docker file, but it is not good for running multiple parts of an application.
Docker came up with Docker Compose as a solution to running multiple parts of a project.

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
The backend container(s) is on port 4567
Make sure both are unlocked.
&NewLine;
&NewLine;
&nbsp;
![Image of ports 3000 and 4567](  )
Click on the frontend URL and you will see the Cruddur application.  We now have access to the web application.  The frontend and backend are able to communicate with each other.
&NewLine;
&NewLine;
&nbsp;
![Cruddur app homepage](  )
&NewLine;
&NewLine;
&nbsp;

# Container Security and Why
What is Container Security?   It is the practice of protecting your applications hosted on compute services like Containers. Examples include Single Page Applications (SPAs), Microservices, and APIs.
&NewLine;
&NewLine;
&nbsp;


Note:  containers can also run outside of the cloud.
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
![Docker Components](   )
&NewLine;
&NewLine;
&nbsp;


### Container Security Components
![Container Security Components](  )

&NewLine;
&NewLine;
&nbsp;

## Top 10 Container Security Best Practices
![Top 10 Container Security Best Practices](  )

&NewLine;
&NewLine;
&nbsp;

## Snyk OpenSource Seccurity
-	Added to provide Container Security by finding opensource vulnerabilities in your Dockerfile itself before using it..
-	Snyk OpenSource and Snyk Container Security are available for personal projects.
-	Used to find vulnerabilities in docker files
![Snyk](   )
&NewLine;
&NewLine;
&nbsp;

Went to www.CloudSecurityBootcamp.com to connect to Snyk open source libraries for this AWS bootcamp.
![Snyk Login] (   )

&NewLine;
&NewLine;
&nbsp;



### Authorize Snyk login with GitHub
![Authorize Snyk] (   )
&NewLine;
&NewLine;
&nbsp;

Choose GitHUb, Public Repository, and skip automation settings

![ github-successfully-connected-snyk]( )
&NewLine;
&NewLine;
&nbsp;

There are no images in the my aws-bootcamp-cruddur-2023 yet.
[Get repository at](https://github.com/snyk-labs/docker-goof)
Create a new fork that is `SBecraft/docker-goof`
In Description wrote “Vulnerable Docker Repository for Testing”
Click “Create fork”
Now I have a `docker-goof` repository under my GitHub account
&NewLine;
&NewLine;
&nbsp;

![Docker-goof repository in my GitHub](     )
&NewLine;
&NewLine;
&nbsp;

S/Becraft/docker-goof is now in Snyk app as a repository
Click on S/Becraft/docker-goof 
Click on `Import and Scan`
&NewLine;
&NewLine;
&nbsp;

Snyk reported that my SBecraft/docker-goof repository had 289 vulnerabilities across 6 projects.
&NewLine;
&NewLine;
&nbsp;

![ Snyk reported vulnerabilities](   )

&NewLine;
&NewLine;
&nbsp;

Click onto the `Docker` project file to see what is causing vulnerabilities in it specifically.
Note:  Snyk is looking at files within the SBeraft/docker-goof repository and not image files.

&NewLine;
&NewLine;
&nbsp;




## AWS Secret Manager
Stores Secrets in EC2 in the AWS Cloud Computing platform.
Another option is Hashicorp Vault to keep Secrets without having to keep secrets in a dockerfile permanently.  It is good practice not to keep secrets indefinitely in those docker files.
Next, I logged into my AWS User account “awsbootcamp”.  This is not my root account.  It is good practice not to use Root for daily tasks.
&NewLine;
&NewLine;
&nbsp;

![AWS Secrets Manager Console](  )

&NewLine;
&NewLine;
&nbsp;


### Create a secret 
I clicked on `Store a new secret` on AWS Secrets Manager webpage.
I chose `Other type of event` for an API
Next, I chose `Key/value` under Key/Value Pairs and put Password and DockerFile as key-value pair.
Kept `aws/secretsmanager` under Encryption Key option. Then, clicked “next’
&NewLine;
&NewLine;
&nbsp;

Configure secret with Secret name `docker/password/dev` and Tag as key value pair `Purpose` and `Docker-test`
&NewLine;
&NewLine;
&nbsp;

### Configure rotation of Secret
Turn on `Automatic rotation` and click “next” if you like, but lamda will have to be configured.
This is ok to do because the Secret is not stored in a Docker file.  It is used temporarily for an API call, so it is ok if Secrets Manager rotates Secrets.

I did not use `Automatic rotation” per the bootcamp instructor so that we could avoid setting up Lamda at this time and move on to AWS Inspector.  I created the Secret without automatic rotation.
&NewLine;
&NewLine;
&nbsp;

![Saved Secret](   )

&NewLine;
&NewLine;
&nbsp;

## AWS Inspector/Clair
Can use AWS Inspector or Clair to check if a docker image is vulnerable, but it is good to know that Clair runs in the background of AWS Inspector.  
Clair is open-source Container Image Seccurity Analyzer by CoreOS.
Amazon Inspector has integration with ECS, EC2 and ECR (registry) to scan Container images.
&NewLine;
&NewLine;
&nbsp;

![Amazon Inspector Console]( )

&NewLine;
&NewLine;
&nbsp;

Click on `Get Started`
On Activate Inspector page click on “Activate Inspector` in lower right-hand corner of webpage.
&NewLine;
&NewLine;
&nbsp;


![Inspector Successful Setup](  )

&NewLine;
&NewLine;
&nbsp;

### Image Scan with Snyk
Can run scan of all containers with Snyk
Install Docker desktop if CLI is challenging.
Refer to the following webpage for instructions on using Docker Compose to scan Containers.
&NewLine;
&NewLine;
&nbsp;

{Docker Compose Overview] Docker Compose overview | Docker Docs

&NewLine;
&NewLine;
&nbsp;
## Value of Managed Container Services 
Problem with Docker Container or Docker Compose is every time you want to make a change you have to stop the machine. The problem with this you can only build one application.  It cannot build more than one machine from one file.  And if you want to make any security updates you have to shut down the machine, make the update, and turn the machine back on.  This may upset the customer.
It is better to use Managed Container services like AWS services like AWS ECS, AWS, EKS, AWS Fargate, AWS APP Runner, or AWS CoPilot. 
&NewLine;
&NewLine;
&nbsp;


![Running Containers in AWS](    )

&NewLine;
&NewLine;
&nbsp;

## References
{Docker Compose Overview] Docker Compose overview | Docker Docs
[Debugging Connection Refused] https://pythonspeed.com/articles/docker-connection-refused/
