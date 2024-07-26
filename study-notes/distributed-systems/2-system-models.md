# System models

System models captures our assumptions on how nodes and the network behaves. A system model is how we specify our
assumptions about what faults may occur.

- 2 General problem
    * army 1 attacks if and only if army 2 attacks
        * General 1 always attacks, even if no response is received?
            * Send lots of messengers to increase probability that one will get through
            * If all are captured, general 2 does not know about the attack, so general 1 loses
        * General 1 only attacks if positive response from general 2 is received?
            * Now general 1 is safe
            * But general 2 knows that general 1 will only attack if general 2’s response gets through I Now general 2
              is in the same situation as general 1 in option 1
        * The problem is that no matter how many messages are exchanged, neither general can ever be certain that the
          other army will also turn up at the same time. A repeated sequence of back-and-forth acknowledgements can
          build up gradually increasing confidence that the generals are in agreement, but it can be proved that they
          cannot reach certainty by exchanging any finite number of messages.

- The Byzantine generals problem
    * Here we can have 3 or more generals
    * We assume that if message is sent it will always deliver correctly.
    * But some general might be corrupt and might mislead other generals.
    * The Byzantine generals problem is then to ensure that all honest generals agree on the same plan
    * By definition, it is impossible to specify what the malicious generals are going to do, so the best we can manage
      is to get the honest generals to agree
    * Each general is either malicious or honest
    * Up to f generals might be malicious
    * Honest generals don’t know who the malicious ones are
    * Now theorem is max 1/3 may be malicious to tolerate the system.

In distributed systems, some systems explicitly deal with the possibility that some nodes may be controlled by a
malicious actor, and such systems are called `Byzantine fault tolerant.`

We have seen two general problems above but in reality in distributed systems both nodes and network can be faulty.

# Describing nodes and network behaviour

Capture assumptions in a system model consisting of:

- Network behaviour (e.g. message loss)
- Node behaviour (e.g. crashes)
- Timing behaviour (e.g. latency)

## Network behaviour

- Network can break
- System model or assumption for network behaviour is
    - Reliable (perfect) links:
      A message is received if and only if it is sent. Messages may be reordered
    - Fair-loss links:
      Messages may be lost, duplicated, or reordered. If you keep retrying, a message eventually gets through.
    - Arbitrary links (active adversary):
      A malicious adversary may interfere with messages
      (eavesdrop, modify, drop, spoof, replay).
- Arbitrary links can be transferred to fair loss links using TLS with assumption that it is not enrely blocked by
  eavesdropper.
- fair loss links can be transferred to reliable links using retry + deduplication.

> Network partition: some links dropping/delaying all messages for extended period of time.
> Any messages sent during a network partition will only be received after the interruption is repaired, which may take a long time

Thus, the assumption of a reliable network link is perhaps not as unrealistic as it may seem at first glance: generally
it is possible for all sent messages to be received, as long as we are willing to wait for a potentially arbitrary
period of time for retries during a network partition.

## Node crash behaviour

We also have to consider the possibility that the sender of a message may crash while attempting to retransmit a
message, which may cause that message to be permanently lost

Each node executes a specified algorithm, assuming one of the following:

- Crash-stop (fail-stop):
  A node is faulty if it crashes (at any moment). After crashing, it stops executing forever.
- Crash-recovery (fail-recovery):
  A node may crash at any moment, losing its in-memory state. It may resume executing sometime later. Data stored on
  disk survives the crash.
- Byzantine (fail-arbitrary):
  A node is faulty if it deviates from the algorithm. Faulty nodes may do anything, including crashing or malicious
  behaviour.

In the case of the network, it was possible to convert one model to another using generic protocols. This is not the
case with the different models of node behaviour. For instance, an algorithm designed for a crash-recovery system model
may look very different from a Byzantine algorithm.

## Timeing behaviour

The three choices we can make here are synchronous, asynchronous, or partially synchronous

> Note: confusingly, these terms are also used with a different meaning in other contexts. For example, in the context of RPC and I/O operations, “synchronous” often means “the caller blocks/waits for the operation to complete”, and “asynchronous” means “the caller continues executing after issuing a request, without waiting for the result”. It’s unfortunate that the same words are used with different meanings, but since these terms are widely used in the literature, we will stick with the standard terminology.

- Synchronous:
  is what we would love to have, Message latency no greater than a known upper bound.Nodes execute algorithm at a known
  speed.
- Asynchronous:
  in which we make no timing assumptions at all: we allow messages to be delayed arbitrarily in the network, and we
  allow arbitrary differences in nodes’ processing speeds:
  Algorithms that are designed for an asynchronous model are typically very robust, because they are unaffected by any
  temporary network interruptions or spikes in latency:
  Unfortunately, some problems in distributed computing are impossible to solve in an asynchronous model
- Partially synchronous:
  In this model, we assume that our system is synchronous and well-behaved most of the time:
  but occasionally it may flip into asynchronous mode:
  Using it correctly requires care.

# Fault tolerance and highly available system.

- one reason for building distributed systems is to achieve higher reliability than is possible with a single computer.
- The availability of a service is typically measured in terms of its ability to respond correctly to requests within a
  certain time
- Faults (such as node crashes or network interruptions) are a common cause of unavailability.
- In order to increase availability , design system that decrease faults.
- **Fault tolerance** is always relative to the maximum number of faults that can be tolerated:
- a single component becoming faulty would cause an outage of the entire system. Such a component is called a **single
  point of failure (SPOF)**
- fault-tolerant generally systems try to avoid having any SPOF

## Fault (Failure detector)

The first step towards tolerating faults is to detect faults

- Failure detector:
  algorithm that detects whether another node is faulty:
  A failure detector usually detects crash faults:
  Byzantine faults are not always detectable:
  A failure detector works by periodically sending messages to other nodes, and labelling a node as crashed if no
  response is received within the expected time:
  Ideally, we would like a timeout to occur if and only if the node really has crashed (**this is called a perfect
  failure detector** )
  But the absence of a response could also be due to message loss or delay:
  A perfect timeout-based failure detector exists only in a synchronous crash-stop system with reliable links:
  In a partially synchronous system, a perfect failure detector does not exist:
  Moreover, in an asynchronous system, no timeout-based failure exists, since timeouts are meaningless

- **Eventually perfect failure detector:**
  May temporarily label a node as crashed, even though it is correct:
  May temporarily label a node as correct, even though it has crashed:
  But eventually, labels a node as crashed if and only if it has crashed:
  Reflects fact that detection is not instantaneous, and we may have spurious timeouts

  

Exercise 4. Reliable network links allow messages to be reordered. Give pseudocode for an algorithm
that strengthens the properties of a reliable point-to-point link such that messages are received in the order
they were sent (this is called a FIFO link), assuming an asynchronous crash-stop system model.
Exercise 5. How do we need to change the algorithm from Exercise 4 if we assume a crash-recovery
model instead of a crash-stop model?