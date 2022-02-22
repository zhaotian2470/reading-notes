# Foundations of Data Systems

## Reliable, Scalable, and Maintainable Applications
concept:
* reliable: tolerating hardware & software faults, human error;
* scalable: measuring load & performance, latency percentiles, throughput;
* maintainable: operability, simplicity & evolvability;

## Data Models and Query Languages
data model: most applications are built by laying one data model on top of another, each layer hides the complexity of the layers below it by providing a clean data model
* relational model:
  * advantages: support many relationships, such as one-to-many, many-to-one, many-to-many; support join;
* document model:
  * advantages: schema flexibility; better performance due to locality; closer to the data structures used by the application;
  * disadvantages: don't support some relationships, such as many-to-one, many-to-many;
* graph-like model: handle complex many-to-many relationships;
  * property graph model: 
    * vertex: unique identifier, a collection of properties, a set of outgoing edges, a set of incoming edges;
	* edge: unique identifier, a collection of properties, label to describe the kind of relationship between the two verties, tail vertex, head vertex;
  * triple-store model:
    * all information is stored in the form fo three-part statement: (subject, predicate, object), object could be primitive datatype or another vertex in the graph;

query language:
* imperative: tell the computer to perform certain operations in a certain order;
  * examples: map-reduce;
* declarative: specify the pattern of the data you want;
  * examples: SQL, Cypher(borrow from SPARQL);

## Storage and Retrieval
hash indexes: keep an in-memory hash map where every key is mapped to byte offset in the data file
* advantage: 
  * appending and segment merging are sequential write operations; 
  * concurrency and crash recovery are much simpler if segment files are append-only or immutable;
  * merging old segments avoids fragment;
* disadvantage:
  * the hash table must fit in memory;
  * range queries are not efficient;

SSTables and LSM-Trees: 
* implements:
  * when a write comes in, add it to memtable (an in-memory tree);
  * when memtable gets bigger, write it to disk as an SSTable(Sorted String Table) file;
  * from time to time, merging and compaction segment files;
  * when read comes, search memtable and all SSTables;
* advantage:
  * higher write throughput: sequential write; lower wirte amplification;
  * less fragment;
* disadvantage:
  * background merging/compacting operations may impact request and get higher percentiles;
  * background merging/compacting operations may impact throughput;

B-Trees:
* advantage:
  * support transactional semantics;

data warehousing: used by analytics
* data model: stars and snowflakes;
* storage: column-oriented;

## Encoding and Evolution
encoding data:
* textual formats, such as XML, JSON
  * advantage: human-readable;
  * disadvantage: ambiguity around the encoding of numbers; don't support binary strings; waster CPU/storage resources;
* binary encoding, such as Thrift(just like Protocol Buffer), Protocol Buffer(tag in data), Avro(no tag in data, used by database dumping)
  * advantage: less CPU/storage resources;
  * disadvantage: not human-readable;

dataflow mode:
* databases: sending a message to your future self;
* message passing: it is asynchronous;
* service calls: it is synchronous, such as REST and RPC;

# Distributed Data
scaling to higher load:
* scaling up:
  * shared-memory: cost grows faster than linearly; limited fault tolerance;
  * shared-disk: limited scalability because of locks;
* scaling horizontal(shared-nothing): complexy applications

common ways data is distributed across multiple nodes:
* replication: improve scalability and reliability;
* partitioning: improve scalability;

## Replication
single leader:
* work flow: 
  * write: data is written to leader; leader sends change to followers; 
  * read: from leader or follower;
* replication mode:
  * synchronnous: follower is guaranteed to have up-to-date data, but write cannot be processed becaused of network patition;
  * asynchronous: a write is not guaranteed to be durable;
* implementation of replication logs:
  * statement-based: nondeterministic stament make data leader/followers different, used in MySQL before version 5.1;
  * write-ahead log(physical log): leader/followers is limited by software version and storage engine, this method is used in PostgreSQL and Oracle;
  * row-based log(logical log): such as MySQL's binlog;
  * trigger-based: used in more flexibility circumstances, such as recplicate a subset of the data, from one kind of database to another;

problems with replication lag(target to transaction):
* read your own writes(read after write): read from leader; read record more recent than write timestamp;
* monotonic reads(read many times): read from the same replica;
* consistent prefix reads(write many times): write anything causally related to the same patition;

solutions for replication lag:
* application code: complex and easy to get wrong;
* database transaction: distributed transaction is expensive in terms of performance and availability;

multi-leader replication:
* use cases: multi-datacenter operation; clients with offline operation; collaborative editing;
* topologies:
  * circular: one failed node prevent others from synchronizing;
  * star: like circular;
  * all-to-all: complex;

