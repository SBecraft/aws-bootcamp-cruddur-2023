# Week 2 — Distributed Tracing

Table of Contents
-	[Technical Tasks](#technical-tasks)
-	[Honeycomb](#honeycomb)
-	[References](#references)



## Technical Tasks
The fractional CTO has suggested that we implement distributed tracing first because as we begin to add cloud services it will become difficult to pinpoint issues and we want to keep pace with the development timeline.

- Instrument our backend flask application to use Open Telemetry (OTEL) with Honeycomb.io as the provider
- Run queries to explore traces within Honeycomb.io
- Instrument AWS X-Ray into backend flask application
- Configure and provision X-Ray daemon within docker-compose and send data back to X-Ray API
- Observe X-Ray traces within the AWS Console
- Integrate Rollbar for Error Logging
- Trigger an error an observe an error with Rollbar
- Install WatchTower and write a custom logger to send application log data to CloudWatch Log group

## Honeycomb
 
### Create Honeycomb Account
-	Created a free tier Honeycomb account at https://ui.honeycomb.io
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
  ![Failed to Compile]()

 #### How I Resolved the Failed to Compile Error

- I had to resolve issues with (1) not seeing ports in PORTS tab and (2) no service available on frontend port 3000, after running docker Compose Up.
- When trying to access front end port 3000 to see Cruddur URL I received the message "Failed to Compile" and "React must be in scope when using JSX   react/react-in-jsx-scope"
- Ruled out any breakpoints in the "scripts" part of my package.json file.
- Ran debugging of package.json file. Received error about hashing algoritm.
- Researched this issue and found that this error occurs when using Node.js version 17 or higher, which does not support the MD4 hash algorithm any longer.

  
- RECOMMENDATION: downgrade your current Node.js version to 16 or lower, which is the current, long-term version, using the Node Version Manager (nvm). 



##### SOLUTION: 

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











## References
- [Honeycomb.io] https://ui.honeycomb.io
- [Honeycomb Documentation for Python] https://docs.honeycomb.io/getting-data-in/opentelemetry/python-distro/

