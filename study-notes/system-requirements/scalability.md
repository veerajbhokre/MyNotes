# Scalability

To increase the throughput of an application we need to take care of two things,

- Latency
- Increase Capacity

To decrease the latency keep the capacity constant and decrease different latencies (Network, memory etc)
Refer [here](performance.md) to see how to decrease different latencies.

Once latency is taken care, we can think of capacity.

Increasing/decreasing capacity based on requirement is handled by scalability techniques.

## Types of scalability

- Vertical
    - This is mostly about increasing the configurations like cpu, memory of the machine.
    - Scaling is limited.
    - Easy to scale
    - Not fault-tolerant.

- Horizontal scaling
    - It is hard to achieve
    - But it has unlimited scalability.

## Reverse Proxy

In horizontal scaling we can have multiple replicas and we can keep on adding and removing it. It is difficult to
remember all IPs for a client to remember.

Hence, there is reverse proxy, which will be in front of all these replicas and client will remenber only this reverse
proxy IP.

It also acts as a load balancer.

## Scalability principles

- Decentralized
    - Monolithic is an antipattern for scalability.
    - We need to think of modularizing the services while designing.
- Independence
    - While modularizing the services it is necessary to take care that these services are independent of each other.
    - This gives up opportunity to scale them independently.
    - We should aim to have as less shared resources between them as possible while designing.

## Replication

We can add replications as

- Stateful
    - It has some limitation can we restrict the number of connection to the server.
    - Say each session take 1mb size, and we have max 500 mb memory then connection is restricted to 500.
    - It helps in minimizing the latency
    - There is possibility od data loss if server went down and user changed his session on that machine
- Stateless
    - Here there is no state is maintained
    - This will significantly increase the number of connections to the servers.
    - No data loss.
    - This wll add some latency as we need to fetch user data every time.
    - We can introduce shared cache like redis and store session information in shared cache.
    - This can decrease the latency.

we need to consider the uses cases while deciding whether to go with stateless or stateful replication.

## Replication Types

- Single leader
- Multi leader
- Leaderless replication

Select your database as per your need.

Refer [this](../designing-data-intensive-applications/5-replication.md)
and [this](../distributed-systems/5.1-replication.md) for more information.

## More things to consider

- Caching
- Async service calls
- Read replicas
- Partitioning
    - Vertical
        - More on modularizing the services and keep its own DB.
        - If we divided services in 10 based on business domain then max 10 partitions can be formed.
    - Horizontal
        - More of data sharding
        - Refer [this](../designing-data-intensive-applications/6-partitioning.md) for more information
- Load balancing
- Auto scaling

## Microservices

### Transactions is the pain point.

Ways of achieving transactions:

- 2PC
- SAGA (Sync communication)
- Event based transactions