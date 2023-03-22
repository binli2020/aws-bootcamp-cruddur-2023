# Week 2 — Distributed Tracing

## Learn **what is observability**
* https://www.honeycomb.io/resources/intro-to-o11y-topic-1-what-is-observability
* Watched other Training Videos
* Document: [Learn About Observability](https://docs.honeycomb.io/concepts/learning-about-observability/)
  - [What is Observability? ](https://docs.honeycomb.io/concepts/learning-about-observability/#what-is-observability)
    > Observability is about being able to ask arbitrary questions about your environment without having to know ahead of time what you wanted to ask.
  - [How Can You Improve Observability?](https://docs.honeycomb.io/concepts/learning-about-observability/#how-can-you-improve-observability)
    > Improving software observability requires two things: the ability to capture telemetry data with a lot of runtime context, and the ability to query that data iteratively in order to find new insights.
    * distributed traces: the path of execution through your distributed system.

## **What is OpenTelemetry**
> OpenTelemetry is an open-source observability framework used to instrument, generate, collect, and export telemetry data (metrics, traces, and logs) from cloud-native applications. The project is a merger of two existing observability projects, OpenCensus and OpenTracing, and is now the default observability framework for cloud-native applications in the Cloud Native Computing Foundation (CNCF).

> OpenTelemetry provides a set of APIs, SDKs, and integrations that developers can use to instrument their applications with telemetry data. It supports multiple programming languages and platforms, including Java, Go, Python, and Node.js, among others.

> OpenTelemetry supports three main types of telemetry data:

  1. Metrics: numerical measurements of the behavior and performance of the application and its underlying infrastructure, such as CPU usage, memory usage, and response time.
  2. Traces: a detailed record of the path that a transaction takes through an application, including the time spent in each operation and any errors encountered.
  3. Logs: detailed records of events that occur within an application, including application-level events and infrastructure-level events such as server logs.
> By using OpenTelemetry, developers can gain better visibility into the behavior and performance of their applications in production. They can use this data to identify performance issues, diagnose errors, and optimize their applications for better performance and reliability.

## What does "instrument their applications with telemetry data" mean?
> Instrumentation in the context of telemetry data refers to the process of adding code to an application to collect and transmit data about its behavior and performance.

> To instrument an application with telemetry data using OpenTelemetry, a developer needs to add code to their application that uses the OpenTelemetry APIs and SDKs to collect and transmit telemetry data. This code can be added to the application at various points, such as when a request is received, when a database query is executed, or when an error occurs.

> The telemetry data collected by instrumentation can include metrics, traces, and logs, which are used to monitor the performance and behavior of the application in real-time. This telemetry data is typically transmitted to a monitoring or observability platform, such as Honeycomb, where it can be analyzed, visualized, and used to diagnose issues and optimize the application's performance.

> In summary, instrumenting an application with telemetry data means adding code to collect and transmit telemetry data to a monitoring or observability platform, which can then be used to gain insights into the behavior and performance of the application.

## About log in to the app
* Looks like everytime need to **sign up** with a dummy email and use `1234` as the reference code
 
## Watching YT video of the living session
* Jessica: use different Dockerfile for 'development' and 'production' containers.
  - 'development' container needs a base image with more utilities installed
  - 'production' container needs a slimmer base image without, i.e. vim, ssh, to make it smaller in size and securer.
* To **add OpenTelemetry instrucmentation to Python code**
  1. Install Packages
    * need install these python libraries. So add the following to the `requirements.txt`
    ```sh
    opentelemetry-api 
    opentelemetry-sdk 
    opentelemetry-exporter-otlp-proto-http 
    opentelemetry-instrumentation-flask 
    opentelemetry-instrumentation-requests
    ```
    * Then run `pip install -r requirements.txt` to install the libraries in the terminal.
  2. Initialize
    * Add codes into `app.py`
    ```python
    from opentelemetry import trace
    from opentelemetry.instrumentation.flask import FlaskInstrumentor
    from opentelemetry.instrumentation.requests import RequestsInstrumentor
    from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    
    # Initialize tracing and an exporter that can send data to Honeycomb
    provider = TracerProvider()
    processor = BatchSpanProcessor(OTLPSpanExporter())
    provider.add_span_processor(processor)
    trace.set_tracer_provider(provider)
    tracer = trace.get_tracer(__name__)
    
    app = Flask(__name__)
    
    # Initialize automatic instrumentation with Flask
    FlaskInstrumentor().instrument_app(app)
    RequestsInstrumentor().instrument()
    ```
  3. Configure and Run
    * Configure OpenTelemetry to send events to Honeycomb using environment variables.
The header x-honeycomb-team is your API key. Your service name will be used as the Service Dataset in Honeycomb, which is where data is stored. The service name is specified by OTEL_SERVICE_NAME.
    * Add to the `docker-compose.yml`
    ```yaml
    OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
    OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
    OTEL_SERVICE_NAME: "backend-flask"

    ```
    * In terminal, set env variable `HONEYCOMB_API_KEY`
    ```sh
    export HONEYCOMB_API_KEY=<api_key>
    gp env HONEYCOMB_API_KEY="<api_key>"  # for gitpos set this env var for each workspace automatically
    ```
    * Run these to restart the containers
    ```sh
    docker compose down
    docker compose up
    ```
    * Visit the backend url `<base_url>/api/activities/home`, and observe traces appearing in Honeycomb UI.
    ![image](https://user-images.githubusercontent.com/71969513/225817916-9a35cd06-13e8-4f49-8ec7-77515963e956.png)

* How to observe via Trace in HoneyComb
  * Add multiple level span and add attributes for a span
    ```python
    tracer = trace.get_tracer("home.activities")

    class HomeActivities:
      def run():
        with tracer.start_as_current_span("home-activities-mock-data"):
          now = datetime.now(timezone.utc).astimezone()
          span = trace.get_current_span()
          span.set_attribute("app.now", now.isoformat())
    ```
  * Observe latency in traces. Learn from [here](https://www.honeycomb.io/resources/intro-to-o11y-topic-11-using-group-by-with-a-heatmap)
    * Use `HEATMAP(duration_ms)` and `P90(duration_ms)` or `MAX(duration_ms)` as VISUALIZE and GROUP BY `http.url` to see latency of the traces.
    ![image](https://user-images.githubusercontent.com/71969513/225839045-b408d524-80d6-434b-abb3-588ef5d48844.png)

    ![image](https://user-images.githubusercontent.com/71969513/225838867-5ec3f718-6c48-4303-99ee-6c397a178623.png)
  * [Doc: working with your data:tracing](https://docs.honeycomb.io/working-with-your-data/tracing/)
## Watched YT video about Spending Considerations by Chirag. Finished the quiz.
## Watched YT video about Observability Security Considerations by Ashish. Finished the quiz.
## Watching YT video about AWS X-Ray
  * Boto3: AWS SDK for Python
  * Actually we don't use boto3 directly. We use a Python library `aws-xray-sdk`. So add it to the `requirements.txt`
    ```sh
    ... ...
    opentelemetry-instrumentation-requests
    aws-xray-sdk
    ```
  * What is a middleware?
    * > In a web backend framework, middleware refers to a software component that sits between the server and the application logic, providing a set of common services or functions that can be shared across different parts of the application.

      > Middleware is used to handle tasks such as authentication, logging, caching, and request/response processing. It allows developers to modularize their code and separate concerns, making it easier to maintain and scale their applications.

      > Middleware functions can be chained together to create a pipeline that processes requests and responses in a specific order. Each middleware function in the pipeline can modify the request or response, or terminate the pipeline by returning a response to the client.

      > Web frameworks such as Express.js, Django, and Ruby on Rails all provide built-in middleware that can be used out-of-the-box, and developers can also create their own custom middleware to handle specific application requirements.
   * Add code in `app.py` to instruct for X-RAY
     ```python
     from aws_xray_sdk.core import xray_recorder
     from aws_xray_sdk.ext.flask.middleware import XRayMiddleware

     xray_url = os.getenv("AWS_XRAY_URL")
     xray_recorder.configure(service='Cruddur', dynamic_naming=xray_url)
     XRayMiddleware(app, xray_recorder)
     ```
   * Use AWS CLI to create a group for X-RAY
     ```sh
     $ aws xray create-group    --group-name "Cruddur"    --filter-expression "service(\"backend-flask\")"
      {
          "Group": {
              "GroupName": "Cruddur",
              "GroupARN": "arn:aws:xray:ap-southeast-2:461075076403:group/Cruddur/ELVUCCCTCVRE7VMACDBN3ST2HQ3NVW73EGPW6JUYMDJZH3VKJ3ZA",
              "FilterExpression": "service(\"backend-flask\")",
              "InsightsConfiguration": {
                  "InsightsEnabled": false,
                  "NotificationsEnabled": false
              }
          }
      }
      ```
      ![image](https://user-images.githubusercontent.com/71969513/226273644-153a5073-d106-4019-a625-f0d10fd92d45.png)

   * Create a sampling rule
     ```sh
     $ aws xray create-sampling-rule --cli-input-json file://aws/json/xray.json
     {
          "SamplingRuleRecord": {
              "SamplingRule": {
                  "RuleName": "Cruddur",
                  "RuleARN": "arn:aws:xray:ap-southeast-2:461075076403:sampling-rule/Cruddur",
                  "ResourceARN": "*",
                  "Priority": 9000,
                  "FixedRate": 0.1,
                  "ReservoirSize": 5,
                  "ServiceName": "backend-flask",
                  "ServiceType": "*",
                  "Host": "*",
                  "HTTPMethod": "*",
                  "URLPath": "*",
                  "Version": 1,
                  "Attributes": {}
              },
              "CreatedAt": "2023-03-20T07:37:58+00:00",
              "ModifiedAt": "2023-03-20T07:37:58+00:00"
          }
      }
     ```
   * Add daemon service to Docker Compose
    ```yml
    xray-daemon:
      image: "amazon/aws-xray-daemon"
      environment:
        AWS_ACCESS_KEY_ID: "${AWS_ACCESS_KEY_ID}"
        AWS_SECRET_ACCESS_KEY: "${AWS_SECRET_ACCESS_KEY}"
        AWS_REGION: "ap-southeast-2"
      command:
        - "xray -o -b xray-daemon:2000"
      ports:
        - 2000:2000/udp
    ```
    Add two env vars to backend-flask in `docker-compose.yml` file

    ```yml
    AWS_XRAY_URL: "*4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}*"
    AWS_XRAY_DAEMON_ADDRESS: "xray-daemon:2000"
    ```
   * Add segments to X-RAY
   > The AWS X-Ray SDK for Python provides a predefined decorator that can be used to instrument Flask application endpoints. The decorator is called `xray_recorder.capture()` and it can be used to automatically trace your endpoint function with an X-Ray segment.

   > Here's an example of how to use the xray_recorder.capture() decorator:
     ```python
     from flask import Flask
     from aws_xray_sdk.core import xray_recorder

     app = Flask(__name__)

     @app.route('/example_endpoint')
     @xray_recorder.capture('example_endpoint')
     def example_endpoint():
         # Your code here
         return 'Hello, World!'
     ```
   > In this example, we apply the xray_recorder.capture() decorator to our example_endpoint() function. We provide a name for the segment as an argument to the decorator.
   > When the endpoint function is called, the xray_recorder.capture() decorator automatically creates a new X-Ray segment with the provided name, records the start and end times of the segment, and captures any subsegments or metadata associated with the endpoint's execution.
   > The xray_recorder.capture() decorator can be a convenient way to add X-Ray tracing to your Flask application without having to manually create and manage X-Ray segments in your code.
     * Add the decorator to the home activity endpoint
       ```python
       @app.route("/api/activities/home", methods=['GET'])
       @xray_recorder.capture('home_endpoint')
       def data_home():
         data = HomeActivities.run()
         return data, 200
       ```
     ![image](https://user-images.githubusercontent.com/71969513/226574042-1e1a0370-facc-40c0-ab44-ba8f0b1aae39.png)
     * No need to call `xray_recorder.begin_segment` explicitely when using `xray_recorder.capture()` decorator
     > It's worth noting that using begin_segment() and end_segment() can be more error-prone and less convenient than using the capture() decorator or the in_segment() context manager. These methods automatically manage the lifecycle of segments and subsegments for you, making it easier to trace the execution of your code without introducing potential bugs.
   * Add subsegments
     * You may use `xray_recorder.begin_subsegment()` to add a subsegment. But remember to use **`xray_recorder.end_subsegment()`** to close it. Otherwise it doesn't work.
     * Or alternatively, you may use `xray_recorder.in_subsegment()` context manager
       ```python
       with xray_recorder.in_subsegment('subsegment_mock') as subsegment_mock:
         dict = {
           "now": now.isoformat(),
           "results-size": len(model['data'])
         }
         subsegment_mock.put_metadata('key', dict, 'namespace')
       ```

## Send logs to AWS CloudWatch Logs - following [YouTube video](https://www.youtube.com/watch?v=ipdFizZjOF4&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=34)
  * [What is AWS CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)
    > You can use Amazon CloudWatch Logs to monitor, store, and access your log files from Amazon Elastic Compute Cloud (Amazon EC2) instances, AWS CloudTrail, Route 53, and other sources.

    > CloudWatch Logs enables you to centralize the logs from all of your systems, applications, and AWS services that you use, in a single, highly scalable service. You can then easily view them, search them for specific error codes or patterns, filter them based on specific fields, or archive them securely for future analysis. CloudWatch Logs enables you to see all of your logs, regardless of their source, as a single and consistent flow of events ordered by time.

    > CloudWatch Logs also supports querying your logs with a powerful query language, auditing and masking sensitive data in logs, and generating metrics from logs using filters or an embedded log format.
