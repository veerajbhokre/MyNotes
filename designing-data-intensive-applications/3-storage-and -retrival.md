# Storage and retrieval

Databases need to do two things: store the data and give the data back to you.

## Data structures that power up your database

- Many databases use a log, which is append-only data file.
- A log is an append-only sequence of records
- Real databases have more issues to deal with tho (concurrency control, reclaiming disk space so the log doesn't grow
  forever and handling errors and partially written records).

---

## Index

- In order to efficiently find the value for a particular key, we need a different data structure: an index.
- An index is an additional structure that is derived from the primary data.
- Well-chosen indexes speed up read queries but every index slows down writes.
- That's why databases don't index everything by default, but require you to choose indexes manually using your
  knowledge on typical query patterns.

### Hash Indexes

- Key-value stores are quite similar to the dictionary type (hash map or hash table).
- Let's say our storage consists only of appending to a file.
- The simplest indexing strategy is to keep an in-memory hash map where every key is mapped to a byte offset in the data
  file.
- Whenever you append a new key-value pair to the file, you also update the hash map to reflect the offset of the data
  you just wrote.
- **Bitcask** (the default storage engine in Riak) does it like that.
- The only requirement it has is that **all the keys fit in the available RAM.**
- Also, Values can use more space than there is available in memory, since they can be loaded from disk.
- A storage engine like Bitcask is well suited to situations where the value for each key is updated frequently.
- There are a lot of writes, but there are too many distinct keys, you have a large number of writes per key, but it's
  feasible to keep all keys in memory.

### As we only ever append to a file, so how do we avoid eventually running out of disk space?

- A good solution is to break the log into segments of certain size by closing the **segment file** when it reaches a
  certain size
- and making subsequent writes to a new segment file
- We can then perform **compaction** on these segments.
  - Compaction means throwing away duplicate keys in the log, and keeping only the most recent update for each key.
- We can also merge several segments together at the same time as performing the compaction
- Segments are never modified after they have been written, so the merged segment is written to a new file.
- **Merging and compaction** of frozen segments can be done in a background thread.
- After the merging process is complete, we switch read requests to use the new merged segment instead of the old
  segments, and the old segment files can simply be deleted.

**Each segment now has its own in-memory hash table, mapping keys to file offsets.**

- In order to find a value for a key, we first check the most recent segment hash map
- if the key is not present we check the second-most recent segment and so on
- The merging process keeps the number of segments small, so lookups don't need to check many hash maps.

**Some issues that are important in a real implementation:**

- File format. It is simpler to use binary format.
- Deleting records. Append special deletion record to the data file (tombstone) that tells the merging process to
  discard previous values.
- Crash recovery. If restarted, the in-memory hash maps are lost. You can recover from reading each segment but that
  would take long time.
  - Bitcask speeds up recovery by storing a snapshot of each segment hash map on disk.
- Partially written records. The database may crash at any time. Bitcask includes checksums allowing corrupted parts of
  the log to be detected and ignored.
- Concurrency control. As writes are appended to the log in a strictly sequential order, a common implementation is to
  have a single writer thread
  - Segments are immutable, so they can be read concurrently by multiple threads.

**Append-only design turns out to be good for several reasons:**

- Appending and segment merging are sequential write operations, much faster than random writes, especially on magnetic
  spinning-disks.
- Concurrency and crash recovery are much simpler.
- Merging old segments avoids files getting fragmented over time.

**Hash table has its limitations too:**

- The hash table must fit in memory. It is difficult to make an on-disk hash map perform well.
- Range queries are not efficient.

---

## SSTables and LSM-Trees

- We introduce a new requirement to segment files: we require that the sequence of key-value pairs is sorted by key.
- **We call this Sorted String Table, or SSTable.**
- We require that each key only appears once within each merged segment file (compaction already ensures that).

**SSTables have few big advantages over log segments with hash indexes:**

- Merging segments is simple and efficient
  - (we can use algorithms like mergesort)
  - When multiple segments contain the same key, we can keep the value from the most recent segment and discard the
    values in older segments.
- You no longer need to keep an index of all the keys in memory.
  - For a key like handiwork, when you know the offsets for the keys handback and handsome, you know handiwork must
    appear between those two.
  - You can jump to the offset for handback and scan from there until you find handiwork, if not, the key is not
    present.
  - You still need an in-memory index to tell you the offsets for some of the keys.
  - One key for every few kilobytes of segment file is sufficient.
- Since read requests need to scan over several key-value pairs in the requested range anyway, it is possible to **group
  those records into a block and compress it** before writing it to disk.

