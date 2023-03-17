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
* To **add OpenTelemetry instrucmentation to Python code**, 
  * need install these python libraries. So add the following to the `requirements.txt`
    ```sh
    opentelemetry-api 
    opentelemetry-sdk 
    opentelemetry-exporter-otlp-proto-http 
    opentelemetry-instrumentation-flask 
    opentelemetry-instrumentation-requests
    ```
    Then run `pip install -r requirements.txt` to install the libraries in the terminal.
  * Add codes into `app.py`
    ```python
    from opentelemetry import trace
    from opentelemetry.instrumentation.flask import FlaskInstrumentor
    from opentelemetry.instrumentation.requests import RequestsInstrumentor
    from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    ```


  
