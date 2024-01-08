# Reliable, scalable, and maintainable applications

A data-intensive application is typically built from standard building blocks. They usually need to:

- Store data (databases)
- Speed up reads (caches)
- Search data (search indexes)
- Send a message to another process asynchronously (stream processing)
- Periodically crunch data (batch processing)

We will focus 3 concerns that are important in most software systems:

- **Reliability**. To work correctly even in the face of adversity.
- **Scalability**. Reasonable ways of dealing with growth.
- **Maintainability**. Be able to work on it productively.

---

# 1. Reliability

Typical expectations:

- Application performs the function the user expected
- Tolerate the user making mistakes
- Its performance is good
- The system prevents abuse

Systems that anticipate faults and can cope with them are called fault-tolerant or resilient.

**A fault is usually defined as one component of the system deviating from its spec**, whereas failure is when the
system as a whole stops providing the required service to the user.

You should generally prefer **tolerating faults over preventing faults.**

- **Hardware faults:**
    - Until recently redundancy of hardware components was sufficient for most applications.
    - As data volumes increase, more applications use a larger number of machines, proportionally increasing the rate of
      hardware faults.
    - **There is a move towards systems that tolerate the loss of entire machines**. A system that tolerates machine
      failure can be patched one node at a time, without downtime of the entire system (rolling upgrade).
- **Software faults:**
    - It is unlikely that a large number of hardware components will fail at the same time.
    - Software errors are a systematic error within the system, they tend to cause many more system failures than
      uncorrelated hardware faults.
- **Human errors:**
    - Humans are known to be unreliable.
    - Configuration errors by operators are a leading cause of outages
    - You can make systems more reliable:
        - Minimising the opportunities for error, eg: with admin interfaces that make easy to do the "right thing" and
          discourage the "wrong thing".
        - Provide fully featured non-production sandbox environments where people can explore and experiment safely.
        - Automated testing.
        - Quick and easy recovery from human error, fast to rollback configuration changes, roll out new code gradually
          and tools to recompute data.
        - Set up detailed and clear monitoring, such as performance metrics and error rates (telemetry).
        - Implement good management practices and training.

---

# 2. Scalability

To cope with the increased load, we need to describe the current load on the system; only then we can discuss growth
questions.

## 2.1. Describing Load

Load can be described with few numbers which we call them **load parameters.**
The best choice of parameters depends on the architecture of your system; It may be :

- Request per sec to the web server
- The ratio of reads and write to the DB
- Number of simultaneously active users in a chat room.
- the hit rate on cache
- etc

### To make the idea more clear, take example of Twitter

Twitter main operations

- Post tweet: a user can publish a new message to their followers (4.6k req/sec, over 12k req/sec peak)
- Home timeline: a user can view tweets posted by the people they follow (300k req/sec)

Two ways of implementing those operations:

- Approach 1:
    - Posting a tweet simply inserts the new tweet into a global collection of tweets.
    - When a user requests their home timeline, look up all the people they follow, find all the tweets for those users,
      and merge them (sorted by time).
    - This could be done with a SQL `JOIN`

- Approach 2:
    - Maintain a cache for each user's home timeline.
    - When a user posts a tweet, look up all the people who follow that user
    - and insert the new tweet into each of their home timeline caches.

Approach 1, systems struggle to keep up with the load of home timeline queries, So the company switched to approach 2.

The average rate of published tweets is almost two orders of magnitude lower than the rate of home timeline reads.

Downside of approach 2 is that :

- posting a tweet now requires a lot of extra work.
- Some users have over 30 million followers (celebrities) so a single tweet may result in over 30 million writes to home
  timelines.

Hence, Twitter moved to an hybrid of both approaches.

- Tweets continue to be fanned out to home timelines
- but a small number of users with a very large number of followers are fetched separately and merged with that user's
  home timeline when it is read, like in approach 1.

**In the example of twitter, the distribution of followers per user (may be weighted by how often those users tweet) is
the key load parameter for discussing scalability, since it determines the fan-out load**

