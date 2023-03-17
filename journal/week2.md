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
  * [Doc](https://docs.honeycomb.io/working-with-your-data/tracing/)
