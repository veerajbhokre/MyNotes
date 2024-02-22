# Design Key Value Store

Step 1: Requirements

Functional Requirement:

- put(key, value);
- value = get(key);
- value -> Can be String, primitive, any object

Non Functional Requirement:

- Max size of key-value -> 10 KB
- Low latency
- High Availability
- Tunable consistency
- Option to scale the system
- Also support auto scalability

Also do capacity estimation for storage

Step 2: Single server implementation

Talk about log append only with hash map which contains data about byte offset. Also talk about SSTable. More
reference [here](../designing-data-intensive-applications/3-storage-and%20-retrival.md)

Step 3: Make system distributed

Partition and Replication:

- Data partition usinf consistent hashing
- Data replication (Also talk about selecting multiple replicas on hash ring of consistent hashing)
- Talk about Replication with single leader and leaderless approaches.
- Also talk about concurrent writes scenario in both approaches

Consistency:

- Talk about CAP
- Talk about Single leader Adv and disadvantages
    - Adv: No inconsistent data as all writes from leader.
    - Dis: Selection leadter in case of failover. -> Need to implement concensus algo.
- Talk about Leaderless replication:
    - Inconsistent data resolution using vector clock
    - Sync using reconcile of data or read repair.
    - Then talk about strong consistenc and eventual consistency ways with tuning parameters.

Handling Failures:

- Failure Detection:
    - Detection of node failure using _Gossip protocol._
    - N0 sends heart beat to random nodes.
    - And maintains list of nodes and there hearthbeat count.
    - If it notises that N2 is failed then it sends to other random nodes to verify if N2 fails, if get confirmation
      that N2 fails then marks N2 as failed.

- Handling temporary failures of nodes:
    - Use sloppy quorom
    - And also use hinted handoff
        - HH means if replica is not avlable for some time another replica will take care of iyts responsibility for
          some time and once it is up it gets its data from that node and start working.
        - To select the new node for hinted handoff we can choose the next node on hash ring.

- Handling permanant failures: (Need to read more on Merkle tree and anti antropy)
    - If node goes down and never comes back then , we can use anti-antropy technique or reconcile to syn data accross
      all nodes.
    - Merkel tree is one way of implementing it.

- Handling datacenter failures:
    - keep nodes in mulitiple datacenters. dd