## 2.2. Describing performance

Once you describe the load on the system, you can investigate what happens when the load increases. You can look at it
in 2 ways:

- How is the performance affected? - By increasing the load parameter and keep the system resource unchanged.
- How much do you need to increase your resources? - if you want to keep performance unchanged.

In a batch processing system such as Hadoop, we usually care about throughput, or the number of records we can process
per second.

> **Latency and response time :**
> The response time is what the client sees. Latency is the duration that a request is waiting to be handled.

It's common to see the **average response time** of a service reported.

- However, the mean is not very good metric if you want to know your "typical" response time,
- it does not tell you how many users actually experienced that delay.

**Better to use percentiles.**

- Median (50th percentile or p50)
    - Half of user requests are served in less than the median response time, and the other half take longer than the
      median
- Percentiles 95th, 99th and 99.9th (p95, p99 and p999)
    - are good to figure out how bad your outliners are.

Amazon describes response time requirements for internal services in terms of the 99.9th percentile because the
customers with the slowest requests are often those who have the most data. The most valuable customers. On the other
hand, optimising for the 99.99th percentile would be too expensive.

**Service level objectives (SLOs) and service level agreements (SLAs)** are contracts that define the expected
performance and availability of a service.

- An SLA may state the median response time to be less than 200ms
- 99th percentile under 1s

These metrics set expectations for clients of the service and allow customers to demand a refund if the SLA is not met.

**Queueing delays** often account for large part of the response times at high percentiles.It is important to measure
times on the client side:
When generating load artificially, the client needs to keep sending requests independently of the response time.

> **Percentiles in practice :**
> Calls in parallel, the end-user request still needs to wait for the slowest of the parallel calls to complete. The chance of getting a slow call increases if an end-user request requires multiple backend calls.
> Ex : If single request , makes multiple calls in backend, it takes just a single slow backend request to slow down the entire end-user request

## 2.3. Approaches for coping with load

- Scaling up or vertical scaling: Moving to a more powerful machine
- Scaling out or horizontal scaling: Distributing the load across multiple smaller machines.
- Elastic systems: Automatically add computing resources when detected load increase. Quite useful if load is
  unpredictable.

Distributing stateless services across multiple machines is fairly straightforward.

Taking stateful data systems from a single node to a distributed setup can introduce a lot of complexity.

Until recently it was common wisdom to keep your database on a single node.

---

# 3. Maintainability

The majority of the cost of software is in its ongoing maintenance. There are three design principles for software
systems:

- **Operability**. Make it easy for operation teams to keep the system running.
- **Simplicity**. Easy for new engineers to understand the system by removing as much complexity as possible.
- **Evolvability**. Make it easy for engineers to make changes to the system in the future.

## 3.1 Operability: Making life easy for operations

A good operations team is responsible for

- Monitoring and quickly restoring service if it goes into bad state
- Tracking down the cause of problems
- Keeping software and platforms up to date
- Keeping tabs on how different systems affect each other
- Anticipating future problems
- Establishing good practices and tools for development
- Perform complex maintenance tasks, like platform migration
- Maintaining the security of the system
- Defining processes that make operations predictable
- Preserving the organisation's knowledge about the system

**Good operability means making routine tasks easy.**

## 3.2 Simplicity: Managing Complexity

When complexity makes maintenance hard, budget and schedules are often overrun. There is a greater risk of introducing
bugs.

Making a system simpler means removing accidental complexity, as non inherent in the problem that the software solves (
as seen by users).

One of the best tools we have for removing accidental complexity is abstraction that hides the implementation details
behind clean and simple to understand APIs and facades.

## 3.3 Evolvability: Making change easy

Agile working patterns provide a framework for adapting to change.

Tools and patterns that are helpful for such frequently changing environment: TDD and refactoring.

---

- Functional requirements: what the application should do
- Nonfunctional requirements: general properties like security, reliability, compliance, scalability, compatibility and
  maintainability.

---