**How do we get the data sorted in the first place?**

- With red-black trees or AVL trees, you can insert keys in any order and read them back in sorted order.
- When a write comes in, add it to an in-memory balanced tree structure **(memtable).**
- When the memtable gets bigger than some threshold (megabytes), write it out to disk as an SSTable file.
- Writes can continue to a new memtable instance.
- On a read request, try to find the key in the memtable, then in the most recent on-disk segment, then in the
  next-older segment, etc.
- From time to time, run merging and compaction in the background to discard overwritten and deleted values.

If the database crashes, the most recent writes are lost.

- We can keep a separate log on disk to which every write is immediately appended.
- That log is not in sorted order, but that doesn't matter, because its only purpose is to restore the memtable after
  crash.
- Every time the memtable is written out to an SSTable, the log can be discarded.

**LSM (Log Structure Merge-Tree):**
> Storage engines that are based on this principle of merging and compacting sorted files are often called LSM structure engines (Log Structure Merge-Tree).

- Lucene, an indexing engine for full-text search used by Elasticsearch and Solr, uses a similar method for storing its
  term dictionary.
- LSM-tree algorithm can be slow when looking up keys that don't exist in the database:
  - To optimise this, storage engines often use additional Bloom filters (a memory-efficient data structure for
    approximating the contents of a set).
- LevelDB , RocksDB , LevelDB and RocksDB, Casandra uses the same LSM algorithm, with different strategies.

---

## B-trees

- This is the most widely used indexing structure.
- B-tress keep key-value pairs sorted by key, which allows efficient key-value lookups and range queries.
- The log-structured indexes break the database down into variable-size segments typically several megabytes or more.
- B-trees break the database down into fixed-size blocks or pages, traditionally 4KB.
- One page is designated as the root and you start from there. The page contains several keys and references to child
  pages.
- If you want to update the value for an existing key in a B-tree, you search for the leaf page containing that key,
  change the value in that page, and write the page back to disk
- If you want to add new key, find the page and add it to the page.
- If there isn't enough free space in the page to accommodate the new key, it is split in two half-full pages, and the
  parent page is updated to account for the new subdivision of key ranges.
- Trees remain balanced. A B-tree with n keys always has a depth of O(log n).

The basic underlying write operation of a B-tree is to overwrite a page on disk with new data.

- It is assumed that the overwrite does not change the location of the page, all references to that page remain intact.
- This is a big contrast to log-structured indexes such as LSM-trees, which only append to files.

Some operations require several different pages to be overwritten. When you split a page, you need to write the two
pages that were split, and also overwrite their parent. If the database crashes after only some of the pages have been
written, you end up with a corrupted index.

It is common to include an additional data structure on disk: a write-ahead log (WAL, also know as the redo log).

Careful concurrency control is required if multiple threads are going to access, typically done protecting the tree
internal data structures with latches (lightweight locks).

---

## B-trees and LSM-trees

- LSM-trees are typically faster for writes, whereas B-trees are thought to be faster for reads.
- Reads are typically slower on LSM-tress as they have to check several different data structures and SSTables at
  different stages of compaction.

Advantages of LSM-trees:

- LSM-trees are typically able to sustain higher write throughput than B-trees, party because they sometimes have lower
  write amplification
- LSM-trees can be compressed better, and thus often produce smaller files on disk than B-trees.
- B-trees tend to leave disk space unused due to fragmentation.

Downsides of LSM-trees:

- Compaction process can sometimes interfere with the performance of ongoing reads and writes.
- The bigger the database, the the more disk bandwidth is required for compaction.
- Compaction cannot keep up with the rate of incoming writes, if not configured properly you can run out of disk space.

B-trees can be more predictable.

- On B-trees, each key exists in exactly one place in the index.
- This offers strong transactional semantics.
- Transaction isolation is implemented using locks on ranges of keys, and in a B-tree index, those locks can be directly
  attached to the tree.

---

## Other indexing structures

- We've only discussed key-value indexes, which are like primary key index.
- There are also **secondary indexes.**
- A secondary index can be easily constructed from a key-value index
- The main difference is that in a secondary index, the indexed values are not necessarily unique.
- There are two ways of doing this:
  - making each value in the index a list of matching row identifiers
  - by making a each entry unique by appending a row identifier to it.

## Full-text search and fuzzy indexes

