# Performance

Keep workload and hardware parameters fixed to accurately gauge system performance.

**Goal:**

- If we increase workload, performance should not be degraded a lot.
- Same if we increase hardware capacity performance should increase.

Now will focus on how to get rid of performance problems, but first understand the performance problems.

**Performance Problems:**

- Performance problem is the result of some requests queue building up somewhere.
    - Like webSocket, network socket, OS run queue, DB or any intermediate component like queue.
- Question is why queue build up, reasons can be,
    - Inefficient slow processing
    - Serial resource access
    - limited resource capacity, e.g. limited cpu or memory
- So to increase the performance we need to indentify these queue and build up and avoid it.

**System Performance Objective:**

- Minimize request-response latency
- Maximise the throughput

Latency:

- We measure latency in time unit.
- How much time request/response takes in the system.
- Latency = Wait time + processing time
- So to decrease latency , decrease wait time and processing time.

Throughput:

- Measure number of request processed in some time.
- Directly depends on latency
- To increase throughput , decrease latency
- Also, we can increase throughput by adding more capacity.
- For batch processing we deal with mostly throughput

So in general as an architect we need to decrease latency, which will also increase throughput.

**We will discuss two kind of Requests**

- Serial request
- Concurrent request

While optimising the performance we will first make sure serial request are optimised correctly and then later we will
focus on concurrent request

**Let's see for serial request what are the possible cause for latency,**

1. Network Latency

Overheads of network latency:

- Network hops
    - If we have to call api at EU region from India region
    - There will be lot of network hops and which will cause a big delay
    - Solution:
        - We can keep servers close to users.

- TCP Connections
    - Now when we call any APIs we need to established TCP connections
    - For each TCP connections there will be two round trips
    - If TCP is over SSL/TLS, there will be 4 round trips
    - Solution:
        - We cannot reduce the connection time.
        - But we can use connection pools which will reduce the number of connections and same connection can be reused.
        - Now, Browsers maintain connection pool post http 1.1
        - We can reduce the size of the data which we are transferring.
        - We can even avoid the data transfer by using cache.
        - Use efficient data format like binary format like Avro.
        - Use compression of data.(Even though compression and uncompression takes some cpu cycles but it is
          significantly lower.)

2. Memory access latency

Overheads of Memory access latency:

- Finite heap memory.
    - Processes use heap to allocating objects.
    - So processes can use finite amount of memory.
    - When heap memory is above the allocated memory, the OS uses hard disk to allocate that extra memory.(_Large heap
      memory_)
    - Solution:
        - Avoid Memory bloat
            - Processes should have less memory both in terms of code base also.
            - AS code base is loaded into the memory.
            - So if number of instructions are less then back and forth is also less.
            - So smaller code base helps
        - Weak/soft reference
            - Useful when using large objects
            - Use of soft references are useful
            - Like in java we can reference objects as weak reference.
            - GC also deletes these objects
            - It is duty of process to check if object exists before use.

- GC
    - If processes is about to go out of memory.
    - Then GC runs aggressively.
    - When GC runs it cause processes to wait.
    - Solution:
        - Multiple smaller processes
        - Multiple smaller processes are better than one big process.
        - As if 1 process (JVM) with 40 GB will make GC worse.

- Finite Buffer memory on DB.
    - Caused by inefficient schema
    - Improper indexing and sqls
    - Solutions:
        - Use normalization of data (trade off)
        - Use Compute over storage
            - If data can be computed by logic then don't store the computed data (trade off)

3. Disk Access Latency

It is very important as disk I/O is the slowest I/O, and every component in the system do disk i/o as they do logging.

Also web aps and DB uses a lot of disk i/o

_Solutions to address disk latency:_

Logging issue:

- Log data in batch which will reduce the back and forth.
- Do logging in async
- Also, as logging is sequential write, so it is fast.

Web content files:

- Web content contains lot of static data like css, html, images
- So we can either use caching here using reverse proxy.

DB disk access latency:

- Use cache
- Proper balance between normalisation and de-normalisation.
- Proper indexes. (No need to scan)
- Query optimisation
- Use SSD disk
- Can also use disk with RAID feature.


4. CPU Latency

CPU latency happens mostly because of,

- Inefficient Algorithm
- Context switching.
    - In multiple threads context switching can happen.
    - So use batch I/o so there will be less switching.
    - Can use thread pools

5. Locking

- Reduce the duration of lock hold
- Split lock to more granular level
- Replace exclusive lock
    - Use read/write lock (Multiple reads a a time , when write takes lock no other threads are allow)
    - Use CAS (Compair and swap)

There are 2 ways lock can be acquired:

- Pessimistic lock
    - Here, we fetch data and lock it
    - we process the data
    - commit
    - and release the lock
    - Hence, lock hold is more.

- Optimistic lock
    - Fetch the data
    - Process data
    - GEt lock when updating the data
        - In update compare like quantity is greater than 0 or (old value, is equal to what is expected) , only then
          update (Compare and swap)
        - Else repeat from beginning

6. Caching

Caching we can do at different levels

- Browsers
- static data cache at reverse proxy
- Session cache
- object cache