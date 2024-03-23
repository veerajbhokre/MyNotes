# Introduction

### Pillars

- Metrics
- Logs
- Traces
- Events

---

## Introduction to Observability Architecture:

1. Different methodologies and tools exist for implementing observability.

- Various architectures can be utilized to achieve system observability.
- Common stages include **instrumentation/configuration, collection and processing, export, and visualization layers.**

2. Vendor-Based Observability:

- Quick implementation using a vendor's services.
- Utilizes vendor's instrumentation libraries, collection agents, and UI.
- Can be costly.

3. Open Source Observability:

- More cost-friendly option.
- Relies on open source instrumentation libraries, collection agents, and UI.
- Requires more maintenance and scalability considerations as infrastructure grows.

4. OpenTelemetry Framework:

- Open source observability framework gaining popularity.
- Provides standard protocols and tools for instrumenting, collecting, and routing telemetry data.
- Offers language-specific instrumentation libraries and a collector for data processing and exporting.
- Compatible with various observability tools and vendors.
- Reduces complexity and vendor lock-in.

5. OpenTelemetry Pros and Cons:

- Pros: Vendor agnostic, easy setup, flexible, and standardized observability implementation.
- Cons: Maintenance may vary, data limitations (e.g., certain signals not supported), and language-specific
  instrumentation availability.

6. Implementation Changes with OpenTelemetry:

- Replace vendor-specific instrumentation with OpenTelemetry (optional).
- Replace collection agents with OpenTelemetry collector.

---

## Metrics Overview:

- **Definition:**
    - Metrics are numeric values that provide insights into a system's characteristics.
    - They can be aggregated and analyzed statistically to measure a system's performance.

- **Types of Metrics:**
    - **Counters:** Increment-only metrics tracking occurrences of specific events or activities.
        - Useful for measuring discrete events like API requests or error occurrences.
        - Helpful for monitoring request volumes and identifying traffic spikes.

    - **Gauges:** Provide snapshots of values at specific points in time, representing the instantaneous state of a
      system.
        - Examples include memory usage, CPU utilization, or active connections.
        - Useful for understanding current system states and identifying optimization needs.

    - **Histograms:** Measure duration and frequency of time durations for specific events.
        - Used to measure event durations, e.g., HTTP request call durations.
        - Helpful for calculating averages or percentiles and optimizing user experience.

- **Use Cases:**
    - **Performance Optimization:**
        - Gauges help identify bottlenecks and performance issues in real time.
        - Enables pinpointing areas requiring optimization like memory-hungry processes or slow database queries.

    - **Capacity Planning:**
        - Counters aid in predicting resource requirements.
        - Helps determine when systems approach capacity limits, facilitating proactive resource scaling.

    - **User Experience Enhancement:**
        - Histograms assist in understanding the distribution of user interactions.
        - Focuses on improving metrics related to user interactions for a smoother and more responsive system.

Understanding and effectively utilizing different types of metrics is crucial for optimizing system performance,
planning resource allocation, and enhancing user experience.

## Mathematical Analysis of Metric Types:

- **Overview:**
    - Mathematical analysis of metrics like sums, rates, percentiles, and averages provides valuable insights into
      system behavior and performance.

- **Sums:**
    - Straightforward mathematical analysis by adding up all individual data points within a specific timeframe.
    - Useful for tracking total count or quantity of events over a period, e.g., counting requests served by a web
      server in an hour.

- **Rates:**
    - Involves dividing the change in quantity by the change in time.
    - Helpful for measuring how something changes over time, e.g., calculating the rate of incoming requests per minute.

- **Averages:**
    - Calculated by summing all data points and dividing by the number of data points.
    - Useful for finding the central tendency of data, e.g., calculating the average response time of a service.

- **Percentiles:**
    - Calculated by dividing data into 100 equal parts and finding the value below which a given percentage of data
      falls.
    - Used to understand data distribution and identify outliers, e.g., determining the 90th percentile of response
      times to gauge user experience.

- **Application of Percentiles:**
    - Percentiles help identify outliers in latency metrics, addressing the challenge of averages masking extreme
      values.
    - Commonly used percentiles include P50, P90, P95, and P99.
    - Examples:
        - P50 latency of one millisecond indicates 50% of requests completed in one millisecond or less.
        - P99 latency of two milliseconds indicates 99% of requests completed in two milliseconds or less.