- Indexes don't allow you to search for similar keys, such as misspelled words.
- Such fuzzy querying requires different techniques.
- Full-text search engines allow synonyms, grammatical variations, occurrences of words near each other.
- Lucene uses SSTable-like structure for its term dictionary. Lucene, the in-memory index is a finite state automaton,
  similar to a trie.

## Keeping everything in memory

- Disks have two significant advantages: they are durable, and they have lower cost per gigabyte than RAM.
- It's quite feasible to keep them entirely in memory, this has lead to in-memory databases.
- Key-value stores, such as Memcached are intended for cache only, it's acceptable for data to be lost if the machine is
  restarted.
- Other in-memory databases aim for durability, with special hardware, writing a log of changes to disk, writing
  periodic snapshots to disk or by replicating in-memory sate to other machines.
- When an in-memory database is restarted, it needs to reload its state, either from disk or over the network from a
  replica.
- The disk is merely used as an append-only log for durability, and reads are served entirely from memory.
- Products such as VoltDB, MemSQL, and Oracle TimesTime are in-memory databases. Redis and Couchbase provide weak
  durability.
- In-memory databases can be faster because they can avoid the overheads of encoding in-memory data structures in a form
  that can be written to disk.
- Another interesting area is that in-memory databases may provide data models that are difficult to implement with
  disk-based indexes. eg : sets

---

## Transaction processing or analytics?

- A transaction is a group of reads and writes that form a logical unit, this pattern became known as online transaction
  processing (OLTP).
- Data analytics has very different access patterns. A query would need to scan over a huge number of records, only
  reading a few columns per record, and calculates aggregate statistics.
- These queries are often written by business analysts, and fed into reports.
- This pattern became known for online analytics processing (OLAP).

### Data warehousing

- A data warehouse is a separate database that analysts can query to their heart's content without affecting OLTP
  operations.
- It contains read-only copy of the data in all various OLTP systems in the company.
- Data is extracted out of OLTP databases (through periodic data dump or a continuous stream of update)
- transformed into an analysis-friendly schema,
- cleaned up, and then loaded into the data warehouse
- This process is Extract-Transform-Load or ETL
- A data warehouse is most commonly relational, but the internals of the systems can look quite different.

Examples:

Amazon RedShift is hosted version of ParAccel. Apache Hive, Spark SQL, Cloudera Impala, Facebook Presto, Apache Tajo,
and Apache Drill. Some of them are based on ideas from Google's Dremel.

**Star Schema**

Data warehouses are used in fairly formulaic style known as a **star schema.**

- **Facts** are captured as individual events, because this allows maximum flexibility of analysis later.
- The fact table can become extremely large.
- Dimensions represent the who, what, where, when, how and why of the event.
- The name "star schema" comes from the fact than when the table relationships are visualised, the fact table is in the
  middle, surrounded by its dimension tables, like the rays of a star.
- Fact tables often have over 100 columns, sometimes several hundred. Dimension tables can also be very wide.

### Column-oriented storage

Some drawback of row oriented storage especially in star schema, as it contains many columns:

- In a row-oriented storage engine, when you do a query that filters on a specific field, the engine will load all those
  rows with all their fields into memory, parse them and filter out the ones that don't meet the requirement.
- This can take a long time.

Solution is column oriented storage:

- Column-oriented storage is simple: don't store all the values from one row together,but store all values from each
  column together instead
- If each column is stored in a separate file, a query only needs to read and parse those columns that are used in a
  query, which can save a lot of work.
- Column-oriented storage often lends itself very well to compression as the sequences of values for each column look
  quite repetitive, which is a good sign for compression.
  - A technique that is particularly effective in data warehouses is bitmap encoding.
- **Cassandra and HBase** have a concept of column families, which they inherited from Bigtable.
- Besides reducing the volume of data that needs to be loaded from disk, column-oriented storage layouts are also good
  for making efficient use of CPU cycles (vectorised processing).

**Column-oriented storage, compression, and sorting helps to make read queries faster and make sense in data warehouses,
where most of the load consist on large read-only queries run by analysts. The downside is that writes are more
difficult.**

An update-in-place approach, like B-tree use, is not possible with compressed columns. If you insert a row in the middle
of a sorted table, you would most likely have to rewrite all column files.

**Materialized view**

- It's worth mentioning materialised aggregates as some cache of the counts ant the sums that queries use most often.
- A way of creating such a cache is with a materialised view,
- on a relational model this is usually called a virtual view: a table-like object whose contents are the results of
  some query.

Drawback:

When the underlying data changes, a materialised view needs to be updated, because it is denormalised copy of the data.
Database can do it automatically, but writes would become more expensive.


