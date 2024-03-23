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