Understanding these metric types and their mathematical analysis enables organizations to gain deeper insights into
system performance and behavior, facilitating better decision-making and optimization efforts.

## Choosing the Right Metrics:

- **Importance of Metrics:**
    - Metrics play a foundational role in monitoring and observability, offering insights into system behavior and
      performance.
    - Often generated from other observability signals for various purposes.

- **Google's Four Golden Signals:**
    - Errors, latency, throughput, and saturation are the four golden signals according to Google's SRE book.
    - Provide insights into system performance.

- **Brief Overview of Golden Signals:**
    - **Errors:** Rate of unsuccessful events within a service or infrastructure, e.g., HTTP 500 responses.
    - **Latency:** Amount of time taken for an event or request to be completed.
    - **Throughput:** Volume of requests or events being received by a service or infrastructure.
    - **Saturation:** Measure of how a service's or infrastructure's limited resources are utilized.

- **Limitations of Golden Signals:**
    - Engineering-focused and may not fully capture product performance aspects.

- **Custom Metrics:**
    - Extend engineering metrics with custom metrics considering product performance.
    - Provides better insights into system behavior.

- **Deciding the Right Metrics:**
    - **Purpose:** Determine what to measure and how to query the metric.
    - **Actionability:** Metrics can be actionable, exploratory, or reporting.
    - **Visualization:** Choose appropriate visualization methods depending on the observability tooling used.

- **Characteristics of Good Metrics:**
    - **Understandable:** Clear purpose and function, providing insight into specific system or product characteristics.
    - **Actionable:** Spur teams to action and guide responses to incidents or issues.
    - **Improvable:** Drive improvements within the system based on metric insights.
    - **Multidimensional:** Utilize tagging to add dimensions to metrics for better understanding and analysis.

Understanding, defining, and visualizing metrics effectively enables organizations to make informed decisions, drive
improvements, and enhance user experiences.

## Tools

- Instrument using OpenTelemetry (Otel) sdk -> Provides the unified way.
- Configure OTel collector to scrap the metrics (Configure OTel collector receiver)
- Configure OTel collector Exporter to convert it into promethous compatible metrics and expose it on /metrics endpoint.
- Install Thanos
- Configure Thanos to scrap metrics from OTel collector.
- In Grafana create a Thanos datastore and use promQL to query data for visualization.

---

# Traces

## Concepts of Tracing and Distributed Systems

- **Tracing Overview:**
    - Traces track the journey of a request through different components in your infrastructure.
    - They visualize the path of a request, from application functions to databases, aiding in performance analysis and
      incident investigation.
    - Traces can be tagged with custom information, offering insights into user interactions and experiences.

- **Components of Traces:**
    - Traces are composed of individual spans, representing each hop a request makes.
    - Spans depict each function call within your application and infrastructure.
    - Length of spans indicates latency or errors, aiding interpretation.

- **Interplay with Metrics:**
    - Metrics provide an aggregated view of performance, while traces offer detailed insights.
    - Drilling down from metrics to corresponding traces helps in understanding performance issues.
    - Traces can be tagged for context, facilitating the generation of business metrics.

- **Sampling for Efficiency:**
    - High traffic applications may generate numerous traces, impacting costs and storage.
    - Sampling is introduced to analyze a subset of requests, reducing overhead.
    - Sampling strategy depends on observability software used.

- **Sampling Configurations for Grafana Tempo:**
    - Various sampling configurations are available for Grafana's tracing tool, Tempo.

## Tracing in Action using Tempo and Grafana Cloud

- **Introduction to Tracing:**
    - Tracing requires instrumentation of the application to emit traces, collected via tracing agents.
    - Traces are visualized as waterfall diagrams, comprising parent traces and child spans representing each hop.
    - Each span's latency is visually represented, aiding in identifying performance bottlenecks.

- **Instrumentation Setup:**
    - Node.js service is instrumented using OpenTelemetry to send traces to Grafana Cloud.
    - Automatic instrumentation libraries capture Node.js actions, HTTP traces, Express middleware, and MongoDB
      interactions.
    - Custom spans can be created, and attributes added, enhancing trace insights.

