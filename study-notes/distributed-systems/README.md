# Lectures: Distributed Systems

- Introduction to distributed systems; RPC. Avantages and challenges of distributed systems; unbounded delay and partial
  failure; network protocols; transparency; client-server systems; remote procedure call (RPC); marshalling; interface
  definition languages (IDLs).
- Introduction to distributed systems; RPC. Avantages and challenges of distributed systems; unbounded delay and partial
  failure; network protocols; transparency; client-server systems; remote procedure call (RPC); marshalling; interface
  definition languages (IDLs).
- Time, clocks, and ordering of events. Physical clocks; leap seconds; UTC; clock synchronisation and drift; Network
  Time Protocol (NTP). Causality; happens-before relation.
- Logical time; Lamport clocks; vector clocks. Broadcast (FIFO, causal, total order); gossip protocols.
- Replication. Quorums; idempotence; replica consistency; read-after-write consistency. State machine replication;
  leader-based replication.
- Consensus and total order broadcast. FLP result; leader election; the Raft consensus algorithm.
- Replica consistency. Two-phase commit; relationship between 2PC and consensus; linearizability; ABD algorithm;
  eventual consistency; CAP theorem.