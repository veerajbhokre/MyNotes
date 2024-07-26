# TODO- Ignore this page

# Functional Requirement

While architecting any systems

Also have look at

- Performance
    - req like 90% of req satisfy in sec.
    - Min latency and increse throughput
- SCalability
    - system able to handle 1 M simaltanious users.
- Reliability
    - system resiliant to failure
    - Make system HA
- Security
    - Secure transfer data
    - store data
    - auth/authz
    - external/internal threats
- DEployment
- TEchnologu stack

# Performance

## High level topics

Understanding performances

- Problems
- Measurements
- Principals on which performance of the system looks like

How do we design and increase the performance of the system

- Minimise latency
    - CPU
    - Memory
    - Network
    - Disk

- Take care of concurrency
    - how do we inprove throughput of the syatem
    - Primary focus on locking
        - Pessimist
        - optimistic locking
    - Coherence

- Finally Caching

## REference Example to improve performance

Browser -> webApp -> Business Appl -> Database -> Batch Processing(For reporting)

## What is performance?

Performance is how fast the system works under given workload

- backend data
- volume given hardware.
- Kind
- Capacity