- **OpenTelemetry Configuration:**
    - OpenTelemetry configuration includes defining receiver, exporter, and processor for traces.
    - OTLP receiver and exporter are configured to send traces to Grafana Cloud.

- **Interacting with Traces in Grafana Cloud:**
    - Traces can be explored using TraceQL or the Search tab, filtering by service name, span name, duration, or tags.
    - Service graph visualization provides insights into request flows and backend interactions.
    - Metrics generated from traces can be explored using the Prometheus data source in Grafana Cloud.
    - Histogram metrics, such as latency percentiles, can be visualized and filtered similar to other metrics.

- **Conclusion:**
  - Tracing and metrics work together to provide comprehensive insights into service performance.
  - Generated metrics from traces can be utilized for dashboarding and alerting, enhancing observability.

---

# Logs

## Concepts of Logging and Log Management

- **Introduction to Logging:**
  - Logging is a fundamental concept in observability and monitoring, providing textual records of events.
  - Default logs are often available for various infrastructure components, such as access logs for proxy services.
  - Developers also output log lines within applications to track system states and events.

- **Characteristics of Logs:**
  - Logs are typically unstructured text lines, making them challenging to analyze.
  - Structured logs provide a more organized format, commonly using JSON to define key fields.
  - Structured logs offer several advantages, including readability, analysis, and reduced log size.
  - JSON format is widely supported by logging tools, enabling enhanced processing capabilities.

- **Benefits of Structured Logs:**
  - Enhanced readability: Structured logs are easier to interpret and analyze.
  - Analytical capabilities: Common fields in structured logs facilitate effective log analysis.
  - Reduced log size: Selective inclusion of fields minimizes log size, optimizing storage and processing.
  - Tooling support: Many logging tools support JSON format, enabling seamless integration and processing.

## Best Practices for Logging

- **Content Considerations:**
  - Be mindful of the content of your logs, especially sensitive data like PII or financial information.
  - Ensure compliance with industry regulations regarding data protection.

- **Log Retention:**
  - Define the lifecycle of your logs based on your specific use case.
  - Consider factors such as active query duration and archival needs.
  - Monitor log growth and retention policies to optimize costs.

- **Tooling Selection:**
  - Choose logging tools based on the intended purpose of your logs.
  - Consider factors such as real-time logging, analytics capabilities, and cost-effectiveness.
  - Align tooling with your team's use case to avoid unnecessary expenses.

- **Log Structuring:**
  - Structure your logs to reflect important information based on their purpose.
  - Include relevant data, such as infrastructure details or customer IDs for e-commerce platforms.

- **Log Level Definition:**
  - Categorize logs into different levels (e.g., error, warning, debug) to indicate severity.
  - Utilize log levels for effective alerting and analysis.

- **Message Clarity:**
  - Provide clear and concise messages in logs to describe events accurately.
  - Enable easy understanding and action by consumers of the logs.

- **Multiple Use Cases:**
  - Recognize the diverse uses of logs beyond troubleshooting, including auditing and analytics.
  - Tailor log formats and contents to suit specific purposes beyond code debugging.

## Log Collection and Application

- Logs can be collected in different ways, such as enabling logging configurations for services or defining logs for
  applications.
- Logging frameworks are available for various programming languages, simplifying the process of defining logs.
- Structured logs should be written to leverage logging tools for analysis, grouping logs by level or tags for easier
  understanding.
- Log filtering and analysis help in understanding service behavior and performance.
- Some tools support alerting based on log messages, specific field values, or log counts to indicate systemic issues.
- Logs can be used to generate metrics, such as counting log types or calculating task latency using start and end time
  fields.
- Logs are a low-effort way to start with observability, offering impactful insights when managed properly.

---

# Monitoring Techniques

## Application Performance Monitoring (APM):

- **Introduction:**
  - APM utilizes observability signals (metrics, logs, traces) to understand application behavior comprehensively.
  - Typically focuses on monitoring backend applications by measuring API endpoint performance.
  - Apdex score is commonly used to rate application health on a scale from zero to one.
  - APM allows going from overall system health to understanding specific functions or endpoints.
  - Proper tagging and application of principles enhance the effectiveness of APM.