handling write conflicts(target to eventual consistency):
* conflict avoidance;
* converging toward a consistent state(auto): overwrite; merge;
* custom conflict resolution(manual): on write and runs in  a background process; on read;

handling node failure(target to eventual consistence): 
* read repair: rarely read data may be missing from some replicas;
* anti-entropy process: it may be a significant delay before data is copied;

leaderless replication: read from/write to quorum replications
* quorum consistency limitations: a write only succeeded on minor replicas; a write succeeded on quorum replicas but some replicas lost new value;
* sloppy quorum: accept request when quorum node is failure;

## Partitioning
partitioning of key-value data:
* partitioning by key range: key is in order, but certain access patterns can lead to hot spots;
* partitioning by hash of key: it is good at distributing keys, but can not do efficient range queries;
  * relieving hot spots: add a random number to the begining or end of the hot spot keys

partitioning and secondary index:
* local index/by document (each partition maintains its own secondary indexes): need scatter/gather read;
* global index/by term (partition global index and store it in many nodes): it is hard to construct synchronous global index;

rebalancing partitions: don't hash mod N
* fixed number of partitions: we can not change partition number;
* dynamic partitioning by: 
  * partition size: suitable for key range/hash partitioned data;
  * node number: need hash-based partitioning;

request routing methods:
* client connect directly to the appropriate node;
* allow clients to contact any node, the node forwards request if necessary;
* send all requests to routing tier;

how to keep track of cluster metadata:
* most rely on seperate coordination service;
* some use protocol to disseminate changes, such as gossip;
* manual;

## Transactions
transaction: a way for an application to group several reads and writes together into a logical unit
* atomicity: abort transaction on error and have all writes discarded;
* consistence: application uses transaction to keep invariants;
* isolation: concurrent transactions are isolated from each other;
  * read committed: 
    * no dirty read: for every object that is written, the database remembers both old and new value;
	* no dirty write: use lock;
  * repeatable read: use snapshot isolation;
  * serializability:
    * actual serial execution: such as redis;
	* pessimistic concurrency control: such as 2PL(two-phase locking);
	* optimistic concurrency control: such as SSI (serializable snapshot isolation);
* durability: transaction will not be forgotten;

## The Trouble with Distributed Systems
philosophies to build large-scale computing systems:
* high-performance computing(HPC): if one node fails, stop the entire cluster workload;
* clouding computing: build fault-tolerance mechanisms into the software;
* traditional datacenter: between HPC and clouding computing;

unreliable network:
* network faults from time to time;
* network faults can half-disconnected;
* network timeouts: bounded(synchronous); unbounded(asynchhronous);

unreliable clocks:
* quartz clock drifts 200 ppm(parts per million); 
* NTP synchronization has 30ms drift over internet;
* leap seconds;
* process pauses;

knowledeg, truth, and lies:
* no byzantine faults: the truth is defined by the majority, and check with fencing token;
* byzantine faults: more than 2/3 nodes must function correctly;

timing assumption system model:
* synchronous model: bounded network delay/process pauses/clock error;
* partially synchronous model(most system uses): behave like synchronous system most of the time, but it sometimes exceeds the bounds;
* asynchronous model: no assumptions;

node failure system model:
* crash-stop faults;
* crash-recovery faults(most system uses);
* byzantine faults;

correctness of an algorithm:
* safety: nothing bad happens;
* liveness: something good eventually happens;

## Consistency and Consensus
consistency guarantees:
* eventual consistency: replicated databases eventually have the same value;
* causality: cause comes before effect, such as snapshot isolation;
* linearizability: make a system appear as if there is only a single copy of the data;
  * single-leader replication (potentially linearizable): read from leader/synchronous follower;
  * multi-leader replication (not linearizable);
  * leaderless replcation (probably linearizable): if write to node 1 in 3 nodes, read A return new value from node 1 and 2, read B return old value from node 2 and 3;
  * consensus algorithms (linearizable);

the cost of linearizability:
* availability: CAP theorem:(when network-partition(P), you can't implement both linearizability consistency (C) and availability (A));
* performance: every CPU core has own cache;

order guarantees:
* causal order: such as snapshot isolation, version vectors;
* total order: 
  * sequene number order: such as sigle-leader's replication log;
  * lamport timestamps: lamport timestamps generate total order, but can't implement something like a uniqueness constraint;
  * total order broadcast: such as zookeeper, etcd;

consensus is equivalent to: linearizable compare-and-set register, total order broadcas;

distributed transactions and consensus:
* 2PC (2 phase commit, if coordinator fails, the cluster may stop to work): such as X/Open XA(eXtended Architecture);
* total order broadcast: leader decide anything by collecting votes from a quorum of nodes, such as Viewstamped Replication, Paxos, Raft, Zab;

limitations of consensus:
* performance impact;
* rely on network timeouts;
* most consensus algorithms assume a fixed set of nodes
