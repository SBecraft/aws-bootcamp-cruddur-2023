# Week 1 — App Containerization

Table of Contents
-	[Technical Tasks](#technical-tasks)
-	[VSCode Docker Extension](#vscode-docker-extension)
-	[Containerize Backend](#containerize-backend)
-	[Containerize Frontend](#containerize-frontend)
-	[Adding DynamoDB Local and Postgres](#adding-dynamodb-local-and-postgres)
-	[Container Security and why](#container-security-and-why)
-	[Docker Architecture and Security Components](#docker-architecture-and-security-components)
-	[Top 10 Container Security Best Practices](#top-10-container-security-best-practices)
-	[Snyk OpenSource Security](#snyk-opensource-security)
-	[AWS Secret Manager](#aws-secret-manager)
-	[AWS Inspector/Clair](#aws-inspector-clair)
-	[Value of Managed Container Services](#value-of-managed-container-services)
-	[References](#references)



## Technical Tasks 

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

## VSCode Docker Extension
[VSCode with Containers Overview](https://code.visualstudio.com/docs/containers/overview)
&NewLine;
&NewLine;
&nbsp;

- Usually Gitpod is pre-installed with the Docker extension.
- I did not have the Docker extension, so I added it to my Gitpod VSCode.


## Containerize Backend
Note:  `workdir` container command is referring to /backend-flask within the container and the container shares the host operating system kernel.

- Backend Containerized Dockerfile based off the following image file:



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

### Add Dockerfile
- Working from inside Container
- Make a new folder 
- WORKDIR /backend-flask

![Show backend-flask folder](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/shows%20docker%20image%20built%20successfully.png)


- Created new file within the backend-flask directory:

![Backend-flask Container with code](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/backend-flask-container-code.png)


- Used command `ls` to list files/directories to see backend-flask  directory on local host.
- Used command `cd` backend-flask/ to change into the backend-flask directory
- Used command `ls` to see that the file called Dockerfile is listed in my backend-flask directory

![Show backend-flask dir and Dockerfile](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/change-into-Dockerfile-before-pip.png)

&NewLine;
&NewLine;
&nbsp;


FROM python:3.10-slim-buster
- Outside Container -> Inside Container
- This contains the libraries to install to run the Cruddur app
- Install the python libraries used for the app
- RUN pip3 install -r requirements.txt
```sh
Pip3 install -r requirements.txt
```

Proof of successful Python install:
&NewLine;
&NewLine;
&nbsp;

![successful python install](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/successful%20python%20install.png)

&NewLine;
&NewLine;
&nbsp;

- Next I ran the python3 flask module with local host and port 4567
- cmd (command), -m for module (flask)
- CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```sh
 python3 -m flask run –host=0.0.0.0  --port 4567
```

![Python3 flask](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/python3-flask.png)

&NewLine;
&NewLine;
&nbsp;

EXPOSE ${PORT}

- After running the flask module I unlocked port 4567 in the PORTS tab in Gitpod Terminal.
- Next, click on URL listed for port 4567 in PORTS tab.
- I received a 404 error which is good because it means the server is getting requests and is running, but it is saying nothing is showing up.  I needed to set up environment variables and some other things before it completely works. I need to set frontend and backend variables for the endpoints.
&NewLine;
&NewLine;
&nbsp;

![404 Not Found Need Env Vars](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/404-URL-Running-Need-Env-Vars.png)

- Here is the 404 Error displayed in the terminal.

![404 Errors](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/404-errors.png)

- Set frontend and backend environment variable for the endpoints which will remain set when the container is running.

```sh
export FRONTEND_URL="*"
```
```sh
export BACKEND_URL="*"
```

- Clicking on the URL under PORTs tab should’ve taken me to webpage with data, but I did not see that. 


- I then copied the URL next to open port 4567, pasted it into a browser, and added `/api/activities/home` and got the following output:

![Env Vars python3](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/env-vars-python3.png)

- I got back JSON which is the output I should see.
&NewLine;
&NewLine;
&nbsp;

Note: There is a difference between RUN and CMD. RUN is used to create a layer in an image file.  CMD essentially is the command the container is going to run when it starts up. RUN as set of processes need to put in the container image and build the image.
&NewLine;
&NewLine;
&nbsp;
- To stop the server press `CTRL + C` to quit, which sends a kill command to the server.
- Two other ways to stop server is to click on the trashcan icon at top right of terminal screen.  Or click on 3 bars in upper left of Gitpod and click on terminal and run new terminal.


- Unset the backend and frontend environment variables in case they cause issue when I run a new container file.
```sh
Unset FRONTEND
Unset BACKEND
```

- Check that the backend and frontend variables are gone.
```sh
env | grep _URL
```
&NewLine;
&NewLine;
&nbsp;
### Build Container
Next step is to build a docker container.
- First we need to make sure we are in are app and not in the backend-flask folder.
- I ran the `pwd` command to see my working directory. Then, ran `cd .. ` to move up one directory.

```sh
Pwd
cd ..
```
- I am now working in the main directory.

![Change into Dockerfile](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/change-into-Dockerfile2.png)

- Run the command to build the container (-t is for tag image)
```sh
docker build -t  backend-flask ./backend-flask
```

![Build backend container](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/build-backend-container.png)

- Proof that docker image was created:

![Proof of docker image](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/proof-of-docker-image.png)

&NewLine;
&NewLine;
&nbsp;

To get HELP for docker build:
```sh
Docker build –help
```

- Be sure to tag version for an image. Ex. `docker build -t backend-flask:version 1.0.0 ./backend-flask`

&NewLine;
&NewLine;
&nbsp;

### Run Docker Container
- Ran the following command to make sure that I can run the container:
```sh
docker run --rm -p 4567:4567 -it backend-flask
```

- Screenshot of PORTS tab in terminal that displays port 4567 is open to the public.
![Port 4567 unlocked](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/port4567-unlocked.png)

- I got a 404-ERROR as expected because I still need to reset the environment variables.

![Image after running container](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/image-after-running-container.png)


- Stop the container either by `CTRL + C`.  OR click on the container file under CONTAINERS in the docker extension, right click on STOP.
- Set the frontend and backend environment variables locally.
```sh
export FRONTEND_URL="*"
```
```sh
export BACKEND_URL="*"
```

- Run the docker container image with the environment variables.
```sh
docker run --rm -p 4567:4567 -it -e FRONTEND_URL=’*’ -e BACKEND_URL=’*’ backend-flask
```

- I got a 404-ERROR after running the container.


- Then, I added  `/app/activites/home` to the end of the URL in order to access data on server.
- Go into new Bash terminal  and run command `docker ps` to see running containers.
```sh
docker ps
```
- Need to kill container to remove it. Can use `--rm` at end of RUN command to kill container right after is run.


&NewLine;
&NewLine;
&nbsp;

## Containerize Frontend
- Change directory to ‘frontend-react-js/`
```sh
cd frontend-react-js/
```

- Next, I ran an NPM install before building the frontend container because it needs to copy the contents of node modules
```sh
npm i
```

- Change back to the main directory (aws-bootcamp-cruddur-2023)
```sh
cd ..
```

- Now create a docker file at `front-end-react-js/Dockerfile`.
- Click on `frontend-react-js` directory in Gitpod, right click and choose NEW FILE.
- Name the new file `Dockerfile`.
- Copy and paste the following code into the newly created ‘Dockerfile`:
```sh
FROM node:16.18
ENV PORT=3000
COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```

![Front-end react js and Dockerfile](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/backend-dockerfile-image.png)

&NewLine;
&NewLine;
&nbsp;

- Created a `docker-compose.yml` file at root of my project (main).
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

- Run `docker-compose up`
```sh
Docker-compose up
```

- The frontend container(s) is on port 3000
- The backend container(s) is on port 4567
- Make sure both are unlocked.

![Image of ports 3000 and 4567](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/both-ports-open.png)

- Click on the frontend URL and you will see the Cruddur application.
- We now have access to the web application.
- The frontend and backend can communicate with each other.

![Cruddur app homepage](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/cruddur-app.png)

## Adding DynamoDB Local and Postgres

-	DynamoDb Local and Postgres will be used in future labs.
-	I am bringing them in as containers to be referenced externally.
-	I added both DynamoDB Local and Postgre to my `docker-compose.yml` file.
-	First, I added the DynamoDB Local code at the bottom of the docker-compose file just ablove the comment “the name flag is a hack to change the default prepend folder”

  
&NewLine;
&NewLine;
&nbsp;

NOTE:  Exclude `services:` if you are putting the DynamoDB Local code into a pre-existing .yml file that already has `services:` listed in the file code. If not, leave it in.

-	DynamoDB Local:
 
```sh
services:
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
```

-	Next, I added the following Postgres .yml code to my docker-compose file right below the last line of the newly added DynamoDB code.
```sh
db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
```



-	Then, I added the following code to docker-compose to the very end of the file just below “name:cruddur”:
```sh
volumes:
  db:
    driver: local
```
NOTE: 
-	`driver: local` in the db volume is referencing the database volume ` db:/var/lib/postgresql/data` in the Postgres code. I’ll be coyping the database data and storing it on my local machine.
-	You can see from the ` ./docker/dynamodb:/home/dynamodblocal/data` code in DynamoDB Local that `./docker/dynamodb` maps to `/home/dynamodblocal/data`.
&NewLine;
&NewLine;
&nbsp;

![DynamoDB Local and Postgres Code](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/dynamodb-postgres-code-add.png)

&NewLine;
&NewLine;
&nbsp;

-	Now to run the docker-compose.yml file with docker `compose up` to run DynamoDB Local and Postgres to see how they interact with the other services in the file.
-	Right-click on  `docker-compose.yml`
-	Click on `Compose Up` to run the services

![Compose Up](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/compose-up.png)

&NewLine;
&NewLine;
&nbsp;

-	Go to PORTS tab in Gitpod terminal that ran DynamoDB and Postgres and unlock ports 4567, 5432, and 8000 to make them Public and accessible. 
-	DnyamoDB Local is running on port 8000 and Postgres is running on port 5432.
-	Don’t open port 38487 since it has to do with Gitpod and not with DynamoDB and PostGres.

![Correct Unlocked Ports](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/correct-unlocked-ports.png)

-	Open a new terminal and run the command ‘aws` to confirm AWS CLI is still installed.
![AWS CLI Confirmed](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/aws-cli-confirmed.png)


-	Now to check that I can interact with the database using database task code from the challenge “100 Days of Cloud” at  https://github.com/100DaysOfCloud/challenge-dynamodb-local
-	I chose List Tables task with the following code:
```sh
aws dynamodb list-tables --endpoint-url http://localhost:8000
```

![List Tables](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/list-tables.png)

-	Next the Postgres client is installed into Gitpod.
-	Place the following Postgres driver code into `gitpod.yml` right after “cd $THEIA….”  
```sh
  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```

-	Run each of the 4 lines of Postgres code SEPARATELY in the Bash Terminal.
&NewLine;
&NewLine;
&nbsp;

![4 Lines of Postgres code](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/4-lines-postgres-code.png)

-	The code added gpg key to read from a remote repository and installed the Debian package.
-	I tried to connect to client with the command ‘psql’, but I received the error ` connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: No such file or directory`
-	I shut down docker  wtth `Compose Down’ and spun it back up with `Compose Up`
-	Checked the ports to make sure they are open and confirmed they are open to public.
-	Commit changes to `gitpod.yml`, exited Gitpod, then reopened Gitpod to make sure it recognizes added 4 lines of Postgres code.
-	I reloaded `docker-compose.yml` using the command ~docker compose up``
```sh
docker compose up
```

-	Added `PostgreSQL` extension to Gitpod and to the file `gitpod.yml`

![Add PostgreSQL Ext to gitpod.yml](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/add-postgresql-to-gitpodyml.png)

-	Proof PostgresSQL extension added to gitpod.yml file

![PostgreSQL in gitpod.yml](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/postgresql-in-gitpodyml.png)

&NewLine;
&NewLine;
&nbsp;

### Connect to Server Via DATABASE Explorer
-	Before continuing my attempt at connecting to the database server by command line, I will connect by Gitpod `DATABASE`.
-	Click on DATABASE icon on left vertical taskbar.
-	Click `+` or `create connection` to create new connection to database server.
-	I made a successful connection to the database server.
-	The database is empty and will be populated in Week 2.

![success](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/succcess-connection-db.png)

&NewLine;
&NewLine;
&nbsp;

-	Now I continue with trying to connect to the database server via command line.

-	Again, run the 4 lines of gitpod.yml Postgre coded SEPARATELY in Terminal.


```sh
  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```

-	Still unable to connect to the database with command `psql`.
-	Need to provide local host option with “psql’

```sh
psql --host localhost
```

-	I was prompted for password, but the password didn’t work and I received error that “psql: error: connection to server at "localhost" (::1), port 5432 failed: FATAL:  password authentication failed for user "gitpod"”
-	Tried again using the command:
```sh
psql -Upostgres --host localhost
```
-	Now I can access postgres
  
![psqlUpostgress](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/psql-Upostgress.png)

-	Now in Postgres
-	At `postgres=#’ prompt type `\l` to see default tables
-	Made remaining commits to main repository

# Container Security and Why
What is Container Security?
- It is the practice of protecting your applications hosted on compute services like Containers.
- Examples include Single Page Applications (SPAs), Microservices, and APIs.

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

- Containers share the Host's operating system kernel.

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

## Snyk OpenSource Security
-	Added to provide Container Security by finding opensource vulnerabilities in your Dockerfile itself before using it..
-	Snyk OpenSource and Snyk Container Security are available for personal projects.
-	Used to find vulnerabilities in docker files
  
Went to www.CloudSecurityBootcamp.com to connect to Snyk open source libraries for this AWS bootcamp.

![Snyk Login](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/bootcamp-snyk-libraries-login.png)

&NewLine;
&NewLine;
&nbsp;

### Authorize Snyk login with GitHub

- Choose GitHUb, Public Repository, and skip automation settings.
- Click `Authorize snyk`. 

![Authorize Snyk](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/authorize-snyk.png)

&NewLine;
&NewLine;
&nbsp;

- Now, My aws-bootcamp-cruddur-2023 appears as a project in Synk.

![github-successfully-connected-snyk](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/github-successfully-connected-snyk.png)

&NewLine;
&NewLine;
&nbsp;

- There are no images in the my aws-bootcamp-cruddur-2023 yet.
&NewLine;
&NewLine;
&nbsp;  

[Get Snyk-Labs Docker-Goof Repository](https://github.com/snyk-labs/docker-goof)

- Create a new fork that is `SBecraft/docker-goof`
- In Description wrote “Vulnerable Docker Repository for Testing”
- Click `Create fork`
- Now I have a `docker-goof` repository under my GitHub account

![Docker-goof repository in my GitHub](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/docker-goof-repository-suzette.png)

&NewLine;
&NewLine;
&nbsp;

- S/Becraft/docker-goof is now in Snyk app as a repository.
- Click on `S/Becraft/docker-goof` 
- Click on `Import and Scan`
- Snyk reported that my `SBecraft/docker-goof` repository had 289 vulnerabilities across 6 projects.

![Snyk reported vulnerabilities](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/snyk-reported-vulnerabilities.png)

&NewLine;
&NewLine;
&nbsp;

- Click onto the `Docker` project file to see what is causing vulnerabilities in it specifically.
&NewLine;
&NewLine;
&nbsp;

Note:  Snyk is looking at files within the `SBeraft/docker-goof` repository and not image files.


## AWS Secret Manager
- Stores Secrets in EC2 in the AWS Cloud Computing platform.
- Another option is Hashicorp Vault to keep Secrets without having to keep secrets in a dockerfile permanently.
- It is good practice not to keep secrets indefinitely in those docker files.
&NewLine;
&NewLine;
&nbsp;
- I logged into my AWS User account “awsbootcamp”.  This is not my root account.  It is good practice not to use Root for daily tasks.

![AWS Secrets Manager Console](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/aws-secrets-manager-homepage.png)

&NewLine;
&NewLine;
&nbsp;


### Create a secret 
- I clicked on `Store a new secret` on AWS Secrets Manager webpage.
- I chose `Other type of event` for an API
- Next, I chose `Key/value` under Key/Value Pairs and put `Password` and `DockerFile` as key-value pair.
- Kept `aws/secretsmanager` under Encryption Key option. Then, clicked “next".

![Create Secret](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/create-secret-type.png)

- Configure secret with Secret name `docker/password/dev` and Tag as key value pair `Purpose` and `Docker-test`

![Configure Secret](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/configure-secret.png)

&NewLine;
&NewLine;
&nbsp;


### Configure rotation of Secret
- Turn on `Automatic rotation` and click “next” if you like, but lamda will have to be configured.
- This is ok to do because the Secret is not stored in a Docker file.  It is used temporarily for an API call, so it is ok if Secrets Manager rotates Secrets.
- I did not use `Automatic rotation” per the bootcamp instructor so that we could avoid setting up Lamda at this time and move on to AWS Inspector.  I created the Secret without automatic rotation.
- Proof I created a Secret in AWS Secret Manager:

![Saved Secret](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/saved-secret-secretmanager.png)

&NewLine;
&NewLine;
&nbsp;

## AWS Inspector/Clair
- Can use AWS Inspector or Clair to check if a docker image is vulnerable, but it is good to know that Clair runs in the background of AWS Inspector.  
- Clair is open-source Container Image Seccurity Analyzer by CoreOS-
- Amazon Inspector has integration with ECS, EC2 and ECR (registry) to scan Container images.

![Amazon Inspector Console](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/amazon-inspector-console.png)

- Click on `Get Started`
- On Activate Inspector page click on “Activate Inspector` in lower right-hand corner of webpage.

![Inspector Successful Setup](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/inspector-successful-setup.png)


&NewLine;
&NewLine;
&nbsp;

### Image Scan with Snyk
- Can run scan of all containers with Snyk.
- Install Docker desktop if CLI is challenging.
- Refer to the following webpage for instructions on using Docker Compose to scan Containers.

[Docker Compose Overview Documentation](https://docs.docker.com/compose/)

&NewLine;
&NewLine;
&nbsp;

## Value of Managed Container Services 
- Problem with Docker Container or Docker Compose is every time you want to make a change you have to stop the machine. The problem with this you can only build one application.  It cannot build more than one machine from one file.  And if you want to make any security updates you have to shut down the machine, make the update, and turn the machine back on.  This may upset the customer.

- It is better to use Managed Container services like AWS services like AWS ECS, AWS, EKS, AWS Fargate, AWS APP Runner, or AWS CoPilot. 

&NewLine;
&NewLine;
&nbsp;

![Running Containers in AWS](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-1-assets/running-containers-in-aws.png)


&NewLine;
&NewLine;
&nbsp;

## References
- [Docker Compose Overview Documentation](https://docs.docker.com/compose/)
&NewLine;
&NewLine;
&nbsp;

- [Debugging Docker "Connection Refused" Error](https://pythonspeed.com/articles/docker-connection-refused/)