- **Implementing APM:**
  - APM can be enabled for services by instrumenting code based on the programming language and observability tool used.
  - Most observability vendors offer APM products with documentation on instrumentation.

- **Grafana Cloud APM:**
  - In Grafana Cloud, APM is accessed through the observability section.
  - Provides a list of services with associated metrics such as request rate, error rate, and latency.
  - Integration of metrics, traces, and logs allows comprehensive analysis within the same view.
  - Service map visualizes downstream and database dependencies, enabling drill-down into individual operations.

- **Benefits of APM:**
  - Improved user experience by presenting service performance holistically without the need to switch between data
    sources.
  - Eliminates context switching between metrics, logs, and traces, streamlining the observability process.
  - Facilitates better observability KPIs such as meantime to detect and resolve incidents.
  - Cost considerations should be taken into account when implementing APM tools.

- **Other APM Tools:**
  - Mature APM tools in the market include Datadog, New Relic, Honeycomb, and Elastic APM.
  - Cost should be considered when selecting and implementing APM solutions.

## Synthetic Monitoring:

- **Introduction:**
  - Acts as black box monitoring to check if an API endpoint responds as expected when given specific inputs.
  - Conditions checked include specific status codes, expected messages (text or JSON), or completion time of requests.
  - Often used to measure uptime and detect system performance issues when outputs deviate from expectations.
  - Results of synthetic monitors can trigger alerts indicating system performance issues.

- **Implementation with Grafana Cloud:**
  - Access synthetic monitoring in Grafana Cloud under the Observability section.
  - Create or view checks to monitor endpoints.
  - Setup includes defining job name, target endpoint, location for testing, run frequency, and timeout.
  - Additional configurations for HTTP request method, TLS settings, authentication, and advanced options.
  - Alerts can be set up based on test results and sensitivity.
  - Dashboard provides summary of test metrics including uptime, reachability, and latency breakdown.
  - Types of checks include ping, DNS resolve, trace route, and TCP checks.
  - Synthetic monitoring provides insights into uptime, reachability, and latency of services by running dummy checks.

## Infrastructure Monitoring:

- **Introduction:**
  - Measures performance of underlying infrastructure resources like VMs or instances.
  - Common metrics include CPU utilization, memory utilization, and disk utilization.
  - Ensures sufficient resources for efficient application operation.

- **Types of Infrastructure Monitoring:**
  - VMs or virtual instances: Measure CPU, disk, and memory utilization.
  - Databases: Specific metrics tailored to database performance.

- **Implementation with Docker:**
  - Utilize scripts or integrations to collect Docker metrics.
  - Scripts can target Docker metrics from Docker Desktop or other Docker environments.
  - Explore tab in Grafana Cloud allows querying and visualization of Docker metrics.
  - Observability tools provide integrations for collecting infrastructure metrics.
  - Integrations come with default metrics and dashboards for easy monitoring setup.

- **Using Observability Tooling:**
  - Grafana Cloud's infrastructure tab offers integrations for various infrastructure types.
  - Add new integrations for different types of infrastructure.
  - Configure integrations to start collecting metrics and utilizing built-in dashboards and alerts.

## Product Monitoring:

- **Introduction:**
  - Customizes observability signals to measure specific aspects of a product.
  - Examples include tracking sales for an e-commerce store or monitoring partner reliability for a FinTech company.
  - Requires customization of metrics, logs, and traces tailored to the product.

- **Custom Metrics, Traces, and Logs:**
  - Utilizes custom metrics, traces with custom attributes, and log attributes.
  - Captures data unique to the product being monitored.

- **Choosing the Right Metrics:**
  - Importance of selecting metrics that accurately reflect the product's performance.
  - Ensures measurement of relevant data in the most effective manner.

- **Instrumentation Process:**
  - Follows documentation of observability tooling for instrumenting product metrics.
  - Utilizes product-specific tags and attributes in traces and logs for context.

- **Analysis and Dissection:**
  - Uses tags and attributes to analyze and dissect observability data for product context.
  - Enables understanding of how product performance aligns with business goals.


