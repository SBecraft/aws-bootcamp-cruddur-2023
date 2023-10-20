# Week 2 — Distributed Tracing

Table of Contents
- [Week-2 Technical Tasks Overview](#week-2-technical-tasks-overview)
-	[Observability](#observability)
-	[Observability Security Considerations](#observability-security-considerations)
-	[Honeycomb](#honeycomb)
-	[Update Gitpod YML File With React-JS Auto NMP Install](#update-gitpod-yml-file-with-react-js-auto-nmp-install)
-	[AWS X-Ray](#aws-x-ray)
-	[Instrument AWS X-Ray Subsegments](#instrument-aws-x-ray-subsegments)
-	[AWS CloudWatch Logs](#aws-cloudwatch-logs)
-	[Integrate Rollbar and Capture and Error](#integrate-rollbar-and-capture-and-error)
-	[How To Tag Work](#how-to-tag-work)
-	[How I Resolved Frontend Failed to Compile Error](#how-i-resolved-frontend-failed-to-compile-error)
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
&NewLine;
&NewLine;
&nbsp;

## Observability 
Observability is relied upon by organizations to help monitor applications and looks at the whole life cycle of an application.  It works with traditional monitoring to enable better monitoring to make sure an application functions optimally.  Observability includes tools and methods of aggregating a continuous flow of measurable data to analyze how applications, and the networks they run on, perform to troubleshoot and debug any issues that may result in a decline in application performance. 
&NewLine;
&NewLine;
&nbsp;

Observability in AWS is the way you would break down an application into multiple processes and look at their individual functions.
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
&NewLine;
&NewLine;
&nbsp;

## Observability Security Considerations
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

[Amazon Detective Documentation](https://docs.aws.amazon.com/detective/)

-	AWS tool to investigate, analyze and identify root cause of potential security issues
-	Adds security to observability
-	Automatically collects log data from AWS resources
-	Assists in conducting faster and more efficient investigations into security issues
-	Provides visuals representing changes in type and volume of activity over time
-	Can access up to one year of historical event data
-	Supported by Amazon GuardDuty, Amazon Inspector, AWS Security Hub, and Amazon Macie.

&NewLine;
&NewLine;
&nbsp;
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

![both env vars honeycomb](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/both-env-vars-honeycomb-new.png)

&NewLine;
&NewLine;
&nbsp;
### Add Open Telemetry (OTEL) Env Vars to backend-flask in docker compose
OTEL, or Open Telemetry, is an open-source/open-standard  observability framework for the backend. Adopted by AWS, Azure and Google Cloud platforms.  It is a collection of tools, APIs and SDKs used to manage telemetry data such as traces, metrics and logs to help analyze software performance through distributive tracing.  Frontend OTEL is not as effective in gathering metrics as for the backend. We’re configuring OTEL to send to Honeycomb.  The librairies we’rr installing inside Cruddur app are open-sourec from the Open Telemetry project. The Open Telemetry project is part of the CNCF (Cloud Native Compute Foundation) which also runs Kubernetes, so OTEL is really well-governed open-source code.Honeycom is not in your cloud environment.  Your cloud environment is sending standardized messages out to Honeycomb. Honeycomb stores the messages in its database and gives you a user interface to look at them. But, you could also configure OTEl to send messages to open-source Telemetry backend observability platforms other than Honeycomb like X-Ray.
&NewLine;
&NewLine;
&nbsp;

-	Add the following OTEL commands to the `docker-compose.yml` file under backend-flask right after BACKEND_URL.
```sh
OTEL_SERVICE_NAME: "backend-flask”
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
```

![otel env vars](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/otel-env-vars.png)

&NewLine;
&NewLine;
&nbsp;
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

![success requirements dependencies](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/success-requirements-dependencies.png)

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

&NewLine;
&NewLine;
&nbsp;
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
&NewLine;
&NewLine;
&nbsp;

NOTE:  There is a line of code in the `Dockerfile` that runs an npm install, so it would seem unnecessary to have to run npm i again. But, in the aws bootcamp we used the same Dockerfile for development and production, which requires running `npm i` each time we open the container.  
Industry standard is to separate development containers from production containers. The containers for development and production come from different images.  Production containers are based on a much more slimmed-down image than that used for development which includes many tools not needed for production.
&NewLine;
&NewLine;
&nbsp;

-	Changed into main directory with `cd ..`
-	Run container by right-clicking on `docker-compose.yml` and then clicking on `Compose Up`
-	Go to PORTS Tab to see ports and make sure that port 3000 and port 4567 are open.  If not, unlock them in order to access frontend Cruddur URL and backend app data.
-	I received a failed to compile error and that React must be in scope when using JSX. As a result, I could not access the frontend URL or the backend data.  And, the PORTs tab did not display port information.
-	The browser tab shows `Cruddur` but I can not see the contents of the webpage.
-	Stopped and removed any and all docker containers that may be open in background competing for open ports  with the CLI commands:
```sh
docker stop $(docker ps -a -q)
```
```sh
docker rm $(docker ps -a -q)
```
  
![Failed to Compile](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/failed-to-compile.png)

&NewLine;
&NewLine;
&nbsp;

- Resolved the failed to compile error with Node.js version downgrade. See `How I Resolved the Failed to Compile Error`
-	Changed into main directory with `cd ..`
-	Run container by right-clicking on ‘docker.-compose.yml`and then clicking on `Compose Up`
- Docker Containers created:
  
![docker containers created](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/docker-containers-created.png)

&NewLine;
&NewLine;
&nbsp;

![docker containers](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/docker-containers.png)


### Set Open Ports for Frontend and Backend
Updated `gitpod.yml` file with code to set frontend and backend port numbers to unlock/become public when Containers are run, so that it doesn’t have to be done manually.
-	Place the following code in `gitpod.yml` below vscode extension block.
```sh
ports:
  - name: frontend
    port: 3000
    onOpen: open-browser
    visibility: public
  - name: backend
    port: 4567
    visibility: public
  - name: xray-daemon
    port: 2000
    visibility: public
```

![ports set](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/ports-set.png)

### SUCCESSFUL RUN OF DISTRIBUTIVE TRACING:
-	Stopped and removed any any and all docker containers that may be open in background competing for open ports using the following the CLI commands:
```sh
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```
- Confirmed backgroud containers were removed by running each of the following commands in CLI to check container running processes:
```sh
docker ps
```
  
```sh
docker container ls -a
```
```sh
docker logs <container id or name>
```

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

![Open ports](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/open-ports.png)

-	I got a 404-ERROR after running the backend container.
-	Then, I added /api/activities/home to the end of the URL for the backend server in order to access cruddur data on server on port 4567.

![backend data](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/backend-data.png)

-	Here is proof I connected to the frontend server on port 3000 to access the Cruddur web application.

![cruddur web app week 2](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/cruddur-webapp-week-2.png)

#### Honecomb Query in Backend-Flask
-	Now that I’ve run Docker Compose-Up and can connect to the backend-flask server, there should now be a dataset in the Honecomb bootcamp query results.
  
![honeycomb data set](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/honeycomb-dataset.png)

&NewLine;
&NewLine;
&nbsp;

![honeycomb data set-2](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/honeycomb-dataset-2.png)

&NewLine;
&NewLine;
&nbsp;

![honeycomb data set-3](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/honeycomb-dataset-3.png)

&NewLine;
&NewLine;
&nbsp;

NOTE: Check that you have the correct Honeycomb api key with the command:
```sh
E | grenv | grep Honey
```
&NewLine;
&NewLine;
&nbsp;

#### Honeycomb-whoami.glitch.me   :  how to find out who  a Honeycomb api belongs to

![honeycomb whoami](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/honeycomb-whoami.png)

### Create Span Around Hard-Coded Data With a Tracer
- Go to  [Honeycomb/OpenTelemetry for Python Documentation}(https://docs.honeycomb.io/getting-data-in/opentelemetry/python-distro/) to get code to create a tracer.  
OpenTelemetry requires that a Tracer is given a name as a string.  Honeycomb takes the name of a tracer associated with a data trace and turns it into  the `library.name` field. 
Honecomb.io recommends “pick a name that matches the appropriate scope for your traces. If you have one tracer for each service, then use the service name. If you have multiple tracers that live in different “layers” of your application, then use the name that corresponds to that “layer”.
-	Add the following code to the `home_activities-py` file that is found in the `backend-flask/services/ ` directory.
```sh
from opentelemetry import trace

tracer = trace.get_tracer("tracer.name.here")
```

![add tracer](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/add-tracer.png)

-	Replaced “tracer.name.here”  with `home.activities`
-	Add the following code to `home.activities.py` right under `def run():`
```sh
with tracer.start_as_current_span("home-activities-mock-data"):
```

-Run docker `Compose Down` and then `Compose Up` to  open new frontend and backend containers.
- Go to the frontend and backend URLS
- Go to Honeycomb.io to see the most recent traces and any spans.  More than one color in a trace means that we successfully created a span.

![trace with two colors](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/trace-with-two-colors.png)

&NewLine;
&NewLine;
&nbsp;

![span showing mock data](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/span-showing-mock-data.png)

#### Add Attributes to Spans
- Add the following attribute code to` home.activities.py` 
```sh
span = trace.get_current_span()
```
```sh
span.set_attribute(“app.now”, now.isoformat())
```

![span attribute 1](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/span-attribute-1.png)

-	Add the following attribute code to line above ~return results~
```sh
span.set_attribute(“app.result_length”, len(results))
``` 
-	Refresh Cruddur backend and frontend URLs to create new data set in Honeycomb.
-	In Honeycomb.io  run a new query with visualize as `count` and group by as `trace.trace_id`.  Here are some results:
	
![app now](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/attribute-app-now.png)

## Update Gitpod.yml With React-JS Auto NMP Install
-	Make it so we don’t have do a ` npm i` install every time we boot up a container.
-	Add the following code to `gitpod.yml`:
```sh
- name: react-js
  command: |
       cd frontend-react-js
       npm i
```
&NewLine;
&NewLine;
&nbsp;

## AWS X-Ray
AWS’s built in distributive tracing and observability tool.
In order for X-Ray to work it needs an X-Ray daemon, another container/application, that runs alongside your application that your application sends data to and the x-ray daemon collects, batches it, and sends it over to the X-Ray API so that you can visualize data in x-Ray.

![xray](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/X-Ray.png)

### Install AWS SDK

[AWS SDK for Python Documentation](https://docs.aws.amazon.com/pythonsdk/)

-	Add ` aws-xray-sdk` to the `requirements.txt`
```sh
aws-xray-sdk
```

![aws-xray-sdk](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/aws-xray-sdk.png)

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

![add xray](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/add-xray.png)

- Next, setup AWS X-Ray Resources by adding the file `aws/json/xray.json’ with the following code:
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

![xray json](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/xray-json.png)

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

![create xray group](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/create-xray-group.png)

-	View new `backend-flask` group in AWS X-Ray Console:

-	Create sampling rule, which determines what information you what to see.  Collecting every data point would be too expensive due to the amount of data. Put the following code in a new bash terminal in the main directory.
```sh
aws xray create-sampling-rule --cli-input-json file://aws/json/xray.json
```

![sampling rule](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/sampling-rule-new.png)

-	View new `backend-flask` group and sampling rule in AWS X-Ray Console at:
`https://us-east-1.console.aws.amazon.com/cloudwatch/home?region=us-east-1#xray:settings`

![xray group in console](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/xray-group-console.png)

### Add X-RAY daemon service to `docker-compose.yml`

[X-ray Daemon Documentation](https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon.html)

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

![daemon and env vars](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/daemon-env-vars.png)


#### Determine if X-RAY Daemon sent data to AWS X-RAY
-	After running the `amazon/aws-xray-daemon` logs I could see that the X-RAY Daemon successfully sent a batch data to AWS X-Ray

![success xray daemon](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/success-xray-daemon-new.png)

-	After refreshing the Cruddur backend URL on port 4567 there were successful data traces with the X-Ray Daemon.
-	Proof our X-Ray Daemon Service can collect data from the backend and send data traces to AWS X-RAY.  

![aws xray query success](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/aws-xray-query-success-new.png)

### Instrument AWS X-Ray Subsegments
Implemented an X-Ray subsegment for the service called `user activities` so when a Cruddur app user hits a specific API route, that data will be sent to AWS X-Ray to be queried and analyzed.
-	To instrument AWS X-Ray subsegments I needed to import the X-Ray recorder.  To do so I added the following code to the `user_activities.py` file that is located at `/backend-flask/services/user_activities.py`
```sh
from aws_xray_sdk.core import xray_recorder
```
-	Added `try:` below line `def run(user_handle):`
-	Then, I added code for the x-ray recorder to begin subsegment
```sh
subsegment = xray_recorder.begin_subsegment('mock-data')
```
-	Defined a dictionary for results and added defined subsegment metadata.
```sh
# xray ---
      dict = {
        "now": now.isoformat(),
        "results-size": len(model['data'])
      }
      subsegment.put_metadata('key', dict, 'namespace')
```
-	Finally, closed the subsegment
```sh
finally:  
    #  # Close the segment
      xray_recorder.end_subsegment()
```

![subsegment code](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/subsegment-code.png)

-	Add X-Ray recorder capture code to @app.route(s)  as follows:
```py
@app.route("/api/activities/home", methods=['GET'])
@xray_recorder.capture('activities_home')
def data_home():
  data = HomeActivities.run()
  return data, 200
```
&NewLine;
&NewLine;
&nbsp;

```py
@app.route("/api/activities/@<string:handle>", methods=['GET'])
@xray_recorder.capture('activities_users')
def data_handle(handle):
  model = UserActivities.run(handle)
  if model['errors'] is not None:
    return model['errors'], 422
  else:
    return model['data'], 200
```
&NewLine;
&NewLine;
&nbsp;

```py
@app.route("/api/activities/<string:activity_uuid>", methods=['GET'])
@xray_recorder.capture('activities_show')
def data_show_activity(activity_uuid):
  data = ShowActivity.run(activity_uuid=activity_uuid)
  return data, 200
```

&NewLine;
&NewLine;
&nbsp;

-	Right-clicked on `docker-compose.yml`.  Chose Docker `Compose Up` to run containers for the AWS Daemon, backend flask and frontend react.
-	Went to frontend URL for Cruddur app and refreshed page multiple times, as well as clicked on different parts of the webpage for generating data for the AWS Daemon to collect and send to AWS X-Ray.
-	Went into my awsbootcamp account in the AWS console and searched `X-Ray` to navigate to my trace information.
-	Here is  the service map showing a connection between the client and my cruddur app.

![service map](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/service-map-new.png)

&NewLine;
&NewLine;
&nbsp;
&NewLine;
&NewLine;
&nbsp;
- Cloudwatch log queries:
  
![service map 2](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/service-map-2-new.png)

&NewLine;
&NewLine;
&nbsp;
&NewLine;
&NewLine;
&nbsp;

- Query for `activities_home`:

![service map 3](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/service-map-3.png)

&NewLine;
&NewLine;
&nbsp;
&NewLine;
&NewLine;
&nbsp;

-  Query for `activities_users`:
  
![service map 4](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/service-map-4.png)

&NewLine;
&NewLine;
&nbsp;
&NewLine;
&NewLine;
&nbsp;


NOTE:  X-Ray spend adds up in cost in Cloudwatch when in production.  I’m using it in development so cost should be minimal.
&NewLine;
&NewLine;
&nbsp;

## AWS CloudWatch Logs
[Python WatchTower Documentation]( https://pypi.org/project/watchtower/)
-	Added WatchTower to `requirements.txt
```
watchtower
```

![watchtower](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/watchtower.png)

-	Change into the `backend-flask` directory in the aws-cli bash terminal.
-	Ran the python pip install in the CLI to install WatchTower.
```sh
pip install -r requirements.txt
```
-	Next, I configured logger to use CloudWatch.
-	To do this, add the imports for WatchTower and logger to the `app.py` file.
```sh
import watchtower
import logging
from time import strftime
```
-	Next, add code to the  `app.py` file that will configure logger to use CloudWatch with log_group=’cruddur’
```sh
# Configuring Logger to Use CloudWatch
LOGGER = logging.getLogger(__name__)
LOGGER.setLevel(logging.DEBUG)
console_handler = logging.StreamHandler()
cw_handler = watchtower.CloudWatchLogHandler(log_group='cruddur')
LOGGER.addHandler(console_handler)
LOGGER.addHandler(cw_handler)
LOGGER.info("some message")
```
-	Add to `app.py` forced logging of errors.
```sh
@app.after_request
def after_request(response):
    timestamp = strftime('[%Y-%b-%d %H:%M]')
    LOGGER.error('%s %s %s %s %s %s', timestamp, request.remote_addr, request.method, request.scheme, request.full_path, response.status)
    return response
```
-	Set the env var in your backend-flask for `docker-compose.yml`
```yml
      AWS_DEFAULT_REGION: "${AWS_DEFAULT_REGION}"
      AWS_ACCESS_KEY_ID: "${AWS_ACCESS_KEY_ID}"
      AWS_SECRET_ACCESS_KEY: "${AWS_SECRET_ACCESS_KEY}"
```
-	Add logger.info(“HomeActivities”) to `home_activities.py~
-	In `app.py` replace “some message” in `LOGGER.info(“some message”)` with “test log”
```sh
Logger.info(“test log”)
```
-	In `home_activities.py` add `logger` to `def run():`
```sh
Def run(logger):
```
-	In `app.py`  go to `@app.route(“/api/activities/home”, methods=[‘GET’])` and add `logger=LOGGER` to the data variable
  
![add loggerto app.route](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/add-logger-to-app-route.png)

-	Run docker `Compose Up`
-	Go to backend URL on port 4567 to see cruddur data.  Need to put `/api/activities/home’ on end of URL to see the data.
-	Refresh backend Cruddur URL multiple times/
-	Go to AWS console to CloudWatch
-	Click on `Log Groups` in CloudWatch Logs.
-	Any kind of logging, including those from instrumentation like X-Ray should show in CloudWatch Logs.
	
![cloudwatch log groups](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/cloudwatch-log-group.png)

&NewLine;
&NewLine;
&nbsp;

![cloudwatch log stream](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/cloudwatch-log-stream.png)

-	Comment out or remove cloudwatch logging code and logger arguments added to `app.py` and `home_activites.py` to turn off CloudWatch to save on spend.
-	Comment out AWS X-Ray  related code in both `app.py` and `user_activities` files to save on spend. 
&NewLine;
&NewLine;
&nbsp;

## Integrate Rollbar and Capture and Error
[Create a Rollbar Account](https://app.rollbar.com/)

Rollbar is an online tool that collects errors in real-time that happen in an application.  It sends notifications about errors and analyzes the errors so they can be debugged and fixed.

&NewLine;
&NewLine;
&nbsp;

-	Create a new project in Rollbar called `Cruddur`
-	Add the Flask SDK to the new project
-	Add to  the bottom of `requirements.txt`
```
blinker
rollbar
```

-	Install blinker and rollbar dependencies in the `backend-flask` directory
```sh
pip install -r requirements.txt
```

-	Set my Rollbar access token.  Access token credential can be found in the Rollbar Cruddur project Set up SDK webpage. One at a time, run the following commands in backend-flask directory in the aws-cli bash terminal.

```sh
export ROLLBAR_ACCESS_TOKEN="your access token here"
```

```sh
gp env ROLLBAR_ACCESS_TOKEN="your access token here"
```

-	Confirm that your Rollbar access token has been set.
```sh
env | grep ROLLBAR
```
  
![env grep rollbar](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/env-grep-rollbar-new.png)

-	Now to instrument rollbar.  Add the following code to `app.py` file
```py
Import os
import rollbar
import rollbar.contrib.flask
from flask import got_request_exception
```

![rollbar imports](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/rollbar-imports.png)

-	Now to initialize Rollbar.  Put following init code in `app.py` below “Honeycomb-----Initialize tracing and an exporter that can send data to Honeycomb”
```py
rollbar_access_token = os.getenv('ROLLBAR_ACCESS_TOKEN')
@app.before_first_request
def init_rollbar():
    """init rollbar module"""
    rollbar.init(
        # access token
        rollbar_access_token,
        # environment name
        'production',
        # server root directory, makes tracebacks prettier
        root=os.path.dirname(os.path.realpath(__file__)),
        # flask already sets up logging
        allow_logging_basic_config=False)

    # send exceptions from `app` to rollbar, using flask's signal system.
    got_request_exception.connect(rollbar.contrib.flask.report_exception, app)
```

![rollbar init](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/rollbar-init.png)

-	Add an endpoint just for testing rollbar to `app.py`
```py
@app.route('/rollbar/test')
def rollbar_test():
    rollbar.report_message('Hello World!', 'warning')
    return "Hello World!"
```

![rollbar endpoint](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/rollbar-endpoint.png)

### Rollbar Cruddur Project Instrumentation Results
-	Docker Compose Up to run backend Container.
-	Went to Cruddur Backend URL and added /api/activites/home
-	Received ~Workspace Not Found` Error
-	Ran log files for backend-flask container and see there is a error for ‘app’ name not defined.
-	Went back to app.py file and see that I need to place the rollbar -init- code block further down in the file below `app = Flask(_name_)`.
-	Ran Docker Compose Up and still could not reach backend URL for Cruddur
-	See a note in the bottom of the Week-2 Distributive Tracing markdown file that there have been changes to Rollbar in the last few months.  There is a newer version of flask that resulted in rollback implementation breaking due to a change in the flask api.
-	I `commented out` the this code in the `app.py` file:
```py
rollbar_access_token = os.getenv('ROLLBAR_ACCESS_TOKEN')
@app.before_first_request
def init_rollbar():
    """init rollbar module"""
    rollbar.init(
        # access token
        rollbar_access_token,
        # environment name
        'production',
        # server root directory, makes tracebacks prettier
        root=os.path.dirname(os.path.realpath(__file__)),
        # flask already sets up logging
        allow_logging_basic_config=False)

    # send exceptions from `app` to rollbar, using flask's signal system.
    got_request_exception.connect(rollbar.contrib.flask.report_exception, app)
```
-	Then added the changed Rollbar code in `app.py` as follows:
```py
# Hack to make request data work with pyrollbar <= 0.16.3
def _get_flask_request():
    print("Getting flask request")
    from flask import request
    print("request:", request)
    return request
rollbar._get_flask_request = _get_flask_request

def _build_request_data(request):
    return rollbar._build_werkzeug_request_data(request)
rollbar._build_request_data = _build_request_data
# End hack

def init_rollbar(app):
  rollbar_access_token = os.getenv('ROLLBAR_ACCESS_TOKEN')
  flask_env = os.getenv('FLASK_ENV')
  rollbar.init(
      # access token
      rollbar_access_token,
      # environment name
      flask_env,
      # server root directory, makes tracebacks prettier
      root=os.path.dirname(os.path.realpath(__file__)),
      # flask already sets up logging
      allow_logging_basic_config=False)
  # send exceptions from `app` to rollbar, using flask's signal system.
  got_request_exception.connect(rollbar.contrib.flask.report_exception, app)
  return rollbar
```

-	Ran Docker Compose Up again and this time I could reach the backend Cruddur URL and see data.
-	Then, I tested for our new endpoint by adding `/rollbar/test` to the end of the backend URL and we get the expected message `Hello World!`
  
![hello world](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/hello-world.png)

-	Check Rollbar website to see if the tool was getting any activity, but is only showing that it is listening for something.
-	Went back to `docker-compose.yml` file to add Rollbar access token as an environment variable.
  
![docker compose rollbar env var](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/docker-compose-Rollbar-env-vars.png)

-	Ran docker Compose Up again to launch the Cruddur app and browse the backend endpoint.  On the Rollbar website, should be able to click `Items` for `FirstProject` and select all the critical levels to see "Hello World!" message and data around this trace.. However, the Rollbar website wasn’t functioning as expected.  Clicking on Items just displayed the Welcome to Rollbar page and there is no access to the filters from the left hand taskbar. Looks as if my data did not reach Rollbar.

![rollbar not working](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/rollbar-not-working.png)

- Found a solution in the AWS Bootcamp Discord Channel.
- Commented out `@app.before_first_request` in the Rollbar block of code in  `app.py` file and replaced it with `with app.app_context():`
- And then indented the entire function codeblock `def_init_rollbar():`

![change rollbar code](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/change-rollbar-code.png)
  
- Ran Docker Compose Up and can now reach backend URL at `/rollbar/test/` to get message `Hello World!`
- Got `Items` tab on Rollbar site  to show filters and Cruddur project name under Projects, but no data to display.
  
![rollbar don’t work](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/rollbar-dont-work.png)


-	Then, tried attaching the shell to the backend container and then run env | grep ROLLBAR. The backend container shell env variables match the set global variables.
-	Other attempts to resolve Rollbar issue:
I tried both sets of ‘app.py’ Rollbar code that Andrew Brown provided with no resolution of missing Rollbar data. I tried different version of flask (2.2.5 and 3.0.0) with both sets of Rollbar code, to no avail.  I tried advice from Stack Overflow and other online sources.  I did `env | grep ROLLBAR` and confirmed my Rollbar access token is set correctly. The backend URL works and I get “Hellow World!”  I did try advice from a bootcamp discord group to comment out “app.before_first_request` and put beneath it `with app.app_context():`. Shifting the code indentation below it.  I have also tried opening new Gitpod Workspaces to start fresh and had no success with that resolving issue.  Also, I made sure that the shell attacked to my backend container had the same rollbar access token environment variable.
-	At this point I will stop working on Rollbar to move on to Week-3
-	I leave with the backend container log giving me this error:
`pyrollbar: No access_token provided. Please configure by calling rollbar.init() with you access token.`

- To upgrade or downgrade the flask version do the following steps:
 1) Check the current version of Flask that you are using. You can do this by running the following command in your terminal:
`pip show flask`

 2) Once you know the current version of Flask that you are using, you can downgrade to a version older than 2.3.0 by running the following command:
`pip install flask==2.2.5`
`pip install flask==3.0.0`

NOTE:
The error AttributeError: 'Flask' object has no attribute 'before_first_request' occurs when you try to use the before_first_request decorator in a Flask app that is using version 2.3.0 or higher. This decorator was deprecated in Flask 2.3.0 and removed in Flask 2.4.0.
To fix this error, you need to upgrade your Flask app to a version older than 2.3.0 or use the got_first_request decorator instead. The got_first_request decorator is a newer decorator that is similar to before_first_request but it is not deprecated.




## How to Tag Work 
```sh
git tag week-2
git push --tags
```
&NewLine;
&NewLine;
&nbsp;

 ## How I Resolved Fronted Failed to Compile Error

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
![Screenshot updated node](https://github.com/SBecraft/aws-bootcamp-cruddur-2023/blob/main/_docs/assets/week-2-assets/screenshot-updated-node.png)

&NewLine;
&NewLine;
&nbsp;zzz

## References
- [Honeycomb.io Website](https://ui.honeycomb.io)
- [Honeycomb Documentation for Python](https://docs.honeycomb.io/getting-data-in/opentelemetry/python-distro/)
- [Olga Timofeeva Blog on Instrumenting AWS X-Ray Subsegments](Olley.hashnode.dev/aws-free-cloud-bootcamp-instrumenting-aws-x-ray-subsegments)

