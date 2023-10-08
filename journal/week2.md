# Week 2 — Distributed Tracing

Table of Contents
- [Week-2 Technical Tasks Overview](#week-2-technical-tasks-overview)
-	[Observability](#observability)
-	[Observability Security](#observability-security)
-	[Honeycomb](#honeycomb)
-	[Add react-js to gitpod.yml](#add-react-js-to-gitpod.yml)
-	[AWS X-Ray](#aws-x-ray)
-	[How To Tag Work](#how-to-tag-work)
-	[How I Resolved the Failed to Compile Error](#how-i-resolved-the-fail-to-compile-error)
-	[References](#references)



## Week-2 Technical Tasks Overview
The fractional CTO has suggested that we implement distributed tracing first because as we begin to add cloud services it will become difficult to pinpoint issues and we want to keep pace with the development timeline.

- Instrument our backend flask application to use Open Telemetry (OTEL) with Honeycomb.io as the provider
- Run queries to explore traces within Honeycomb.io
- Instrument AWS X-Ray into backend flask application
- Configure and provision X-Ray daemon within docker-compose and send data back to X-Ray API
- Observe X-Ray traces within the AWS Console
- Integrate Rollbar for Error Logging
- Trigger an error an observe an error with Rollbar
- Install WatchTower and write a custom logger to send application log data to CloudWatch Log group

## Observability 
Observability is relied upon by organizations to help monitor applications and looks at the whole life cycle of an application.  It works with traditional monitoring to enable better monitoring to make sure an application functions optimally.  Observability includes tools and methods of aggregating a continuous flow of measurable data to analyze how applications, and the networks they run on, perform to troubleshoot and debug any issues that may result in a decline in application performance. 
&NewLine;
&NewLine;
&nbsp;
Observability in AWS is the way you would break down an application into multiple processes and looking at the individual functions.
&NewLine;
&NewLine;
&nbsp;
### Importance of Observability:
-	Visibility of end-to-end logs
-	Decreased alert fatigue for SOC teams
-	Troubleshoot and debug quickly with minimal cost
-	Application Health
-	Minimize operational costs
-	Increase in customer satisfaction when applications function properly
### 3 Pillars of Observability
-	Metrics
-	Traces
-	Logs

### AWS Observability Services
-	AWS Cloudwatch Logs
-	AWS Cloudwatch Metrics
-	AWS X Ray Traces


### Instrumentation
To enable observability in AWS you need instrumentation, which is what helps you create or produce logs, metrics, traces.  In AWS that could be X-Ray Daemon, Cloudwatch Agent or an AWS distro for open-source OpenTelemetry.

### Logs
-	Logging helps determine what might have gone wrong or took place during an incident.
-	Monolithic logging is everything in one place and very prevalent in Enterprise environments.
-	Current state of logging includes on-premises logs and Cloud logs. Both can include log types such as infrastructure, application(s), anti-virus, and firewall.
-	Cloud platforms introduce Infrastructure as Code, Platform as a Service, and Software as a Service which add extra levels of complexity and additional logs.


## Observability Security 
AWS observability tools are not security tools, but you can use logs,  AWS serverless event driven architecture,  or AWS services for threat detection (Amazon GuardDuty) to provide application security.  
&NewLine;
&NewLine;
&nbsp;

### Building Security Metrics and Logs for Tracing
-	Choose an application to analyze for security issues
-	Choose type of application (compute, monolith, microservices)
-	Conduct threat modelling sessions
-	Identify Attack Vectors
-	Map Attack Vectors to TTP in Attack MITRE framework
-	Identify instrumentation agents to create tracing (Cloudwatch, Firelens, 3rd party agent)
-	Use AWS services like ASW Distro for OpenTelementry for metrics and traces
-	View dashboard for practical Attack Vectors only for the application being analyzed. Possible dashboards to use include SIEM, AWS Security Hub with Amazon EventBridge, or open source dashboards.
-	Repeat this process for the next application to be analyzed.

  
### Amazon Detective
[Amazon Detective Documentation](Amazon Detective Documentation)
-	AWS tool to investigate, analyze and identify root cause of potential security issues
-	Adds security to observability
-	Automatically collects log data from AWS resources
-	Assists in conducting faster and more efficient investigations into security issues
-	Provides visuals representing changes in type and volume of activity over time
-	Can access up to one year of historical event data
-	Supported by Amazon GuardDuty, Amazon Inspector, AWS Security Hub, and Amazon Macie.


## Honeycomb

Honeycomb is an observability tool that provides information about code and customer application experience through very quick analysis of data collected about software code performance and behavior.   It is a tool that allows for software debugging and distributive tracing to solve problems with distributive services.  
 
### Create Honeycomb Account
-	Created a free tier Honeycomb account at `https://ui.honeycomb.io`
-	From within my Honeycomb account I clicked on `Environment` > `Manage Environments` > `Create Environment`
-	Named new data set environment `bootcamp’ and made it purple for Cruddur. 
-	Clicked `Create Environment’ to complete data set environment set-up.

 ###  Set Honeycomb API Key Environment Variable
All parts of the Cruddur project should use the same Honeycomb API key.  However, the different parts of the project, or services, should have different names.
-	From within my Honeycomb account I copied the `bootcamp` Honeycomb data set API key.
-	In Gitpod, created a text file as a notepad and pasted API key.
-	Ran the export command to mark `HONEYCOMB_API_KEY` and `HONEYCOMB_SERVICE_NAME` as environment variables. Note: service name is associated with the docker.compose.yml and the backend container.

```sh
export HONEYCOMB_API_KEY=" "
export HONEYCOMB_SERVICE_NAME="Cruddur"
```
-	Made HONEYCOMB_API_KEY` and `HONEYCOMB_SERVICE_NAME` global variables.
```sh
gp env HONEYCOMB_API_KEY="  "
gp env HONEYCOMB_SERVICE_NAME="Cruddur"
```
-	Checked that `HONEYCOMB_API_KEY` and `HONEYCOMB_SERVICE_NAME`  variables are set using the `grep` command.. 
```sh
Env | grep HONEY
```
![both env vars honeycomb](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/both-env-vars-honeycomb.png)

### Add Open Telemetry (OTEL) Env Vars to backend-flask in docker compose
OTEL, or Open Telemetry, is an open-source/open-standard  observability framework for the backend. Adopted by AWS, Azure and Google Cloud platforms.  It is a collection of tools, APIs and SDKs used to manage telemetry data such as traces, metrics and logs to help analyze software performance through distributive tracing.  Frontend OTEL is not as effective in gathering metrics as for the backend. We’re configuring OTEL to send to Honeycomb.  The librairies we’rr installing inside Cruddur app are open-sourec from the Open Telemetry project. The Open Telemetry project is part of the CNCF (Cloud Native Compute Foundation) which also runs Kubernetes, so OTEL is really well-governed open-source code.Honeycom is not in your cloud environment.  Your cloud environment is sending standardized messages out to Honeycomb. Honeycomb stores the messages in its database and gives you a user interface to look at them. But, you could also configure OTEl to send messages to open-source Telemetry backend observability platforms other than Honeycomb like X-Ray.
-	Add the following OTEL commands to the `docker-compose.yml` file under backend-flask right after BACKEND_URL.
```sh
OTEL_SERVICE_NAME: "backend-flask”
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
```
![otel env vars](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/otel-env-vars.png)


### Set Up Honeycomb Instrumentation
-	Went to the Honeycomb website and into my Honeycomb account under `bootcamp` data set environment and clicked on `Home ‘in the left-hand taskbar.
-	Clicked on `Python` to get the Honeycomb OTEL python istall package to put into `requirements.txt` located in the `backend-flask` directory.
-	Copy and paste following into `requirements.txt`
```sh
opentelemetry-api 
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests
```
![python requirements txt](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/python-requirements-txt.png)
-	Change directory to backend-flask
```sh
cd backend-flask
```
-	Install python opentelemetry-api
```sh
pip install opentelemetry-api
```
Proof opentelemetry-api installed successfully:
![otel python install](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/otel-python-install.png)

-	Next, `installed all dependencies` listed in the backend-flask `requirements.txt` file, including ones for sdk, exporter-otlp-proto-http, instrumentation-flask, and instrumentation-requests..
```sh
pip install -r requirements.txt
```
![success requirements dependencies]( https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/success-requirements-dependencies.png)

Refer to https://ui.honeycomb.io

-	Add app.py updates to the `app.py` file located in backend-flask directory.
```sh
# Honeycomb
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
```
![add to app py](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/add-to-app-py.png)

####	Initialize tracing and an exporter that can send data to Honeycomb
- Add the following code to the `app.py` file below the # Honeycomb - from opentelemetry - code block.
```sh
#Honeycomb
# Initialize tracing and an exporter that can send data to Honeycomb
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)
```
![initialize tracing](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/intialize%20tracing.png)

####	Intialize automatic instrumentation with Flask
- Add the following code to the `app.py` file below the “initialize tracing….” code block.
```sh
#Honeycomb
# Initialize automatic instrumentation with Flask
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```
NOTE:  I excluded the line ` app = Flask(__name__)` listed in the Honeycomb/python instructions because I already had that line of code in my app.py file.  If listed twice, ` app = Flask(__name__)` would throw an error.

### Run Distributive Tracing with Honeycomb
-	Go back to main directory `aws-bootcamp-cruddur-2023`. Then change directory to `frontend-react-js/`
```sh
cd ..
cd frontend-react-js/
```

-	Run an NPM install.
```sh
npm i
```
NOTE:  There is a line of code in the `Dockerfile` that runs an npm install, so it would seem unnecessary to have to run npm i again. But, in the aws bootcamp we used the same Dockerfile for development and production, which requires running `npm i` each time we open the container.  
Industry standard is to separate development containers from production containers. The containers for development and production come from different images.  Production containers are based on a much more slimmed-down image than that used for development which includes many tools not needed for production.

-	Changed into main directory with `cd ..`
-	Run container by right-clicking on `docker-compose.yml`and then clicking on `Compose Up`
-	Go to PORTS Tab to see ports and make sure that port 3000 and port 4567 are open.  If not, unlock them in order to access frontend Cruddur URL and backend app data.
-	I received a failed to compile error and that React must be in scope when using JSX. As a result, I could not access the frontend URL or the backend data.  And, the PORTs tab did not display port information.
-	
  ![Failed to Compile](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/failed-to-compile.png)

&NewLine;
&NewLine;
&nbsp;

- Resolved the failed to compile error with Node.js version downgrade. See `How I Resolved the Failed to Compile Error`
-	Changed into main directory with `cd ..`
-	Run container by right-clicking on ‘docker.-compose.yml`and then clicking on `Compose Up`
- Docker Containers created:
- 
![docker containers created](  )

![docker containers](  )

### SUCCESSFUL RUN OF DISTRIBUTIVE TRACING:
-	Went back to main directory `aws-bootcamp-cruddur-2023` to make another attempt to connect to frontend and backend servers. C
-	Changed directory to `frontend-react-js/`
```sh
cd ..
cd frontend-react-js/
```

-	Run an NPM install to install javascript dependencies that will be listed in a file called `package.json`.
```sh
npm i
```
-	Changed into main directory with `cd ..`
-	Run container by right-clicking on ‘docker.-compose.yml`and then clicking on `Compose Up`
-	The frontend container(s) is on port 3000
-	The backend container(s) is on port 4567
-	Make sure both are unlocked.

![Open ports](  )

-	I got a 404-ERROR after running the backend container.
-	Then, I added /api/activities/home to the end of the URL for the backend server in order to access cruddur data on server on port 4567.

![backend data](  )

-	Here is proof I connected to the frontend server on port 3000 to access the Cruddur web application.

![cruddur web app week 2](  )

#### Honecomb Query in Backend-Flask
-	Now that I’ve run Docker Compose-Up and can connect to the backend-flask server, there should now be a dataset in the Honecomb bootcamp query results.
![honeycomb data set](  )
![honeycomb data set-2](  )
![honeycomb data set-3](  )

Note: Can check that you have the correct Honeycomb api key with the command
```sh
E | grenv | grep Honey
```

Honeycomb-whoami.glitch.me   :  how to find out who  a Honeycomb api belongs to
![honeycomb whoami](  )

### Create Span Around Hard-Coded Data With a Tracer
-	Go to  [Honeycomb/OpenTelemetry for Python Documentation}(https://docs.honeycomb.io/getting-data-in/opentelemetry/python-distro/) to get code to create a tracer.  
OpenTelemetry requires that a Tracer is given a name as a string.  Honeycomb takes the name of a tracer associated with a data trace and turns it into  the `library.name` field. 
Honecomb.io recommends “pick a name that matches the appropriate scope for your traces. If you have one tracer for each service, then use the service name. If you have multiple tracers that live in different “layers” of your application, then use the name that corresponds to that “layer”.
-	Add the following code to the `home_activities-py` file that is found in the `backend-flask/services/ ` directory.
```sh
from opentelemetry import trace

tracer = trace.get_tracer("tracer.name.here")
```
![add tracer](   )
-	Replaced “tracer.name.here”  with `home.activities`
-	Add the following code to `home.activities.py` right under `def run():`
```sh
with tracer.start_as_current_span("home-activities-mock-data"):
```

-Run docker `Compose Down` and then `Compose Up` to  open new frontend and backend containers.
- Go to the frontend and backend URLS
- Go to Honeycomb.io to see the most recent traces and any spans.  More than one color in a trace means that we successfully created a span.

![trace with two colors](  )
![span showing mock data](  )
#### Add Attributes to Spans
-	Add the following attribute code to` home.activities.py` 
```sh
span = trace.get_current_span()
```
```sh
span.set_attribute(“app.now”, now.isoformat())
```
![span attribute 1](  )

-	Add the following attribute code to line above ~return results~
```sh
span.set_attribute(“app.result_length”, len(results))
``` 
-	Refresh Cruddur backend and frontend URLs to create new data set in Honeycomb.
-	In Honeycomb.io  run a new query with visualize as `count` and group by as `trace.trace_id`.  Here are some results:
![app now]( )

## Update Gitpod.yml with react-js
-	Make it so we don’t have do a ` npm i` install every time we boot up a container.
-	Add the following code to `gitpod.yml`:
```sh
- name: react-js
  command: |
       cd frontend-react-js
       npm i
```


## AWS X-Ray
AWS’s built in distributive tracing and observability tool.
In order for X-Ray to work it needs an X-Ray daemon, another container/application, that runs alongside your application that your application sends data to and the x-ray daemon collects, batches it, and sends it over to the X-Ray API so that you can visualize data in x-Ray.
![xray](  )

### Install AWS SDK
[AWS SDK for Python Documentation](   )
-	Add ` aws-xray-sdk` to the `requirements.txt`
```sh
aws-xray-sdk
```
![ aws-xray-sdk](    )

-	Install Python dependencies by CLI in the backend-flask directory
```sh
cd backend-flask/
```
```sh
pip install -r requirements.txt
```
-	Add code to the `app.py` file to add middleware to flask
-	Place the following code into ~app.py~ right below Honeycomb OpenTelemetry code block:
```sh
from aws_xray_sdk.core import xray_recorder
from aws_xray_sdk.ext.flask.middleware import XRayMiddleware
```
-	Place the following code into `app.py` right below ~#Honeycomb Intiialize tracing and an exporter that can send date to Honeycomb`
```sh
xray_url = os.getenv("AWS_XRAY_URL")
xray_recorder.configure(service='backend-flask', dynamic_naming=xray_url)
XRayMiddleware(app, xray_recorder)
```
![add xray](  )
-	Next, setup AWS X-Ray Resources by adding the file `aws/json/xray.json’ with the following code:
```sh
{
  "SamplingRule": {
      "RuleName": "Cruddur",
      "ResourceARN": "*",
      "Priority": 9000,
      "FixedRate": 0.1,
      "ReservoirSize": 5,
      "ServiceName": "backend-flask",
      "ServiceType": "*",
      "Host": "*",
      "HTTPMethod": "*",
      "URLPath": "*",
      "Version": 1
  }
}
```
![xray json](   )
-	While still in backend-flask directory, create X-Ray group called `Cruddur` with the following code in the AWS CLI:
```sh
aws xray create-group \
```
```sh
--group-name "Cruddur" \
```
```sh
--filter-expression "service(\“backend-flask\")”
```

![create xray group](  )

-	View new `backend-flask` group in AWS X-Ray Console:

-	Create sampling rule, which determines what information you what to see.  Collecting every data point would be too expensive due to the amount of data. Put the following code in a new bash terminal in the main directory.
```sh
aws xray create-sampling-rule --cli-input-json file://aws/json/xray.json
```
![sampling rule]( )
-	View new `backend-flask` group and sampling rule in AWS X-Ray Console at:
`https://us-east-1.console.aws.amazon.com/cloudwatch/home?region=us-east-1#xray:settings`

![xray group in console]( )
-	Add X-RAY daemon service to `docker-compose.yml`
```sh
  xray-daemon:
    image: "amazon/aws-xray-daemon"
    environment:
      AWS_ACCESS_KEY_ID: "${AWS_ACCESS_KEY_ID}"
      AWS_SECRET_ACCESS_KEY: "${AWS_SECRET_ACCESS_KEY}"
      AWS_REGION: "us-east-1"
    command:
      - "xray -o -b xray-daemon:2000"
    ports:
      - 2000:2000/udp
```
-	Add two environment variables to the backend-flask in the `docker-compose.yml`
```sh
      AWS_XRAY_URL: "*4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}*"
      AWS_XRAY_DAEMON_ADDRESS: "xray-daemon:2000"
```
![daemon and env vars](  )
#### Determine if X-RAY Daemon sent data to AWS X-RAY
-	After running the `amazon/aws-xray-daemon` logs I could see that the X-RAY Daemon successfully sent a batch data to AWS X-Ray
![success xray daemon](  )
-	After refreshing the Cruddur backend URL on port 4567 there were successful data traces with the X-Ray Daemon.
-	Proof our X-Ray Daemon Service can collect data from the backend and send data traces to AWS X-RAY.  

![aws xray query success]( )















## How to Tag Work 
```sh
git tag week-2
git push --tags
```

 ## How I Resolved the Failed to Compile Error

- I had to resolve issues with (1) not seeing ports in PORTS tab and (2) no service available on frontend port 3000, after running docker Compose Up.
- When trying to access front end port 3000 to see Cruddur URL I received the message "Failed to Compile" and "React must be in scope when using JSX   react/react-in-jsx-scope"
- Ruled out any breakpoints in the "scripts" part of my package.json file.
- Ran debugging of package.json file. Received error about MD4 hashing algoritm incompatibility with Node version 18.
- Researched this issue and found that this error occurs when using Node.js version 17 or higher, which does not support the MD4 hash algorithm any longer.

  
- RECOMMENDATION: downgrade your current Node.js version to 16 or lower, which is the current, long-term version, using the Node Version Manager (nvm). 

&NewLine;
&NewLine;
&nbsp;

### SOLUTION: 

- While in frontend-react-js directory, make sure you have nvm manager installed already in Gitpod VSCode (Node Version Manager):
```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

- While in frontend-react-js directory, display your current/default node version:
```sh
node -v
```

- While in frontend-react-js directory, find what node versions are available:
```sh
nvm list
```

- While in frontend-react-js directory, if you currently have version 18, try using version 16 to see if it resolves issues with not seeing services on frontend port 3000:
```sh
nvm use 16.20.2
```

- From the (main) directory:
```sh
Run docker-compose.yml --> Compose Up 
```

- If port information is now visible in the PORTS tab of the Bash Terminal then install node version 16.20.2 while in frontend-react-js directory:
```sh
nvm install 16.20.2 
```

- While in frontend-react-js directory, make node version 16.20.2 the DEFAULT version for your Gitpod VSCode:
```sh
nvm alias default 16.20.2
```
![Screenshot updated node](  )










## References
- [Honeycomb.io Website](https://ui.honeycomb.io)
- [Honeycomb Documentation for Python](https://docs.honeycomb.io/getting-data-in/opentelemetry/python-distro/)

