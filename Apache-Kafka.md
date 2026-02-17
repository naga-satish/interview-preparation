# Comprehensive Kafka Interview Questions by Topic

## Kafka Fundamentals

### 1. What is Apache Kafka and what are its main use cases?

**Answer:** Apache Kafka is a distributed event streaming platform designed for high-throughput, fault-tolerant, real-time data pipelines and streaming applications.

**Main use cases:**
- **Event streaming**: Real-time data pipelines between systems
- **Log aggregation**: Collecting logs from multiple services
- **Metrics and monitoring**: Real-time analytics and monitoring dashboards
- **Stream processing**: Real-time data transformation and enrichment
- **Message queue**: Decoupling microservices with asynchronous messaging
- **Event sourcing**: Capturing state changes as immutable event logs
- **CDC (Change Data Capture)**: Tracking database changes for downstream systems

### 2. What are the key features and advantages of Kafka?

**Answer:** Kafka's key features include:
- **High throughput**: Handles millions of messages per second with low latency
- **Scalability**: Horizontally scalable by adding brokers and partitions
- **Durability**: Data persisted to disk with configurable replication
- **Fault tolerance**: Automatic failover and data replication across brokers
- **Distributed architecture**: No single point of failure
- **Message retention**: Configurable retention policies (time or size-based)
- **Pull-based consumption**: Consumers control their reading pace
- **Stream processing**: Built-in Kafka Streams library for real-time processing
- **Strong ordering guarantees**: Within partitions, messages maintain order
- **Exactly-once semantics**: Transactional support for reliable processing

### 3. Explain Kafka's architecture and how components interact

**Answer:** Kafka follows a distributed architecture with several key components:
- **Brokers**: Servers that store and serve data; form a cluster
- **Topics**: Logical channels for organizing messages
- **Partitions**: Topics split into partitions for parallelism and scalability
- **Producers**: Applications that publish messages to topics
- **Consumers**: Applications that subscribe to topics and read messages
- **ZooKeeper/KRaft**: Metadata management and cluster coordination

**Interaction flow:**
1. Producers send messages to topic partitions based on keys or round-robin
2. Brokers store messages in partition logs on disk
3. Consumers pull messages from partitions they're assigned to
4. Consumer groups coordinate to distribute partition consumption
5. ZooKeeper/KRaft maintains broker metadata, leader election, and configuration

### 4. What is the pub-sub messaging model in Kafka?

**Answer:** Kafka implements a publish-subscribe messaging model where producers publish messages to topics without knowing who will consume them, and consumers subscribe to topics to receive messages.

**Key characteristics:**
- **Decoupling**: Producers and consumers are independent
- **Multiple subscribers**: Many consumer groups can read the same topic simultaneously
- **Message persistence**: Messages retained based on retention policy, not consumption
- **Topic-based routing**: Messages organized by topics rather than queues
- **Scalability**: New consumers can subscribe without affecting producers

**Difference from traditional pub-sub:** Kafka retains messages even after consumption, allowing replay and multiple consumer groups to process the same data independently.

### 5. How does Kafka differ from traditional messaging systems like RabbitMQ?

**Answer:** Key differences:

| Aspect | Kafka | RabbitMQ |
|--------|-------|----------|
| **Model** | Log-based, append-only | Queue-based, message deletion after ACK |
| **Consumption** | Pull-based, consumer controls pace | Push-based, broker pushes messages |
| **Message retention** | Time/size-based, regardless of consumption | Deleted after acknowledgment |
| **Ordering** | Guaranteed within partition | Per-queue ordering |
| **Throughput** | Very high (millions/sec) | Moderate |
| **Use case** | Event streaming, big data pipelines | Task queues, request-reply patterns |
| **Replay** | Messages can be replayed | No replay capability |
| **Scalability** | Horizontal via partitions | Vertical, limited horizontal scaling |

**When to choose Kafka:** High-throughput event streaming, log aggregation, data pipelines, multiple consumers needing same data.

### 6. What is a distributed streaming platform?

**Answer:** A distributed streaming platform is a system that processes continuous streams of data in real-time across multiple nodes for scalability and fault tolerance.

**Key capabilities:**
- **Publish and subscribe**: Ingest data streams from multiple sources
- **Store**: Persist streams durably and reliably with replication
- **Process**: Transform, aggregate, and analyze streams in real-time

**Kafka as a streaming platform:**
- **Distributed**: Runs across multiple servers for high availability
- **Scalable**: Handle growing data volumes by adding resources
- **Fault-tolerant**: Data replicated across nodes
- **Real-time**: Low-latency processing (milliseconds)
- **Integrated**: Combines messaging, storage, and stream processing (Kafka Streams)

### 7. Why is Kafka preferred for real-time data processing?

**Answer:** Kafka excels at real-time processing due to:

- **Low latency**: Sub-millisecond message delivery with proper tuning
- **High throughput**: Process millions of events per second
- **Pull-based model**: Consumers control consumption rate, preventing overload
- **Zero-copy optimization**: Efficient data transfer from disk to network
- **Partitioning**: Parallel processing across multiple consumers
- **Stream processing**: Native Kafka Streams API for stateful transformations
- **Message ordering**: Guaranteed order within partitions
- **Durability**: Data persisted without sacrificing performance
- **Horizontal scaling**: Add brokers/partitions to handle increased load

**Real-time use cases:** Fraud detection, recommendation engines, monitoring dashboards, real-time analytics, and live data enrichment.

### 8. What are the main components of Kafka ecosystem?

**Answer:** The Kafka ecosystem consists of:

- **Kafka Core**: Distributed messaging system with brokers, topics, and partitions
- **Kafka Streams**: Java library for building stream processing applications
- **Kafka Connect**: Framework for integrating Kafka with external systems (databases, cloud storage, etc.)
- **Schema Registry**: Centralized schema management for Avro, JSON, Protobuf
- **ksqlDB**: SQL-like query engine for stream processing
- **MirrorMaker**: Tool for replicating data between Kafka clusters
- **Kafka REST Proxy**: HTTP interface for producing/consuming messages
- **Control Center**: Web-based management and monitoring UI (Confluent)
- **ZooKeeper/KRaft**: Cluster coordination and metadata management
- **Monitoring tools**: JMX metrics, Prometheus exporters, Grafana dashboards

## Kafka Core Components

### 1. What is a Kafka broker and what role does it play?

**Answer:** A Kafka broker is a server that stores and serves data in a Kafka cluster.

**Key responsibilities:**
- **Data storage**: Persists messages to disk in topic partitions
- **Serving requests**: Handles produce and fetch requests from clients
- **Replication**: Maintains replicas of partitions for fault tolerance
- **Leader management**: Acts as leader for some partitions, follower for others
- **Cluster membership**: Coordinates with other brokers via ZooKeeper/KRaft
- **Log management**: Manages retention policies, segment deletion, and compaction

**Cluster formation:** Multiple brokers form a cluster, distributing partitions and replicas across nodes. Each broker is identified by a unique `broker.id`. Typical production clusters have 3-100+ brokers depending on throughput and data volume requirements.

### 2. What is a Kafka topic and how is it structured?

**Answer:** A topic is a logical channel or category for organizing related messages in Kafka.

**Structure:**
- **Partitions**: Topics are split into ordered, immutable partitions for parallelism
- **Segments**: Each partition consists of log segments (files on disk)
- **Offsets**: Messages within partitions are identified by sequential offsets
- **Replication**: Each partition has replicas across multiple brokers

**Key characteristics:**
- **Immutable log**: Messages appended only, never modified
- **Retention**: Messages retained based on time or size policies
- **Ordering**: Order guaranteed only within a partition, not across partitions
- **Multiple consumers**: Different consumer groups can read the same topic independently

**Example:** A `user-events` topic with 10 partitions, replication factor 3, and 7-day retention.

### 3. What are producers in Kafka and how do they work?

**Answer:** Producers are client applications that publish messages to Kafka topics.

**How they work:**
1. **Serialization**: Convert messages to byte arrays using configured serializers
2. **Partitioning**: Determine target partition based on key (hash) or round-robin
3. **Batching**: Group messages into batches for efficiency
4. **Compression**: Optionally compress batches (gzip, snappy, lz4, zstd)
5. **Send to broker**: Transmit batches to partition leader
6. **Acknowledgment**: Wait for broker confirmation based on `acks` setting

**Key configurations:**
- `acks`: 0 (no wait), 1 (leader only), -1/all (all in-sync replicas)
- `batch.size`: Maximum batch size in bytes
- `linger.ms`: Wait time to fill batches
- `compression.type`: Compression algorithm
- `retries`: Number of retry attempts on failure

### 4. What are consumers in Kafka and how do they read data?

**Answer:** Consumers are client applications that subscribe to topics and read messages from Kafka.

**How they read:**
1. **Subscribe**: Join a consumer group and subscribe to topics
2. **Partition assignment**: Get assigned specific partitions via rebalancing
3. **Fetch messages**: Pull messages from assigned partitions starting from last committed offset
4. **Deserialize**: Convert byte arrays back to application objects
5. **Process**: Handle messages in application logic
6. **Commit offsets**: Track progress by committing offsets periodically

**Key characteristics:**
- **Pull-based**: Consumers control consumption rate
- **Consumer groups**: Enable parallel processing across multiple consumers
- **Offset management**: Track position using offsets stored in `__consumer_offsets` topic
- **At-least-once by default**: Messages may be redelivered on failure
- **Independent reading**: Multiple consumer groups read independently without affecting each other

### 5. Explain what a Kafka cluster is

**Answer:** A Kafka cluster is a group of Kafka brokers working together to provide distributed, scalable, and fault-tolerant messaging.

**Key characteristics:**
- **Multiple brokers**: Typically 3+ brokers for production environments
- **Data distribution**: Partitions distributed across brokers for load balancing
- **Replication**: Partition replicas spread across brokers for fault tolerance
- **Coordinated management**: Brokers coordinate via ZooKeeper or KRaft
- **Single namespace**: All brokers share the same topics and partition metadata
- **Load balancing**: Clients connect to any broker, which routes requests appropriately

**Benefits:** High availability (survives broker failures), horizontal scalability (add brokers for capacity), and performance (parallel processing across nodes).

## 6. What is the role of ZooKeeper in Kafka?

**Answer:** ZooKeeper serves as the centralized coordination service for Kafka clusters (pre-KRaft mode).

**Key responsibilities:**
- **Cluster membership**: Tracks which brokers are alive and healthy
- **Leader election**: Coordinates partition leader election when brokers fail
- **Configuration management**: Stores topic configurations and broker settings
- **Access control**: Manages ACLs (Access Control Lists) for security
- **Consumer group coordination**: Tracks consumer group membership and offsets (legacy)
- **Metadata storage**: Maintains topic metadata, partition assignments, and ISR lists

**Architecture:** ZooKeeper runs as a separate ensemble (typically 3-5 nodes) that Kafka brokers connect to. However, as of Kafka 3.x, KRaft mode is replacing ZooKeeper.

### 7. What is KRaft mode in Kafka and how does it replace ZooKeeper?

**Answer:** KRaft (Kafka Raft) is Kafka's native consensus protocol that eliminates the dependency on ZooKeeper for cluster coordination.

**Key features:**
- **Built-in consensus**: Uses Raft protocol directly within Kafka brokers
- **Controller quorum**: Dedicated controller nodes manage metadata via Raft
- **Simpler architecture**: One less system to deploy and manage
- **Faster recovery**: Metadata changes propagate faster than ZooKeeper
- **Better scalability**: Supports more partitions per cluster (millions)
- **Unified log**: Metadata stored as event log in internal topics

**Benefits:** Reduced operational complexity, faster startup and recovery times, improved scalability, and single system to monitor. KRaft became production-ready in Kafka 3.3 and is the default in Kafka 4.0+.

### 8. How do brokers handle data storage and retrieval?

**Answer:** Brokers store messages in partition logs on disk and serve them to consumers efficiently.

**Storage mechanism:**
- **Log segments**: Each partition consists of multiple segment files (typically 1GB each)
- **Sequential writes**: Messages appended to active segment for high write throughput
- **Indexed access**: Offset and timestamp indexes enable fast lookups
- **Retention**: Old segments deleted or compacted based on retention policies
- **Page cache**: OS page cache used for efficient reads without broker-side caching

**Retrieval process:**
1. Consumer sends fetch request with topic, partition, and offset
2. Broker locates segment file using offset index
3. Data read from segment file (often served from page cache)
4. Zero-copy transfer sends data directly from disk to network socket
5. Consumer receives batch of messages

**Performance:** Sequential I/O and zero-copy achieve high throughput with minimal CPU overhead.

## Topics and Partitions

### 1. What is a partition in Kafka and why is it important?

**Answer:** A partition is an ordered, immutable sequence of messages within a topic, serving as the fundamental unit of parallelism and scalability.

**Importance:**
- **Parallelism**: Multiple consumers can read different partitions simultaneously
- **Scalability**: Distributes data across brokers for horizontal scaling
- **Ordering**: Guarantees message order within each partition
- **Throughput**: Enables high-throughput by parallel writes and reads
- **Fault tolerance**: Each partition can have multiple replicas across brokers
- **Load distribution**: Balances storage and processing load across cluster

**Structure:** Each message in a partition has a sequential offset (0, 1, 2...) that uniquely identifies it within that partition.

### 2. How do partitions work and how are they distributed?

**Answer:** Partitions function as append-only logs where messages are written sequentially and distributed across brokers for scalability.

**How they work:**
- **Sequential writes**: Messages appended to the end of partition log
- **Immutable**: Once written, messages cannot be modified
- **Offset assignment**: Each message receives an increasing offset number
- **Log segments**: Partition stored as multiple segment files on disk

**Distribution across cluster:**
- **Leader**: One broker serves as leader for each partition, handling reads/writes
- **Replicas**: Configured number of replicas spread across different brokers
- **Even distribution**: Kafka attempts to balance partition leaders across brokers
- **Rack awareness**: Can distribute replicas across racks for better fault tolerance

### 3. How are partitions distributed across a Kafka cluster?

**Answer:** Kafka distributes partitions across brokers to balance load and ensure fault tolerance.

**Distribution strategy:**
- **Round-robin assignment**: Partitions assigned to brokers in circular fashion
- **Leader distribution**: Each broker leads some partitions and follows others
- **Replica placement**: Replicas placed on different brokers than leader
- **Rack awareness**: If configured, replicas distributed across different racks
- **Rebalancing**: When brokers added/removed, partitions may be reassigned

**Example:** Topic with 6 partitions, replication factor 3, on 3-broker cluster:
- Broker 1: P0 (leader), P1 (replica), P2 (replica)
- Broker 2: P1 (leader), P2 (replica), P0 (replica)
- Broker 3: P2 (leader), P0 (replica), P1 (replica)

**Tools:** `kafka-reassign-partitions.sh` for manual redistribution when needed.

### 4. How does Kafka handle message ordering?

**Answer:** Kafka guarantees ordering only within a partition, not across partitions.

**Ordering guarantees:**
- **Within partition**: Messages maintain strict order based on offset sequence
- **Same key**: Messages with same key go to same partition, preserving order
- **Producer order**: Messages from single producer to same partition arrive in send order
- **No cross-partition order**: Messages across different partitions have no ordering guarantee

**Maintaining order:**
- Set `max.in.flight.requests.per.connection=1` on producer for strict ordering
- Use message keys to route related messages to same partition
- Enable `enable.idempotence=true` to maintain order even with retries

**Trade-offs:** Single partition for total ordering limits parallelism. Best practice is to partition by entity ID (user_id, order_id) to maintain per-entity ordering while enabling parallelism.

### 5. What is the relationship between topics and partitions?

**Answer:** Topics are logical groupings composed of one or more partitions, with partitions serving as the physical implementation.

**Relationship:**
- **One-to-many**: One topic contains multiple partitions (configurable)
- **Topic = category**: Represents message category (e.g., "user-events")
- **Partition = storage unit**: Physical log file storing subset of topic's messages
- **Independent consumption**: Each partition can be consumed independently
- **Replication applies to partitions**: Each partition (not topic) is replicated

**Example:** `orders` topic with 10 partitions means 10 independent logs, each storing a subset of order messages. Consumer groups can have up to 10 consumers for parallel processing.

### 6. How do you decide the number of partitions for a topic?

**Answer:** Partition count depends on throughput requirements, consumer parallelism needs, and cluster capacity.

**Factors to consider:**
- **Target throughput**: Divide desired throughput by per-partition throughput (typically 10-30 MB/s)
- **Consumer parallelism**: Maximum consumers in a group equals partition count
- **Broker capacity**: Partitions should distribute evenly across brokers
- **Replication overhead**: More partitions = more network/disk overhead for replication
- **Recovery time**: More partitions = longer recovery time during broker failures

**Formula:** `Partitions = max(desired_throughput / partition_throughput, desired_consumers)`

**Best practices:**
- Start conservatively (6-12 partitions for most topics)
- Ensure partition count is multiple of broker count for even distribution
- Can increase later, but cannot decrease without recreating topic
- Very large clusters: thousands of partitions per broker is manageable

### ### 7. Can you change the number of partitions after topic creation?

**Answer:** You can increase partition count but cannot decrease it without recreating the topic.

**Increasing partitions:**
```bash
kafka-topics.sh --bootstrap-server localhost:9092 \
  --alter --topic my-topic --partitions 20
```

**Important considerations:**
- **Breaking key-based ordering**: New partition count changes key-to-partition mapping
- **Existing data stays**: Messages in old partitions remain there
- **Consumer rebalance**: Triggers rebalancing in consumer groups
- **No guarantee of distribution**: New messages may not evenly distribute immediately

**Cannot decrease partitions because:**
- Data in removed partitions would be lost
- Offsets would become invalid
- Consumer position tracking would break

**Workaround for decreasing:** Create new topic with fewer partitions, migrate consumers, delete old topic.

### ### 8. What is a partitioning key and how does it work?

**Answer:** A partitioning key is an optional field used to determine which partition a message is sent to, ensuring related messages go to the same partition.

**How it works:**
1. Producer includes key when sending message
2. Kafka applies hash function to key: `hash(key) % num_partitions`
3. Result determines target partition number
4. All messages with same key always go to same partition

**Key benefits:**
- **Ordering**: Messages with same key maintain order
- **Co-location**: Related events (same user, same order) stored together
- **Stateful processing**: Enables stateful stream processing by entity

**Example:**
```java
producer.send(new ProducerRecord<>("orders",
    "user123",  // key - all user123 orders go to same partition
    orderData   // value
));
```

**Without key:** Round-robin distribution across partitions (no ordering guarantee).

### ### 9. How does Kafka ensure data is evenly distributed across partitions?

**Answer:** Kafka uses different strategies based on whether messages have keys:

**With keys:**
- **Hash-based**: `hash(key) % num_partitions` deterministically assigns messages
- **Not perfectly even**: Distribution depends on key distribution
- **Consistent**: Same key always goes to same partition

**Without keys (null key):**
- **Round-robin**: Messages distributed cyclically across partitions
- **Batch-level round-robin**: For efficiency, batches rotate rather than individual messages
- **Even distribution**: Achieves balanced load across partitions

**Custom partitioner:**
```java
public class CustomPartitioner implements Partitioner {
    public int partition(String topic, Object key, byte[] keyBytes,
                        Object value, byte[] valueBytes, Cluster cluster) {
        // Custom logic for partition assignment
        return targetPartition;
    }
}
```

**Monitoring:** Use broker metrics to verify partition balance and adjust if skewed.

### ### 10. What happens when you add new partitions to an existing topic?

**Answer:** Adding partitions increases parallelism but has several important implications:

**Immediate effects:**
- **New partition assignment**: Brokers assigned new partitions as leaders/followers
- **Consumer rebalance**: All consumers in groups rebalance to include new partitions
- **Increased parallelism**: Can add more consumers up to new partition count

**Key impacts:**
- **Key-to-partition mapping changes**: Existing key hashing formula now divides by new partition count
- **Ordering breaks**: Messages with same key may now go to different partition than before
- **Historical data unmoved**: Existing messages stay in original partitions
- **Uneven distribution initially**: New partitions start empty while old ones contain historical data

**Best practices:**
- Avoid adding partitions to topics using keyed messages if order matters
- Plan partition count carefully upfront
- If needed, consider creating new topic and migrating

**Example:** Topic with 10 partitions increased to 15. Key "ABC" previously mapped to partition 3, now maps to partition 12.

## Offsets and Message Tracking

### 1. What is an offset in Kafka?

**Answer:** An offset is a unique sequential identifier assigned to each message within a partition, representing its position in the partition log.

**Key characteristics:**
- **Sequential numbering**: Starts at 0 and increments for each message
- **Partition-specific**: Offset 100 in partition 0 is different from offset 100 in partition 1
- **Immutable**: Once assigned, never changes for that message
- **Consumer position**: Consumers track which offset they've processed
- **No expiration**: Offset remains valid as long as message is retained

**Use:** Enables consumers to track progress, resume from last position after restart, and reprocess messages by resetting to earlier offsets.

### 2. How do offsets help in message tracking?

**Answer:** Offsets serve as bookmarks that enable reliable message consumption and recovery.

**Tracking benefits:**
- **Progress tracking**: Consumer knows exactly which messages have been processed
- **Resume capability**: After restart, consumer continues from last committed offset
- **Reprocessing**: Can reset to earlier offset to replay messages
- **Parallel consumption**: Each consumer in group tracks offsets for assigned partitions
- **Fault recovery**: If consumer crashes, new consumer starts from last committed offset
- **Monitoring**: Consumer lag calculated by comparing current offset vs latest offset

**Example:** Consumer processes offset 1000. If it crashes, upon restart it fetches from offset 1001, ensuring no messages are skipped or duplicated (with proper commit strategy).

### 3. What is offset commit and how does it work?

**Answer:** Offset commit is the process of storing a consumer's current position in a partition to enable recovery and progress tracking.

**How it works:**
1. Consumer fetches and processes messages from partition
2. Consumer commits offset to `__consumer_offsets` internal topic
3. Committed offset stored with consumer group ID and partition info
4. On restart, consumer reads last committed offset and resumes from there

**Commit types:**
- **Auto-commit**: Periodic commits based on `auto.commit.interval.ms` (default 5s)
- **Manual commit (sync)**: `consumer.commitSync()` blocks until commit completes
- **Manual commit (async)**: `consumer.commitAsync()` non-blocking with callback

**Storage:** Offsets stored in `__consumer_offsets` topic (50 partitions by default), managed by Kafka itself.

### 4. What are the different offset commit strategies?

**Answer:** Kafka supports multiple offset commit strategies with different trade-offs:

**Automatic commit:**
- **Config**: `enable.auto.commit=true`, `auto.commit.interval.ms=5000`
- **Behavior**: Commits offsets periodically in background
- **Pros**: Simple, no code needed
- **Cons**: At-least-once semantics, potential duplicate processing

**Manual synchronous commit:**
```java
consumer.commitSync();
```
- **Pros**: Guarantees commit before proceeding, better control
- **Cons**: Blocks processing, reduces throughput

**Manual asynchronous commit:**
```java
consumer.commitAsync((offsets, exception) -> {
    if (exception != null) handleError(exception);
});
```
- **Pros**: Non-blocking, higher throughput
- **Cons**: No guarantee of commit before failure

**Best practice:** Combine async commits during processing with sync commit on shutdown for performance and reliability.

### 5. How can consumers manually manage offsets?

**Answer:** Manual offset management provides precise control over when and what offsets are committed.

**Basic manual commit:**
```java
Properties props = new Properties();
props.put("enable.auto.commit", "false");
KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);

while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
    for (ConsumerRecord<String, String> record : records) {
        processRecord(record);
    }
    consumer.commitSync(); // Commit after processing batch
}
```

**Fine-grained control:**
```java
Map<TopicPartition, OffsetAndMetadata> offsets = new HashMap<>();
offsets.put(
    new TopicPartition("topic", 0),
    new OffsetAndMetadata(record.offset() + 1)
);
consumer.commitSync(offsets); // Commit specific offset
```

**Use cases:** Exactly-once processing, external state management, custom retry logic, integration with databases for transactional processing.

### 6. What happens if a consumer fails before committing offsets?

**Answer:** Uncommitted offsets result in message reprocessing when a new consumer takes over the partition.

**Failure scenarios:**

**With auto-commit enabled:**
- Messages processed since last auto-commit will be redelivered
- Results in at-least-once delivery (potential duplicates)
- Duplicate window = `auto.commit.interval.ms` duration

**With manual commit:**
- All messages since last explicit commit will be reprocessed
- If commit after each batch, only that batch reprocessed
- If commit after each message, minimal reprocessing

**Example:** Consumer processes offsets 100-150, commits offset 100, crashes. New consumer starts from offset 101, reprocessing messages 101-150.

**Mitigation:** Use idempotent processing, decrease commit interval, implement exactly-once semantics with transactions, or store offsets externally with application state.

### 7. How do you reset consumer offsets?

**Answer:** Offset reset allows consumers to reprocess messages or skip ahead to specific positions.

**Using kafka-consumer-groups command:**
```bash
# Reset to earliest (beginning of partition)
kafka-consumer-groups.sh --bootstrap-server localhost:9092 \
  --group my-group --topic my-topic --reset-offsets --to-earliest --execute

# Reset to latest (skip to end)
--reset-offsets --to-latest --execute

# Reset to specific offset
--reset-offsets --to-offset 1000 --execute

# Reset by time (e.g., 2 hours ago)
--reset-offsets --to-datetime 2026-02-16T10:00:00.000 --execute

# Shift forward/backward
--reset-offsets --shift-by 100 --execute  # or negative to go back
```

**Programmatically:**
```java
consumer.seek(new TopicPartition("topic", 0), 1000);
```

**Important:** Consumer group must be inactive (all consumers stopped) for command-line reset to work.

### 8. What is the __consumer_offsets topic?

**Answer:** `__consumer_offsets` is an internal Kafka topic that stores consumer group offset commits and metadata.

**Key characteristics:**
- **Partition count**: 50 partitions by default (`offsets.topic.num.partitions`)
- **Replication**: Typically replicated (default RF=3)
- **Compacted**: Uses log compaction to retain only latest offset per partition
- **Key format**: `(group.id, topic, partition)` → offset value
- **Retention**: Offsets retained for inactive groups based on `offsets.retention.minutes`

**Contents:**
- Consumer group offsets for each partition
- Consumer group metadata (members, assignments)
- Tombstone records (null values) for deleted groups

**Partition assignment:** Consumer group hashed to determine which partition stores its offsets: `hash(group.id) % 50`

**Monitoring:** Can read topic to audit consumer positions, but typically accessed via consumer group APIs.

## Producers

### 1. How do Kafka producers send messages to topics?

**Answer:** Producers send messages through a multi-step process involving serialization, partitioning, batching, and transmission.

**Send flow:**
1. **Create record**: Producer creates `ProducerRecord` with topic, optional key/partition, and value
2. **Serialize**: Key and value serialized to byte arrays
3. **Partition**: Determine target partition (via key hash, explicit partition, or round-robin)
4. **Buffer**: Message added to in-memory buffer for target partition
5. **Batch**: Messages accumulated into batches for efficiency
6. **Compress**: Optional compression applied to batch
7. **Send**: Batch transmitted to partition leader broker
8. **Acknowledge**: Broker confirms receipt based on `acks` setting
9. **Callback**: Success/failure callback executed

**Example:**
```java
producer.send(new ProducerRecord<>("topic", "key", "value"),
    (metadata, exception) -> {
        if (exception == null) {
            System.out.println("Sent to " + metadata.partition());
        }
    });
```

### 2. What are producer acknowledgments (acks)?

**Answer:** Producer acknowledgments (acks) control how many broker confirmations are required before considering a message successfully sent.

**Purpose:**
- **Durability control**: Trade-off between performance and data safety
- **Replication guarantee**: Ensures data copied to replicas before acknowledging
- **Failure handling**: Determines when producer considers send successful

**Configuration:**
```java
props.put("acks", "all"); // or "0", "1"
```

**Impact on durability:**
- Higher acks = more durability but lower throughput
- Lower acks = higher throughput but risk of data loss
- Choice depends on use case criticality

**Related configs:** `min.in.sync.replicas` works with `acks=all` to ensure minimum replica copies.

### 3. Explain the different ack levels: 0, 1, and all

**Answer:** Kafka provides three acknowledgment levels with different durability guarantees:

| acks | Behavior | Durability | Performance | Use Case |
|------|----------|------------|-------------|----------|
| **0** | No wait for broker confirmation | Lowest - may lose data | Highest throughput | Metrics, logs where loss acceptable |
| **1** | Wait for leader to write to log | Medium - leader failure may lose data | Medium | Balanced use cases |
| **all/-1** | Wait for all in-sync replicas | Highest - no data loss | Lowest throughput | Critical data, financial transactions |

**Details:**

**acks=0:** Fire-and-forget. Network errors undetected.

**acks=1:** Leader writes to local log but doesn't wait for replicas. Data lost if leader fails before replication.

**acks=all:** Leader waits for all ISR replicas to acknowledge. Combined with `min.insync.replicas=2`, guarantees durability.

**Best practice:** Use `acks=all` with `min.insync.replicas=2` for production critical data.

### 4. What is producer batching and how does it improve performance?

**Answer:** Producer batching groups multiple messages destined for the same partition into a single batch before sending to broker.

**How it works:**
- Messages accumulated in memory buffer up to `batch.size` bytes or `linger.ms` timeout
- Entire batch compressed and sent as single request
- Broker writes batch to log in one operation

**Performance benefits:**
- **Reduced network overhead**: Fewer requests for same message count
- **Better compression**: Larger batches compress more efficiently
- **Higher throughput**: Broker handles fewer, larger writes
- **Lower CPU**: Less per-message processing overhead

**Key configurations:**
```java
props.put("batch.size", 16384);      // 16KB batches
props.put("linger.ms", 10);          // Wait up to 10ms to fill batch
props.put("compression.type", "lz4"); // Compress batches
```

**Trade-off:** Larger batches increase throughput but add latency. `linger.ms=0` minimizes latency but reduces batching benefits.

### 5. What is idempotent producer in Kafka?

**Answer:** Idempotent producer ensures exactly-once delivery to a partition by preventing duplicate messages even with retries.

**Problem it solves:**
Without idempotence, network failures or timeouts can cause producer retries, potentially writing the same message multiple times.

**How it works:**
- Each producer assigned unique Producer ID (PID) by broker
- Producer attaches sequence number to each message
- Broker tracks sequence numbers per PID and partition
- Duplicate sequence numbers rejected, preventing duplicates

**Configuration:**
```java
props.put("enable.idempotence", true);  // Kafka 3.0+ default
```

**Automatic settings when enabled:**
- `acks=all`
- `retries=Integer.MAX_VALUE`
- `max.in.flight.requests.per.connection` ≤ 5

**Limitations:** Idempotence guaranteed only within single producer session and partition. For cross-partition or cross-session guarantees, use transactions.

### 6. How do producers handle failures and retries?

**Answer:** Producers automatically retry failed sends based on configured retry policies, with different handling for retriable vs non-retriable errors.

**Retriable errors (automatically retried):**
- `NOT_LEADER_FOR_PARTITION`: Leader election in progress
- `NETWORK_EXCEPTION`: Temporary network issues
- `REQUEST_TIMED_OUT`: Broker timeout

**Non-retriable errors (fail immediately):**
- `INVALID_CONFIG`: Bad producer configuration
- `RECORD_TOO_LARGE`: Message exceeds `max.message.bytes`
- `AUTHORIZATION_FAILED`: Insufficient permissions

**Retry configuration:**
```java
props.put("retries", Integer.MAX_VALUE);  // Number of retries
props.put("retry.backoff.ms", 100);       // Wait between retries
props.put("delivery.timeout.ms", 120000); // Total time including retries
props.put("max.in.flight.requests.per.connection", 5);
```

**Best practices:**
- Enable idempotence to maintain order during retries
- Set `delivery.timeout.ms` to bound total retry duration
- Monitor failed sends via callbacks
- Implement dead letter queue for permanently failed messages

### 7. What is producer compression and what compression types are supported?

**Answer:** Producer compression reduces message size before transmission, improving throughput and reducing storage requirements.

**Supported compression types:**

| Type | Compression Ratio | CPU Usage | Speed | Best For |
|------|------------------|-----------|-------|----------|
| **none** | None | Lowest | Fastest | Already compressed data |
| **gzip** | Highest | High | Slowest | Network-constrained, storage-sensitive |
| **snappy** | Good | Low | Fast | Balanced performance |
| **lz4** | Good | Lowest | Fastest | High-throughput, low latency |
| **zstd** | Highest | Medium | Medium | Best overall (Kafka 2.1+) |

**Configuration:**
```java
props.put("compression.type", "lz4");
```

**How it works:**
- Compression applied to entire batch (not individual messages)
- Compressed batch sent to broker
- Messages stored compressed on broker disk
- Consumers decompress when reading

**Benefits:** Lower network bandwidth, reduced disk usage, faster replication. **Trade-off:** CPU overhead for compression/decompression.

### 8. How do you configure a high-throughput producer?

**Answer:** High-throughput configuration optimizes batching, compression, and parallelism at the cost of some latency.

**Recommended configuration:**
```java
Properties props = new Properties();
// Maximize batching
props.put("batch.size", 32768);           // 32KB batches
props.put("linger.ms", 20);               // Wait up to 20ms
props.put("buffer.memory", 67108864);     // 64MB buffer

// Compression
props.put("compression.type", "lz4");     // Fast compression

// Parallelism
props.put("max.in.flight.requests.per.connection", 5);

// Durability (with idempotence)
props.put("enable.idempotence", true);
props.put("acks", "all");

// Timeouts
props.put("delivery.timeout.ms", 120000);
```

**Key strategies:**
- **Larger batches**: Increase `batch.size` and `linger.ms` for better batching
- **Compression**: Use `lz4` or `zstd` for efficient compression
- **More buffers**: Increase `buffer.memory` to handle bursts
- **Async sends**: Use callbacks instead of blocking on `get()`
- **Multiple producers**: Partition across multiple producer instances

**Monitoring:** Track `batch-size-avg`, `compression-rate-avg`, `record-send-rate` metrics.

### 9. What is the role of buffer memory in producers?

**Answer:** Buffer memory (`buffer.memory`) is the total memory allocated by the producer for buffering messages before sending to brokers.

**Purpose:**
- **Message accumulation**: Stores messages waiting to be batched and sent
- **Batching space**: Allows efficient batching by holding multiple records
- **Backpressure handling**: Buffers messages during temporary broker slowdowns
- **Per-partition buffers**: Memory divided across partition buffers

**Behavior when full:**
- Producer blocks up to `max.block.ms` (default 60s) waiting for space
- If timeout exceeded, throws `BufferExhaustedException`
- Backpressure propagates to application

**Configuration:**
```java
props.put("buffer.memory", 33554432);  // 32MB default
props.put("max.block.ms", 60000);      // Block up to 60s
```

**Sizing guidance:**
- Default (32MB) sufficient for most use cases
- Increase for high-throughput or bursty workloads
- Monitor `buffer-available-bytes` metric
- Consider: message rate × message size × buffering time needed

### 10. How do you handle serialization in Kafka producers?

**Answer:** Serialization converts Java objects to byte arrays for transmission. Kafka provides built-in serializers and supports custom implementations.

**Built-in serializers:**
```java
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

// Other built-in: IntegerSerializer, LongSerializer, ByteArraySerializer, etc.
```

**Custom serializer:**
```java
public class CustomSerializer implements Serializer<MyObject> {
    @Override
    public byte[] serialize(String topic, MyObject data) {
        // Convert object to bytes
        return objectToBytes(data);
    }
}

props.put("value.serializer", "com.example.CustomSerializer");
```

**Best practices:**
- Use Avro/Protobuf with Schema Registry for schema evolution
- Implement versioning for backward/forward compatibility
- Handle null values appropriately
- Consider JSON for human-readable debugging
- Avoid Java serialization (slow, not cross-language)

**Example with Avro:**
```java
props.put("value.serializer", "io.confluent.kafka.serializers.KafkaAvroSerializer");
props.put("schema.registry.url", "http://localhost:8081");
```

## Consumers and Consumer Groups

### 1. What is a consumer group in Kafka?

**Answer:** A consumer group is a set of consumers that cooperatively consume messages from topics, with each partition assigned to exactly one consumer in the group.

**Key characteristics:**
- **Group ID**: All consumers share same `group.id` configuration
- **Partition assignment**: Each partition consumed by only one consumer in group
- **Load balancing**: Partitions distributed among active consumers
- **Independent groups**: Different groups can consume same topic independently
- **Offset tracking**: Group tracks collective progress per partition

**Example:**
```java
props.put("group.id", "order-processing-group");
```

**Use cases:** Parallel processing, load distribution, fault tolerance through automatic reassignment when consumers fail.

**Multiple groups:** Same topic can be consumed by multiple groups simultaneously for different purposes (e.g., analytics group + ETL group).

### 2. How do consumer groups enable parallel processing?

**Answer:** Consumer groups enable parallelism by distributing partition consumption across multiple consumer instances.

**Parallel processing mechanism:**
- **Partition-level parallelism**: Each partition assigned to one consumer
- **Multiple consumers**: Up to N consumers for topic with N partitions
- **Independent processing**: Consumers process assigned partitions concurrently
- **Automatic rebalancing**: Work redistributed when consumers join/leave
- **Scalability**: Add consumers to increase processing capacity

**Example:** Topic with 10 partitions:
- 1 consumer: Reads all 10 partitions sequentially
- 5 consumers: Each reads 2 partitions in parallel (5x throughput)
- 10 consumers: Each reads 1 partition (maximum parallelism)
- 15 consumers: 5 idle (max parallelism = partition count)

**Throughput formula:** `Total throughput ≈ consumer_count × per_consumer_throughput` (up to partition count)

### 3. What happens when a new consumer joins a consumer group?

**Answer:** When a new consumer joins, Kafka triggers a rebalance to redistribute partitions among all active consumers.

**Join process:**
1. New consumer sends `JoinGroup` request to group coordinator
2. Coordinator detects group membership change
3. All consumers stop processing and enter rebalance
4. Coordinator selects group leader consumer
5. Leader calculates new partition assignment
6. Assignment distributed to all consumers
7. Consumers resume processing with new partitions

**Effects:**
- **Partition redistribution**: Some partitions reassigned from existing consumers to new one
- **Processing pause**: Brief interruption during rebalance
- **Load balancing**: Better distribution across consumers
- **Offset commits**: Consumers commit offsets before rebalance

**Example:** 3 consumers, 9 partitions. Consumer 4 joins. Assignment changes from 3-3-3 to 2-2-2-3 distribution.

### 4. What is consumer rebalancing?

**Answer:** Rebalancing is the process of redistributing partition ownership among consumers in a group when membership changes or partition count changes.

**Triggers:**
- Consumer joins group
- Consumer leaves/crashes
- Consumer deemed dead (exceeds `session.timeout.ms`)
- Topic partition count changes
- Consumer unsubscribes from topics

**Rebalance protocol:**
1. **Stop the world**: All consumers stop processing
2. **Revoke partitions**: Consumers give up current assignments
3. **Reassign**: New assignments calculated and distributed
4. **Resume**: Consumers start processing new assignments

**Impact:**
- **Unavailability**: Group unavailable during rebalance (usually seconds)
- **Duplicate processing**: Messages processed before rebalance may be reprocessed
- **Performance hit**: Frequent rebalances reduce throughput

**Incremental cooperative rebalancing** (Kafka 2.4+): Only affected partitions reassigned, reducing disruption.

### 5. What triggers a rebalance in a consumer group?

**Answer:** Rebalances are triggered by membership changes, failures, or configuration changes.

**Common triggers:**

**Membership changes:**
- New consumer joins group
- Consumer explicitly leaves (`consumer.close()`)
- Consumer crashes or becomes unresponsive

**Health check failures:**
- `session.timeout.ms` exceeded without heartbeat
- `max.poll.interval.ms` exceeded between `poll()` calls
- Consumer takes too long to process messages

**Configuration changes:**
- Topic partition count increased
- Consumer subscribes to new topics
- Consumer unsubscribes from topics

**Prevention strategies:**
```java
props.put("session.timeout.ms", 45000);       // Time before considered dead
props.put("heartbeat.interval.ms", 3000);     // Heartbeat frequency
props.put("max.poll.interval.ms", 300000);    // Max time between polls
```

**Best practices:** Process messages quickly, increase `max.poll.interval.ms` for slow processing, reduce `max.poll.records` to process smaller batches.

### 6. How does Kafka assign partitions to consumers?

**Answer:** Kafka uses partition assignment strategies executed by the consumer group leader to distribute partitions among consumers.

**Assignment process:**
1. Group coordinator identifies all consumers in group
2. One consumer elected as group leader
3. Leader receives list of all consumers and topic metadata
4. Leader applies configured assignment strategy
5. Leader sends assignments back to coordinator
6. Coordinator distributes assignments to consumers

**Assignment goals:**
- Balance partitions evenly across consumers
- Minimize partition movement during rebalances
- Support sticky assignment when possible

**Group coordinator:** Kafka broker responsible for managing consumer group, determined by hashing `group.id`.

**Configuration:**
```java
props.put("partition.assignment.strategy", "org.apache.kafka.clients.consumer.RangeAssignor");
```

Multiple strategies can be configured, with most preferred listed first.

### 7. What are the different partition assignment strategies?

**Answer:** Kafka provides several partition assignment strategies with different trade-offs:

**RangeAssignor (default):**
- Assigns partitions per topic in ranges
- Can lead to uneven distribution across topics
- Example: 10 partitions, 3 consumers → 4, 3, 3 partitions

**RoundRobinAssignor:**
- Distributes partitions evenly across all topics
- Better balance but more partition movement
- Example: 10 partitions → 4, 3, 3 or 3, 3, 4 distribution

**StickyAssignor:**
- Balances partitions evenly
- Minimizes partition movement during rebalances
- Preserves existing assignments when possible
- Best for reducing rebalance overhead

**CooperativeStickyAssignor (recommended):**
- Incremental rebalancing (Kafka 2.4+)
- Only revokes/reassigns affected partitions
- Consumers keep processing unaffected partitions
- Reduces rebalance disruption significantly

**Configuration:**
```java
props.put("partition.assignment.strategy",
    "org.apache.kafka.clients.consumer.CooperativeStickyAssignor");
```

### 8. Can a consumer read from multiple partitions?

**Answer:** Yes, a single consumer can read from multiple partitions concurrently.

**How it works:**
- Consumer assigned multiple partitions during rebalancing
- Fetches messages from all assigned partitions in parallel
- Processes messages from different partitions interleaved
- Maintains separate offset for each partition

**Example scenario:**
```java
// Topic with 10 partitions, 2 consumers in group
// Consumer 1: Assigned partitions 0, 1, 2, 3, 4
// Consumer 2: Assigned partitions 5, 6, 7, 8, 9
```

**Processing pattern:**
- `poll()` returns records from multiple partitions
- Can process in order within each partition
- No ordering guarantee across partitions

**Limitation:** One partition cannot be consumed by multiple consumers in same group simultaneously (but can by different groups).

**Scalability:** Consumer can handle multiple partitions until processing capacity saturated, then add more consumers.

### 9. How many consumers can read from a single partition simultaneously?

**Answer:** Within a consumer group, only one consumer can read from a partition at a time. However, multiple consumer groups can read the same partition simultaneously.

**Within same consumer group:**
- **Maximum 1 consumer per partition**
- Ensures ordered processing
- Prevents duplicate consumption
- Exclusive partition ownership

**Across different consumer groups:**
- **Unlimited consumer groups**
- Each group has independent consumer reading partition
- Groups maintain separate offsets
- Enables multiple processing pipelines

**Example:**
```
Partition 0:
├─ Consumer Group "analytics": Consumer A reads
├─ Consumer Group "etl": Consumer B reads
└─ Consumer Group "audit": Consumer C reads
```

**Implication:** Maximum parallelism within a group = number of partitions. If you have 10 partitions, having more than 10 consumers in a group leaves extras idle.

### 10. What is the maximum number of consumers in a consumer group?

**Answer:** There's no hard limit, but only as many consumers as partitions can actively consume messages.

**Effective limits:**
- **Active consumers**: Equal to partition count
- **Idle consumers**: Unlimited, but wasteful
- **Example**: Topic with 10 partitions → 10 active consumers maximum

**Scenarios:**

**Fewer consumers than partitions (N < P):**
- Each consumer reads multiple partitions
- Under-utilized, potential bottleneck

**Equal consumers and partitions (N = P):**
- Optimal: Each consumer reads one partition
- Maximum parallelism achieved

**More consumers than partitions (N > P):**
- Excess consumers remain idle
- No performance benefit, wasted resources
- Serve as hot standby for failover

**Best practices:**
- Match consumer count to partition count for optimal parallelism
- Use extra consumers only for high-availability standby
- Monitor `assigned-partitions` metric per consumer
- Plan partition count based on expected consumer parallelism needs

## Replication and Fault Tolerance

### 1. What is replication in Kafka?

**Answer:** Replication is the process of maintaining multiple copies of partition data across different brokers to ensure fault tolerance and high availability.

**How it works:**
- Each partition has one leader and multiple follower replicas
- Leader handles all reads and writes
- Followers continuously replicate data from leader
- If leader fails, a follower promoted to leader
- Replication factor determines number of copies

**Key benefits:**
- **Fault tolerance**: Survives broker failures without data loss
- **High availability**: Service continues despite failures
- **Data durability**: Multiple copies prevent data loss
- **No downtime**: Automatic failover to replicas

**Configuration:**
```bash
kafka-topics.sh --create --topic orders \
  --partitions 3 --replication-factor 3
```

**Example:** RF=3 means 1 leader + 2 followers on different brokers.

### 2. What is a replication factor and how do you choose it?

**Answer:** Replication factor (RF) specifies how many copies of each partition exist across the cluster.

**Common configurations:**

| RF | Copies | Failures Tolerated | Use Case |
|----|--------|-------------------|----------|
| 1 | Leader only | 0 | Development, non-critical logs |
| 2 | 1 leader + 1 follower | 1 | Less critical data |
| 3 | 1 leader + 2 followers | 2 | **Production standard** |
| 5 | 1 leader + 4 followers | 4 | Mission-critical systems |

**Choosing replication factor:**
- **Production minimum**: RF=3 (industry standard)
- **Failures to tolerate**: RF = failures + 1
- **Cluster size**: RF ≤ broker count
- **Cost consideration**: RF=3 uses 3x storage
- **Criticality**: Higher RF for financial, compliance data

**Best practice:** RF=3 with `min.insync.replicas=2` balances durability and performance.

**Cannot modify** after topic creation without recreating topic or manual partition reassignment.

### 3. Explain the leader and follower concept in Kafka

**Answer:** Each partition has one leader replica and zero or more follower replicas implementing a leader-follower replication model.

**Leader replica:**
- Handles all produce requests (writes)
- Handles all fetch requests (reads) from consumers
- Maintains the authoritative log for partition
- Tracks follower replication progress
- Stays on one broker

**Follower replicas:**
- Continuously fetch data from leader
- Replicate leader's log exactly
- Do not serve client requests (except with `replica.selector` in newer versions)
- Participate in leader election if in-sync
- Located on different brokers than leader

**Interaction:**
1. Producer sends message to leader
2. Leader appends to local log
3. Followers fetch new messages from leader
4. Once all in-sync replicas acknowledge, leader commits message
5. Message becomes available to consumers

**Partition distribution:** Kafka distributes leaders evenly across brokers for load balancing.

### 4. What is an In-Sync Replica (ISR)?

**Answer:** In-Sync Replica (ISR) is a follower replica that is fully caught up with the leader and eligible for leader election.

**ISR criteria:**
- Successfully fetching messages from leader
- Caught up within `replica.lag.time.max.ms` (default 10s)
- Heartbeating to ZooKeeper/controller regularly
- Leader is always in ISR

**ISR list dynamics:**
- **Replica falls behind**: Removed from ISR if lag exceeds threshold
- **Replica catches up**: Added back to ISR when fully synchronized
- **Tracked by leader**: Leader maintains ISR list and reports to controller

**Importance:**
- Only ISR replicas eligible for leader election (by default)
- `acks=all` waits for all ISR replicas, not all replicas
- ISR shrinking indicates replication issues

**Monitoring:**
```bash
kafka-topics.sh --describe --topic orders
# Shows Leader, Replicas, and ISR for each partition
```

**Example:** RF=3, but one follower lagging → ISR contains only leader + 1 follower.

### 5. How does leader election work in Kafka?

**Answer:** When a partition leader fails, Kafka automatically elects a new leader from the in-sync replicas.

**Election process:**
1. **Failure detection**: Controller detects leader broker failure via ZooKeeper/KRaft
2. **Select new leader**: Controller chooses first available ISR replica as new leader
3. **Update metadata**: Controller updates cluster metadata with new leader
4. **Notify brokers**: All brokers receive metadata update
5. **Clients reconnect**: Producers/consumers redirect to new leader

**Leader selection criteria:**
- Must be in ISR list
- Prefers replica with highest offset (most up-to-date)
- First in ISR list typically chosen

**Controller role:**
- One Kafka broker elected as cluster controller
- Controller manages all leader elections
- If controller fails, new controller elected

**Timing:** Leader election typically completes in milliseconds to a few seconds.

**Unclean election:** If no ISR available, can elect out-of-sync replica (`unclean.leader.election.enable=true`) but risks data loss.

### 6. What happens when a broker fails?

**Answer:** Broker failure triggers automatic recovery with partition reassignment and leader election.

**Immediate effects:**
- Broker becomes unavailable
- Client connections to broker lost
- Partitions where broker was leader become unavailable briefly

**Recovery process:**

**For partitions where failed broker was leader:**
1. Controller detects failure
2. New leader elected from ISR
3. Clients redirect to new leader (2-30s depending on configuration)

**For partitions where failed broker was follower:**
1. Replica removed from ISR
2. No leader change needed
3. Replication continues with remaining replicas

**For partitions where failed broker was sole ISR:**
- Partition unavailable until broker recovers (if `unclean.leader.election.enable=false`)
- Or unclean election from out-of-sync replica (if enabled)

**When broker returns:**
- Rejoins cluster
- Catches up as follower
- Added back to ISR when caught up
- May regain leadership through rebalancing

### 7. How does Kafka ensure fault tolerance?

**Answer:** Kafka achieves fault tolerance through replication, automatic failover, and distributed architecture.

**Fault tolerance mechanisms:**

**Data replication:**
- Multiple copies across brokers (replication factor)
- Survives broker failures without data loss
- `min.insync.replicas` ensures minimum copies before acknowledging

**Automatic failover:**
- Leader election when broker fails
- No manual intervention required
- Clients automatically discover new leaders

**Distributed architecture:**
- No single point of failure
- Partitions distributed across cluster
- Controller election if controller broker fails

**Client retry logic:**
- Producers retry failed sends
- Consumers rebalance when consumer fails
- Idempotent producers prevent duplicates

**Configuration for maximum fault tolerance:**
```java
// Producer
props.put("acks", "all");
props.put("enable.idempotence", true);

// Topic
replication.factor=3
min.insync.replicas=2
unclean.leader.election.enable=false
```

**Tolerates:** RF-1 broker failures without data loss.

### 8. What is the minimum in-sync replicas setting?

**Answer:** `min.insync.replicas` (min ISR) specifies the minimum number of replicas that must acknowledge a write for it to be considered successful.

**Purpose:**
- Ensures data durability threshold
- Works with `acks=all` producer setting
- Prevents accepting writes when too few replicas available

**Configuration:**
```bash
# Topic-level setting
kafka-configs.sh --alter --topic orders \
  --add-config min.insync.replicas=2

# Or broker-level default
min.insync.replicas=2
```

**Behavior:**
- **min.insync.replicas=2, acks=all**: Write succeeds when leader + 1 follower acknowledge
- If ISR count < min.insync.replicas: Broker returns `NOT_ENOUGH_REPLICAS` error
- Producer can retry or fail based on retry configuration

**Common configurations:**

| RF | min ISR | Failures Tolerated | Durability |
|----|---------|-------------------|------------|
| 3 | 2 | 1 | High (standard) |
| 3 | 1 | 2 | Medium (risky) |
| 5 | 3 | 2 | Very high |

**Best practice:** min ISR = RF - 1 balances availability and durability (e.g., RF=3, min ISR=2).

### 9. How does Kafka handle data durability?

**Answer:** Kafka ensures data durability through replication, configurable acknowledgments, and persistent storage.

**Durability mechanisms:**

**Persistent storage:**
- All messages written to disk immediately
- Uses OS page cache for performance
- Configurable flush intervals (`log.flush.interval.messages`)

**Replication:**
- Data replicated to multiple brokers
- Leader waits for follower acknowledgments
- Survives broker failures

**Producer acknowledgments:**
```java
// Maximum durability
props.put("acks", "all");              // Wait for all ISR
props.put("min.insync.replicas", 2);   // Require 2+ replicas
props.put("enable.idempotence", true); // Prevent duplicates
```

**Broker configuration:**
```properties
# Topic settings
replication.factor=3
min.insync.replicas=2
unclean.leader.election.enable=false  // Prevent data loss
```

**Durability guarantees:**
- **acks=all + min ISR=2 + RF=3**: Data survives 1 broker failure with no loss
- **No unclean election**: Prevents promoting out-of-sync replicas
- **Idempotent producer**: Exactly-once semantics within partition

**Trade-off:** Higher durability reduces throughput and increases latency.

### 10. What is unclean leader election?

**Answer:** Unclean leader election allows an out-of-sync replica to become leader when no in-sync replicas are available.

**Scenario:** All ISR replicas failed/unavailable, only out-of-sync replicas remain.

**Configuration:**
```properties
unclean.leader.election.enable=false  # Recommended for production
```

**Behavior when disabled (false):**
- Partition remains offline until ISR replica available
- **Prioritizes consistency**: No data loss
- **Reduces availability**: Partition unavailable during outage

**Behavior when enabled (true):**
- Out-of-sync replica promoted to leader
- **Prioritizes availability**: Partition comes online
- **Risks data loss**: Messages not replicated to new leader are lost

**Example:**
- Leader has offsets 0-1000
- Follower crashed at offset 900
- Leader fails, follower only ISR candidate
- If enabled: Follower becomes leader, offsets 901-1000 lost
- If disabled: Partition offline until original leader recovers

**Best practice:** Disable for critical data (financial, compliance), enable only for metrics/logs where availability > durability.

## Delivery Semantics

### 1. What are the three message delivery semantics in Kafka?

**Answer:** Kafka supports three message delivery guarantees that define how messages are delivered between producer, broker, and consumer.

**Three semantics:**

| Semantic | Guarantee | Possibility | Use Case |
|----------|-----------|-------------|----------|
| **At-most-once** | Messages delivered 0 or 1 time | Message loss possible | Metrics, monitoring where loss acceptable |
| **At-least-once** | Messages delivered 1 or more times | Duplicates possible | Default, works with idempotent processing |
| **Exactly-once** | Messages delivered exactly 1 time | No loss, no duplicates | Financial transactions, critical data |

**Implementation:**
- At-most-once: `acks=0`, auto-commit before processing
- At-least-once: `acks=all`, commit after processing
- Exactly-once: Idempotent producer + transactions + careful consumer offset management

**Trade-offs:** Stronger guarantees require more complexity and lower throughput.

### 2. Explain at-most-once delivery semantics

**Answer:** At-most-once delivery ensures messages are delivered zero or one time, potentially losing messages but never creating duplicates.

**How it works:**

**Producer side:**
- `acks=0`: Fire and forget, no acknowledgment waited
- No retries on failure
- Network errors result in lost messages

**Consumer side:**
- Commit offsets before processing messages
- If consumer crashes during processing, messages lost
- Auto-commit before `poll()` returns

**Configuration:**
```java
// Producer
props.put("acks", "0");
props.put("retries", 0);

// Consumer
props.put("enable.auto.commit", "true");
props.put("auto.commit.interval.ms", "1000");
```

**Characteristics:**
- **Lowest latency**: No waiting for acknowledgments
- **Highest throughput**: No retry overhead
- **Data loss risk**: Acceptable for metrics, logs, sensor data where occasional loss tolerable

**Use when:** Performance matters more than completeness.

### 3. Explain at-least-once delivery semantics

**Answer:** At-least-once delivery guarantees every message is delivered at least once, possibly creating duplicates but never losing messages.

**How it works:**

**Producer side:**
- `acks=all`: Wait for all ISR acknowledgments
- Retries on failure
- Network timeout may cause retry of already-sent message

**Consumer side:**
- Process messages first
- Commit offsets after processing
- Crash before commit causes reprocessing

**Configuration:**
```java
// Producer
props.put("acks", "all");
props.put("retries", Integer.MAX_VALUE);
props.put("enable.idempotence", false);  // Without idempotence

// Consumer
props.put("enable.auto.commit", "false");
// Manual commit after processing
consumer.commitSync();
```

**Characteristics:**
- **No data loss**: Every message eventually delivered
- **Possible duplicates**: Message may be delivered multiple times
- **Requires idempotent processing**: Application must handle duplicates

**Use when:** Data loss unacceptable, application can deduplicate (e.g., using unique IDs).

### 4. Explain exactly-once delivery semantics

**Answer:** Exactly-once semantics (EOS) guarantees each message is delivered and processed exactly once, with no loss and no duplicates.

**How it works:**
- Combines idempotent producer, transactions, and atomic offset commits
- Producer assigns unique sequence numbers to messages
- Broker deduplicates based on sequence numbers
- Transactions ensure atomic writes across partitions
- Consumer offsets committed atomically with processing results

**Two levels of EOS:**

**EOS within Kafka (Kafka-to-Kafka):**
- Kafka Streams applications
- Transactions span input consumption and output production
- No external state changes

**EOS end-to-end:**
- Includes external systems (databases, etc.)
- Requires transactional outbox pattern or two-phase commit
- More complex to implement

**Benefits:**
- Perfect accuracy for financial systems, billing, inventory
- No duplicate processing overhead
- Clean semantics for stream processing

**Trade-offs:** Increased latency (20-30%), more complex configuration, higher resource usage.

### 5. How do you achieve exactly-once semantics in Kafka?

**Answer:** Exactly-once semantics requires combining idempotent producer, transactions, and proper consumer configuration.

**Producer configuration:**
```java
Properties props = new Properties();
props.put("enable.idempotence", true);         // Prevents duplicates
props.put("transactional.id", "my-tx-id");     // Enable transactions
// Following set automatically with idempotence:
// acks=all, retries=MAX_INT, max.in.flight=5

KafkaProducer<String, String> producer = new KafkaProducer<>(props);
producer.initTransactions();
```

**Transaction usage:**
```java
try {
    producer.beginTransaction();

    // Consume messages
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));

    // Process and produce
    for (ConsumerRecord<String, String> record : records) {
        ProducerRecord<String, String> result = process(record);
        producer.send(result);
    }

    // Commit offsets to transaction
    producer.sendOffsetsToTransaction(offsets, consumerGroupMetadata);

    producer.commitTransaction();
} catch (Exception e) {
    producer.abortTransaction();
}
```

**Consumer configuration:**
```java
props.put("isolation.level", "read_committed");  // Only read committed messages
props.put("enable.auto.commit", "false");        // Manual offset management
```

**Requirements:** All participating topics must have replication factor ≥ 3 and `min.insync.replicas` ≥ 2.

### 6. What is idempotence in the context of Kafka?

**Answer:** Idempotence ensures that sending the same message multiple times results in exactly one copy in the log, eliminating duplicates from producer retries.

**How it works:**
- Producer assigned unique Producer ID (PID) on startup
- Each message tagged with PID and sequence number
- Broker tracks (PID, partition, sequence) tuples
- Duplicate sequence numbers detected and rejected
- Transparent to application

**Enabling idempotence:**
```java
props.put("enable.idempotence", true);  // Default in Kafka 3.0+
```

**Automatic configurations:**
- `acks=all`: Ensures leader and followers receive message
- `retries=Integer.MAX_VALUE`: Retry indefinitely
- `max.in.flight.requests.per.connection=5`: Limits in-flight batches

**Guarantees:**
- No duplicates within single producer session and partition
- Maintains ordering even with retries
- No performance penalty in normal operation

**Limitations:**
- Only within single producer instance lifetime
- Only within single partition
- Doesn't span topics or partitions (use transactions for that)

### 7. What are Kafka transactions and when should you use them?

**Answer:** Kafka transactions enable atomic writes across multiple partitions and topics, with all-or-nothing semantics.

**Capabilities:**
- Write to multiple partitions atomically
- Include consumer offset commits in transaction
- Abort/commit all writes as single unit
- Read-committed isolation for consumers

**When to use transactions:**

**Essential use cases:**
- **Exactly-once stream processing**: Consuming and producing atomically
- **Multi-partition writes**: All succeed or all fail
- **Consume-transform-produce**: Offset commit with output
- **Aggregations**: Reading from multiple topics, writing results

**Example scenarios:**
- Order processing writing to multiple topics (inventory, billing, shipping)
- Kafka Streams stateful operations
- ETL pipelines with EOS requirements
- Deduplication with state management

**When NOT to use:**
- Simple produce-only workloads (use idempotence)
- Read-only consumers
- Performance-critical low-latency systems (transactions add 20-30% latency)

**Configuration:**
```java
props.put("transactional.id", "unique-tx-id");
props.put("enable.idempotence", true);
```

### 8. How does the transaction API work in Kafka?

**Answer:** The transaction API coordinates atomic multi-partition writes through a two-phase commit protocol.

**Transaction lifecycle:**

**1. Initialize:**
```java
producer.initTransactions();  // Register transactional.id with coordinator
```

**2. Begin transaction:**
```java
producer.beginTransaction();  // Start new transaction
```

**3. Perform operations:**
```java
// Produce to multiple topics/partitions
producer.send(new ProducerRecord<>("topic1", key, value));
producer.send(new ProducerRecord<>("topic2", key, value));

// Include consumer offsets
Map<TopicPartition, OffsetAndMetadata> offsets = getOffsets();
producer.sendOffsetsToTransaction(offsets, "consumer-group-id");
```

**4. Commit or abort:**
```java
producer.commitTransaction();   // Atomically commit all writes
// OR
producer.abortTransaction();    // Rollback all writes
```

**Behind the scenes:**
- **Transaction coordinator**: Special broker managing transaction state
- **Transaction log**: Internal topic `__transaction_state` storing transaction metadata
- **Two-phase commit**: Prepare phase, then commit phase across all partitions
- **Markers**: Control messages in logs indicating transaction boundaries

**Consumer side:**
```java
props.put("isolation.level", "read_committed");  // Only see committed messages
```

**Guarantees:** Either all writes succeed or none do, including offset commits.

## Data Retention and Log Management

### 1. How does Kafka handle data retention?

**Answer:** Kafka retains messages on disk based on configurable retention policies, independent of whether messages have been consumed.

**Key characteristics:**
- **Time-based**: Delete data older than specified duration
- **Size-based**: Delete oldest data when partition size exceeds limit
- **Log compaction**: Retain only latest value per key
- **Independent of consumption**: Messages retained even after all consumers read them
- **Configurable per topic**: Different topics can have different policies

**Default configuration:**
```properties
log.retention.hours=168        # 7 days default
log.retention.bytes=-1         # Unlimited size
log.cleanup.policy=delete      # Delete old segments
```

**Benefits:**
- Enables message replay and reprocessing
- Supports multiple consumers at different speeds
- Allows new consumers to read historical data
- Facilitates debugging and auditing

**Storage management:** Old segments deleted asynchronously by background threads.

### 2. What are the different retention policies in Kafka?

**Answer:** Kafka supports three primary retention policies: time-based, size-based, and log compaction.

**1. Time-based retention (delete):**
```properties
log.retention.hours=168           # Delete after 7 days
log.retention.minutes=10080       # Or specify in minutes
log.retention.ms=604800000        # Or milliseconds (most precise)
```
- Deletes segments older than specified time
- Based on last modified timestamp of segment file
- Default for most use cases

**2. Size-based retention (delete):**
```properties
log.retention.bytes=1073741824   # 1GB per partition
```
- Deletes oldest segments when partition exceeds size
- Applied per partition, not per topic
- Combined with time-based (whichever limit hit first)

**3. Log compaction (compact):**
```properties
log.cleanup.policy=compact
```
- Retains only latest value for each key
- Keeps complete history of changes until compaction runs
- Ideal for changelog/state topics

**Combined policy:**
```properties
log.cleanup.policy=compact,delete  # Both compaction and retention
```

### 3. What is log compaction in Kafka?

**Answer:** Log compaction is a retention policy that retains at least the last known value for each message key within a partition, creating a "changelog" semantics.

**How it works:**
- Divides log into two sections: head (active, not compacted) and tail (eligible for compaction)
- Background compaction thread scans tail
- Keeps latest value for each key, removes duplicates
- Tombstones (null values) mark deletions
- Guarantees at least last value per key retained

**Key properties:**
```properties
log.cleanup.policy=compact
min.compaction.lag.ms=0                    # Min time before compaction
delete.retention.ms=86400000               # Keep tombstones 24h
segment.ms=604800000                       # Roll segment weekly
min.cleanable.dirty.ratio=0.5              # Compact when 50% duplicates
```

**Use cases:**
- Database changelog (CDC)
- Application state snapshots
- User profile/configuration storage
- Kafka Streams state stores

**Example:** Messages with key "user123" → only latest profile retained after compaction.

### 4. When should you use log compaction vs time-based retention?

**Answer:** Choose retention policy based on data semantics and use case requirements.

**Use log compaction when:**
- **Changelog semantics**: Latest state of entities matters (user profiles, configurations)
- **Infinite retention**: Need to retain data indefinitely but only latest values
- **State recovery**: Rebuilding application state from Kafka (Kafka Streams, KSQL)
- **Database sync**: CDC use cases where latest record matters
- **Key-value semantics**: Data naturally keyed and updates replace previous values

**Use time-based retention when:**
- **Event streaming**: All events matter, not just latest (clickstreams, logs, metrics)
- **Time-series data**: Historical sequence important
- **Audit trails**: Need complete history within timeframe
- **Analytics**: Require all events for calculations
- **Storage limits**: Want predictable storage based on time window

**Comparison:**

| Aspect | Compaction | Time-based |
|--------|-----------|------------|
| **Data semantics** | Current state | Event history |
| **Storage** | Bounded by key cardinality | Bounded by time/size |
| **Use case** | Database tables | Event logs |
| **Query pattern** | Latest value lookup | Time-range scans |

**Combined:** Can use both (`compact,delete`) for recent complete history + older compacted data.

### 5. How does log compaction work?

**Answer:** Log compaction is a background process that reduces storage while preserving the latest value for each key.

**Compaction process:**

**1. Log structure:**
- **Head (active segment)**: Recent messages, not eligible for compaction
- **Tail (older segments)**: Eligible for compaction after `min.compaction.lag.ms`

**2. Compaction algorithm:**
```
For each segment in tail:
  1. Build map: key → latest offset in segment
  2. Create new cleaned segment
  3. Copy only records with latest offset for each key
  4. Replace old segment with cleaned segment
```

**3. Triggering compaction:**
- `dirty ratio = dirty_bytes / total_bytes`
- Compaction runs when `dirty ratio > min.cleanable.dirty.ratio`
- "Dirty" = bytes with duplicate keys

**4. Tombstone handling:**
- Message with null value marks key for deletion
- Tombstone retained for `delete.retention.ms` (default 24h)
- After grace period, tombstone and key deleted
- Allows consumers time to process deletion

**Configuration:**
```properties
log.cleanup.policy=compact
min.cleanable.dirty.ratio=0.5      # Compact when 50% duplicates
segment.ms=86400000                # Daily segments
min.compaction.lag.ms=0            # Immediate eligibility
```

**Guarantees:** Any consumer that reads from beginning will see at least final value for each key.

### 6. What is the difference between delete and compact cleanup policies?

**Answer:** Delete and compact are fundamentally different retention strategies with different semantics and use cases.

**Delete policy:**
```properties
log.cleanup.policy=delete
log.retention.hours=168
```

**Behavior:**
- Removes entire segments based on time or size
- All messages in old segments deleted regardless of keys
- No deduplication
- Segment deletion is atomic (whole segment removed)

**Data guarantee:** Messages available for retention period, then removed

**Use case:** Event streams, logs, time-series data

**Compact policy:**
```properties
log.cleanup.policy=compact
```

**Behavior:**
- Keeps latest value for each key
- Removes old duplicate values
- Works within segments
- Never deletes latest value (unless tombstone)

**Data guarantee:** Latest value per key always available

**Use case:** Database changelog, state storage, snapshots

**Combined policy:**
```properties
log.cleanup.policy=compact,delete
log.retention.hours=720  # 30 days
```

**Behavior:** Compacts recent data, deletes old data after retention period. Best of both worlds: efficient storage for recent data, bounded storage overall.

### 7. How do you configure retention time for a topic?

**Answer:** Retention time can be configured at broker-level (default) or per-topic (override).

**Broker-level default:**
```properties
# In server.properties
log.retention.hours=168        # 7 days
log.retention.minutes=10080    # Alternative: minutes
log.retention.ms=604800000     # Alternative: milliseconds (highest precedence)
```

**Topic-level override (creation time):**
```bash
kafka-topics.sh --create --topic my-topic \
  --bootstrap-server localhost:9092 \
  --partitions 3 \
  --replication-factor 3 \
  --config retention.ms=86400000  # 1 day
```

**Topic-level override (existing topic):**
```bash
kafka-configs.sh --bootstrap-server localhost:9092 \
  --entity-type topics \
  --entity-name my-topic \
  --alter \
  --add-config retention.ms=172800000  # 2 days
```

**Programmatically (Admin API):**
```java
Map<String, String> configs = new HashMap<>();
configs.put("retention.ms", "86400000");

ConfigResource resource = new ConfigResource(ConfigResource.Type.TOPIC, "my-topic");
AlterConfigOp op = new AlterConfigOp(
    new ConfigEntry("retention.ms", "86400000"),
    AlterConfigOp.OpType.SET
);
adminClient.incrementalAlterConfigs(Map.of(resource, List.of(op)));
```

**Precedence:** `retention.ms` > `retention.minutes` > `retention.hours`

### 8. What is segment in Kafka logs?

**Answer:** A segment is a physical file on disk that stores a portion of a partition's message log.

**Structure:**
Each partition consists of multiple segment files:
```
/kafka-logs/topic-0/
├── 00000000000000000000.log      # Messages 0-999
├── 00000000000000000000.index    # Offset index
├── 00000000000000000000.timeindex # Timestamp index
├── 00000000000001000000.log      # Messages 1000-1999
├── 00000000000001000000.index
└── 00000000000001000000.timeindex
```

**Segment properties:**
- **Active segment**: Current segment receiving writes (cannot be deleted/compacted)
- **Closed segments**: Full segments eligible for deletion/compaction
- **Naming**: Filename = base offset of first message in segment
- **Size limit**: Rolls to new segment when size or time limit reached

**Configuration:**
```properties
log.segment.bytes=1073741824       # 1GB per segment
log.segment.ms=604800000           # Roll segment after 7 days
```

**Purpose:**
- Enables efficient deletion (delete whole file vs individual messages)
- Facilitates log compaction on closed segments
- Allows fast seeking via indexes
- Improves I/O performance with sequential writes

### 9. How are old segments deleted?

**Answer:** Old segments are deleted asynchronously by background cleaner threads based on retention policies.

**Deletion process:**

**1. Segment eligibility:**
- Segment must be closed (not active segment)
- For time-based: Segment's last modified time > `retention.ms`
- For size-based: Total partition size > `retention.bytes` (oldest segments first)

**2. Background deletion:**
- Cleaner thread runs every `log.retention.check.interval.ms` (default 5 minutes)
- Scans all partitions for eligible segments
- Marks segments for deletion
- Deletes segment files (.log, .index, .timeindex)

**3. Configuration:**
```properties
log.retention.ms=604800000                  # 7 days
log.retention.bytes=1073741824             # 1GB per partition
log.retention.check.interval.ms=300000     # Check every 5 min
```

**Deletion triggers:**
- Time-based: Segment older than retention period
- Size-based: Partition exceeds size limit
- Both configured: Whichever threshold hit first

**Safety:**
- Active segment never deleted
- Deletion atomic per segment
- Indices deleted with log segments

**Note:** Segment timestamps based on largest timestamp in segment or file modification time.

### 10. Can you have different retention policies for different topics?

**Answer:** Yes, each topic can have its own retention policy independent of broker defaults and other topics.

**Per-topic configuration:**

**Different time retention:**
```bash
# Topic 1: 1 day retention
kafka-configs.sh --alter --topic metrics \
  --add-config retention.ms=86400000

# Topic 2: 30 days retention
kafka-configs.sh --alter --topic transactions \
  --add-config retention.ms=2592000000

# Topic 3: Infinite retention (compaction)
kafka-configs.sh --alter --topic user-profiles \
  --add-config cleanup.policy=compact
```

**Different size retention:**
```bash
kafka-configs.sh --alter --topic logs \
  --add-config retention.bytes=5368709120  # 5GB per partition
```

**Mixed policies:**
```bash
# Compact + time-based retention
kafka-configs.sh --alter --topic changelog \
  --add-config cleanup.policy=compact,delete \
  --add-config retention.ms=2592000000
```

**Configuration hierarchy:**
1. Topic-level config (highest priority)
2. Broker-level default (fallback)

**Use cases:**
- Short retention for high-volume metrics
- Long retention for compliance/audit topics
- Infinite retention (compaction) for state/changelog topics
- Different policies based on data criticality and storage costs

**Viewing topic config:**
```bash
kafka-configs.sh --describe --topic my-topic
```

## Performance and Scalability

### 1. How does Kafka achieve high throughput?

**Answer:** Kafka achieves exceptional throughput through several architectural optimizations.

**Key techniques:**

**Sequential I/O:**
- Append-only log structure enables sequential disk writes
- Sequential writes 6x faster than random writes on modern disks
- Read-ahead and write-behind optimizations from OS

**Zero-copy:**
- sendfile() system call transfers data from disk to network directly
- Bypasses application layer, reduces CPU and memory copying
- 2-4x performance improvement for consumers

**Batching:**
- Producers batch messages before sending
- Brokers write batches as single unit
- Consumers fetch batches in single request
- Reduces network overhead per message

**Page cache:**
- Heavy reliance on OS page cache instead of in-memory cache
- Recently written data served from RAM
- OS manages memory more efficiently than JVM

**Compression:**
- Batches compressed together for better ratio
- Reduces network and disk I/O
- CPU overhead offset by I/O savings

**Partitioning:**
- Parallel writes across partitions
- Horizontal scalability

**Result:** Millions of messages/second per broker with proper configuration.

### 2. How do partitions help in Kafka scaling?

**Answer:** Partitions are the fundamental unit of parallelism and scalability in Kafka.

**Scaling dimensions:**

**Horizontal scalability:**
- Distribute partitions across multiple brokers
- Each broker handles subset of partitions
- Add brokers to handle more partitions

**Producer scalability:**
- Multiple producers write to different partitions in parallel
- No coordination needed between producers
- Throughput scales linearly with partition count

**Consumer scalability:**
- Up to N consumers can process N partitions in parallel
- Each consumer handles dedicated partitions
- Add consumers for higher processing capacity

**Storage scalability:**
- Data distributed across broker disks
- Large topics span multiple machines
- No single broker storage bottleneck

**Example scaling:**
```
Topic with 100 partitions across 10 brokers:
- Each broker: 10 partitions (leaders)
- 100 producers: Each writes to dedicated partition
- 100 consumers: Each reads from dedicated partition
- Aggregate throughput: 100x single partition
```

**Limitation:** Can't have more active consumers in a group than partitions.

### 3. What factors affect Kafka performance?

**Answer:** Kafka performance depends on multiple interconnected factors across hardware, configuration, and workload.

**Hardware factors:**
- **Disk I/O**: Fast SSDs vs HDDs, RAID configuration
- **Network bandwidth**: 10GbE+ recommended for high throughput
- **Memory**: More RAM = larger page cache = better read performance
- **CPU**: Compression/decompression, SSL/TLS overhead

**Configuration factors:**
- **Batch size**: Larger batches = higher throughput, higher latency
- **Compression**: Reduces I/O but increases CPU usage
- **Replication factor**: Higher RF = more network/disk overhead
- **Acknowledgment level**: `acks=all` slower than `acks=1`
- **Buffer sizes**: `socket.send.buffer.bytes`, `socket.receive.buffer.bytes`

**Workload factors:**
- **Message size**: Larger messages = fewer per batch
- **Partition count**: More partitions = more parallelism but more overhead
- **Consumer lag**: Slow consumers = memory pressure
- **Producer rate**: Bursts vs steady rate

**Network factors:**
- **Latency**: Cross-datacenter replication slower
- **Packet loss**: Triggers retries
- **Bandwidth saturation**: Limits throughput

**Monitoring:** Track `MessagesInPerSec`, `BytesInPerSec`, `NetworkProcessorAvgIdlePercent`, `RequestQueueSize`.

### 4. How do you scale Kafka producers?

**Answer:** Scale producers horizontally across multiple instances and optimize configuration for throughput.

**Horizontal scaling:**
```
Single producer → Multiple producer instances
- Each instance writes to different partitions (via keys)
- Or round-robin across all partitions
- No coordination needed between instances
- Linear throughput scaling
```

**Configuration optimization:**
```java
Properties props = new Properties();

// Batching
props.put("batch.size", 32768);           // 32KB batches
props.put("linger.ms", 20);               // Wait up to 20ms

// Compression
props.put("compression.type", "lz4");     // Fast compression

// Buffer
props.put("buffer.memory", 67108864);     // 64MB buffer

// Parallelism
props.put("max.in.flight.requests.per.connection", 5);

// Durability vs performance
props.put("acks", "1");                   // Leader only (if acceptable)
```

**Partitioning strategy:**
- Use keys to distribute load evenly across partitions
- Avoid hot partitions (uneven key distribution)
- Consider custom partitioner for specific routing

**Application patterns:**
- Async sends with callbacks (don't block on `get()`)
- Producer pooling in multi-threaded apps
- Connection reuse (producers are thread-safe)

**Scaling formula:** `Throughput = producers × per_producer_throughput`

### 5. How do you scale Kafka consumers?

**Answer:** Scale consumers by adding more instances to consumer group, up to partition count limit.

**Horizontal scaling:**
```
1 consumer, 10 partitions → Each consumer reads 10 partitions
5 consumers, 10 partitions → Each consumer reads 2 partitions
10 consumers, 10 partitions → Each consumer reads 1 partition (optimal)
15 consumers, 10 partitions → 5 consumers idle (over-provisioned)
```

**Configuration optimization:**
```java
Properties props = new Properties();

// Fetch size
props.put("fetch.min.bytes", 1);              // Don't wait
props.put("fetch.max.wait.ms", 500);          // Max wait time
props.put("max.partition.fetch.bytes", 1048576); // 1MB per partition

// Batch processing
props.put("max.poll.records", 500);           // Records per poll

// Heartbeat tuning
props.put("session.timeout.ms", 30000);
props.put("heartbeat.interval.ms", 3000);
```

**Processing patterns:**
- Process in parallel with thread pools
- Commit offsets in batches
- Use async commits for throughput
- Implement backpressure handling

**Scalability limits:**
- Maximum parallelism = partition count
- Add more partitions if needed (requires topic recreation or alteration)

**Multi-topic consumption:** Single consumer can subscribe to multiple topics for efficiency.

### 6. How do you expand a Kafka cluster?

**Answer:** Expand Kafka cluster by adding brokers and redistributing partitions for balanced load.

**Step-by-step process:**

**1. Add new broker:**
```bash
# Configure new broker with unique broker.id
broker.id=4
zookeeper.connect=zk1:2181,zk2:2181,zk3:2181

# Start broker
kafka-server-start.sh server.properties
```

**2. Verify broker joined:**
```bash
kafka-broker-api-versions.sh --bootstrap-server localhost:9092
```

**3. Generate partition reassignment plan:**
```bash
# List topics to reassign
cat topics-to-move.json
{"topics": [{"topic": "my-topic"}], "version": 1}

# Generate reassignment
kafka-reassign-partitions.sh --bootstrap-server localhost:9092 \
  --topics-to-move-json-file topics-to-move.json \
  --broker-list "0,1,2,3,4" \
  --generate
```

**4. Execute reassignment:**
```bash
kafka-reassign-partitions.sh --bootstrap-server localhost:9092 \
  --reassignment-json-file reassignment.json \
  --execute
```

**5. Verify completion:**
```bash
kafka-reassign-partitions.sh --bootstrap-server localhost:9092 \
  --reassignment-json-file reassignment.json \
  --verify
```

**Best practices:**
- Reassign during low-traffic periods
- Monitor network and disk I/O during reassignment
- Reassign one topic at a time for large clusters
- Set `replica.fetch.max.bytes` high for faster replication

### 7. What is the impact of increasing replication factor on performance?

**Answer:** Higher replication factor improves durability and availability but reduces performance and increases resource usage.

**Performance impacts:**

**Write performance:**
- **With acks=all**: Leader waits for all replicas before acknowledging
- RF=3 vs RF=1: ~30-50% throughput reduction with `acks=all`
- Network bandwidth: RF×data must be transferred
- Disk I/O: RF×data written across cluster

**Read performance:**
- Generally no impact (consumers read from leader only)
- Exception: Follower fetching in newer versions for geo-proximity

**Resource usage:**
```
Storage: RF × data_size
Network: RF × write_throughput for replication
CPU: Minimal overhead
```

**Recovery time:**
- Higher RF = faster recovery (more replicas to elect from)
- More replicas = longer initial sync for new brokers

**Configuration impact:**

| RF | acks=all latency | Storage | Failures tolerated |
|----|------------------|---------|-------------------|
| 1 | N/A (no replicas) | 1x | 0 |
| 2 | +20-30% | 2x | 1 |
| 3 | +40-60% | 3x | 2 |

**Mitigation:**
- Use `acks=1` if durability less critical
- Ensure sufficient network bandwidth
- Use compression to reduce replication data

### 8. How does compression affect Kafka performance?

**Answer:** Compression trades CPU for reduced network and disk I/O, generally improving overall throughput.

**Performance characteristics:**

| Type | Compression Ratio | CPU | Speed | Best For |
|------|------------------|-----|-------|----------|
| **lz4** | Good | Low | Fastest | General use, low latency |
| **snappy** | Good | Low-Med | Fast | Balanced performance |
| **gzip** | Excellent | High | Slow | Network/storage constrained |
| **zstd** | Excellent | Med | Medium | Best overall (Kafka 2.1+) |

**Benefits:**
- **Reduced network bandwidth**: 50-70% reduction for text
- **Lower disk usage**: Same compression ratio for storage
- **Higher throughput**: I/O bottleneck reduction outweighs CPU cost
- **Faster replication**: Less data to replicate

**Costs:**
- **Producer CPU**: Compression overhead during send
- **Broker CPU**: Decompression for validation (if enabled)
- **Consumer CPU**: Decompression during read
- **Latency increase**: 1-5ms for compression

**Configuration:**
```java
props.put("compression.type", "lz4");
```

**Best practices:**
- Use lz4 for low latency, zstd for throughput
- Test with actual data (JSON compresses better than binary)
- Monitor CPU utilization
- Compression happens at batch level (larger batches = better ratio)

**Recommendation:** Enable lz4 or zstd by default unless CPU-constrained.

### 9. What is zero-copy in Kafka and how does it improve performance?

**Answer:** Zero-copy is an optimization that transfers data from disk to network socket without copying through application memory.

**Traditional approach (with copying):**
1. Read data from disk to OS buffer
2. Copy from OS buffer to application buffer (Kafka process)
3. Copy from application buffer to socket buffer
4. Transfer socket buffer to NIC
- **Total: 4 copies, 2 system calls**

**Zero-copy approach:**
1. Read data from disk to OS page cache
2. sendfile() transfers directly from page cache to NIC
- **Total: 1-2 copies, 1 system call**

**Implementation in Kafka:**
```java
// Uses java.nio.channels.FileChannel.transferTo()
// Leverages sendfile() system call on Linux
fileChannel.transferTo(position, count, socketChannel);
```

**Performance benefits:**
- **2-4x faster** data transfer to consumers
- **Reduced CPU**: No user-space copying
- **Reduced memory**: No application buffers needed
- **Better cache utilization**: Data stays in page cache

**When zero-copy applies:**
- Broker serving data to consumers
- Broker replicating to followers
- Messages not compressed differently than stored format

**When NOT used:**
- SSL/TLS enabled (data must be encrypted in application)
- Message format conversion required
- Deep packet inspection needed

**Monitoring:** Check `NetworkProcessorAvgIdlePercent` - zero-copy reduces CPU usage.

### 10. How do you optimize Kafka for low latency?

**Answer:** Low latency optimization requires minimizing batching, buffering, and network delays.

**Producer configuration:**
```java
Properties props = new Properties();

// Minimize batching
props.put("linger.ms", 0);                    // Send immediately
props.put("batch.size", 16384);               // Smaller batches

// Reduce buffering
props.put("buffer.memory", 33554432);         // 32MB buffer

// Fast acknowledgment
props.put("acks", "1");                       // Leader only (if acceptable)

// Compression (optional)
props.put("compression.type", "lz4");         // If needed, use fast algorithm
```

**Broker configuration:**
```properties
# Reduce flush delays
num.network.threads=8
num.io.threads=16

# Socket buffers
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400

# Replica fetching
replica.lag.time.max.ms=10000
```

**Consumer configuration:**
```java
// Minimal fetch wait
props.put("fetch.min.bytes", 1);              // Don't wait for data
props.put("fetch.max.wait.ms", 100);          // Max 100ms wait

// Small poll batches
props.put("max.poll.records", 100);
```

**Infrastructure:**
- Use SSDs over HDDs
- Low-latency network (same datacenter)
- Sufficient broker resources (CPU, memory)
- Disable unnecessary replication (RF=1 if acceptable)

**Trade-offs:**
- Lower throughput (less batching)
- Higher CPU usage (more requests)
- Potentially higher costs

**Target latency:** p99 < 10ms achievable with proper configuration.

## Kafka Streams

### 1. What is Kafka Streams?

**Answer:** Kafka Streams is a client library for building stream processing applications that read from and write to Kafka topics.

**Key features:**
- **Lightweight library**: No separate cluster needed, runs in application
- **Exactly-once semantics**: Built-in transactional support
- **Stateful processing**: Local state stores with changelog topics
- **Scalability**: Automatically distributes processing across instances
- **Fault tolerance**: Automatic recovery from failures
- **Event-time processing**: Supports out-of-order events and windowing

**Example:**
```java
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> source = builder.stream("input-topic");

source.filter((key, value) -> value.length() > 5)
      .mapValues(value -> value.toUpperCase())
      .to("output-topic");

KafkaStreams streams = new KafkaStreams(builder.build(), props);
streams.start();
```

**Use cases:** Real-time analytics, data enrichment, event-driven microservices, stream-to-stream joins, aggregations.

### 2. How is Kafka Streams different from other stream processing frameworks?

**Answer:** Kafka Streams differs significantly from frameworks like Flink, Spark Streaming, and Storm.

**Comparison:**

| Aspect | Kafka Streams | Flink/Spark/Storm |
|--------|---------------|-------------------|
| **Deployment** | Library embedded in app | Separate cluster required |
| **Operations** | No cluster to manage | Cluster management needed |
| **Scaling** | Start more instances | Cluster resource management |
| **State** | Local RocksDB + changelog | Distributed state backend |
| **Dependencies** | Only Kafka | Kafka + processing cluster |
| **Latency** | Milliseconds | Varies (ms to seconds) |

**Kafka Streams advantages:**
- **Simpler ops**: No separate cluster (Flink, Spark)
- **Kafka-native**: Tight integration, same security model
- **Elastic**: Scale by starting/stopping instances
- **Exactly-once**: Built-in with Kafka transactions

**When to use alternatives:**
- Complex CEP (Complex Event Processing): Flink
- Batch + streaming: Spark
- Non-Kafka sources/sinks: Flink/Spark
- ML model serving: Flink/Spark

**Best fit:** Kafka-to-Kafka transformations with simple to moderate complexity.

### 3. What are KStream and KTable?

**Answer:** KStream and KTable are the two fundamental abstractions in Kafka Streams representing different data semantics.

**KStream (Event Stream):**
- Represents an unbounded stream of immutable events
- Each record is a new, independent event
- Inserts-only semantics
- Example: Click events, transactions, log entries

```java
KStream<String, String> clicks = builder.stream("clickstream");
clicks.filter((user, event) -> event.contains("purchase"))
      .to("purchases");
```

**KTable (Changelog Stream):**
- Represents a changelog stream where latest value per key is the current state
- Updates override previous values for same key
- Upsert semantics (insert or update)
- Example: User profiles, product inventory, configuration

```java
KTable<String, String> users = builder.table("user-profiles");
// Only latest profile per user maintained
```

**Duality:** KTable can be derived from KStream via aggregation; KStream can be created from KTable changelog.

**Storage:** KTable backed by state store; KStream is stateless.

### 4. What is the difference between KStream and KTable?

**Answer:** KStream and KTable differ in data semantics, operations, and use cases.

**Semantic differences:**

| Aspect | KStream | KTable |
|--------|---------|--------|
| **Semantics** | Event stream | State/changelog |
| **Record interpretation** | New independent event | Update to current state |
| **Duplicate keys** | Multiple records allowed | Latest value wins |
| **Storage** | No state | Backed by state store |
| **Example** | Transaction log | Account balance |

**Operational differences:**

**KStream:**
```java
KStream<String, Purchase> purchases = builder.stream("purchases");
// Stateless transformations
purchases.filter(...)
         .map(...)
         .flatMap(...);

// Stateful: aggregation creates KTable
KTable<String, Long> counts = purchases.groupByKey()
                                       .count();
```

**KTable:**
```java
KTable<String, User> users = builder.table("users");
// Stateful by nature
users.filter(...)
     .mapValues(...);

// Queryable via interactive queries
ReadOnlyKeyValueStore<String, User> store = ...
User user = store.get("user123");
```

**Example distinction:**
- **KStream**: 3 records with key "user1" = 3 events
- **KTable**: 3 records with key "user1" = 1 current state (latest value)

### 5. What are GlobalKTables?

**Answer:** GlobalKTable is a fully replicated table where each application instance has a complete copy of all data.

**Characteristics:**
- **Full replication**: Every instance has all partition data
- **No partitioning**: Not partitioned by application instances
- **Read-only**: Can't write to GlobalKTable directly
- **No repartitioning**: Joins don't require repartitioning
- **More memory**: Each instance stores complete dataset

**Regular KTable vs GlobalKTable:**

| Aspect | KTable | GlobalKTable |
|--------|--------|--------------|
| **Data distribution** | Partitioned across instances | Fully replicated to each |
| **Memory usage** | 1/N of data per instance | Full dataset per instance |
| **Join key requirement** | Must be co-partitioned | Any key works |
| **Use case** | Large datasets | Small reference data |

**Example:**
```java
// Regular KTable - partitioned
KTable<String, User> users = builder.table("users");

// GlobalKTable - replicated everywhere
GlobalKTable<String, Product> products = builder.globalTable("products");

// Stream-GlobalKTable join (no repartitioning needed)
KStream<String, Order> orders = builder.stream("orders");
orders.join(products,
    (orderId, order) -> order.getProductId(),  // Key extractor
    (order, product) -> enrichOrder(order, product));
```

**Use cases:** Product catalogs, currency rates, configuration data, country codes - small reference datasets needed by all instances.

### 6. How does stateful processing work in Kafka Streams?

**Answer:** Stateful processing maintains state across events using local state stores backed by changelog topics for fault tolerance.

**State management:**
- **Local state store**: RocksDB or in-memory store per instance
- **Changelog topic**: Kafka topic backing up state changes
- **Automatic recovery**: State restored from changelog on failure
- **Partitioned state**: State co-partitioned with input data

**Stateful operations:**
```java
// Aggregation
KTable<String, Long> wordCounts = textStream
    .groupBy((key, word) -> word)
    .count(Materialized.as("word-counts-store"));

// Windowed aggregation
TimeWindowedKStream<String, Long> windowed = clicks
    .groupByKey()
    .windowedBy(TimeWindows.of(Duration.ofMinutes(5)))
    .count();

// Join (requires state)
KStream<String, Enriched> enriched = orders.join(users,
    (order, user) -> new Enriched(order, user));
```

**Behind the scenes:**
1. State updates written to local RocksDB
2. Changes sent to changelog topic (transactionally)
3. On instance failure, new instance restores from changelog
4. Standby replicas can be configured for faster recovery

**Configuration:**
```java
props.put(StreamsConfig.STATE_DIR_CONFIG, "/tmp/kafka-streams");
props.put(StreamsConfig.NUM_STANDBY_REPLICAS_CONFIG, 1);
```

### 7. What is a state store in Kafka Streams?

**Answer:** A state store is a local database that stores and manages state for stateful operations in Kafka Streams.

**Types of state stores:**

**Persistent (RocksDB - default):**
```java
StoreBuilder<KeyValueStore<String, Long>> store =
    Stores.keyValueStoreBuilder(
        Stores.persistentKeyValueStore("my-store"),
        Serdes.String(),
        Serdes.Long()
    );
```
- Disk-backed, survives restarts
- Better for large state
- Slower than in-memory

**In-memory:**
```java
StoreBuilder<KeyValueStore<String, Long>> store =
    Stores.keyValueStoreBuilder(
        Stores.inMemoryKeyValueStore("my-store"),
        Serdes.String(),
        Serdes.Long()
    );
```
- RAM-only, lost on restart (restored from changelog)
- Faster access
- Limited by memory

**Store types:**
- **KeyValueStore**: Basic key-value storage
- **WindowStore**: Time-based windows
- **SessionStore**: Session-based windows

**Interactive queries:**
```java
ReadOnlyKeyValueStore<String, Long> store =
    streams.store(StoreQueryParameters.fromNameAndType(
        "word-counts-store",
        QueryableStoreTypes.keyValueStore()
    ));

Long count = store.get("hello");
```

**Fault tolerance:** All stores backed by changelog topics for automatic recovery.

### 8. How does windowing work in Kafka Streams?

**Answer:** Windowing groups events into time-based buckets for time-bounded aggregations and computations.

**Window types:**

**1. Tumbling windows (fixed, non-overlapping):**
```java
TimeWindows.ofSizeWithNoGrace(Duration.ofMinutes(5))
// [0-5min), [5-10min), [10-15min)...
```

**2. Hopping windows (fixed, overlapping):**
```java
TimeWindows.ofSizeAndGrace(Duration.ofMinutes(5), Duration.ofMinutes(1))
           .advanceBy(Duration.ofMinutes(1))
// [0-5min), [1-6min), [2-7min)...
```

**3. Sliding windows (data-driven, for joins):**
```java
JoinWindows.ofTimeDifferenceWithNoGrace(Duration.ofMinutes(5))
// Window spans ±5min from each event
```

**4. Session windows (activity-based):**
```java
SessionWindows.ofInactivityGapWithNoGrace(Duration.ofMinutes(30))
// Windows merge if events within 30min
```

**Example - 5-minute tumbling window:**
```java
KTable<Windowed<String>, Long> windowedCounts = clicks
    .groupByKey()
    .windowedBy(TimeWindows.ofSizeWithNoGrace(Duration.ofMinutes(5)))
    .count();
```

**Grace period:** Additional time to accept late events after window closes:
```java
TimeWindows.ofSizeAndGrace(Duration.ofMinutes(5), Duration.ofMinutes(1))
// Accept events up to 1 minute late
```

### 9. What are the different types of joins in Kafka Streams?

**Answer:** Kafka Streams supports multiple join types between streams and tables with different semantics.

**Join combinations:**

**KStream-KStream join:**
```java
KStream<String, Order> orders = builder.stream("orders");
KStream<String, Payment> payments = builder.stream("payments");

// Inner join (windowed)
KStream<String, OrderPayment> joined = orders.join(payments,
    (order, payment) -> new OrderPayment(order, payment),
    JoinWindows.ofTimeDifferenceWithNoGrace(Duration.ofMinutes(5))
);

// Left/Outer join also supported
```
- Windowed only (events must arrive within time window)
- Requires co-partitioning

**KStream-KTable join:**
```java
KStream<String, Order> orders = builder.stream("orders");
KTable<String, Customer> customers = builder.table("customers");

// Stream enrichment
KStream<String, EnrichedOrder> enriched = orders.join(customers,
    (order, customer) -> new EnrichedOrder(order, customer)
);
```
- Not windowed (uses latest table value)
- Requires co-partitioning

**KStream-GlobalKTable join:**
```java
GlobalKTable<String, Product> products = builder.globalTable("products");

orders.join(products,
    (orderId, order) -> order.getProductId(),  // Key mapper
    (order, product) -> enrich(order, product)
);
```
- No co-partitioning required
- No windowing

**KTable-KTable join:**
```java
KTable<String, User> users = builder.table("users");
KTable<String, Address> addresses = builder.table("addresses");

KTable<String, UserAddress> joined = users.join(addresses,
    (user, address) -> new UserAddress(user, address)
);
```
- Continuous join (updates when either table changes)

### 10. How do you handle late-arriving data in Kafka Streams?

**Answer:** Kafka Streams handles late-arriving data through grace periods, retention, and event-time processing.

**Grace period:**
```java
// Accept events up to 1 hour after window closes
TimeWindows.ofSizeAndGrace(
    Duration.ofHours(1),      // Window size
    Duration.ofHours(1)       // Grace period
);
```
- Late events within grace period update window results
- After grace period, window finalized and late events dropped
- Configured per window definition

**Retention period:**
```java
Materialized.<String, Long, WindowStore<Bytes, byte[]>>as("store")
    .withRetention(Duration.ofDays(2));
```
- How long to keep window data
- Must be ≥ window size + grace period

**Handling strategies:**

**1. Configure grace period:**
- Trade-off: Longer grace = more accurate, higher memory
- Set based on expected max delay

**2. Monitor dropped records:**
```java
props.put(StreamsConfig.DEFAULT_DESERIALIZATION_EXCEPTION_HANDLER_CLASS_CONFIG,
    LogAndContinueExceptionHandler.class);
```

**3. Side output for late events:**
```java
KStream<String, Event> onTime = stream.filter((k, v) -> !isLate(v));
KStream<String, Event> late = stream.filter((k, v) -> isLate(v));

late.to("late-events-topic");  // Process separately
```

**4. Use event time vs processing time:**
```java
// Extract event timestamp
stream.selectKey((k, v) -> v.getEventTime())
```

**Best practice:** Set grace period to p99 of expected lateness, monitor late-event metrics, consider separate processing for very late data.

## Kafka Connect

### 1. What is Kafka Connect?

**Answer:** Kafka Connect is a framework for scalable and reliable streaming data integration between Kafka and external systems.

**Key features:**
- **Declarative configuration**: No code needed for simple integrations
- **Scalability**: Distributed mode for high-throughput connectors
- **Fault tolerance**: Automatic failover and recovery
- **Offset management**: Automatic offset tracking and commits
- **Connectors ecosystem**: 100+ pre-built connectors available
- **Exactly-once semantics**: Supports transactional delivery

**Architecture:**
- **Source connectors**: Import data from external systems into Kafka
- **Sink connectors**: Export data from Kafka to external systems
- **Workers**: Processes that execute connector tasks
- **Converters**: Handle serialization/deserialization
- **Transforms**: Optional lightweight message modifications

**Example deployment:**
```bash
connect-standalone.sh config/connect-standalone.properties \
  config/connector-jdbc-source.properties
```

**Use cases:** Database CDC, cloud storage sync, search indexing, data warehouse loading.

### 2. What is the difference between source and sink connectors?

**Answer:** Source and sink connectors move data in opposite directions relative to Kafka.

**Source connectors (Import to Kafka):**
- Read data from external systems
- Write to Kafka topics
- Examples: JDBC source, file source, MongoDB source

```json
{
  "name": "mysql-source",
  "config": {
    "connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector",
    "connection.url": "jdbc:mysql://localhost:3306/mydb",
    "table.whitelist": "users,orders",
    "mode": "incrementing",
    "incrementing.column.name": "id",
    "topic.prefix": "mysql-"
  }
}
```

**Sink connectors (Export from Kafka):**
- Read from Kafka topics
- Write to external systems
- Examples: JDBC sink, S3 sink, Elasticsearch sink

```json
{
  "name": "s3-sink",
  "config": {
    "connector.class": "io.confluent.connect.s3.S3SinkConnector",
    "topics": "orders,payments",
    "s3.bucket.name": "my-kafka-data",
    "flush.size": "1000"
  }
}
```

**Common properties:** Both use converters, transformations, error handling, and offset management. Direction of data flow is the only fundamental difference.

### 3. How does Kafka Connect enable integration with external systems?

**Answer:** Kafka Connect provides a pluggable framework with standardized APIs for building connectors to external systems.

**Integration mechanism:**

**1. Connector abstraction:**
- **SourceConnector/SinkConnector**: Define how to connect to external system
- **SourceTask/SinkTask**: Execute actual data transfer
- **Configuration**: Declarative setup via properties

**2. Offset management:**
- Connect tracks progress in Kafka topics
- Source: Tracks which records already imported
- Sink: Uses consumer offsets
- Automatic resume on failure

**3. Converters:**
- **Key/Value converters**: Transform between Kafka format and Connect format
- Supported: Avro, JSON, Protobuf, String, ByteArray
- Schema Registry integration for schema evolution

**4. Transforms:**
- Lightweight modifications without custom code
- Insert fields, filter, rename, route messages

**5. Error handling:**
- Dead letter queues for failed records
- Configurable retry policies
- Tolerance settings for bad data

**Deployment:**
```
External DB ←→ JDBC Connector ←→ Kafka Connect Workers ←→ Kafka Cluster
```

**Benefit:** Reusable connectors eliminate custom integration code.

### 4. What are some common Kafka Connect use cases?

**Answer:** Kafka Connect excels at real-time data integration across diverse systems.

**Database integration:**
- **CDC (Change Data Capture)**: Debezium connectors stream database changes
- **Data replication**: Sync data between databases via Kafka
- **ETL pipelines**: Extract from RDBMS, transform, load to data warehouse
- **Examples**: MySQL, PostgreSQL, Oracle, SQL Server connectors

**Cloud storage:**
- **Data lake ingestion**: S3, GCS, Azure Blob connectors
- **Archival**: Long-term storage of Kafka topics
- **Batch processing**: Parquet/Avro files for Spark/Presto
- **Examples**: S3 Sink, GCS Sink with time/size-based partitioning

**Search and analytics:**
- **Elasticsearch**: Real-time search indexing from Kafka
- **Splunk**: Log aggregation and analysis
- **HDFS**: Big data platform integration

**Message queue migration:**
- **RabbitMQ, ActiveMQ**: Migrate to Kafka or bridge systems
- **IBM MQ**: Enterprise integration

**IoT and monitoring:**
- **MQTT**: IoT device data ingestion
- **Syslog**: System log collection
- **JMX**: Metrics collection from Java applications

**SaaS platforms:**
- **Salesforce**: CRM data synchronization
- **ServiceNow**: ITSM integration

### 5. How do you deploy Kafka Connect in standalone vs distributed mode?

**Answer:** Kafka Connect supports two deployment modes with different use cases and operational characteristics.

**Standalone mode:**
```bash
connect-standalone.sh connect-standalone.properties \
  connector1.properties connector2.properties
```

**Characteristics:**
- Single worker process
- Connector configs passed as files
- No fault tolerance (if process dies, stops running)
- Simple setup, lightweight
- Offset stored locally or in Kafka
- No coordination overhead

**Use cases:**
- Development and testing
- Single-node deployments
- Non-critical pipelines
- Resource-constrained environments

**Distributed mode:**
```bash
# Start multiple workers
connect-distributed.sh connect-distributed.properties
```

**Characteristics:**
- Multiple worker processes in cluster
- Connectors submitted via REST API
- Automatic failover and rebalancing
- Scalable (add more workers)
- Offsets and configs stored in Kafka topics
- Work distributed across workers

**Configuration:**
```properties
# Distributed mode config
group.id=connect-cluster
config.storage.topic=connect-configs
offset.storage.topic=connect-offsets
status.storage.topic=connect-status
```

**Managing connectors (distributed):**
```bash
# Create connector via REST
curl -X POST http://localhost:8083/connectors \
  -H "Content-Type: application/json" \
  -d @connector-config.json

# List connectors
curl http://localhost:8083/connectors

# Connector status
curl http://localhost:8083/connectors/my-connector/status
```

**Production recommendation:** Always use distributed mode for fault tolerance and scalability.

### 6. What is a connector configuration?

**Answer:** A connector configuration defines how a connector connects to external systems, what data to transfer, and how to handle it.

**Configuration structure:**
```json
{
  "name": "postgres-source",
  "config": {
    // Connector class
    "connector.class": "io.debezium.connector.postgresql.PostgresConnector",

    // Connection properties
    "database.hostname": "localhost",
    "database.port": "5432",
    "database.user": "postgres",
    "database.password": "secret",
    "database.dbname": "mydb",

    // Data selection
    "table.include.list": "public.users,public.orders",
    "column.exclude.list": "users.password",

    // Kafka topic mapping
    "topic.prefix": "postgres",
    "topic.creation.enable": "true",

    // Behavior
    "tasks.max": "3",
    "poll.interval.ms": "1000",

    // Converters
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter": "io.confluent.connect.avro.AvroConverter",
    "value.converter.schema.registry.url": "http://localhost:8081",

    // Error handling
    "errors.tolerance": "all",
    "errors.deadletterqueue.topic.name": "dlq-postgres"
  }
}
```

**Common properties:**
- `name`: Unique connector identifier
- `connector.class`: Connector implementation class
- `tasks.max`: Parallelism level
- `topics` (sink) or `topic.prefix` (source): Topic configuration
- Connection details specific to external system
- Converters and transforms
- Error handling policies

**Validation:** Connect validates configuration before starting connector.

### 7. How does Kafka Connect handle schema evolution?

**Answer:** Kafka Connect handles schema evolution through converters and Schema Registry integration.

**Schema evolution support:**

**With Schema Registry (Avro/Protobuf):**
```json
{
  "value.converter": "io.confluent.connect.avro.AvroConverter",
  "value.converter.schema.registry.url": "http://localhost:8081",
  "value.converter.schemas.enable": "true"
}
```

**Automatic schema registration:**
- Connector detects schema from source data
- Registers schema in Schema Registry
- Includes schema ID in each message
- Consumers use schema ID to deserialize

**Compatibility modes:**
- **Backward**: New schema can read old data (add optional fields)
- **Forward**: Old schema can read new data (remove fields)
- **Full**: Both backward and forward
- **None**: No validation

**Schema changes:**

**Adding column (backward compatible):**
```sql
ALTER TABLE users ADD COLUMN phone VARCHAR(20);
```
- Source connector detects new schema
- Registers new version in registry
- Old consumers use old schema (ignore new field)
- New consumers use new schema

**Removing column (forward compatible):**
- New schema version registered
- Old consumers expect field (use default if provided)

**Handling incompatible changes:**
```json
{
  "errors.tolerance": "all",
  "errors.deadletterqueue.topic.name": "schema-errors",
  "errors.deadletterqueue.context.headers.enable": "true"
}
```

**Best practices:** Use Avro with Schema Registry, configure appropriate compatibility mode, version topics when making breaking changes.

### 8. What is the role of converters in Kafka Connect?

**Answer:** Converters handle serialization/deserialization between Kafka's byte array format and Connect's internal data representation.

**Converter types:**

**Avro Converter (recommended):**
```json
{
  "key.converter": "io.confluent.connect.avro.AvroConverter",
  "value.converter": "io.confluent.connect.avro.AvroConverter",
  "value.converter.schema.registry.url": "http://localhost:8081"
}
```
- Schema evolution support
- Compact binary format
- Type safety
- Best for production

**JSON Converter:**
```json
{
  "key.converter": "org.apache.kafka.connect.json.JsonConverter",
  "value.converter": "org.apache.kafka.connect.json.JsonConverter",
  "value.converter.schemas.enable": "false"
}
```
- Human-readable
- Larger message size
- No built-in schema evolution
- Good for debugging

**String Converter:**
```json
{
  "key.converter": "org.apache.kafka.connect.storage.StringConverter",
  "value.converter": "org.apache.kafka.connect.storage.StringConverter"
}
```
- Simple text data
- No structure preservation
- Minimal overhead

**ByteArray Converter:**
- Pass-through, no conversion
- For binary data

**Conversion flow:**
```
Source: External System → Connector → Converter → Kafka (bytes)
Sink:   Kafka (bytes) → Converter → Connector → External System
```

**Per-connector override:** Converters can be set globally or per-connector.

### 9. How do you monitor Kafka Connect?

**Answer:** Monitor Kafka Connect using JMX metrics, REST API, logs, and external monitoring tools.

**REST API monitoring:**
```bash
# Cluster info
curl http://localhost:8083/

# All connectors
curl http://localhost:8083/connectors

# Connector status
curl http://localhost:8083/connectors/my-connector/status

# Connector metrics
curl http://localhost:8083/connectors/my-connector/tasks/0/status
```

**Key metrics (JMX):**

**Connector-level:**
- `connector-total-task-count`: Number of tasks
- `connector-running-task-count`: Active tasks
- `connector-paused-task-count`: Paused tasks
- `connector-failed-task-count`: Failed tasks

**Task-level:**
- `source-record-poll-total`: Records polled (source)
- `source-record-write-total`: Records written (source)
- `sink-record-read-total`: Records read (sink)
- `sink-record-send-total`: Records sent (sink)
- `offset-commit-completion-total`: Offset commits

**Error metrics:**
- `total-errors-logged`: Errors encountered
- `total-record-failures`: Failed records
- `total-retries`: Retry attempts

**Monitoring tools:**
```properties
# Enable JMX
KAFKA_JMX_OPTS="-Dcom.sun.management.jmxremote"
```

**Integration with:**
- Prometheus (JMX Exporter)
- Grafana dashboards
- Confluent Control Center
- Custom monitoring solutions

**Health checks:**
- Task states (RUNNING, FAILED, PAUSED)
- Lag monitoring for source connectors
- Error rates and DLQ topic monitoring

### 10. What are Single Message Transforms (SMTs) in Kafka Connect?

**Answer:** Single Message Transforms (SMTs) are lightweight, built-in transformations applied to each message as it flows through Connect.

**Common SMTs:**

**InsertField:**
```json
{
  "transforms": "addTimestamp",
  "transforms.addTimestamp.type": "org.apache.kafka.connect.transforms.InsertField$Value",
  "transforms.addTimestamp.timestamp.field": "ingest_time"
}
```

**ReplaceField:**
```json
{
  "transforms": "renameField",
  "transforms.renameField.type": "org.apache.kafka.connect.transforms.ReplaceField$Value",
  "transforms.renameField.renames": "old_name:new_name"
}
```

**MaskField:**
```json
{
  "transforms": "maskPII",
  "transforms.maskPII.type": "org.apache.kafka.connect.transforms.MaskField$Value",
  "transforms.maskPII.fields": "ssn,credit_card"
}
```

**Filter:**
```json
{
  "transforms": "filterDeletes",
  "transforms.filterDeletes.type": "io.confluent.connect.transforms.Filter",
  "transforms.filterDeletes.filter.condition": "$[?(@.op == 'd')]",
  "transforms.filterDeletes.filter.type": "exclude"
}
```

**ValueToKey:**
```json
{
  "transforms": "extractKey",
  "transforms.extractKey.type": "org.apache.kafka.connect.transforms.ValueToKey",
  "transforms.extractKey.fields": "user_id"
}
```

**TimestampRouter:**
```json
{
  "transforms": "routeByTime",
  "transforms.routeByTime.type": "org.apache.kafka.connect.transforms.TimestampRouter",
  "transforms.routeByTime.topic.format": "${topic}-${timestamp}",
  "transforms.routeByTime.timestamp.format": "YYYY-MM-dd"
}
```

**Chaining transforms:**
```json
{
  "transforms": "insertField,maskPII,routeByDate",
  "transforms.insertField.type": "InsertField$Value",
  "transforms.maskPII.type": "MaskField$Value",
  "transforms.routeByDate.type": "TimestampRouter"
}
```

**When to use:** Simple transformations, field-level changes. For complex logic, use Kafka Streams instead.

## Schema Management

### 1. What is schema registry in Kafka?

**Answer:** Schema Registry is a centralized service that stores and manages schemas for Kafka messages, enabling schema evolution and compatibility checking.

**Key features:**
- **Schema storage**: Central repository for Avro, JSON, Protobuf schemas
- **Version management**: Tracks schema versions per subject
- **Compatibility checking**: Enforces compatibility rules before registration
- **Schema ID assignment**: Unique ID for each schema version
- **REST API**: Register, retrieve, and check schema compatibility
- **High availability**: Clustered deployment with leader election

**Architecture:**
```
Producer → Schema Registry (get schema ID) → Kafka (data + schema ID)
Kafka (data + schema ID) → Schema Registry (get schema) → Consumer
```

**Usage:**
```java
// Producer
Properties props = new Properties();
props.put("value.serializer", "io.confluent.kafka.serializers.KafkaAvroSerializer");
props.put("schema.registry.url", "http://localhost:8081");

// Schema Registry automatically registers schema
```

**Storage:** Schemas stored in internal Kafka topic `_schemas` for durability and replication.

### 2. Why is schema management important in Kafka?

**Answer:** Schema management ensures data quality, compatibility, and enables safe schema evolution in distributed systems.

**Key benefits:**

**Data quality:**
- **Type safety**: Prevents sending wrong data types
- **Validation**: Rejects invalid data at produce time
- **Documentation**: Schema serves as data contract
- **Consistency**: All producers/consumers use same format

**Schema evolution:**
- **Safe changes**: Compatibility checks prevent breaking changes
- **Version tracking**: Historical schema versions maintained
- **Gradual migration**: Old and new schemas coexist
- **Rollback capability**: Can revert to previous versions

**Operational benefits:**
- **Reduced payload size**: Schema not sent with each message (only ID)
- **Backwards compatibility**: Old consumers work with new data
- **Forward compatibility**: New consumers work with old data
- **Error prevention**: Catch schema issues at deployment, not production

**Without schema management:**
- Runtime errors from schema mismatches
- No validation of data quality
- Difficult schema evolution
- Larger message payloads (include schema in each message)

**Best practice:** Use Schema Registry with Avro for production Kafka deployments.

### 3. How does schema evolution work?

**Answer:** Schema evolution allows schemas to change over time while maintaining compatibility with existing producers and consumers.

**Evolution process:**
1. Developer modifies schema (add/remove/modify fields)
2. Schema Registry checks compatibility against configured mode
3. If compatible, new version registered with incremental version number
4. Producers/consumers automatically use appropriate version

**Example evolution:**

**Version 1:**
```json
{
  "type": "record",
  "name": "User",
  "fields": [
    {"name": "id", "type": "int"},
    {"name": "name", "type": "string"}
  ]
}
```

**Version 2 (backward compatible - add optional field):**
```json
{
  "type": "record",
  "name": "User",
  "fields": [
    {"name": "id", "type": "int"},
    {"name": "name", "type": "string"},
    {"name": "email", "type": ["null", "string"], "default": null}
  ]
}
```

**Compatible changes:**
- Add optional fields with defaults
- Remove fields with defaults
- Rename fields (with aliases in Avro)
- Widen data types (int → long)

**Incompatible changes:**
- Add required field without default
- Remove field without default
- Change field type incompatibly
- Rename without alias

**Versioning strategy:**
- Schemas versioned per subject (topic-key, topic-value)
- Version numbers sequential: 1, 2, 3...
- Latest version used by default

### 4. What are the different compatibility types in schema registry?

**Answer:** Schema Registry supports multiple compatibility modes that control what schema changes are allowed.

**Compatibility types:**

**BACKWARD (default):**
- New schema can read data written with old schema
- Allows: Delete fields, add optional fields
- Use case: Consumers upgrade before producers
```
Old Producer → New Consumer ✓
```

**FORWARD:**
- Old schema can read data written with new schema
- Allows: Add fields, delete optional fields
- Use case: Producers upgrade before consumers
```
New Producer → Old Consumer ✓
```

**FULL:**
- Both backward and forward compatible
- Allows: Only add/remove optional fields with defaults
- Most restrictive, safest
```
Old ↔ New  ✓
```

**BACKWARD_TRANSITIVE:**
- New schema compatible with all previous versions
- Not just immediate previous version

**FORWARD_TRANSITIVE:**
- All previous schemas compatible with new schema

**FULL_TRANSITIVE:**
- New schema compatible with all previous, and vice versa

**NONE:**
- No compatibility checking
- Allows any changes
- Use with caution

**Configuration:**
```bash
# Global default
curl -X PUT http://localhost:8081/config \
  -H "Content-Type: application/json" \
  -d '{"compatibility": "BACKWARD"}'

# Per-subject
curl -X PUT http://localhost:8081/config/users-value \
  -d '{"compatibility": "FULL"}'
```

**Recommendation:** Use BACKWARD for most cases, FULL for critical data requiring strict compatibility.

### 5. What serialization formats does Kafka support?

**Answer:** Kafka supports multiple serialization formats through built-in and custom serializers.

**Built-in formats:**

**String:**
```java
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
```
- Simple text data
- UTF-8 encoding
- No schema or type information

**Avro (with Confluent):**
```java
props.put("value.serializer", "io.confluent.kafka.serializers.KafkaAvroSerializer");
props.put("schema.registry.url", "http://localhost:8081");
```
- Binary format, compact
- Schema evolution support
- Type safe

**JSON:**
```java
props.put("value.serializer", "org.apache.kafka.connect.json.JsonSerializer");
```
- Human-readable
- Larger than Avro
- Can include schema or schema-less

**Protobuf:**
```java
props.put("value.serializer", "io.confluent.kafka.serializers.protobuf.KafkaProtobufSerializer");
```
- Google's format
- Efficient binary encoding
- Schema evolution

**Primitive types:**
- IntegerSerializer, LongSerializer, DoubleSerializer
- ByteArraySerializer
- ByteBufferSerializer

**Comparison:**

| Format | Size | Speed | Schema | Readability |
|--------|------|-------|--------|-------------|
| Avro | Smallest | Fast | Yes | Binary |
| Protobuf | Small | Fast | Yes | Binary |
| JSON | Large | Medium | Optional | Human-readable |
| String | Medium | Fastest | No | Human-readable |

**Recommendation:** Avro with Schema Registry for production, JSON for development/debugging.

### 6. What is Avro and why is it commonly used with Kafka?

**Answer:** Avro is a binary serialization framework with rich data structures and a compact format, ideal for Kafka use cases.

**Avro characteristics:**
- **Schema-based**: Data always written/read with schema
- **Binary format**: Compact, efficient storage and transmission
- **Dynamic typing**: Schema resolution at runtime
- **Schema evolution**: Built-in support for compatible changes
- **Language agnostic**: Works across Java, Python, Go, etc.

**Why Avro with Kafka:**

**Compact size:**
- Binary encoding 30-50% smaller than JSON
- Schema not sent with data (only schema ID)
- Reduces network bandwidth and storage

**Schema evolution:**
```json
// Schema with default values for evolution
{
  "type": "record",
  "name": "Order",
  "fields": [
    {"name": "id", "type": "string"},
    {"name": "amount", "type": "double"},
    {"name": "currency", "type": "string", "default": "USD"}
  ]
}
```

**Type safety:**
- Compile-time validation with generated classes
- Runtime validation against schema
- Prevents data quality issues

**Schema Registry integration:**
```java
// Producer automatically registers schema
KafkaAvroSerializer serializer = new KafkaAvroSerializer();
GenericRecord record = new GenericData.Record(schema);
record.put("id", "12345");
record.put("amount", 99.99);
producer.send(new ProducerRecord<>("orders", record));
```

**Performance:**
- Fast serialization/deserialization
- Efficient CPU and memory usage

**Alternatives:** Protobuf (better performance, more complex), JSON (more readable, larger size).

### 7. How do you handle schema changes without breaking consumers?

**Answer:** Handle schema changes safely using compatibility modes, defaults, and phased rollouts.

**Strategies:**

**1. Add fields with defaults (backward compatible):**
```json
{
  "fields": [
    {"name": "id", "type": "string"},
    {"name": "email", "type": ["null", "string"], "default": null}  // New optional field
  ]
}
```
- Old consumers ignore new field
- New consumers use default if field missing

**2. Remove fields (forward compatible):**
- Ensure removed field has default in old schema
- New producers don't send field
- Old consumers use default value

**3. Phased rollout:**
```
1. Register new backward-compatible schema
2. Deploy new consumers (can read old and new data)
3. Deploy new producers (write new schema)
4. Verify all working
5. Remove old consumers
```

**4. Schema aliasing (Avro):**
```json
{
  "name": "emailAddress",
  "type": "string",
  "aliases": ["email"]  // Old name
}
```

**5. Use compatibility checking:**
```bash
# Test compatibility before registering
curl -X POST http://localhost:8081/compatibility/subjects/orders-value/versions/latest \
  -H "Content-Type: application/json" \
  -d @new-schema.json
```

**6. Versioned topics (breaking changes):**
```
orders-v1  →  orders-v2
```
- Create new topic for incompatible changes
- Dual-write during migration
- Migrate consumers gradually

**Best practices:**
- Always add fields with defaults
- Test compatibility before deployment
- Monitor consumer lag during rollout
- Use FULL compatibility for critical data

### 8. What is the difference between backward and forward compatibility?

**Answer:** Backward and forward compatibility differ in which version of schema can read data written by the other.

**Backward compatibility:**
- **Definition**: New schema can read data written with old schema
- **Reader**: New code
- **Writer**: Old code
- **Upgrade order**: Consumers first, then producers

**Example:**
```json
// Old schema
{"fields": [{"name": "id", "type": "int"}]}

// New schema (backward compatible)
{"fields": [
  {"name": "id", "type": "int"},
  {"name": "name", "type": "string", "default": "unknown"}  // Added with default
]}
```
- New consumers can read old messages (use default for missing field)

**Forward compatibility:**
- **Definition**: Old schema can read data written with new schema
- **Reader**: Old code
- **Writer**: New code
- **Upgrade order**: Producers first, then consumers

**Example:**
```json
// Old schema
{"fields": [
  {"name": "id", "type": "int"},
  {"name": "temp", "type": ["null", "string"], "default": null}
]}

// New schema (forward compatible)
{"fields": [
  {"name": "id", "type": "int"}
  // Removed temp field that had default
]}
```
- Old consumers can read new messages (use default for removed field)

**Comparison table:**

| Aspect | Backward | Forward |
|--------|----------|---------|
| **New schema reads old data** | ✓ | ✗ |
| **Old schema reads new data** | ✗ | ✓ |
| **Allowed changes** | Add optional fields | Remove optional fields |
| **Upgrade order** | Consumer → Producer | Producer → Consumer |
| **Use case** | Consumer upgrades first | Producer upgrades first |

**Full compatibility:** Both backward AND forward (most restrictive, safest).

## Monitoring and Operations

### 1. How do you monitor Kafka cluster health?

**Answer:** Monitor Kafka cluster health through broker metrics, ZooKeeper/controller status, and overall cluster state.

**Key health indicators:**

**Broker availability:**
```bash
# List active brokers
kafka-broker-api-versions.sh --bootstrap-server localhost:9092
```
- All expected brokers online
- No brokers in failed state

**Controller status:**
- Exactly one active controller
- No frequent controller elections
- Metric: `kafka.controller:type=KafkaController,name=ActiveControllerCount` = 1

**Under-replicated partitions:**
- Metric: `kafka.server:type=ReplicaManager,name=UnderReplicatedPartitions`
- Should be 0 in healthy cluster
- Non-zero indicates replication issues

**Offline partitions:**
- Metric: `kafka.controller:type=KafkaController,name=OfflinePartitionsCount`
- Should always be 0
- Indicates partitions without leader

**ISR shrinking:**
- Metric: `kafka.server:type=ReplicaManager,name=IsrShrinksPerSec`
- Frequent shrinking indicates slow replicas

**Health check script:**
```bash
# Check cluster metadata
kafka-metadata.sh --snapshot /tmp/metadata --print

# Topic health
kafka-topics.sh --describe --bootstrap-server localhost:9092 --under-replicated-partitions
```

**Alerting thresholds:** Under-replicated > 0, offline partitions > 0, controller count != 1.

### 2. What are the key metrics to monitor in Kafka?

**Answer:** Monitor metrics across brokers, producers, consumers, and topics for comprehensive visibility.

**Broker metrics (JMX):**

**Throughput:**
- `kafka.server:type=BrokerTopicMetrics,name=MessagesInPerSec`
- `kafka.server:type=BrokerTopicMetrics,name=BytesInPerSec`
- `kafka.server:type=BrokerTopicMetrics,name=BytesOutPerSec`

**Request handling:**
- `kafka.network:type=RequestMetrics,name=TotalTimeMs,request={Produce|Fetch}`
- `kafka.network:type=RequestMetrics,name=RequestQueueTimeMs`
- `kafka.network:type=RequestChannel,name=RequestQueueSize`

**Replication:**
- `kafka.server:type=ReplicaManager,name=UnderReplicatedPartitions`
- `kafka.server:type=ReplicaManager,name=IsrShrinksPerSec`
- `kafka.server:type=ReplicaManager,name=LeaderCount`

**Resource usage:**
- `kafka.log:type=LogFlushStats,name=LogFlushRateAndTimeMs`
- `kafka.server:type=BrokerTopicMetrics,name=TotalProduceRequestsPerSec`
- `java.lang:type=Memory,name=HeapMemoryUsage`

**Producer metrics:**
- `record-send-rate`: Messages sent per second
- `record-error-rate`: Failed sends
- `request-latency-avg`: Request latency
- `buffer-available-bytes`: Available buffer memory

**Consumer metrics:**
- `records-consumed-rate`: Consumption rate
- `records-lag-max`: Maximum consumer lag
- `fetch-latency-avg`: Fetch latency
- `commit-latency-avg`: Offset commit latency

**Critical alerts:** Under-replicated > 0, lag increasing, error rates > 0.1%, request latency > 100ms.

### 3. How do you monitor producer performance?

**Answer:** Monitor producer performance using client metrics, broker-side metrics, and throughput/latency measurements.

**Key producer metrics:**

**Throughput metrics:**
```java
// Enable metrics
props.put(ProducerConfig.METRICS_SAMPLE_WINDOW_MS_CONFIG, 30000);

// Access metrics
Map<MetricName, ? extends Metric> metrics = producer.metrics();
Metric recordSendRate = metrics.get(new MetricName(
    "record-send-rate", "producer-metrics", "", tags));
```

**Important metrics:**
- `record-send-rate`: Records sent per second
- `byte-send-rate`: Bytes sent per second
- `record-queue-time-avg`: Time records wait in buffer
- `request-latency-avg`: Average request latency
- `batch-size-avg`: Average batch size
- `compression-rate-avg`: Compression effectiveness
- `buffer-available-bytes`: Available buffer space

**Error metrics:**
- `record-error-rate`: Failed sends per second
- `record-retry-rate`: Retries per second
- `request-timeout-rate`: Timeouts

**Latency percentiles:**
- `request-latency-max`: Maximum latency
- `record-send-total`: Total records sent

**Monitoring strategy:**
```java
producer.metrics().forEach((name, metric) -> {
    if (name.group().equals("producer-metrics")) {
        System.out.println(name.name() + ": " + metric.metricValue());
    }
});
```

**Performance indicators:**
- High `record-send-rate`: Good throughput
- Low `request-latency-avg`: Good performance
- High `batch-size-avg`: Good batching
- Low `buffer-available-bytes`: Potential bottleneck

### 4. How do you monitor consumer lag?

**Answer:** Monitor consumer lag to ensure consumers keep pace with producers and avoid data freshness issues.

**Consumer lag definition:**
- Lag = Latest offset - Consumer offset
- Measures how far behind consumer is from latest message

**Monitoring methods:**

**1. kafka-consumer-groups command:**
```bash
kafka-consumer-groups.sh --bootstrap-server localhost:9092 \
  --group my-group --describe

# Output shows LAG column per partition
TOPIC    PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG
orders   0          1000            1250            250
orders   1          2000            2000            0
```

**2. Consumer metrics:**
```java
Metric lag = consumer.metrics().get(new MetricName(
    "records-lag-max", "consumer-fetch-manager-metrics", "", tags));
```

**Key metrics:**
- `records-lag-max`: Maximum lag across partitions
- `records-lag`: Current lag per partition
- `records-lead-min`: Minimum lead (for time-based lag)

**3. External tools:**
- **Burrow**: LinkedIn's lag monitoring tool
- **Confluent Control Center**: Visual lag monitoring
- **Prometheus + Grafana**: Custom dashboards

**4. JMX metrics:**
```bash
# Consumer lag per partition
kafka.consumer:type=consumer-fetch-manager-metrics,client-id=*,partition=*,topic=*,name=records-lag
```

**Alerting thresholds:**
- **Warning**: Lag > 1000 messages or > 1 minute time lag
- **Critical**: Lag growing continuously
- **SLA-based**: Lag > acceptable data freshness window

**Remediation:** Scale consumers, optimize processing, increase `max.poll.records`, check for slow external dependencies.

### 5. What tools can be used for Kafka monitoring?

**Answer:** Multiple open-source and commercial tools provide Kafka monitoring capabilities.

**Open-source tools:**

**Prometheus + Grafana:**
```yaml
# Prometheus JMX Exporter
- job_name: 'kafka'
  static_configs:
    - targets: ['kafka-broker:7071']
```
- JMX Exporter exposes Kafka metrics
- Prometheus scrapes and stores metrics
- Grafana dashboards for visualization
- Most popular open-source stack

**Kafka Manager (CMAK):**
- Web UI for cluster management
- Topic management and monitoring
- Consumer group monitoring
- Partition reassignment

**Burrow:**
- Consumer lag monitoring specifically
- Lag evaluation algorithm
- HTTP API for lag status
- No need for consumer metrics

**Kafdrop:**
- Web UI for browsing Kafka clusters
- View topics, partitions, messages
- Consumer group monitoring
- Lightweight, easy deployment

**Commercial tools:**

**Confluent Control Center:**
- Enterprise monitoring and management
- Real-time metrics and alerting
- Stream lineage and data flow
- Built-in with Confluent Platform

**Datadog:**
- SaaS monitoring platform
- Kafka integration
- Dashboards and alerts
- APM integration

**New Relic, Dynatrace:**
- Application performance monitoring
- Kafka agent/integration
- End-to-end visibility

**Cloud-native:**
- **AWS CloudWatch**: MSK integration
- **Confluent Cloud**: Built-in monitoring
- **Azure Monitor**: Event Hubs (Kafka-compatible)

**Recommendation:** Prometheus + Grafana for flexibility, Confluent Control Center for enterprise features, Burrow specifically for lag monitoring.

### 6. How do you troubleshoot high consumer lag?

**Answer:** Troubleshoot consumer lag by identifying bottlenecks and applying targeted optimizations.

**Diagnostic steps:**

**1. Identify lag source:**
```bash
kafka-consumer-groups.sh --describe --group my-group

# Check which partitions have high lag
# Check if lag growing or stable
```

**2. Check consumer metrics:**
- `fetch-rate`: Is consumer fetching messages?
- `records-consumed-rate`: Consumption rate
- `poll-idle-ratio`: Time spent waiting vs processing

**3. Analyze processing time:**
```java
long start = System.currentTimeMillis();
ConsumerRecords<> records = consumer.poll(Duration.ofMillis(100));
long fetchTime = System.currentTimeMillis() - start;

// Process records
long processTime = System.currentTimeMillis() - start - fetchTime;
```

**Common causes and solutions:**

**Slow processing:**
- **Symptom**: Low `records-consumed-rate`, long processing time
- **Solution**: Optimize business logic, use parallel processing, increase consumers

**Under-provisioned consumers:**
- **Symptom**: Fewer consumers than partitions
- **Solution**: Add more consumer instances (up to partition count)

**Small fetch batches:**
```java
props.put("max.poll.records", 500);       // Increase batch size
props.put("fetch.min.bytes", 1024);       // Fetch minimum bytes
props.put("fetch.max.wait.ms", 500);      // Wait for batch
```

**External dependencies:**
- **Symptom**: Consumers waiting on database/API calls
- **Solution**: Batch external calls, use async I/O, add caching

**Consumer rebalancing:**
- **Symptom**: Frequent rebalances in logs
- **Solution**: Increase `max.poll.interval.ms`, reduce processing time

**Network issues:**
- **Symptom**: High `fetch-latency-avg`
- **Solution**: Check network, move consumers closer to brokers

**Monitoring during troubleshooting:**
```bash
# Watch lag in real-time
watch -n 5 'kafka-consumer-groups.sh --describe --group my-group'
```

### 7. What is JMX and how is it used with Kafka?

**Answer:** JMX (Java Management Extensions) is a Java technology for monitoring and managing applications, extensively used by Kafka for exposing metrics.

**JMX in Kafka:**
- All Kafka components (brokers, producers, consumers) expose JMX metrics
- Metrics organized in MBeans (Managed Beans)
- Accessible via JMX port (default 9999)
- Standard Java monitoring interface

**Enabling JMX:**
```bash
# Broker
export KAFKA_JMX_OPTS="-Dcom.sun.management.jmxremote \
  -Dcom.sun.management.jmxremote.port=9999 \
  -Dcom.sun.management.jmxremote.authenticate=false \
  -Dcom.sun.management.jmxremote.ssl=false"

kafka-server-start.sh config/server.properties
```

**Accessing JMX metrics:**

**1. JConsole (GUI):**
```bash
jconsole localhost:9999
# Navigate to MBeans tab
# Browse kafka.* metrics
```

**2. Command-line (cmdline-jmxclient):**
```bash
java -jar cmdline-jmxclient.jar - localhost:9999 \
  kafka.server:type=BrokerTopicMetrics,name=MessagesInPerSec
```

**3. Programmatically:**
```java
JMXServiceURL url = new JMXServiceURL("service:jmx:rmi:///jndi/rmi://localhost:9999/jmxrmi");
JMXConnector connector = JMXConnectorFactory.connect(url);
MBeanServerConnection mbsc = connector.getMBeanServerConnection();

ObjectName name = new ObjectName("kafka.server:type=BrokerTopicMetrics,name=MessagesInPerSec");
Object value = mbsc.getAttribute(name, "OneMinuteRate");
```

**4. JMX Exporter (for Prometheus):**
```yaml
# jmx_exporter config
rules:
  - pattern: "kafka.server<type=(.+), name=(.+)><>(.+)"
    name: kafka_server_$1_$2_$3
```

**Common JMX metrics:**
- `kafka.server:type=BrokerTopicMetrics,name=*`
- `kafka.network:type=RequestMetrics,name=*`
- `kafka.controller:type=KafkaController,name=*`

### 8. How do you perform a rolling restart of Kafka brokers?

**Answer:** Rolling restart upgrades or restarts brokers one at a time without cluster downtime.

**Prerequisites:**
- Replication factor ≥ 2
- `min.insync.replicas` configured properly
- No under-replicated partitions

**Rolling restart procedure:**

**1. Prepare:**
```bash
# Verify cluster health
kafka-topics.sh --describe --under-replicated-partitions

# Ensure no under-replicated partitions
# Check controller (don't restart controller first if possible)
```

**2. For each broker:**

**a. Identify broker to restart:**
```bash
# Start with non-controller brokers
kafka-broker-api-versions.sh --bootstrap-server localhost:9092
```

**b. Gracefully shutdown broker:**
```bash
kafka-server-stop.sh
# Or: kill -TERM <kafka-pid>
# Do NOT use kill -9 (unsafe)
```

**c. Wait for shutdown:**
```bash
# Check logs for "shut down completed"
tail -f logs/server.log
```

**d. Make changes if needed:**
```bash
# Update configuration, upgrade binaries, etc.
vim config/server.properties
```

**e. Start broker:**
```bash
kafka-server-start.sh -daemon config/server.properties
```

**f. Verify broker health:**
```bash
# Check broker joined cluster
kafka-broker-api-versions.sh --bootstrap-server localhost:9092

# Wait for under-replicated partitions to reach 0
kafka-topics.sh --describe --under-replicated-partitions

# Check logs
tail -f logs/server.log | grep -i "started"
```

**g. Wait before next broker:**
- Ensure ISR catches up (under-replicated = 0)
- Typically 5-15 minutes depending on data volume

**3. Repeat for remaining brokers**

**Best practices:**
- Restart during low-traffic periods
- Monitor under-replicated partitions between restarts
- Save controller broker for last
- Set `controlled.shutdown.enable=true`

### 9. What is a graceful shutdown in Kafka?

**Answer:** Graceful shutdown properly closes Kafka broker connections, syncs data, and transfers leadership before stopping.

**Graceful shutdown process:**

**1. Enable controlled shutdown:**
```properties
# server.properties
controlled.shutdown.enable=true
controlled.shutdown.max.retries=3
controlled.shutdown.retry.backoff.ms=5000
```

**2. Shutdown sequence:**
```bash
# Use stop script (sends SIGTERM)
kafka-server-stop.sh

# Or manually
kill -TERM <kafka-pid>  # Graceful
# NOT: kill -9 <kafka-pid>  # Forceful, ungraceful
```

**3. Broker actions during graceful shutdown:**
- **Stop accepting new connections**: No new client requests
- **Complete in-flight requests**: Finish processing current requests
- **Transfer partition leadership**: Elect new leaders for partitions
- **Sync data to disk**: Flush all unwritten data
- **Update ZooKeeper/controller**: Deregister from cluster
- **Close file handles**: Clean resource cleanup
- **Exit process**: Shutdown JVM

**Benefits vs forceful shutdown:**

| Aspect | Graceful (SIGTERM) | Forceful (SIGKILL) |
|--------|-------------------|-------------------|
| **Partition leadership** | Transferred before shutdown | Waits for timeout, then election |
| **Data sync** | Flushed to disk | May lose unflushed data |
| **Downtime** | Minimal (~seconds) | Higher (~30s+ for leader election) |
| **Consumer impact** | Seamless failover | Brief interruption |
| **Log corruption** | None | Risk of corruption |

**Monitoring graceful shutdown:**
```bash
# Check logs
tail -f logs/server.log | grep -i shutdown

# Expected log messages:
# "Starting controlled shutdown"
# "Completed transfer of leadership"
# "Shut down completed"
```

**Timeout handling:**
- If shutdown takes too long, may need to force kill
- Increase `controlled.shutdown.max.retries` for large clusters
- Check for stuck threads or I/O issues

### 10. How do you handle disk failures in Kafka?

**Answer:** Handle disk failures through RAID configuration, failed disk detection, and broker replacement strategies.

**Prevention:**

**1. RAID configuration:**
- **RAID 10**: Recommended for Kafka (performance + redundancy)
- **RAID 0**: Performance only, no redundancy (not recommended)
- **RAID 5/6**: Redundancy but slower writes
- Multiple disks per broker for better I/O

**2. Log directory configuration:**
```properties
# Distribute logs across multiple disks
log.dirs=/mnt/disk1/kafka,/mnt/disk2/kafka,/mnt/disk3/kafka
```
- Kafka distributes partitions across log directories
- Single disk failure affects only subset of partitions

**Failure detection:**

**3. Enable disk failure handling:**
```properties
# Kafka 1.1+
disk.failure.handling.enabled=true

# Broker stays running even if one disk fails
# Failed partitions marked as offline
# Replication continues from other replicas
```

**Recovery procedures:**

**4. Partial disk failure (multiple log.dirs):**
```bash
# Broker continues running
# Check broker logs for disk errors
# Monitor under-replicated partitions

# Replace failed disk
# Stop broker gracefully
# Replace disk, mount new disk
# Delete data from failed disk path in log.dirs
# Restart broker - Kafka replicates from ISR
```

**5. Complete disk failure (single disk):**
```bash
# Broker will crash or become unresponsive
# Controller detects broker failure
# Leader election for affected partitions
# Replicas on other brokers promoted to leaders

# Recovery:
# 1. Replace failed disk
# 2. Stop broker (if running)
# 3. Delete all data in log.dirs
# 4. Start broker - joins cluster as empty broker
# 5. Kafka replicates all assigned partitions from leaders
```

**6. Prevent data loss:**
- **Replication factor ≥ 3**: Survive multiple failures
- **min.insync.replicas ≥ 2**: Ensure data written to multiple brokers
- **Monitoring**: Alert on disk failures immediately
- **Backups**: MirrorMaker or Confluent Replicator for DR

**Monitoring disk health:**
```bash
# Check disk usage
df -h /mnt/kafka*

# Check SMART status
smartctl -a /dev/sda

# Monitor metrics
kafka.log:type=LogFlushStats,name=LogFlushRateAndTimeMs
```

## Security

### 1. What security features does Kafka provide?

**Answer:** Kafka provides comprehensive security features for authentication, authorization, and encryption.

**Core security features:**

**Authentication:**
- **SSL/TLS**: Certificate-based authentication
- **SASL/PLAIN**: Username/password
- **SASL/SCRAM**: Salted challenge response
- **SASL/GSSAPI (Kerberos)**: Enterprise authentication
- **SASL/OAUTHBEARER**: OAuth 2.0 token-based
- **Delegation tokens**: Temporary credentials

**Authorization:**
- **ACLs (Access Control Lists)**: Fine-grained permissions
- **Resource-based**: Control access to topics, consumer groups, clusters
- **Operation-based**: Read, write, describe, create, delete
- **Pluggable authorizer**: Custom authorization logic

**Encryption:**
- **TLS/SSL**: Data in transit encryption
- **Client-broker**: Encrypted connections
- **Broker-broker**: Inter-broker encryption
- **Data at rest**: Via filesystem encryption (external to Kafka)

**Audit:**
- **Authorization logs**: Track access attempts
- **Audit logs**: Record security events

**Multi-tenancy:**
- **Quotas**: Rate limiting per client/user
- **User/group isolation**: Separate topics per tenant

**Configuration example:**
```properties
# Authentication
security.inter.broker.protocol=SASL_SSL
sasl.mechanism.inter.broker.protocol=PLAIN

# Authorization
authorizer.class.name=kafka.security.authorizer.AclAuthorizer
allow.everyone.if.no.acl.found=false

# Encryption
ssl.keystore.location=/var/private/ssl/kafka.server.keystore.jks
ssl.truststore.location=/var/private/ssl/kafka.server.truststore.jks
```

### 2. How do you enable SSL/TLS encryption in Kafka?

**Answer:** Enable SSL/TLS by generating certificates, configuring brokers and clients with keystores/truststores.

**Step-by-step setup:**

**1. Generate certificates:**
```bash
# Create Certificate Authority (CA)
openssl req -new -x509 -keyout ca-key -out ca-cert -days 365

# Create broker keystore
keytool -keystore kafka.server.keystore.jks -alias localhost \
  -keyalg RSA -validity 365 -genkey

# Create certificate signing request
keytool -keystore kafka.server.keystore.jks -alias localhost \
  -certreq -file cert-file

# Sign certificate with CA
openssl x509 -req -CA ca-cert -CAkey ca-key \
  -in cert-file -out cert-signed -days 365 -CAcreateserial

# Import CA cert and signed cert into keystore
keytool -keystore kafka.server.keystore.jks -alias CARoot \
  -import -file ca-cert
keytool -keystore kafka.server.keystore.jks -alias localhost \
  -import -file cert-signed

# Create truststore
keytool -keystore kafka.server.truststore.jks -alias CARoot \
  -import -file ca-cert
```

**2. Broker configuration:**
```properties
# server.properties
listeners=SSL://kafka.example.com:9093
advertised.listeners=SSL://kafka.example.com:9093
security.inter.broker.protocol=SSL

# SSL settings
ssl.keystore.location=/var/private/ssl/kafka.server.keystore.jks
ssl.keystore.password=keystore-password
ssl.key.password=key-password
ssl.truststore.location=/var/private/ssl/kafka.server.truststore.jks
ssl.truststore.password=truststore-password

# Optional: Client authentication
ssl.client.auth=required
```

**3. Client configuration:**
```java
Properties props = new Properties();
props.put("bootstrap.servers", "kafka.example.com:9093");
props.put("security.protocol", "SSL");

props.put("ssl.truststore.location", "/var/private/ssl/kafka.client.truststore.jks");
props.put("ssl.truststore.password", "truststore-password");

// If client auth required
props.put("ssl.keystore.location", "/var/private/ssl/kafka.client.keystore.jks");
props.put("ssl.keystore.password", "keystore-password");
props.put("ssl.key.password", "key-password");
```

**4. Verify connection:**
```bash
openssl s_client -connect kafka.example.com:9093
```

**Performance impact:** SSL adds 10-30% latency overhead but necessary for security.

### 3. What is SASL and how is it used in Kafka?

**Answer:** SASL (Simple Authentication and Security Layer) is a framework for authentication in Kafka supporting multiple mechanisms.

**SASL mechanisms in Kafka:**

**SASL/PLAIN (username/password):**
```properties
# Broker
listeners=SASL_SSL://kafka.example.com:9093
security.inter.broker.protocol=SASL_SSL
sasl.mechanism.inter.broker.protocol=PLAIN
sasl.enabled.mechanisms=PLAIN

# JAAS config
listener.name.sasl_ssl.plain.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
  username="admin" \
  password="admin-secret" \
  user_admin="admin-secret" \
  user_alice="alice-secret";
```

**SASL/SCRAM (more secure):**
```bash
# Create SCRAM credentials
kafka-configs.sh --zookeeper localhost:2181 \
  --alter --add-config 'SCRAM-SHA-256=[password=alice-secret]' \
  --entity-type users --entity-name alice

# Broker config
sasl.enabled.mechanisms=SCRAM-SHA-256
```

**SASL/GSSAPI (Kerberos):**
```properties
# For enterprise environments
sasl.mechanism.inter.broker.protocol=GSSAPI
sasl.kerberos.service.name=kafka
```

**SASL/OAUTHBEARER:**
```properties
# OAuth 2.0 token-based
sasl.enabled.mechanisms=OAUTHBEARER
```

**Client configuration:**
```java
Properties props = new Properties();
props.put("bootstrap.servers", "kafka:9093");
props.put("security.protocol", "SASL_SSL");
props.put("sasl.mechanism", "PLAIN");
props.put("sasl.jaas.config",
  "org.apache.kafka.common.security.plain.PlainLoginModule required " +
  "username='alice' password='alice-secret';");
```

**Choosing mechanism:**
- **Development**: PLAIN (simple but insecure over non-SSL)
- **Production**: SCRAM-SHA-256 or SCRAM-SHA-512
- **Enterprise**: GSSAPI/Kerberos
- **Modern**: OAUTHBEARER with identity providers

### 4. How do you implement authentication in Kafka?

**Answer:** Implement authentication by configuring security protocols and authentication mechanisms on brokers and clients.

**Authentication setup:**

**1. Choose security protocol:**
- `PLAINTEXT`: No encryption, no auth (development only)
- `SSL`: TLS encryption + optional SSL auth
- `SASL_PLAINTEXT`: SASL auth, no encryption
- `SASL_SSL`: SASL auth + TLS encryption (recommended for production)

**2. Broker configuration:**
```properties
# Enable SASL_SSL
listeners=SASL_SSL://0.0.0.0:9093
security.inter.broker.protocol=SASL_SSL
sasl.mechanism.inter.broker.protocol=SCRAM-SHA-256
sasl.enabled.mechanisms=SCRAM-SHA-256

# SSL settings
ssl.keystore.location=/path/to/keystore.jks
ssl.keystore.password=keystore-pass
ssl.truststore.location=/path/to/truststore.jks
ssl.truststore.password=truststore-pass
```

**3. Create user credentials (SCRAM):**
```bash
kafka-configs.sh --bootstrap-server localhost:9093 \
  --alter --add-config 'SCRAM-SHA-256=[password=user-secret]' \
  --entity-type users --entity-name username \
  --command-config admin.properties
```

**4. Producer authentication:**
```java
Properties props = new Properties();
props.put("bootstrap.servers", "kafka:9093");
props.put("security.protocol", "SASL_SSL");
props.put("sasl.mechanism", "SCRAM-SHA-256");
props.put("sasl.jaas.config",
  "org.apache.kafka.common.security.scram.ScramLoginModule required " +
  "username='alice' password='alice-secret';");

props.put("ssl.truststore.location", "/path/to/truststore.jks");
props.put("ssl.truststore.password", "truststore-pass");
```

**5. Consumer authentication:**
```java
// Same configuration as producer
```

**Verify authentication:**
- Check broker logs for successful authentication
- Failed auth attempts logged with "Authentication failed"
- Test with incorrect credentials to verify rejection

### 5. How do you implement authorization in Kafka?

**Answer:** Implement authorization using ACLs (Access Control Lists) to control which users can perform which operations on resources.

**Enable authorization:**
```properties
# server.properties
authorizer.class.name=kafka.security.authorizer.AclAuthorizer
super.users=User:admin;User:kafka

# Deny by default
allow.everyone.if.no.acl.found=false
```

**ACL operations:**
- Read, Write, Create, Delete, Alter, Describe, ClusterAction, etc.

**Resource types:**
- Topic, Group (consumer group), Cluster, TransactionalId

**Grant ACLs:**
```bash
# Allow alice to write to orders topic
kafka-acls.sh --bootstrap-server localhost:9093 \
  --add --allow-principal User:alice \
  --operation Write --topic orders \
  --command-config admin.properties

# Allow bob to read from orders and commit offsets
kafka-acls.sh --bootstrap-server localhost:9093 \
  --add --allow-principal User:bob \
  --operation Read --topic orders \
  --command-config admin.properties

kafka-acls.sh --bootstrap-server localhost:9093 \
  --add --allow-principal User:bob \
  --operation Read --group my-consumer-group \
  --command-config admin.properties

# Allow admin to create topics
kafka-acls.sh --bootstrap-server localhost:9093 \
  --add --allow-principal User:admin \
  --operation Create --cluster \
  --command-config admin.properties
```

**List ACLs:**
```bash
kafka-acls.sh --bootstrap-server localhost:9093 \
  --list --topic orders \
  --command-config admin.properties
```

**Remove ACLs:**
```bash
kafka-acls.sh --bootstrap-server localhost:9093 \
  --remove --allow-principal User:alice \
  --operation Write --topic orders \
  --command-config admin.properties
```

**Wildcard ACLs:**
```bash
# Allow alice to read from all topics starting with "public-"
kafka-acls.sh --add --allow-principal User:alice \
  --operation Read --topic "public-*" \
  --resource-pattern-type prefixed
```

**Testing authorization:**
- Try operations without ACL - should fail with `AuthorizationException`
- Grant ACL - operation should succeed
- Monitor authorization failures in broker logs

### 6. What are ACLs (Access Control Lists) in Kafka?

**Answer:** ACLs are rules that specify which principals (users) can perform which operations on which resources in Kafka.

**ACL structure:**
```
(Principal, Operation, Resource, Permission)
```

**Components:**

**Principal:**
- User: `User:alice`, `User:CN=alice,OU=eng`
- Wildcard: `User:*` (all users)

**Operations:**
- Read, Write, Create, Delete, Alter, Describe
- ClusterAction, DescribeConfigs, AlterConfigs
- IdempotentWrite, All

**Resources:**
- Topic: `Topic:orders`
- Group: `Group:consumer-group-1`
- Cluster: `Cluster:kafka-cluster`
- TransactionalId: `TransactionalId:tx-1`

**Permission:**
- Allow: Permit operation
- Deny: Explicitly deny (takes precedence over Allow)

**ACL examples:**

**Producer ACLs:**
```bash
# Write to topic
kafka-acls.sh --add --allow-principal User:producer \
  --operation Write --topic orders

# Describe topic (get metadata)
kafka-acls.sh --add --allow-principal User:producer \
  --operation Describe --topic orders

# Idempotent producer
kafka-acls.sh --add --allow-principal User:producer \
  --operation IdempotentWrite --cluster
```

**Consumer ACLs:**
```bash
# Read from topic
kafka-acls.sh --add --allow-principal User:consumer \
  --operation Read --topic orders

# Read from consumer group
kafka-acls.sh --add --allow-principal User:consumer \
  --operation Read --group my-group
```

**Admin ACLs:**
```bash
# Create topics
kafka-acls.sh --add --allow-principal User:admin \
  --operation Create --cluster

# Delete topics
kafka-acls.sh --add --allow-principal User:admin \
  --operation Delete --topic '*'
```

**Storage:** ACLs stored in ZooKeeper (`/kafka-acl`) or KRaft metadata.

**Default behavior:** `allow.everyone.if.no.acl.found=false` denies all unless explicitly allowed.

### 7. How do you secure data in transit?

**Answer:** Secure data in transit using TLS/SSL encryption for all client-broker and broker-broker communication.

**Implementation:**

**1. Enable SSL on brokers:**
```properties
# server.properties
listeners=SSL://0.0.0.0:9093,PLAINTEXT://0.0.0.0:9092
advertised.listeners=SSL://kafka1:9093,PLAINTEXT://kafka1:9092

# Inter-broker encryption
security.inter.broker.protocol=SSL

# SSL configuration
ssl.keystore.location=/var/ssl/kafka.server.keystore.jks
ssl.keystore.password=keystore-password
ssl.key.password=key-password
ssl.truststore.location=/var/ssl/kafka.server.truststore.jks
ssl.truststore.password=truststore-password

# Cipher suites (optional, for stronger security)
ssl.cipher.suites=TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256

# Protocol versions
ssl.enabled.protocols=TLSv1.2,TLSv1.3
```

**2. Client SSL configuration:**
```java
Properties props = new Properties();
props.put("bootstrap.servers", "kafka1:9093");
props.put("security.protocol", "SSL");

props.put("ssl.truststore.location", "/path/to/client.truststore.jks");
props.put("ssl.truststore.password", "truststore-password");

// Mutual TLS (client authentication)
props.put("ssl.keystore.location", "/path/to/client.keystore.jks");
props.put("ssl.keystore.password", "keystore-password");
props.put("ssl.key.password", "key-password");
```

**3. ZooKeeper-Kafka encryption:**
```properties
# Kafka to ZooKeeper
zookeeper.ssl.client.enable=true
zookeeper.clientCnxnSocket=org.apache.zookeeper.ClientCnxnSocketNetty
zookeeper.ssl.keystore.location=/path/to/keystore.jks
zookeeper.ssl.truststore.location=/path/to/truststore.jks
```

**4. Schema Registry SSL:**
```properties
schema.registry.url=https://schema-registry:8081
schema.registry.ssl.truststore.location=/path/to/truststore.jks
schema.registry.ssl.truststore.password=password
```

**Verification:**
```bash
# Check SSL connection
openssl s_client -connect kafka1:9093

# Verify cipher
echo | openssl s_client -connect kafka1:9093 2>/dev/null | grep Cipher
```

**Performance:** SSL adds 10-30% overhead but essential for security in production.

### 8. How do you secure data at rest in Kafka?

**Answer:** Secure data at rest using filesystem encryption and secure broker deployment practices.

**Filesystem encryption:**

**1. Linux dm-crypt/LUKS:**
```bash
# Encrypt disk partition
cryptsetup luksFormat /dev/sdb1
cryptsetup luksOpen /dev/sdb1 kafka_encrypted

# Create filesystem
mkfs.ext4 /dev/mapper/kafka_encrypted

# Mount encrypted volume
mount /dev/mapper/kafka_encrypted /var/lib/kafka
```

**2. Cloud provider encryption:**
- **AWS**: EBS encryption with KMS
- **GCP**: Persistent disk encryption
- **Azure**: Azure Disk Encryption

**3. Configure Kafka on encrypted filesystem:**
```properties
log.dirs=/var/lib/kafka/logs  # Points to encrypted mount
```

**Additional security measures:**

**File system permissions:**
```bash
# Restrict access to Kafka user only
chown -R kafka:kafka /var/lib/kafka
chmod 700 /var/lib/kafka
```

**Secure log directory:**
```properties
# Delete segments securely (optional)
log.cleaner.delete.retention.ms=86400000
```

**Key management:**
- Store encryption keys in hardware security module (HSM)
- Use cloud KMS (AWS KMS, Azure Key Vault, GCP KMS)
- Rotate encryption keys periodically
- Never store keys in application code

**Limitations:**
- Kafka doesn't provide built-in data-at-rest encryption
- Must rely on OS/filesystem/cloud encryption
- Performance impact minimal with hardware-accelerated encryption

**Compliance:**
- GDPR, HIPAA, PCI-DSS may require encryption at rest
- Document encryption implementation for audits
- Combine with access controls and audit logging

### 9. What is the role of the Authorizer interface?

**Answer:** The Authorizer interface allows custom authorization logic to be plugged into Kafka for fine-grained access control.

**Default implementation:**
```properties
authorizer.class.name=kafka.security.authorizer.AclAuthorizer
```

**Custom authorizer:**
```java
public class CustomAuthorizer implements Authorizer {
    @Override
    public void configure(Map<String, ?> configs) {
        // Initialize (load config, connect to external authz service)
    }

    @Override
    public List<AuthorizationResult> authorize(
        AuthorizableRequestContext requestContext,
        List<Action> actions) {

        // Custom authorization logic
        String principal = requestContext.principal().getName();
        List<AuthorizationResult> results = new ArrayList<>();

        for (Action action : actions) {
            ResourcePattern resource = action.resourcePattern();
            AclOperation operation = action.operation();

            // Check against custom rules
            if (checkPermission(principal, resource, operation)) {
                results.add(AuthorizationResult.ALLOWED);
            } else {
                results.add(AuthorizationResult.DENIED);
            }
        }

        return results;
    }

    private boolean checkPermission(String principal,
                                   ResourcePattern resource,
                                   AclOperation operation) {
        // Implement custom logic:
        // - Query external authorization service (e.g., OPA, AWS IAM)
        // - Apply business-specific rules
        // - Integrate with LDAP/Active Directory
        // - Time-based access control
        // - Rate limiting per user
        return true;
    }

    @Override
    public void close() {
        // Cleanup resources
    }
}
```

**Configuration:**
```properties
authorizer.class.name=com.example.CustomAuthorizer

# Custom authorizer config
custom.authorizer.url=https://authz-service:8080
custom.authorizer.cache.ttl=300
```

**Use cases:**
- **External authorization**: Integrate with enterprise authz systems (OPA, AWS IAM)
- **Attribute-based access control (ABAC)**: Dynamic rules based on attributes
- **Time-based access**: Allow access only during business hours
- **IP-based restrictions**: Restrict by client IP/location
- **Custom audit logging**: Log authorization decisions to external system
- **Multi-tenancy**: Complex tenant isolation rules

**Interface methods:**
- `configure()`: Initialize authorizer
- `authorize()`: Check if action allowed
- `createAcls()`: Create ACL entries (optional)
- `deleteAcls()`: Delete ACL entries (optional)
- `acls()`: List ACLs (optional)

**Performance:** Cache authorization decisions, use async lookups, monitor latency impact on request handling.

## Advanced Topics

### 1. What is MirrorMaker and when would you use it?

**Answer:** MirrorMaker is Kafka's cross-cluster replication tool for mirroring data between Kafka clusters.

**MirrorMaker 2 (current version):**
- Built on Kafka Connect framework
- Bi-directional replication support
- Automatic topic creation and configuration sync
- Offset translation for failover
- Replication metrics and monitoring

**Architecture:**
```
Source Cluster → MirrorMaker 2 → Target Cluster
```

**Use cases:**

**Disaster recovery:**
- Replicate production cluster to DR site
- Failover capability during outages
- Business continuity

**Data aggregation:**
- Collect data from multiple regional clusters into central cluster
- Multi-datacenter consolidation

**Cloud migration:**
- Gradual migration from on-prem to cloud
- Hybrid cloud deployments

**Active-active replication:**
- Bi-directional replication between clusters
- Multi-region writes

**Configuration:**
```properties
# mm2.properties
clusters = primary, backup
primary.bootstrap.servers = primary-kafka:9092
backup.bootstrap.servers = backup-kafka:9092

# Replication flow
primary->backup.enabled = true
primary->backup.topics = orders.*, payments.*

# Offset sync
sync.topic.acls.enabled = true
emit.checkpoints.enabled = true
```

**Starting MirrorMaker 2:**
```bash
connect-mirror-maker.sh mm2.properties
```

**When to use:** DR, geo-replication, data center migration, aggregation across regions.

### 2. How do you set up multi-datacenter replication?

**Answer:** Multi-datacenter replication requires careful planning of network topology, replication patterns, and failover strategies.

**Replication patterns:**

**1. Active-Passive (DR):**
```
Primary DC (Active) → MirrorMaker → Backup DC (Passive)
```
- Primary handles all traffic
- Backup for disaster recovery
- Simplest to manage

**2. Active-Active (Multi-region writes):**
```
DC1 ↔ MirrorMaker ↔ DC2
```
- Both datacenters accept writes
- Complex conflict resolution
- Lower latency for regional users

**3. Hub-and-Spoke (Aggregation):**
```
Regional DC1 → MirrorMaker → Central DC
Regional DC2 → MirrorMaker → Central DC
```
- Regional clusters for local processing
- Central cluster for analytics

**Setup steps:**

**1. Network configuration:**
```bash
# Ensure connectivity between clusters
telnet backup-kafka:9092

# Consider VPN or direct connect for security and performance
```

**2. Topic configuration:**
```bash
# Create topics with same partitions in both clusters
kafka-topics.sh --create --topic orders \
  --partitions 10 --replication-factor 3 \
  --bootstrap-server primary:9092

kafka-topics.sh --create --topic orders \
  --partitions 10 --replication-factor 3 \
  --bootstrap-server backup:9092
```

**3. MirrorMaker 2 configuration:**
```properties
clusters = us-west, us-east

us-west.bootstrap.servers = us-west-kafka:9092
us-east.bootstrap.servers = us-east-kafka:9092

us-west->us-east.enabled = true
us-west->us-east.topics = .*
us-west->us-east.groups = .*

# Offset sync for consumer failover
emit.checkpoints.enabled = true
sync.group.offsets.enabled = true
refresh.groups.interval.seconds = 60

# Topic config sync
sync.topic.configs.enabled = true
```

**4. Consumer failover:**
```java
// Consumers use RemoteClusterUtils for offset translation
Map<TopicPartition, OffsetAndMetadata> translatedOffsets =
    RemoteClusterUtils.translateOffsets(sourceOffsets, "us-west", "us-east");
```

**5. Monitoring:**
- Replication lag per topic
- Network bandwidth usage
- Checkpoint lag for consumer groups
- End-to-end latency

**Best practices:**
- Use dedicated replication cluster
- Monitor network latency between DCs
- Test failover procedures regularly
- Set appropriate retention in both clusters
- Use compression to reduce bandwidth

### 3. What is rack awareness in Kafka?

**Answer:** Rack awareness is a feature that ensures replicas are distributed across different racks/availability zones for better fault tolerance.

**Purpose:**
- Protect against rack-level failures (power, network, cooling)
- Distribute replicas across physical infrastructure
- Improve availability in multi-AZ deployments

**Configuration:**

**1. Set broker rack ID:**
```properties
# server.properties on broker 1 (rack A)
broker.id=1
broker.rack=rack-a

# server.properties on broker 2 (rack B)
broker.id=2
broker.rack=rack-b

# server.properties on broker 3 (rack C)
broker.id=3
broker.rack=rack-c
```

**2. Create rack-aware topic:**
```bash
kafka-topics.sh --create --topic orders \
  --partitions 6 --replication-factor 3 \
  --bootstrap-server localhost:9092

# Kafka automatically distributes replicas across racks
```

**Example distribution:**
```
Partition 0: Leader=Broker1(rack-a), Followers=[Broker2(rack-b), Broker3(rack-c)]
Partition 1: Leader=Broker2(rack-b), Followers=[Broker3(rack-c), Broker1(rack-a)]
Partition 2: Leader=Broker3(rack-c), Followers=[Broker1(rack-a), Broker2(rack-b)]
```

**Cloud deployment:**
```properties
# AWS AZs as racks
broker.rack=us-east-1a  # Broker in AZ 1a
broker.rack=us-east-1b  # Broker in AZ 1b
broker.rack=us-east-1c  # Broker in AZ 1c
```

**Benefits:**
- Survive rack/AZ failures
- No two replicas on same rack
- Better disaster recovery
- Reduced blast radius

**Replica assignment algorithm:**
- First replica placed randomly
- Additional replicas placed in different racks
- Round-robin within racks for load balancing

**Verification:**
```bash
kafka-topics.sh --describe --topic orders

# Check that replicas span different racks
```

**Limitations:** Requires at least as many racks as replication factor for full rack diversity.

### 4. How do you handle message deduplication?

**Answer:** Handle deduplication through idempotent producers, exactly-once semantics, and application-level deduplication strategies.

**Producer-level deduplication:**

**1. Idempotent producer:**
```java
props.put("enable.idempotence", true);
```
- Prevents duplicates from producer retries
- Works within single producer session and partition
- No application changes needed

**2. Transactional producer:**
```java
props.put("transactional.id", "unique-tx-id");
producer.initTransactions();

producer.beginTransaction();
producer.send(record1);
producer.send(record2);
producer.commitTransaction();
```
- Exactly-once across multiple partitions
- Eliminates duplicates from failures

**Consumer-level deduplication:**

**3. Track processed message IDs:**
```java
Set<String> processedIds = new HashSet<>();

for (ConsumerRecord record : records) {
    String messageId = record.key();

    if (processedIds.contains(messageId)) {
        continue;  // Skip duplicate
    }

    process(record);
    processedIds.add(messageId);
}
```

**4. Database unique constraints:**
```java
// Use message ID as primary key
INSERT INTO orders (id, ...) VALUES (?, ...)
ON CONFLICT (id) DO NOTHING;
```

**5. Bloom filter (for large-scale):**
```java
BloomFilter<String> filter = BloomFilter.create(
    Funnels.stringFunnel(Charset.defaultCharset()),
    1000000,  // Expected insertions
    0.01      // False positive probability
);

if (filter.mightContain(messageId)) {
    // Possibly duplicate, check database
} else {
    // Definitely not duplicate
    process(record);
    filter.put(messageId);
}
```

**6. Windowed deduplication (stream processing):**
```java
// Kafka Streams
KStream<String, Event> stream = builder.stream("events");

stream.groupByKey()
      .windowedBy(TimeWindows.of(Duration.ofMinutes(5)))
      .reduce((v1, v2) -> v1)  // Keep first, discard duplicates
      .toStream();
```

**Best practices:**
- Include unique message ID in each message
- Use idempotent producers for free deduplication
- Combine multiple strategies for robustness
- Consider tradeoffs: memory vs accuracy

### 5. What is the difference between synchronous and asynchronous producers?

**Answer:** Synchronous and asynchronous producers differ in how they handle send acknowledgments and blocking behavior.

**Synchronous producer:**
```java
try {
    RecordMetadata metadata = producer.send(record).get();  // Blocks until ack
    System.out.println("Sent to partition: " + metadata.partition());
} catch (ExecutionException e) {
    // Handle send failure
}
```

**Characteristics:**
- **Blocks** until broker acknowledgment
- **Lower throughput**: One request at a time per partition
- **Immediate error handling**: Exception thrown on failure
- **Simpler logic**: Sequential execution
- **Use case**: Critical messages where confirmation needed before proceeding

**Asynchronous producer:**
```java
producer.send(record, (metadata, exception) -> {
    if (exception != null) {
        // Handle error asynchronously
        handleError(exception);
    } else {
        // Success
        System.out.println("Sent to partition: " + metadata.partition());
    }
});

// Continue immediately without waiting
```

**Characteristics:**
- **Non-blocking**: Returns immediately
- **Higher throughput**: Multiple in-flight requests
- **Callback-based**: Error handling in callback
- **Batching friendly**: Accumulates messages for efficient batching
- **Use case**: High-throughput scenarios

**Comparison:**

| Aspect | Synchronous | Asynchronous |
|--------|-------------|--------------|
| **Blocking** | Yes | No |
| **Throughput** | Lower | Higher |
| **Error handling** | Immediate exception | Callback |
| **Complexity** | Simple | More complex |
| **Latency impact** | Higher | Lower |
| **Batching** | Limited | Efficient |

**Hybrid approach:**
```java
// Fire async sends
for (Record record : records) {
    futures.add(producer.send(record));
}

// Wait for all before proceeding
for (Future<RecordMetadata> future : futures) {
    future.get();  // Batch synchronization
}
```

**Best practice:** Use async with callbacks for performance, sync only when order dependencies exist.

### 6. How do you implement custom partitioner?

**Answer:** Implement custom partitioner by extending the `Partitioner` interface to control partition assignment logic.

**Custom partitioner implementation:**
```java
public class CustomPartitioner implements Partitioner {

    @Override
    public void configure(Map<String, ?> configs) {
        // Initialize with configuration
        // Can read custom config properties here
    }

    @Override
    public int partition(String topic, Object key, byte[] keyBytes,
                        Object value, byte[] valueBytes, Cluster cluster) {

        List<PartitionInfo> partitions = cluster.partitionsForTopic(topic);
        int numPartitions = partitions.size();

        // Custom partitioning logic examples:

        // 1. VIP customer routing (priority partition)
        if (key != null && key.toString().startsWith("VIP-")) {
            return 0;  // VIP partition
        }

        // 2. Geographic routing
        String region = extractRegion(key);
        if ("US".equals(region)) {
            return 0;
        } else if ("EU".equals(region)) {
            return 1;
        } else if ("ASIA".equals(region)) {
            return 2;
        }

        // 3. Load balancing based on key length
        if (key != null) {
            return Math.abs(key.hashCode()) % numPartitions;
        }

        // 4. Time-based partitioning
        int hour = LocalDateTime.now().getHour();
        return hour % numPartitions;

        // 5. Round-robin for null keys
        return ThreadLocalRandom.current().nextInt(numPartitions);
    }

    @Override
    public void close() {
        // Cleanup resources
    }

    private String extractRegion(Object key) {
        // Extract region from key
        return "US";
    }
}
```

**Using custom partitioner:**
```java
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("partitioner.class", "com.example.CustomPartitioner");

// Optional: custom partitioner config
props.put("custom.partitioner.vip.partition", "0");

KafkaProducer<String, String> producer = new KafkaProducer<>(props);
```

**Use cases:**
- **Priority routing**: Route urgent messages to dedicated partitions
- **Geographic distribution**: Partition by region/country
- **Hot key mitigation**: Distribute hot keys across partitions
- **Tenant isolation**: One partition per tenant
- **Time-based**: Partition by time periods

**Best practices:**
- Ensure even distribution to avoid hot partitions
- Make deterministic (same key always to same partition)
- Consider partition count changes
- Test with production data distribution
- Monitor partition sizes for imbalance

### 7. How do you implement custom serializers and deserializers?

**Answer:** Implement custom serializers/deserializers by implementing `Serializer` and `Deserializer` interfaces for custom object serialization.

**Custom serializer:**
```java
public class OrderSerializer implements Serializer<Order> {

    @Override
    public void configure(Map<String, ?> configs, boolean isKey) {
        // Read configuration if needed
    }

    @Override
    public byte[] serialize(String topic, Order order) {
        if (order == null) {
            return null;
        }

        try {
            // Option 1: JSON serialization
            ObjectMapper mapper = new ObjectMapper();
            return mapper.writeValueAsBytes(order);

            // Option 2: Custom binary format
            ByteBuffer buffer = ByteBuffer.allocate(1024);
            buffer.putLong(order.getId());
            buffer.putInt(order.getCustomerId());
            buffer.putDouble(order.getAmount());
            byte[] productBytes = order.getProduct().getBytes(StandardCharsets.UTF_8);
            buffer.putInt(productBytes.length);
            buffer.put(productBytes);
            return buffer.array();

            // Option 3: Protobuf
            return order.toByteArray();

        } catch (Exception e) {
            throw new SerializationException("Error serializing Order", e);
        }
    }

    @Override
    public void close() {
        // Cleanup resources
    }
}
```

**Custom deserializer:**
```java
public class OrderDeserializer implements Deserializer<Order> {

    @Override
    public void configure(Map<String, ?> configs, boolean isKey) {
        // Configuration
    }

    @Override
    public Order deserialize(String topic, byte[] data) {
        if (data == null) {
            return null;
        }

        try {
            // Option 1: JSON deserialization
            ObjectMapper mapper = new ObjectMapper();
            return mapper.readValue(data, Order.class);

            // Option 2: Custom binary format
            ByteBuffer buffer = ByteBuffer.wrap(data);
            Order order = new Order();
            order.setId(buffer.getLong());
            order.setCustomerId(buffer.getInt());
            order.setAmount(buffer.getDouble());
            int productLength = buffer.getInt();
            byte[] productBytes = new byte[productLength];
            buffer.get(productBytes);
            order.setProduct(new String(productBytes, StandardCharsets.UTF_8));
            return order;

        } catch (Exception e) {
            throw new SerializationException("Error deserializing Order", e);
        }
    }

    @Override
    public void close() {
        // Cleanup
    }
}
```

**Using custom serializers:**
```java
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "com.example.OrderSerializer");
props.put("value.deserializer", "com.example.OrderDeserializer");

KafkaProducer<String, Order> producer = new KafkaProducer<>(props);
KafkaConsumer<String, Order> consumer = new KafkaConsumer<>(props);
```

**Best practices:**
- Handle null values gracefully
- Include version field for schema evolution
- Use efficient serialization format (Avro, Protobuf)
- Add error handling for malformed data
- Consider backward/forward compatibility
- Test with edge cases

### 8. What are interceptors in Kafka?

**Answer:** Interceptors allow you to intercept and modify records before they are sent (producer) or after they are received (consumer).

**Producer interceptor:**
```java
public class CustomProducerInterceptor implements ProducerInterceptor<String, String> {

    @Override
    public void configure(Map<String, ?> configs) {
        // Initialize
    }

    @Override
    public ProducerRecord<String, String> onSend(ProducerRecord<String, String> record) {
        // Modify record before sending

        // Add headers
        Headers headers = record.headers();
        headers.add("timestamp", String.valueOf(System.currentTimeMillis()).getBytes());
        headers.add("hostname", InetAddress.getLocalHost().getHostName().getBytes());

        // Modify value
        String modifiedValue = record.value() + "|enriched";

        return new ProducerRecord<>(
            record.topic(),
            record.partition(),
            record.key(),
            modifiedValue,
            headers
        );
    }

    @Override
    public void onAcknowledgement(RecordMetadata metadata, Exception exception) {
        // Called when broker acknowledges or on error

        if (exception != null) {
            // Log send failure
            logger.error("Send failed", exception);
            metrics.incrementCounter("send.failures");
        } else {
            // Log success
            logger.info("Sent to partition: " + metadata.partition());
            metrics.incrementCounter("send.success");
        }
    }

    @Override
    public void close() {
        // Cleanup
    }
}
```

**Consumer interceptor:**
```java
public class CustomConsumerInterceptor implements ConsumerInterceptor<String, String> {

    @Override
    public void configure(Map<String, ?> configs) {
        // Initialize
    }

    @Override
    public ConsumerRecords<String, String> onConsume(ConsumerRecords<String, String> records) {
        // Modify records after polling, before application processes

        Map<TopicPartition, List<ConsumerRecord<String, String>>> modifiedRecords = new HashMap<>();

        for (TopicPartition partition : records.partitions()) {
            List<ConsumerRecord<String, String>> partitionRecords = new ArrayList<>();

            for (ConsumerRecord<String, String> record : records.records(partition)) {
                // Filter records
                if (shouldProcess(record)) {
                    // Add timestamp tracking
                    long receiveTime = System.currentTimeMillis();
                    long latency = receiveTime - record.timestamp();
                    metrics.recordLatency(latency);

                    partitionRecords.add(record);
                }
            }

            modifiedRecords.put(partition, partitionRecords);
        }

        return new ConsumerRecords<>(modifiedRecords);
    }

    @Override
    public void onCommit(Map<TopicPartition, OffsetAndMetadata> offsets) {
        // Called after offset commit
        logger.info("Committed offsets: " + offsets);
    }

    @Override
    public void close() {
        // Cleanup
    }
}
```

**Using interceptors:**
```java
// Producer
props.put("interceptor.classes", "com.example.CustomProducerInterceptor");

// Consumer
props.put("interceptor.classes", "com.example.CustomConsumerInterceptor");

// Multiple interceptors (comma-separated)
props.put("interceptor.classes",
    "com.example.MetricsInterceptor,com.example.AuditInterceptor");
```

**Use cases:**
- **Monitoring**: Track message latency, throughput
- **Auditing**: Log all produces/consumes
- **Enrichment**: Add metadata headers
- **Filtering**: Drop unwanted messages
- **Validation**: Verify message format
- **Tracing**: Distributed tracing integration

### 9. How do you handle poison messages in Kafka?

**Answer:** Handle poison messages (malformed data that crashes consumers) through error handling, dead letter queues, and retry logic.

**Strategies:**

**1. Try-catch with logging:**
```java
while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));

    for (ConsumerRecord<String, String> record : records) {
        try {
            process(record);
        } catch (Exception e) {
            // Log poison message
            logger.error("Failed to process record: " + record, e);

            // Send to monitoring
            alerting.sendAlert("Poison message detected", record);

            // Continue to next message (skip poison message)
            continue;
        }
    }

    consumer.commitSync();
}
```

**2. Dead Letter Queue (DLQ):**
```java
KafkaProducer<String, String> dlqProducer = new KafkaProducer<>(dlqProps);

for (ConsumerRecord<String, String> record : records) {
    try {
        process(record);
    } catch (Exception e) {
        // Send to DLQ
        ProducerRecord<String, String> dlqRecord = new ProducerRecord<>(
            "dlq-topic",
            record.key(),
            record.value()
        );

        // Add error metadata
        dlqRecord.headers().add("error", e.getMessage().getBytes());
        dlqRecord.headers().add("original-topic", record.topic().getBytes());
        dlqRecord.headers().add("original-partition",
            String.valueOf(record.partition()).getBytes());
        dlqRecord.headers().add("original-offset",
            String.valueOf(record.offset()).getBytes());

        dlqProducer.send(dlqRecord);

        logger.warn("Sent poison message to DLQ", e);
    }
}
```

**3. Retry with exponential backoff:**
```java
int maxRetries = 3;
int retryCount = 0;

while (retryCount < maxRetries) {
    try {
        process(record);
        break;  // Success
    } catch (TemporaryException e) {
        retryCount++;
        long backoff = (long) Math.pow(2, retryCount) * 1000;  // Exponential backoff
        Thread.sleep(backoff);
    } catch (PermanentException e) {
        sendToDLQ(record, e);
        break;
    }
}
```

**4. Deserialization error handling:**
```java
props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, JsonDeserializer.class);
props.put(ErrorHandlingDeserializer.VALUE_DESERIALIZER_CLASS,JsonDeserializer.class);
props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, ErrorHandlingDeserializer.class);

// Access deserialization exceptions
DeserializationException exception = (DeserializationException)
    record.headers().lastHeader(SerializationExceptionHeader.KEY).value();
```

**5. Circuit breaker pattern:**
```java
CircuitBreaker breaker = new CircuitBreaker(5, Duration.ofMinutes(1));

for (ConsumerRecord record : records) {
    if (breaker.isOpen()) {
        logger.warn("Circuit breaker open, skipping processing");
        sendToDLQ(record);
        continue;
    }

    try {
        process(record);
        breaker.recordSuccess();
    } catch (Exception e) {
        breaker.recordFailure();
        sendToDLQ(record, e);
    }
}
```

**Best practices:**
- Always use try-catch around message processing
- Implement DLQ for investigation
- Add metadata to DLQ messages
- Monitor DLQ size and alert
- Have process to replay/fix DLQ messages
- Use schema validation to prevent poison messages

### 10. What is the dead letter queue pattern?

**Answer:** Dead Letter Queue (DLQ) is a pattern for handling messages that cannot be processed successfully after multiple attempts.

**DLQ implementation:**

**1. Producer setup:**
```java
// Main processor
KafkaConsumer<String, Order> consumer = new KafkaConsumer<>(consumerProps);
consumer.subscribe(Collections.singletonList("orders"));

// DLQ producer
KafkaProducer<String, Order> dlqProducer = new KafkaProducer<>(producerProps);
```

**2. Processing with DLQ:**
```java
while (true) {
    ConsumerRecords<String, Order> records = consumer.poll(Duration.ofMillis(100));

    for (ConsumerRecord<String, Order> record : records) {
        int retries = getRetryCount(record);  // From headers

        if (retries >= MAX_RETRIES) {
            // Max retries exceeded, send to DLQ
            sendToDLQ(dlqProducer, record, "Max retries exceeded");
            continue;
        }

        try {
            processOrder(record.value());
        } catch (RetryableException e) {
            // Increment retry count and retry
            retryLater(record, retries + 1);
        } catch (NonRetryableException e) {
            // Immediate DLQ (no retry)
            sendToDLQ(dlqProducer, record, e.getMessage());
        }
    }

    consumer.commitSync();
}
```

**3. Send to DLQ method:**
```java
private void sendToDLQ(KafkaProducer producer,
                       ConsumerRecord<String, Order> record,
                       String reason) {

    ProducerRecord<String, Order> dlqRecord = new ProducerRecord<>(
        "orders-dlq",  // DLQ topic
        record.key(),
        record.value()
    );

    // Add metadata
    Headers headers = dlqRecord.headers();
    headers.add("dlq.original.topic", record.topic().getBytes());
    headers.add("dlq.original.partition",
        String.valueOf(record.partition()).getBytes());
    headers.add("dlq.original.offset",
        String.valueOf(record.offset()).getBytes());
    headers.add("dlq.error.reason", reason.getBytes());
    headers.add("dlq.timestamp",
        String.valueOf(System.currentTimeMillis()).getBytes());
    headers.add("dlq.error.stacktrace", getStackTrace().getBytes());

    // Copy original headers
    record.headers().forEach(headers::add);

    producer.send(dlqRecord, (metadata, exception) -> {
        if (exception != null) {
            logger.error("Failed to send to DLQ", exception);
            // Escalate - critical issue
        }
    });
}
```

**4. DLQ consumer for investigation:**
```java
// Separate consumer to process DLQ
KafkaConsumer<String, Order> dlqConsumer = new KafkaConsumer<>(props);
dlqConsumer.subscribe(Collections.singletonList("orders-dlq"));

while (true) {
    ConsumerRecords<String, Order> dlqRecords = dlqConsumer.poll(Duration.ofMillis(100));

    for (ConsumerRecord<String, Order> record : dlqRecords) {
        // Log for manual investigation
        String reason = new String(record.headers().lastHeader("dlq.error.reason").value());
        logger.error("DLQ message - Reason: " + reason + ", Record: " + record);

        // Store in database for analysis
        saveToDLQDatabase(record);

        // Alert on-call engineer
        sendAlert("DLQ message received", record);
    }
}
```

**5. Replay from DLQ:**
```java
// After fixing issue, replay DLQ messages
for (ConsumerRecord<String, Order> dlqRecord : dlqRecords) {
    String originalTopic = new String(
        dlqRecord.headers().lastHeader("dlq.original.topic").value());

    ProducerRecord<String, Order> replayRecord = new ProducerRecord<>(
        originalTopic,
        dlqRecord.key(),
        dlqRecord.value()
    );

    producer.send(replayRecord);
}
```

**DLQ topic naming:**
- `<original-topic>-dlq`
- `<original-topic>.dlq`
- `dlq.<original-topic>`

**Best practices:**
- One DLQ per source topic
- Include comprehensive metadata
- Monitor DLQ size and alert
- Implement DLQ replay mechanism
- Set retention on DLQ (longer than source)
- Document DLQ investigation procedures
- Automated alerting on DLQ arrivals

## Troubleshooting and Best Practices

### 1. How do you troubleshoot slow consumers?

**Answer:** Troubleshoot slow consumers by analyzing metrics, identifying bottlenecks, and applying targeted optimizations.

**Diagnostic steps:**

**1. Check consumer metrics:**
```bash
# Consumer lag
kafka-consumer-groups.sh --describe --group my-group

# Check metrics
jconsole  # Connect to consumer JVM
# Look at: records-consumed-rate, fetch-latency-avg, poll-idle-ratio
```

**2. Profile processing time:**
```java
while (true) {
    long pollStart = System.currentTimeMillis();
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
    long pollTime = System.currentTimeMillis() - pollStart;

    long processStart = System.currentTimeMillis();
    for (ConsumerRecord record : records) {
        process(record);
    }
    long processTime = System.currentTimeMillis() - processStart;

    logger.info("Poll: {}ms, Process: {}ms", pollTime, processTime);
}
```

**Common issues and solutions:**

**Slow processing logic:**
- Use profiler to identify bottlenecks
- Optimize database queries
- Implement caching
- Parallelize processing within consumer

**Under-provisioned consumers:**
```java
// Solution: Add more consumers (up to partition count)
// Scale horizontally
```

**Small fetch batches:**
```java
props.put("max.poll.records", 500);      // Increase batch size
props.put("fetch.min.bytes", 52428800);  // 50MB minimum
props.put("fetch.max.wait.ms", 500);     // Wait for batch
```

**External service latency:**
```java
// Batch external calls
List<String> ids = records.stream()
    .map(r -> r.value().getId())
    .collect(Collectors.toList());

Map<String, Data> enrichmentData = externalService.batchGet(ids);
```

**Frequent rebalancing:**
```java
props.put("max.poll.interval.ms", 600000);  // 10 minutes
props.put("session.timeout.ms", 45000);
```

**Best practices:**
- Monitor processing time per batch
- Set alerts on increasing lag
- Optimize hot paths in code
- Use async I/O where possible
- Keep processing lightweight

### 2. How do you handle out-of-memory errors in Kafka?

**Answer:** Handle OOM errors by tuning JVM settings, optimizing memory usage, and preventing memory leaks.

**JVM heap tuning:**

**Broker JVM settings:**
```bash
export KAFKA_HEAP_OPTS="-Xms6g -Xmx6g"  # 6GB heap
export KAFKA_JVM_PERFORMANCE_OPTS="-XX:+UseG1GC -XX:MaxGCPauseMillis=20 \
  -XX:InitiatingHeapOccupancyPercent=35 -XX:G1HeapRegionSize=16M \
  -XX:MinMetaspaceSize=96m -XX:MaxMetaspaceSize=256m"
```

**Producer JVM settings:**
```bash
export KAFKA_HEAP_OPTS="-Xms1g -Xmx1g"
```

**Consumer JVM settings:**
```bash
export KAFKA_HEAP_OPTS="-Xms2g -Xmx2g"
```

**Common causes:**

**1. Producer buffer exhaustion:**
```java
// Reduce buffer memory
props.put("buffer.memory", 33554432);  // 32MB instead of 64MB

// Limit in-flight requests
props.put("max.in.flight.requests.per.connection", 1);

// Add backpressure handling
try {
    producer.send(record);
} catch (BufferExhaustedException e) {
    Thread.sleep(100);  // Backoff
    producer.send(record);
}
```

**2. Consumer large message batches:**
```java
// Reduce poll size
props.put("max.poll.records", 100);  // Smaller batches
props.put("max.partition.fetch.bytes", 1048576);  // 1MB per partition
props.put("fetch.max.bytes", 52428800);  // 50MB total
```

**3. Broker page cache pressure:**
```bash
# Increase system memory for page cache
# Reduce heap, increase available RAM for OS cache
# Kafka benefits from large page cache
```

**4. Memory leaks:**
```java
// Close resources properly
producer.close();
consumer.close();

// Don't accumulate records
records.clear();  // After processing
```

**Monitoring:**
```bash
# Enable GC logging
-XX:+PrintGCDetails -XX:+PrintGCDateStamps -Xloggc:/var/log/kafka/gc.log

# Monitor heap usage
jstat -gc <pid> 1000

# Heap dump on OOM
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/kafka-heap-dump.hprof
```

**Analysis:**
```bash
# Analyze heap dump
jhat /tmp/kafka-heap-dump.hprof
# Or use Eclipse MAT
```

**Prevention:**
- Right-size JVM heap based on workload
- Monitor GC metrics (pause time, frequency)
- Use G1GC for predictable pauses
- Leave 50% of RAM for OS page cache

### 3. What causes consumer rebalancing storms and how do you prevent them?

**Answer:** Rebalancing storms occur when consumers repeatedly rebalance, causing instability. Prevent through proper configuration and processing optimization.

**Common causes:**

**1. Slow message processing:**
```java
// Processing exceeds max.poll.interval.ms
props.put("max.poll.interval.ms", 300000);  // 5 minutes default

// If processing takes longer, consumer deemed dead → rebalance
```

**Solution:**
```java
// Increase timeout
props.put("max.poll.interval.ms", 600000);  // 10 minutes

// Reduce batch size
props.put("max.poll.records", 100);  // Process faster

// Optimize processing
// Move heavy computation outside poll loop
```

**2. Network issues:**
```java
// Heartbeat timeout
props.put("session.timeout.ms", 10000);  // 10s default
props.put("heartbeat.interval.ms", 3000);  // 3s default

// Network glitch → missed heartbeat → rebalance
```

**Solution:**
```java
// Increase timeouts
props.put("session.timeout.ms", 45000);  // 45s
props.put("heartbeat.interval.ms", 3000);  // Keep low for faster detection
```

**3. GC pauses:**
```bash
# Long GC pauses → consumer unresponsive → rebalance
```

**Solution:**
```bash
# Tune GC
-XX:+UseG1GC -XX:MaxGCPauseMillis=20

# Reduce heap size
-Xms2g -Xmx2g  # Smaller heap = shorter GC

# Monitor GC logs
```

**4. Too many consumers:**
```java
// Adding/removing consumers frequently
// Each change triggers rebalance
```

**Solution:**
- Deploy consumers in batches with delays
- Use static membership (Kafka 2.3+)

**5. Static membership (prevention):**
```java
// Assign permanent group.instance.id
props.put("group.instance.id", "consumer-1");  // Unique per instance

// Benefits:
// - Consumer restart doesn't trigger rebalance
// - Only triggers rebalance if timeout exceeded
// - Faster recovery
```

**6. Incremental cooperative rebalancing:**
```java
// Use CooperativeStickyAssignor (Kafka 2.4+)
props.put("partition.assignment.strategy",
    "org.apache.kafka.clients.consumer.CooperativeStickyAssignor");

// Benefits:
// - Only affected partitions rebalanced
// - Consumers keep processing unaffected partitions
// - Reduced rebalance disruption
```

**Detection:**
```bash
# Monitor rebalance frequency
grep "Rebalance" consumer.log | wc -l

# Check metrics
consumer_coordinator_rebalance_latency_avg
consumer_coordinator_rebalance_total
```

**Prevention checklist:**
- Set `max.poll.interval.ms` > max processing time
- Increase `session.timeout.ms` for unstable networks
- Tune GC for low pause times
- Use static membership for stable consumers
- Use cooperative rebalancing
- Deploy consumers gradually
- Monitor and alert on rebalance frequency

### 4. How do you handle data loss scenarios?

**Answer:** Prevent data loss through proper configuration, replication, and recovery procedures.

**Prevention:**

**1. Producer durability settings:**
```java
// Maximum durability
props.put("acks", "all");  // Wait for all ISR
props.put("enable.idempotence", true);  // Prevent duplicates
props.put("retries", Integer.MAX_VALUE);  // Retry indefinitely
props.put("max.in.flight.requests.per.connection", 5);
```

**2. Topic configuration:**
```bash
# Replication factor
--replication-factor 3

# Minimum in-sync replicas
kafka-configs.sh --alter --topic orders \
  --add-config min.insync.replicas=2
```

**3. Disable unclean leader election:**
```properties
# Prevent out-of-sync replicas from becoming leader
unclean.leader.election.enable=false
```

**4. Consumer offset management:**
```java
// Commit after processing
for (ConsumerRecord record : records) {
    process(record);  // Process first
}
consumer.commitSync();  // Then commit

// Don't commit before processing (causes loss on crash)
```

**Recovery scenarios:**

**1. Broker failure:**
- If RF ≥ 2: Automatic failover to replica, no data loss
- If RF = 1: Data on failed broker lost until recovery

**Recovery:**
```bash
# Restore broker
# Data replicates from other brokers

# If data corrupted
# Delete corrupt data
rm -rf /var/lib/kafka/logs/*
# Restart broker - replicates from ISR
```

**2. Topic deletion:**
```bash
# Prevent accidental deletion
delete.topic.enable=false  # Disable topic deletion

# If deleted and auto.create.topics.enable=false
# Recreate topic manually
# Data permanently lost

# Mitigation: MirrorMaker for backup cluster
```

**3. Consumer skipping messages:**
```java
// Scenario: Commit offsets too early
consumer.poll(Duration.ofMillis(100));
consumer.commitSync();  // Committed before processing!
process(records);  // Crash here = data loss

// Fix: Commit after processing (shown above)
```

**4. Retention expiry:**
```bash
# Messages deleted after retention period
log.retention.hours=168  # 7 days

# If processing delayed > retention
# Messages lost

# Solution: Increase retention or process faster
log.retention.hours=720  # 30 days
```

**Backup and disaster recovery:**

**1. MirrorMaker for replication:**
```bash
# Replicate to backup cluster
connect-mirror-maker.sh mm2.properties
```

**2. Broker backups:**
```bash
# Backup log directories
rsync -av /var/lib/kafka/logs/ /backup/kafka/

# Or snapshot disks (cloud)
aws ec2 create-snapshot
```

**3. Point-in-time recovery:**
- Use MirrorMaker with offset translation
- Restore from backup cluster
- Replay from specific offset

**Best practices:**
- Use `acks=all` with `min.insync.replicas=2`
- RF ≥ 3 for production
- Disable unclean leader election
- Monitor under-replicated partitions
- Test failover procedures
- Regular backup of critical topics

### 5. What are the best practices for choosing partition count?

**Answer:** Choose partition count based on throughput requirements, consumer parallelism, and cluster capacity.

**Factors to consider:**

**1. Target throughput:**
```
Partitions needed = Target throughput / Per-partition throughput
```
- Single partition: ~10-30 MB/s (varies by setup)
- Example: 300 MB/s target ÷ 15 MB/s per partition = 20 partitions

**2. Consumer parallelism:**
```
Max consumers = Partition count
```
- Want 10 parallel consumers? Need at least 10 partitions
- More partitions = more parallelism potential

**3. Broker count:**
```
Partitions should be multiple of broker count
```
- 3 brokers, 12 partitions = 4 partitions per broker (balanced)
- 3 brokers, 10 partitions = 4,3,3 (slightly unbalanced)

**4. Overhead considerations:**
- More partitions = more file handles
- More partitions = longer leader election time
- More partitions = higher end-to-end latency

**Recommendations:**

**Small topics (<1 GB/day):**
```bash
--partitions 3-6
```
- Sufficient for most workloads
- Good parallelism
- Low overhead

**Medium topics (1-10 GB/day):**
```bash
--partitions 10-30
```
- Balance throughput and overhead
- Room for growth

**Large topics (>10 GB/day):**
```bash
--partitions 30-100+
```
- High throughput requirements
- Many consumers needed
- Monitor cluster performance

**General guidelines:**
```
# Conservative starting point
Partitions = 3 × broker_count

# Throughput-driven
Partitions = (target_throughput_MB/s ÷ 15 MB/s)

# Consumer-driven
Partitions = max_parallel_consumers

# Use maximum of above
```

**Example calculation:**
```
Requirements:
- 100 MB/s throughput
- 20 parallel consumers desired
- 6 brokers

Calculations:
- Throughput: 100 ÷ 15 = 7 partitions
- Consumers: 20 partitions
- Brokers: 3 × 6 = 18 partitions

Choose: 20-24 partitions (multiple of broker count)
```

**Important notes:**
- Can increase partitions later (but changes key distribution)
- Cannot decrease without recreating topic
- Start conservative, increase based on monitoring
- Very large partition counts (>1000) require careful tuning

### 6. What are the best practices for choosing replication factor?

**Answer:** Choose replication factor based on durability requirements, cluster size, and resource constraints.

**Replication factor options:**

**RF=1 (No replication):**
- **Data loss risk**: High (broker failure = data loss)
- **Performance**: Highest (no replication overhead)
- **Use cases**: Development, non-critical logs, metrics
- **Not recommended for production**

**RF=2 (One replica):**
- **Data loss risk**: Medium (tolerates 1 broker failure)
- **Performance**: Good
- **Use cases**: Less critical production data
- **Minimum for production**: But not recommended

**RF=3 (Two replicas) - RECOMMENDED:**
- **Data loss risk**: Low (tolerates 2 broker failures)
- **Performance**: Acceptable (~20-30% overhead vs RF=1)
- **Use cases**: Standard production workload
- **Industry standard**: Most common configuration

**RF=5 (Four replicas):**
- **Data loss risk**: Very low (tolerates 4 broker failures)
- **Performance**: Lower (significant overhead)
- **Use cases**: Mission-critical data (financial, compliance)
- **Resource intensive**: 5× storage

**Configuration:**
```bash
# Topic creation
kafka-topics.sh --create --topic orders \
  --partitions 10 --replication-factor 3

# With min.insync.replicas
kafka-configs.sh --alter --topic orders \
  --add-config min.insync.replicas=2
```

**Best practice combinations:**

| Use Case | RF | min ISR | acks | Data Loss Risk |
|----------|----|---------| -----|----------------|
| Development | 1 | 1 | 1 | High |
| Logs/Metrics | 2 | 1 | 1 | Medium |
| **Standard Production** | **3** | **2** | **all** | **Low** |
| Financial/Critical | 5 | 3 | all | Very Low |

**Considerations:**

**Storage cost:**
```
Total storage = Data size × Replication factor
RF=3 → 3× storage cost
```

**Network bandwidth:**
```
Replication bandwidth = Write throughput × (RF - 1)
RF=3 → 2× replication traffic
```

**Minimum cluster size:**
```
Minimum brokers = Replication factor
```
- RF=3 requires at least 3 brokers
- Ideally more brokers than RF for load distribution

**Failure tolerance:**
```
Failures tolerated = RF - min ISR
```
- RF=3, min ISR=2 → Tolerates 1 broker failure
- RF=3, min ISR=1 → Tolerates 2 failures (but risky)

**Recommendations:**
- **Default**: RF=3, min ISR=2, acks=all
- **Critical data**: RF=5, min ISR=3, acks=all
- **Test/Dev**: RF=1 (acceptable)
- **Never**: RF=2 with min ISR=2 (no failure tolerance)
- **Consider**: Rack awareness for cross-AZ distribution

### 7. How do you handle backpressure in Kafka?

**Answer:** Handle backpressure by controlling flow rate, buffering, and applying rate limiting when consumers can't keep up with producers.

**Producer-side backpressure:**

**1. Buffer blocking:**
```java
props.put("buffer.memory", 33554432);  // 32MB
props.put("max.block.ms", 60000);  // Block up to 60s

// When buffer full, send() blocks for max.block.ms
// Then throws BufferExhaustedException
```

**2. Explicit rate limiting:**
```java
RateLimiter limiter = RateLimiter.create(1000);  // 1000 messages/sec

for (Record record : records) {
    limiter.acquire();  // Blocks if rate exceeded
    producer.send(record);
}
```

**3. Monitoring and throttling:**
```java
Metrics metrics = producer.metrics();
Metric bufferAvailable = metrics.get(new MetricName(
    "buffer-available-bytes", "producer-metrics", "", tags));

if ((Long) bufferAvailable.metricValue() < threshold) {
    Thread.sleep(100);  // Slow down
}
```

**Consumer-side backpressure:**

**1. Reduce fetch rate:**
```java
props.put("max.poll.records", 50);  // Smaller batches
props.put("fetch.min.bytes", 1024);
props.put("fetch.max.wait.ms", 500);
```

**2. Pause/resume partitions:**
```java
// Pause when processing slow
if (processingQueueSize() > threshold) {
    consumer.pause(consumer.assignment());

    // Process backlog
    processBacklog();

    // Resume when caught up
    consumer.resume(consumer.assignment());
}
```

**3. Manual offset management:**
```java
// Only commit when processing complete
for (ConsumerRecord record : records) {
    queue.add(record);  // Add to processing queue
}

// Process asynchronously
processQueue(queue);

// Commit only when processed
if (queue.isEmpty()) {
    consumer.commitSync();
}
```

**Broker-side quotas:**

**1. Producer quotas:**
```bash
# Limit producer throughput
kafka-configs.sh --alter --add-config 'producer_byte_rate=1048576' \
  --entity-type users --entity-name producer-user
```

**2. Consumer quotas:**
```bash
# Limit consumer throughput
kafka-configs.sh --alter --add-config 'consumer_byte_rate=2097152' \
  --entity-type users --entity-name consumer-user
```

**3. Request quotas:**
```bash
# Limit request rate
kafka-configs.sh --alter --add-config 'request_percentage=50' \
  --entity-type users --entity-name client-user
```

**Application-level strategies:**

**1. Circuit breaker:**
```java
if (consecutiveFailures > 5) {
    circuitOpen = true;
    // Stop consuming temporarily
    consumer.pause(consumer.assignment());

    // Wait for downstream recovery
    Thread.sleep(60000);

    consumer.resume(consumer.assignment());
}
```

**2. Dynamic batching:**
```java
// Adjust batch size based on lag
long lag = getLag();
if (lag > 10000) {
    props.put("max.poll.records", 1000);  // Larger batches
} else {
    props.put("max.poll.records", 100);  // Normal
}
```

**3. Drop non-critical messages:**
```java
for (ConsumerRecord record : records) {
    if (isCritical(record)) {
        process(record);  // Always process critical
    } else if (queue.size() < threshold) {
        process(record);  // Process if capacity available
    } else {
        drop(record);  // Drop under load
    }
}
```

**Best practices:**
- Monitor consumer lag continuously
- Set up alerts for growing lag
- Use quotas to prevent resource exhaustion
- Implement graceful degradation
- Scale consumers horizontally
- Optimize processing logic
- Use pause/resume for temporary slowdowns

### 8. What is the recommended approach for versioning messages?

**Answer:** Version messages using schema evolution with Schema Registry, message headers, or explicit version fields.

**Approach 1: Schema Registry (recommended):**
```java
// Avro with Schema Registry
props.put("value.serializer", "io.confluent.kafka.serializers.KafkaAvroSerializer");
props.put("schema.registry.url", "http://localhost:8081");

// Schema automatically versioned
// Producer sends schema ID with message
// Consumer retrieves schema by ID
// Backward/forward compatibility enforced
```

**Version 1 schema:**
```json
{
  "type": "record",
  "name": "Order",
  "namespace": "com.example",
  "fields": [
    {"name": "id", "type": "string"},
    {"name": "amount", "type": "double"}
  ]
}
```

**Version 2 schema (backward compatible):**
```json
{
  "type": "record",
  "name": "Order",
  "namespace": "com.example",
  "fields": [
    {"name": "id", "type": "string"},
    {"name": "amount", "type": "double"},
    {"name": "currency", "type": "string", "default": "USD"}
  ]
}
```

**Approach 2: Message headers:**
```java
// Producer adds version header
ProducerRecord<String, Order> record = new ProducerRecord<>("orders", order);
record.headers().add("schema-version", "2".getBytes());
producer.send(record);

// Consumer checks version
for (ConsumerRecord<String, Order> record : records) {
    Header versionHeader = record.headers().lastHeader("schema-version");
    String version = new String(versionHeader.value());

    if ("1".equals(version)) {
        processV1(record.value());
    } else if ("2".equals(version)) {
        processV2(record.value());
    }
}
```

**Approach 3: Embedded version field:**
```java
public class VersionedMessage {
    private int version;
    private String payload;

    // Constructor, getters, setters
}

// Producer
VersionedMessage msg = new VersionedMessage();
msg.setVersion(2);
msg.setPayload(serialize(order));

// Consumer
VersionedMessage msg = deserialize(record.value());
switch (msg.getVersion()) {
    case 1:
        processV1(msg.getPayload());
        break;
    case 2:
        processV2(msg.getPayload());
        break;
}
```

**Approach 4: Topic versioning:**
```bash
# Separate topics for incompatible versions
orders-v1
orders-v2

# Gradual migration
# 1. Create orders-v2
# 2. Dual-write to both topics
# 3. Migrate consumers from v1 to v2
# 4. Stop writing to v1
# 5. Deprecate v1 topic
```

**Best practices:**
- **Prefer**: Schema Registry for automatic version management
- **Include**: Version in every message (header or field)
- **Document**: Version compatibility matrix
- **Test**: Old consumers with new messages, new consumers with old messages
- **Avoid**: Breaking changes; use topic versioning if necessary
- **Monitor**: Version distribution in production

### 9. How do you test Kafka applications?

**Answer:** Test Kafka applications using unit tests, integration tests with embedded Kafka, and end-to-end tests.

**Unit testing (mocked):**
```java
@Test
public void testMessageProcessing() {
    // Mock Kafka components
    KafkaProducer<String, String> mockProducer = mock(KafkaProducer.class);
    KafkaConsumer<String, String> mockConsumer = mock(KafkaConsumer.class);

    // Mock behavior
    when(mockConsumer.poll(any()))
        .thenReturn(createMockRecords());

    // Test logic
    MyProcessor processor = new MyProcessor(mockConsumer, mockProducer);
    processor.processOnce();

    // Verify
    verify(mockProducer).send(any());
}
```

**Integration testing (embedded Kafka):**
```java
@SpringBootTest
@EmbeddedKafka(partitions = 1, topics = {"test-topic"})
public class KafkaIntegrationTest {

    @Autowired
    private KafkaTemplate<String, String> template;

    @Autowired
    private KafkaListenerEndpointRegistry registry;

    @Test
    public void testProducerConsumer() throws Exception {
        // Send message
        template.send("test-topic", "key", "value");

        // Wait for consumption
        Thread.sleep(1000);

        // Assert message processed
        assertThat(consumed).contains("value");
    }
}
```

**Using TestContainers:**
```java
@Testcontainers
public class KafkaContainerTest {

    @Container
    static KafkaContainer kafka = new KafkaContainer(
        DockerImageName.parse("confluentinc/cp-kafka:7.4.0"));

    @Test
    public void testWithRealKafka() {
        Properties props = new Properties();
        props.put("bootstrap.servers", kafka.getBootstrapServers());

        KafkaProducer<String, String> producer = new KafkaProducer<>(props);
        producer.send(new ProducerRecord<>("test", "key", "value"));

        // Test consumer
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
        consumer.subscribe(Collections.singletonList("test"));

        ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(10));
        assertThat(records).hasSize(1);
    }
}
```

**Kafka Streams testing:**
```java
@Test
public void testTopology() {
    StreamsBuilder builder = new StreamsBuilder();

    // Build topology
    KStream<String, String> input = builder.stream("input");
    input.mapValues(v -> v.toUpperCase()).to("output");

    // Test with TopologyTestDriver
    TopologyTestDriver testDriver = new TopologyTestDriver(
        builder.build(), config);

    // Send input
    TestInputTopic<String, String> inputTopic = testDriver.createInputTopic(
        "input", stringSerializer, stringSerializer);
    inputTopic.pipeInput("key", "value");

    // Verify output
    TestOutputTopic<String, String> outputTopic = testDriver.createOutputTopic(
        "output", stringDeserializer, stringDeserializer);
    assertThat(outputTopic.readValue()).isEqualTo("VALUE");

    testDriver.close();
}
```

**Property-based testing:**
```java
@Property
public void testIdempotence(@ForAll List<String> messages) {
    // Send messages twice
    messages.forEach(msg -> producer.send(new ProducerRecord<>("test", msg)));
    messages.forEach(msg -> producer.send(new ProducerRecord<>("test", msg)));

    // Verify idempotent processing (no duplicates)
    Set<String> processed = consumeAll();
    assertThat(processed).containsExactlyInAnyOrderElementsOf(messages);
}
```

**Performance testing:**
```java
@Test
public void testThroughput() {
    int messageCount = 100000;
    long start = System.currentTimeMillis();

    for (int i = 0; i < messageCount; i++) {
        producer.send(new ProducerRecord<>("perf-test", "key", "value"));
    }
    producer.flush();

    long duration = System.currentTimeMillis() - start;
    double throughput = messageCount / (duration / 1000.0);

    assertThat(throughput).isGreaterThan(10000);  // 10k msg/s minimum
}
```

**Test types:**
- **Unit**: Business logic, mocked Kafka
- **Integration**: Embedded Kafka, full flow
- **Contract**: Schema compatibility
- **Performance**: Throughput, latency
- **Chaos**: Failure scenarios (broker down, network partition)

### 10. What are common anti-patterns to avoid in Kafka?

**Answer:** Avoid common Kafka anti-patterns that lead to poor performance, reliability issues, or operational complexity.

**Anti-pattern 1: Using Kafka as a database**
```java
// BAD: Storing all user profiles in Kafka, querying by ID
kafkaConsumer.poll(); // Search through all messages for one user

// GOOD: Use Kafka for events, database for state
// Kafka: User registration events
// Database: Current user profiles
```

**Anti-pattern 2: Sending messages synchronously**
```java
// BAD: Blocking on every send
for (Record record : records) {
    producer.send(record).get();  // Blocks, kills throughput
}

// GOOD: Async with callbacks
for (Record record : records) {
    producer.send(record, callback);  // Non-blocking
}
```

**Anti-pattern 3: Creating too many topics**
```java
// BAD: One topic per user/entity
// topics: user-1, user-2, user-3... (thousands of topics)

// GOOD: Partition by entity ID within shared topics
// topic: users (with user_id as key)
```

**Anti-pattern 4: Not using consumer groups**
```java
// BAD: Multiple independent consumers on same topic
// No load balancing, all consume all messages

// GOOD: Use consumer groups for parallelism
props.put("group.id", "my-group");
```

**Anti-pattern 5: Committing offsets before processing**
```java
// BAD: At-most-once (data loss)
ConsumerRecords records = consumer.poll();
consumer.commitSync();  // Commit first
process(records);  // Crash here = data loss

// GOOD: At-least-once
ConsumerRecords records = consumer.poll();
process(records);  // Process first
consumer.commitSync();  // Then commit
```

**Anti-pattern 6: Using default partition count**
```bash
// BAD: Creating topics with 1 partition
kafka-topics.sh --create --topic orders --partitions 1

// GOOD: Plan partitions for parallelism
kafka-topics.sh --create --topic orders --partitions 30
```

**Anti-pattern 7: Not handling rebalancing**
```java
// BAD: Long-running processing without heartbeat
while (true) {
    records = consumer.poll(Duration.ofMillis(100));
    longRunningProcess(records);  // 10+ minutes, no poll()
    // Rebalance triggered, partitions revoked
}

// GOOD: Short polling intervals, manual pause/resume
while (true) {
    records = consumer.poll(Duration.ofMillis(100));
    consumer.pause(partitions);
    process(records);
    consumer.resume(partitions);
}
```

**Anti-pattern 8: Ignoring monitoring**
```java
// BAD: No metrics, no alerting
// Problems discovered when users complain

// GOOD: Comprehensive monitoring
// Metrics: lag, throughput, errors
// Alerts: lag increasing, under-replicated partitions
```

**Anti-pattern 9: Single consumer reading multiple topics inefficiently**
```java
// BAD: Separate consumers for each topic in same application
consumer1.subscribe(Arrays.asList("topic1"));
consumer2.subscribe(Arrays.asList("topic2"));
consumer3.subscribe(Arrays.asList("topic3"));

// GOOD: One consumer for multiple related topics
consumer.subscribe(Arrays.asList("topic1", "topic2", "topic3"));
```

**Anti-pattern 10: Not planning for schema evolution**
```java
// BAD: No version handling
// Breaking schema changes break all consumers

// GOOD: Use Schema Registry with compatibility checks
// Backward/forward compatible changes only
props.put("value.serializer", "KafkaAvroSerializer");
props.put("schema.registry.url", "http://localhost:8081");
```

**Anti-pattern 11: Logging every message**
```java
// BAD: Log flooding
for (ConsumerRecord record : records) {
    logger.info("Processing: " + record);  // Millions of logs
}

// GOOD: Sample logging
if (Math.random() < 0.01) {  // 1% sampling
    logger.info("Sample: " + record);
}
```

**Anti-pattern 12: Not setting retention policies**
```bash
// BAD: Default infinite retention
# Disk fills up, cluster fails

// GOOD: Appropriate retention
kafka-configs.sh --alter --topic orders \
  --add-config retention.ms=604800000  # 7 days
```

## Scenario-Based Questions

### 1. How would you design a Kafka architecture for a high-volume e-commerce platform?

**Answer:** Design a multi-topic architecture with event-driven microservices, proper partitioning, and high availability.

**Architecture design:**

**Topics structure:**
```
orders (30 partitions, RF=3)
  - Order placed, updated, canceled events
  - Partitioned by order_id

payments (20 partitions, RF=3)
  - Payment initiated, completed, failed events
  - Partitioned by payment_id

inventory (50 partitions, RF=3)
  - Stock updates, reservations, releases
  - Partitioned by product_id

user-events (100 partitions, RF=3)
  - Clicks, views, searches
  - Partitioned by user_id

notifications (10 partitions, RF=3)
  - Email, SMS, push notifications
  - Partitioned by user_id
```

**Infrastructure:**
- **Kafka cluster**: 9-12 brokers across 3 AZs (rack-aware)
- **ZooKeeper/KRaft**: 5 nodes for high availability
- **Schema Registry**: 3 instances with Avro schemas
- **Kafka Connect**: Distributed mode for CDC from databases

**Configuration:**
```properties
# High durability
replication.factor=3
min.insync.replicas=2
unclean.leader.election.enable=false

# Performance tuning
num.network.threads=16
num.io.threads=16
compression.type=lz4

# Retention
log.retention.hours=168  # 7 days for most topics
log.retention.hours=2160 # 90 days for orders (compliance)
```

**Services:**
```
Order Service → orders topic
Payment Service → payments topic
Inventory Service → consumes orders, produces inventory updates
Notification Service → consumes orders, payments → produces notifications
Analytics Service → consumes all topics → data warehouse
```

**Key features:**
- **Exactly-once semantics** for order processing
- **Saga pattern** for distributed transactions
- **Event sourcing** for order state reconstruction
- **CQRS** with materialized views in databases
- **Dead letter queues** for failed message handling
- **Monitoring**: Prometheus + Grafana for metrics

**Scaling strategy:**
- Horizontal: Add brokers and partitions as volume grows
- Consumer groups scaled independently per service
- Burst handling with producer buffering and quotas

### 2. How would you migrate from an existing messaging system to Kafka?

**Answer:** Execute a phased migration with dual-write pattern, validation, and gradual cutover to minimize risk.

**Migration phases:**

**Phase 1: Assessment (1-2 weeks)**
```
Current state analysis:
- Message volumes and patterns
- Topic/queue inventory
- Producer/consumer applications
- Dependencies and integrations
- Performance requirements
```

**Phase 2: Kafka setup (2-3 weeks)**
```bash
# Deploy Kafka cluster
- 3+ brokers with replication
- Schema Registry for data governance
- Monitoring stack (Prometheus, Grafana)

# Create equivalent topics
kafka-topics.sh --create --topic orders \
  --partitions 30 --replication-factor 3

# Map existing queues to Kafka topics
RabbitMQ queue: orders → Kafka topic: orders
```

**Phase 3: Bridge deployment (1 week)**
```java
// Option 1: Write to both systems
public void sendMessage(Order order) {
    // Write to old system
    rabbitMQProducer.send(order);

    // Write to Kafka
    kafkaProducer.send(new ProducerRecord<>("orders", order));
}

// Option 2: Bridge consumer
while (true) {
    Message msg = rabbitMQ.receive();
    kafkaProducer.send(convert(msg));
    msg.acknowledge();
}
```

**Phase 4: Consumer migration (3-4 weeks)**
```
Week 1-2: Shadow consumers
- Deploy Kafka consumers in read-only mode
- Compare results with existing consumers
- Validate no data loss or errors

Week 3-4: Gradual cutover
- Migrate 10% of consumers to Kafka
- Monitor lag, errors, performance
- Increase to 50%, 75%, 100%
- Keep old consumers as backup
```

**Phase 5: Producer migration (2-3 weeks)**
```
Week 1: Dual write validation
- Continue dual-write pattern
- Monitor both systems

Week 2: Cutover producers
- Switch producers to Kafka-only
- Remove old system writes
- Monitor for issues

Week 3: Cleanup
- Decomission old system consumers
- Remove bridge components
```

**Phase 6: Optimization (Ongoing)**
```
- Tune partitions based on load
- Optimize consumer lag
- Implement advanced features (transactions, streams)
```

**Rollback strategy:**
```
At any point:
- Switch traffic back to old system
- Bridge remains operational
- No data loss with dual-write

Complete rollback window: 2-4 weeks
```

**Key success factors:**
- Comprehensive testing at each phase
- Monitoring and alerting from day 1
- Feature parity before migration
- Clear rollback procedures
- Stakeholder communication

### 3. How would you handle a scenario where one consumer is slower than others?

**Answer:** Identify bottleneck, isolate slow consumer, and apply targeted optimizations or scaling strategies.

**Diagnosis:**
```bash
# Check consumer lag
kafka-consumer-groups.sh --describe --group my-group

# Output shows per-consumer lag
CONSUMER-ID        HOST        PARTITION  LAG
consumer-1         host-1      0          100
consumer-1         host-1      1          150
consumer-2         host-2      2          5000     # SLOW!
consumer-2         host-2      3          4800     # SLOW!
```

**Solutions:**

**1. Isolate slow partitions:**
```java
// Move slow partitions to dedicated consumer group
// Fast consumer group (partitions 0,1)
props.put("group.id", "fast-group");

// Slow consumer group (partitions 2,3 - needs optimization)
props.put("group.id", "slow-group");
props.put("max.poll.records", 10);  // Process smaller batches
```

**2. Optimize slow consumer:**
```java
// Profile processing time
for (ConsumerRecord record : records) {
    long start = System.currentTimeMillis();
    process(record);
    long duration = System.currentTimeMillis() - start;

    if (duration > 100) {
        logger.warn("Slow processing: {}ms for {}", duration, record.key());
    }
}

// Optimize bottleneck:
// - Database query optimization
// - Caching frequently accessed data
// - Async external API calls
// - Parallel processing within consumer
```

**3. Scale slow consumer:**
```java
// Add thread pool for parallel processing
ExecutorService executor = Executors.newFixedThreadPool(10);

for (ConsumerRecord record : records) {
    executor.submit(() -> process(record));
}

executor.shutdown();
executor.awaitTermination(5, TimeUnit.MINUTES);
consumer.commitSync();
```

**4. Rebalance partitions:**
```bash
# Reassign slow partitions to multiple consumers
# Before: consumer-2 has partitions [2,3]
# After:  consumer-2 has [2], consumer-3 has [3]

# Trigger rebalance by adding consumer-3
# Each consumer now processes fewer partitions
```

**5. Separate processing pipeline:**
```java
// If optimization not possible, use different approach
// E.g., slow partitions go to batch processing

// Real-time consumer (partitions 0,1)
fastConsumer.subscribe(Arrays.asList(0, 1));

// Batch consumer (partitions 2,3 - allow lag)
slowConsumer.subscribe(Arrays.asList(2, 3));
// Process in larger batches, less frequently
```

**6. Hot partition mitigation:**
```java
// If slow due to hot partition (uneven key distribution)
// Implement custom partitioner for better distribution

public class BalancedPartitioner implements Partitioner {
    public int partition(String topic, Object key, ...) {
        // Add salt to hot keys
        if (isHotKey(key)) {
            int salt = ThreadLocalRandom.current().nextInt(4);
            return (hash(key + salt)) % numPartitions;
        }
        return hash(key) % numPartitions;
    }
}
```

**Prevention:**
- Monitor consumer lag per partition
- Alert on lag imbalance
- Load test consumers before production
- Use consistent processing logic across consumers
- Avoid hot partitions with good key distribution

### 4. How would you implement event sourcing using Kafka?

**Answer:** Use Kafka as the event store with compacted topics for state snapshots and regular topics for event history.

**Event sourcing architecture:**

**1. Event topics (append-only log):**
```java
// Events are immutable facts
public class OrderEvent {
    String eventId;        // Unique event ID
    String orderId;        // Aggregate ID
    String eventType;      // CREATED, UPDATED, CANCELED
    long timestamp;
    OrderData data;
    int version;           // Aggregate version
}

// Publish events
kafkaProducer.send(new ProducerRecord<>(
    "order-events",        // Event log topic
    order.getOrderId(),    // Key = aggregate ID
    orderCreatedEvent
));
```

**2. Event store topics:**
```bash
# Event history (all events)
order-events (partitioned by order_id, retention=infinite)

# Snapshots (latest state, log compacted)
order-snapshots (partitioned by order_id, cleanup.policy=compact)
```

**3. Write events:**
```java
@Service
public class OrderService {

    public void createOrder(CreateOrderCommand cmd) {
        // Generate event
        OrderCreatedEvent event = new OrderCreatedEvent(
            UUID.randomUUID(),
            cmd.getOrderId(),
            cmd.getData(),
            1  // version
        );

        // Persist to event store
        producer.send(new ProducerRecord<>(
            "order-events",
            cmd.getOrderId(),
            event
        ));

        // Optionally update snapshot
        updateSnapshot(cmd.getOrderId(), event);
    }

    public void updateOrder(UpdateOrderCommand cmd) {
        // Load current state
        Order order = loadOrder(cmd.getOrderId());

        // Generate event with incremented version
        OrderUpdatedEvent event = new OrderUpdatedEvent(
            UUID.randomUUID(),
            cmd.getOrderId(),
            cmd.getData(),
            order.getVersion() + 1
        );

        // Persist event
        producer.send(new ProducerRecord<>(
            "order-events",
            cmd.getOrderId(),
            event
        ));
    }
}
```

**4. Read/rebuild state:**
```java
public class OrderEventSourcingRepository {

    public Order loadOrder(String orderId) {
        // Option 1: Load from snapshot (fast)
        Order order = loadSnapshot(orderId);
        if (order != null) {
            return order;
        }

        // Option 2: Rebuild from events (full history)
        order = new Order(orderId);
        List<OrderEvent> events = loadEvents(orderId);

        for (OrderEvent event : events) {
            order.apply(event);  // Apply each event
        }

        return order;
    }

    private List<OrderEvent> loadEvents(String orderId) {
        // Query Kafka for all events with this order_id
        KafkaConsumer<String, OrderEvent> consumer = createConsumer();
        consumer.assign(partitionsFor(orderId));
        consumer.seekToBeginning(consumer.assignment());

        List<OrderEvent> events = new ArrayList<>();
        while (true) {
            ConsumerRecords<String, OrderEvent> records = consumer.poll(Duration.ofSeconds(1));
            if (records.isEmpty()) break;

            for (ConsumerRecord<String, OrderEvent> record : records) {
                if (orderId.equals(record.key())) {
                    events.add(record.value());
                }
            }
        }

        return events.stream()
            .sorted(Comparator.comparing(OrderEvent::getVersion))
            .collect(Collectors.toList());
    }
}
```

**5. Snapshot optimization:**
```java
// Periodically create snapshots for faster loading
public void createSnapshot(String orderId) {
    Order order = loadOrder(orderId);  // Rebuild from events

    producer.send(new ProducerRecord<>(
        "order-snapshots",  // Compacted topic
        orderId,
        order
    ));
}

// Rebuild from snapshot + subsequent events
public Order loadOrderOptimized(String orderId) {
    Order order = loadSnapshot(orderId);
    if (order == null) {
        order = new Order(orderId);
    }

    // Apply events after snapshot
    List<OrderEvent> events = loadEventsSince(orderId, order.getVersion());
    for (OrderEvent event : events) {
        order.apply(event);
    }

    return order;
}
```

**6. Projections/views:**
```java
// Materialized views in databases for queries
@KafkaListener(topics = "order-events")
public void updateProjection(OrderEvent event) {
    switch (event.getEventType()) {
        case "CREATED":
            orderRepository.create(event.getData());
            break;
        case "UPDATED":
            orderRepository.update(event.getOrderId(), event.getData());
            break;
        case "CANCELED":
            orderRepository.markCanceled(event.getOrderId());
            break;
    }
}
```

**Benefits:**
- Complete audit trail
- Time travel (replay to any point)
- Debugging and analysis
- Multiple views from same events
- Event-driven architecture

**Configuration:**
```properties
# Infinite retention for events
log.retention.ms=-1

# Compaction for snapshots
cleanup.policy=compact
```

### 5. How would you design a CDC (Change Data Capture) pipeline using Kafka?

**Answer:** Use Debezium connectors to capture database changes, stream to Kafka, and propagate to downstream systems.

**CDC architecture:**

**Components:**
```
Source Database (PostgreSQL/MySQL)
  ↓ (Debezium Connector reads transaction log)
Kafka Connect (Distributed Mode)
  ↓ (Publishes to Kafka topics)
Kafka Cluster
  ↓ (Consumed by multiple systems)
Target Systems (Elasticsearch, Data Warehouse, Cache, etc.)
```

**Implementation:**

**1. Deploy Kafka Connect with Debezium:**
```bash
# Download Debezium connector
wget https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/2.4.0.Final/debezium-connector-postgres-2.4.0.Final-plugin.tar.gz

# Extract to Kafka Connect plugins directory
tar -xzf debezium-connector-postgres-*.tar.gz -C /usr/share/kafka-connect/plugins/
```

**2. Configure PostgreSQL CDC connector:**
```json
{
  "name": "postgres-cdc-connector",
  "config": {
    "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
    "database.hostname": "postgres-db",
    "database.port": "5432",
    "database.user": "debezium_user",
    "database.password": "password",
    "database.dbname": "ecommerce",
    "database.server.name": "ecommerce_db",

    "table.include.list": "public.orders,public.users,public.products",
    "column.exclude.list": "public.users.password",

    "plugin.name": "pgoutput",
    "slot.name": "debezium_slot",

    "topic.prefix": "cdc",
    "transforms": "route",
    "transforms.route.type": "org.apache.kafka.connect.transforms.RegexRouter",
    "transforms.route.regex": "([^.]+)\\.([^.]+)\\.([^.]+)",
    "transforms.route.replacement": "cdc.$3",

    "key.converter": "io.confluent.connect.avro.AvroConverter",
    "value.converter": "io.confluent.connect.avro.AvroConverter",
    "key.converter.schema.registry.url": "http://schema-registry:8081",
    "value.converter.schema.registry.url": "http://schema-registry:8081",

    "snapshot.mode": "initial",
    "tombstones.on.delete": "true"
  }
}
```

**3. Deploy connector:**
```bash
curl -X POST http://kafka-connect:8083/connectors \
  -H "Content-Type: application/json" \
  -d @postgres-cdc-connector.json
```

**4. Kafka topics created:**
```
cdc.orders       - Order changes
cdc.users        - User changes
cdc.products     - Product changes
```

**5. Change event format:**
```json
{
  "before": {
    "id": 123,
    "status": "PENDING",
    "amount": 99.99
  },
  "after": {
    "id": 123,
    "status": "COMPLETED",
    "amount": 99.99
  },
  "source": {
    "version": "2.4.0.Final",
    "connector": "postgresql",
    "name": "ecommerce_db",
    "ts_ms": 1645564800000,
    "db": "ecommerce",
    "schema": "public",
    "table": "orders"
  },
  "op": "u",  // c=create, u=update, d=delete, r=read (snapshot)
  "ts_ms": 1645564800123
}
```

**6. Consumer for Elasticsearch:**
```java
@KafkaListener(topics = "cdc.orders")
public void syncToElasticsearch(ConsumerRecord<String, ChangeEvent> record) {
    ChangeEvent change = record.value();

    switch (change.getOp()) {
        case "c":  // Create
        case "u":  // Update
            elasticsearchClient.index(
                "orders",
                change.getAfter().getId(),
                change.getAfter()
            );
            break;
        case "d":  // Delete
            elasticsearchClient.delete(
                "orders",
                change.getBefore().getId()
            );
            break;
    }
}
```

**7. Consumer for data warehouse:**
```java
@KafkaListener(topics = "cdc.*")
public void syncToWarehouse(ConsumerRecord<String, ChangeEvent> record) {
    ChangeEvent change = record.value();
    String table = record.topic().split("\\.")[1];

    // Upsert to data warehouse
    dataWarehouse.upsert(table, change.getAfter());
}
```

**8. Sink connector for S3 (data lake):**
```json
{
  "name": "s3-sink-cdc",
  "config": {
    "connector.class": "io.confluent.connect.s3.S3SinkConnector",
    "topics": "cdc.orders,cdc.users,cdc.products",
    "s3.bucket.name": "data-lake-cdc",
    "s3.region": "us-east-1",
    "format.class": "io.confluent.connect.s3.format.parquet.ParquetFormat",
    "partitioner.class": "io.confluent.connect.storage.partitioner.TimeBasedPartitioner",
    "path.format": "'year'=YYYY/'month'=MM/'day'=dd",
    "partition.duration.ms": "3600000",
    "flush.size": "1000"
  }
}
```

**Monitoring:**
```bash
# Connector status
curl http://kafka-connect:8083/connectors/postgres-cdc-connector/status

# Kafka lag
kafka-consumer-groups.sh --describe --group elasticsearch-sync

# Debezium metrics
curl http://kafka-connect:8083/connectors/postgres-cdc-connector/metrics
```

**Benefits:**
- Real-time data synchronization
- No application code changes
- Event history for replay
- Multiple consumers from single source
- Decoupled systems

### 6. How would you handle data reprocessing scenarios?

**Answer:** Implement offset reset strategies, replay topics, and use versioned consumers for safe reprocessing.

**Scenario 1: Reprocess recent data (bug fix)**
```bash
# Reset offsets to specific timestamp (2 hours ago)
kafka-consumer-groups.sh --bootstrap-server localhost:9092 \
  --group my-group --topic orders \
  --reset-offsets --to-datetime 2026-02-16T10:00:00.000 \
  --execute

# Or reset by duration
kafka-consumer-groups.sh --reset-offsets --by-duration PT2H --execute
```

**Scenario 2: Reprocess all data (new feature)**
```bash
# Reset to beginning
kafka-consumer-groups.sh --reset-offsets --to-earliest --execute

# Or create new consumer group
# Old group continues normal processing
# New group processes from beginning
```

**Scenario 3: Reprocess with different logic:**
```java
// Deploy versioned consumer
public class OrderProcessorV2 {
    public static void main(String[] args) {
        Properties props = new Properties();
        props.put("group.id", "order-processor-v2");  // New group

        KafkaConsumer<String, Order> consumer = new KafkaConsumer<>(props);
        consumer.subscribe(Arrays.asList("orders"));

        // Seek to beginning for full reprocessing
        consumer.poll(Duration.ZERO);  // Initial poll to get assignment
        consumer.seekToBeginning(consumer.assignment());

        while (true) {
            ConsumerRecords<String, Order> records = consumer.poll(Duration.ofMillis(100));
            for (ConsumerRecord<String, Order> record : records) {
                processWithNewLogic(record.value());
            }
            consumer.commitSync();
        }
    }
}
```

**Scenario 4: Backfill with rate limiting:**
```java
// Avoid overwhelming downstream systems
RateLimiter limiter = RateLimiter.create(100);  // 100 msg/s

consumer.seekToBeginning(consumer.assignment());

while (hasMoreToProcess()) {
    ConsumerRecords<String, Order> records = consumer.poll(Duration.ofMillis(100));

    for (ConsumerRecord<String, Order> record : records) {
        limiter.acquire();  // Throttle
        process(record);

        if (record.offset() >= targetOffset) {
            break;  // Stop at target
        }
    }
}
```

**Scenario 5: Parallel reprocessing:**
```java
// Reprocess partitions in parallel with separate consumers
ExecutorService executor = Executors.newFixedThreadPool(10);

for (int partition = 0; partition < 10; partition++) {
    final int p = partition;
    executor.submit(() -> {
        KafkaConsumer<String, Order> consumer = createConsumer();
        TopicPartition tp = new TopicPartition("orders", p);
        consumer.assign(Collections.singleton(tp));
        consumer.seekToBeginning(Collections.singleton(tp));

        // Process partition independently
        while (true) {
            ConsumerRecords<String, Order> records = consumer.poll(Duration.ofMillis(100));
            records.forEach(this::process);

            if (reachedTarget()) break;
        }
    });
}
```

**Scenario 6: Dual processing (shadow mode):**
```java
// Run new logic alongside old for comparison
@KafkaListener(topics = "orders", groupId = "production")
public void processProduction(Order order) {
    processWithOldLogic(order);
}

@KafkaListener(topics = "orders", groupId = "shadow")
public void processShadow(Order order) {
    try {
        Result newResult = processWithNewLogic(order);
        Result oldResult = loadOldResult(order.getId());

        // Compare results
        if (!newResult.equals(oldResult)) {
            logger.warn("Mismatch for order {}: {} vs {}",
                order.getId(), oldResult, newResult);
        }
    } catch (Exception e) {
        logger.error("Shadow processing failed", e);
        // Don't affect production
    }
}
```

**Scenario 7: Replay to different topic:**
```java
// Reprocess and write to new topic
consumer.seekToBeginning(consumer.assignment());

KafkaProducer<String, ProcessedOrder> producer = new KafkaProducer<>(props);

while (true) {
    ConsumerRecords<String, Order> records = consumer.poll(Duration.ofMillis(100));

    for (ConsumerRecord<String, Order> record : records) {
        ProcessedOrder result = reprocess(record.value());

        producer.send(new ProducerRecord<>(
            "orders-reprocessed-v2",  // New topic
            record.key(),
            result
        ));
    }
}
```

**Best practices:**
- Test reprocessing logic on subset first
- Monitor reprocessing progress
- Use rate limiting to avoid overwhelming systems
- Keep production and reprocessing separate (different consumer groups)
- Document offset reset procedures
- Have rollback plan

### 7. How would you implement a retry mechanism for failed messages?

**Answer:** Implement retry mechanism using retry topics, exponential backoff, and eventual dead letter queue for unprocessable messages.

**Approach 1: Retry topics with delay:**
```java
public class RetryableKafkaConsumer {

    private static final int MAX_RETRIES = 3;
    private static final String[] RETRY_TOPICS = {
        "orders",
        "orders-retry-1",  // 1 minute delay
        "orders-retry-2",  // 5 minutes delay
        "orders-retry-3"   // 15 minutes delay
    };
    private static final String DLQ_TOPIC = "orders-dlq";

    @KafkaListener(topics = {"orders", "orders-retry-1", "orders-retry-2", "orders-retry-3"})
    public void process(ConsumerRecord<String, Order> record) {
        try {
            processOrder(record.value());
        } catch (RetryableException e) {
            int retryCount = getRetryCount(record);

            if (retryCount < MAX_RETRIES) {
                sendToRetryTopic(record, retryCount + 1);
            } else {
                sendToDLQ(record, "Max retries exceeded");
            }
        } catch (NonRetryableException e) {
            sendToDLQ(record, "Non-retryable error: " + e.getMessage());
        }
    }

    private void sendToRetryTopic(ConsumerRecord<String, Order> record, int retryCount) {
        String retryTopic = RETRY_TOPICS[retryCount];

        ProducerRecord<String, Order> retryRecord = new ProducerRecord<>(
            retryTopic,
            record.key(),
            record.value()
        );

        // Add retry metadata
        retryRecord.headers().add("retry-count", String.valueOf(retryCount).getBytes());
        retryRecord.headers().add("original-topic", record.topic().getBytes());
        retryRecord.headers().add("retry-timestamp",
            String.valueOf(System.currentTimeMillis()).getBytes());

        producer.send(retryRecord);
    }

    private int getRetryCount(ConsumerRecord<String, Order> record) {
        Header retryHeader = record.headers().lastHeader("retry-count");
        return retryHeader != null ? Integer.parseInt(new String(retryHeader.value())) : 0;
    }
}
```

**Approach 2: In-memory retry with exponential backoff:**
```java
public class BackoffRetryConsumer {

    private final ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(10);

    @KafkaListener(topics = "orders")
    public void process(ConsumerRecord<String, Order> record) {
        processWithRetry(record, 0);
    }

    private void processWithRetry(ConsumerRecord<String, Order> record, int attempt) {
        try {
            processOrder(record.value());
        } catch (RetryableException e) {
            if (attempt < 5) {
                long delay = (long) Math.pow(2, attempt) * 1000;  // Exponential backoff

                scheduler.schedule(
                    () -> processWithRetry(record, attempt + 1),
                    delay,
                    TimeUnit.MILLISECONDS
                );

                logger.info("Scheduled retry {} for order {} in {}ms",
                    attempt + 1, record.key(), delay);
            } else {
                sendToDLQ(record, "Max retries exceeded");
            }
        }
    }
}
```

**Approach 3: Kafka Streams retry:**
```java
StreamsBuilder builder = new StreamsBuilder();

KStream<String, Order> orders = builder.stream("orders");

// Split stream
Map<String, KStream<String, Order>> branches = orders.split()
    .branch((key, order) -> processSuccessfully(order),
        Branched.withConsumer(ks -> ks.to("orders-processed")))
    .branch((key, order) -> isRetryable(order),
        Branched.withConsumer(ks -> ks.to("orders-retry")))
    .noDefaultBranch();

// Retry stream with delay (using processor API)
builder.stream("orders-retry")
       .process(() -> new RetryProcessor())
       .to("orders");
```

**Approach 4: Spring Kafka retry template:**
```java
@Configuration
public class KafkaRetryConfig {

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, Order> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, Order> factory =
            new ConcurrentKafkaListenerContainerFactory<>();

        factory.setConsumerFactory(consumerFactory());
        factory.setCommonErrorHandler(errorHandler());

        return factory;
    }

    @Bean
    public DefaultErrorHandler errorHandler() {
        // Exponential backoff: 1s, 2s, 4s, 8s, 16s
        ExponentialBackOffWithMaxRetries backOff = new ExponentialBackOffWithMaxRetries(5);
        backOff.setInitialInterval(1000L);
        backOff.setMultiplier(2.0);
        backOff.setMaxInterval(16000L);

        DefaultErrorHandler errorHandler = new DefaultErrorHandler(
            (record, exception) -> {
                // Send to DLQ after max retries
                sendToDLQ(record, exception);
            },
            backOff
        );

        // Don't retry certain exceptions
        errorHandler.addNotRetryableExceptions(IllegalArgumentException.class);

        return errorHandler;
    }
}

@KafkaListener(topics = "orders")
public void process(Order order) {
    // Throws exception → automatic retry with backoff
    processOrder(order);
}
```

**Approach 5: Database-backed retry queue:**
```java
@KafkaListener(topics = "orders")
public void process(ConsumerRecord<String, Order> record) {
    try {
        processOrder(record.value());
    } catch (RetryableException e) {
        // Store in database for later retry
        RetryRecord retry = new RetryRecord();
        retry.setTopic(record.topic());
        retry.setKey(record.key());
        retry.setValue(record.value());
        retry.setRetryCount(0);
        retry.setNextRetryTime(LocalDateTime.now().plusMinutes(1));

        retryRepository.save(retry);
    }
}

// Scheduled task to retry from database
@Scheduled(fixedDelay = 60000)  // Every minute
public void processRetries() {
    List<RetryRecord> retries = retryRepository.findDueRetries();

    for (RetryRecord retry : retries) {
        try {
            processOrder(retry.getValue());
            retryRepository.delete(retry);
        } catch (Exception e) {
            retry.setRetryCount(retry.getRetryCount() + 1);
            retry.setNextRetryTime(calculateBackoff(retry.getRetryCount()));

            if (retry.getRetryCount() > MAX_RETRIES) {
                sendToDLQ(retry);
                retryRepository.delete(retry);
            } else {
                retryRepository.save(retry);
            }
        }
    }
}
```

**Best practices:**
- Distinguish retriable vs non-retriable errors
- Use exponential backoff
- Set maximum retry limit
- Implement DLQ for permanently failed messages
- Monitor retry rates and DLQ size
- Add retry metadata (count, timestamp, reason)
- Test retry logic thoroughly

### 8. How would you design a Kafka system for IoT data ingestion?

**Answer:** Design a scalable, high-throughput system with edge processing, efficient serialization, and time-series storage.

**Architecture:**

**1. Edge layer (IoT devices):**
```
IoT Devices → Edge Gateway → Kafka
- Devices send to local gateway
- Gateway batches and compresses
- Reduces network overhead
```

**2. Kafka topics structure:**
```bash
# Raw sensor data
iot-raw-data (1000 partitions, RF=3)
  - Partitioned by device_id
  - High volume, short retention (1 day)

# Aggregated metrics
iot-metrics-1min (100 partitions, RF=3)
iot-metrics-1hour (50 partitions, RF=3)
  - Aggregated data
  - Longer retention (30-90 days)

# Alerts
iot-alerts (20 partitions, RF=3)
  - Critical events
  - Immediate processing

# Device metadata
iot-devices (10 partitions, RF=3, compacted)
  - Device registration, config
  - Log compacted
```

**3. Data format (efficient serialization):**
```protobuf
// Protobuf for compact binary format
message SensorReading {
  string device_id = 1;
  int64 timestamp = 2;
  float temperature = 3;
  float humidity = 4;
  float pressure = 5;
  bytes custom_data = 6;
}
```

**4. Producer (edge gateway):**
```java
public class IoTGatewayProducer {
    private final KafkaProducer<String, SensorReading> producer;
    private final Map<String, List<SensorReading>> buffer = new ConcurrentHashMap<>();

    public void sendReading(SensorReading reading) {
        // Buffer locally
        buffer.computeIfAbsent(reading.getDeviceId(), k -> new ArrayList<>())
               .add(reading);
    }

    @Scheduled(fixedDelay = 5000)  // Flush every 5 seconds
    public void flushBuffer() {
        buffer.forEach((deviceId, readings) -> {
            // Send batch per device
            for (SensorReading reading : readings) {
                producer.send(new ProducerRecord<>(
                    "iot-raw-data",
                    deviceId,  // Partition by device
                    reading
                ));
            }
        });

        buffer.clear();
    }

    // Configuration
    private Properties getProducerConfig() {
        Properties props = new Properties();
        props.put("bootstrap.servers", "kafka:9092");
        props.put("compression.type", "lz4");  // Fast compression
        props.put("batch.size", 100000);  // Large batches
        props.put("linger.ms", 100);  // Small delay for batching
        props.put("acks", "1");  // Leader only (acceptable for IoT)

        props.put("key.serializer", StringSerializer.class);
        props.put("value.serializer", ProtobufSerializer.class);

        return props;
    }
}
```

**5. Stream processing (aggregation):**
```java
// Kafka Streams for real-time aggregation
StreamsBuilder builder = new StreamsBuilder();

KStream<String, SensorReading> rawData = builder.stream("iot-raw-data");

// 1-minute windowed aggregation
rawData.groupByKey()
       .windowedBy(TimeWindows.ofSizeWithNoGrace(Duration.ofMinutes(1)))
       .aggregate(
           () -> new AggregatedMetrics(),
           (key, reading, metrics) -> metrics.update(reading),
           Materialized.with(Serdes.String(), aggregatedMetricsSerde())
       )
       .toStream()
       .to("iot-metrics-1min");

// Anomaly detection
rawData.filter((deviceId, reading) -> isAnomaly(reading))
       .mapValues(reading -> new Alert(reading))
       .to("iot-alerts");

// Device stats
rawData.groupByKey()
       .count(Materialized.as("device-message-counts"));
```

**6. Consumer (time-series database):**
```java
@KafkaListener(topics = "iot-metrics-1min", concurrency = "10")
public void storeMetrics(ConsumerRecord<String, AggregatedMetrics> record) {
    // Store in time-series DB (InfluxDB, TimescaleDB)
    timeSeriesDB.write(
        "iot_metrics",
        Map.of(
            "device_id", record.key(),
            "timestamp", record.value().getTimestamp(),
            "avg_temperature", record.value().getAvgTemperature(),
            "max_temperature", record.value().getMaxTemperature(),
            "sample_count", record.value().getCount()
        )
    );
}
```

**7. Alerting consumer:**
```java
@KafkaListener(topics = "iot-alerts")
public void handleAlert(Alert alert) {
    // Send notifications
    if (alert.getSeverity() == Severity.CRITICAL) {
        notificationService.sendSMS(alert);
        notificationService.sendEmail(alert);
    }

    // Store alert
    alertRepository.save(alert);

    // Trigger automated response
    if (alert.getType() == AlertType.TEMPERATURE_HIGH) {
        deviceController.coolDown(alert.getDeviceId());
    }
}
```

**8. Configuration and tuning:**
```properties
# Broker config
num.partitions=1000  # High partition count for many devices
log.retention.hours=24  # Short retention for raw data
log.segment.bytes=1073741824  # 1GB segments
compression.type=lz4

# Topic configs
iot-raw-data:
  retention.ms=86400000  # 1 day
  cleanup.policy=delete

iot-metrics-1min:
  retention.ms=2592000000  # 30 days
  cleanup.policy=delete

iot-devices:
  cleanup.policy=compact  # Keep latest device state
```

**9. Monitoring:**
```
- Ingestion rate (messages/sec)
- End-to-end latency
- Consumer lag per consumer group
- Alert rate and types
- Device connectivity status
- Data quality metrics
```

**Scalability:**
- Handle millions of devices
- 100K+ messages/second
- Horizontal scaling: Add brokers and partitions
- Edge processing reduces network load
- Efficient serialization (Protobuf) reduces size
- Tiered storage for cost optimization

### 9. How would you implement stream-table joins for real-time enrichment?

**Answer:** Use Kafka Streams to join event streams with reference tables for real-time data enrichment.

**Scenario: Enrich order events with customer and product information**

**1. Topics:**
```bash
# Event stream
orders (partitions=30, key=order_id)

# Reference tables (compacted)
customers (partitions=10, key=customer_id, compacted)
products (partitions=10, key=product_id, compacted)
```

**2. Basic KStream-KTable join:**
```java
StreamsBuilder builder = new StreamsBuilder();

// Stream of orders
KStream<String, Order> orders = builder.stream("orders");

// Table of customers (latest state)
KTable<String, Customer> customers = builder.table("customers");

// Join: Need to re-key orders by customer_id
KStream<String, Order> ordersByCustomer = orders
    .selectKey((orderId, order) -> order.getCustomerId());

// Stream-table join
KStream<String, EnrichedOrder> enrichedOrders = ordersByCustomer
    .join(
        customers,
        (order, customer) -> new EnrichedOrder(order, customer)
    );

enrichedOrders.to("orders-enriched");
```

**3. Multi-level join (customer + product):**
```java
// Products table
KTable<String, Product> products = builder.table("products");

// First join with customer
KStream<String, OrderWithCustomer> ordersWithCustomer = orders
    .selectKey((orderId, order) -> order.getCustomerId())
    .join(customers, (order, customer) ->
        new OrderWithCustomer(order, customer)
    );

// Second join with product
KStream<String, FullyEnrichedOrder> fullyEnriched = ordersWithCustomer
    .selectKey((customerId, orderWithCustomer) ->
        orderWithCustomer.getOrder().getProductId())
    .join(products, (orderWithCustomer, product) ->
        new FullyEnrichedOrder(
            orderWithCustomer.getOrder(),
            orderWithCustomer.getCustomer(),
            product
        )
    );

fullyEnriched.to("orders-fully-enriched");
```

**4. Using GlobalKTable (no repartitioning):**
```java
// GlobalKTable: Replicated to all instances
GlobalKTable<String, Customer> customersGlobal = builder.globalTable("customers");
GlobalKTable<String, Product> productsGlobal = builder.globalTable("products");

// Join without repartitioning
KStream<String, EnrichedOrder> enriched = orders
    // Join with customer
    .join(
        customersGlobal,
        (orderId, order) -> order.getCustomerId(),  // Key extractor
        (order, customer) -> new OrderWithCustomer(order, customer)
    )
    // Join with product
    .join(
        productsGlobal,
        (orderId, orderWithCustomer) ->
            orderWithCustomer.getOrder().getProductId(),
        (orderWithCustomer, product) ->
            new FullyEnrichedOrder(
                orderWithCustomer.getOrder(),
                orderWithCustomer.getCustomer(),
                product
            )
    );
```

**5. Handling missing reference data:**
```java
// Left join: Proceed even if customer not found
KStream<String, EnrichedOrder> enriched = ordersByCustomer
    .leftJoin(
        customers,
        (order, customer) -> {
            if (customer == null) {
                logger.warn("Customer not found: {}", order.getCustomerId());
                return new EnrichedOrder(order, Customer.unknown());
            }
            return new EnrichedOrder(order, customer);
        }
    );
```

**6. Caching for performance:**
```java
// Configure caching for KTables
Properties props = new Properties();
props.put(StreamsConfig.CACHE_MAX_BYTES_BUFFERING_CONFIG, 10 * 1024 * 1024);  // 10MB

KTable<String, Customer> customers = builder.table(
    "customers",
    Materialized.<String, Customer, KeyValueStore<Bytes, byte[]>>as("customers-store")
        .withCachingEnabled()
);
```

**7. Complex enrichment with lookup service:**
```java
// External lookup (e.g., database, cache)
KStream<String, EnrichedOrder> enriched = orders.mapValues(order -> {
    // Lookup from external system
    Customer customer = customerService.getById(order.getCustomerId());
    Product product = productService.getById(order.getProductId());

    return new EnrichedOrder(order, customer, product);
});

// Note: Can be slow, prefer Kafka tables when possible
```

**8. Async enrichment with processor API:**
```java
class AsyncEnrichmentProcessor implements Processor<String, Order, String, EnrichedOrder> {

    private ProcessorContext<String, EnrichedOrder> context;
    private final ExecutorService executor = Executors.newFixedThreadPool(10);

    @Override
    public void process(Record<String, Order> record) {
        Order order = record.value();

        // Async enrichment
        executor.submit(() -> {
            try {
                Customer customer = customerService.getById(order.getCustomerId());
                Product product = productService.getById(order.getProductId());

                EnrichedOrder enriched = new EnrichedOrder(order, customer, product);

                // Forward enriched record
                context.forward(new Record<>(record.key(), enriched, record.timestamp()));
            } catch (Exception e) {
                logger.error("Enrichment failed", e);
            }
        });
    }
}

// Use in topology
orders.process(() -> new AsyncEnrichmentProcessor());
```

**9. Monitoring and debugging:**
```java
// Add metrics
enriched.peek((key, value) -> {
    metrics.recordEnrichmentTime(value.getProcessingTime());
    metrics.recordJoinSuccess();
})
.to("orders-enriched");

// Handle join failures
orders.leftJoin(customers, joiner)
      .filter((key, enrichedOrder) -> {
          if (enrichedOrder.getCustomer() == null) {
              metrics.recordMissingCustomer();
              return false;
          }
          return true;
      });
```

**Best practices:**
- Use GlobalKTable for small reference data (<GB)
- Use KTable for large reference data
- Handle missing reference data gracefully (left join)
- Monitor join rates and missing lookups
- Keep reference tables updated
- Consider caching for performance
- Test with production-like data volumes

### 10. How would you handle schema evolution in a production system with multiple consumers?

**Answer:** Implement schema evolution using Schema Registry with compatibility modes, phased rollouts, and consumer versioning.

**Strategy:**

**1. Schema Registry setup:**
```java
// Producer with Schema Registry
Properties props = new Properties();
props.put("value.serializer", "io.confluent.kafka.serializers.KafkaAvroSerializer");
props.put("schema.registry.url", "http://schema-registry:8081");
props.put("auto.register.schemas", "true");  // Auto-register new schemas
```

**2. Initial schema (v1):**
```json
{
  "type": "record",
  "name": "Order",
  "namespace": "com.example",
  "version": 1,
  "fields": [
    {"name": "id", "type": "string"},
    {"name": "amount", "type": "double"},
    {"name": "status", "type": "string"}
  ]
}
```

**3. Schema evolution (v2 - backward compatible):**
```json
{
  "type": "record",
  "name": "Order",
  "namespace": "com.example",
  "version": 2,
  "fields": [
    {"name": "id", "type": "string"},
    {"name": "amount", "type": "double"},
    {"name": "status", "type": "string"},
    {"name": "currency", "type": "string", "default": "USD"},  // New field with default
    {"name": "customer_id", "type": ["null", "string"], "default": null}  // Optional
  ]
}
```

**4. Set compatibility mode:**
```bash
# Global compatibility
curl -X PUT http://schema-registry:8081/config \
  -H "Content-Type: application/json" \
  -d '{"compatibility": "BACKWARD"}'

# Per-subject compatibility
curl -X PUT http://schema-registry:8081/config/orders-value \
  -d '{"compatibility": "FULL"}'  # Both backward and forward
```

**5. Test compatibility before deploying:**
```bash
# Test if new schema is compatible
curl -X POST http://schema-registry:8081/compatibility/subjects/orders-value/versions/latest \
  -H "Content-Type: application/json" \
  -d @new-schema.json

# Response:
# {"is_compatible": true}
```

**6. Phased rollout:**
```
Week 1: Deploy new producers with v2 schema
  - Write new fields (currency, customer_id)
  - Old consumers ignore new fields (backward compatible)
  - Monitor for errors

Week 2: Deploy updated consumers
  - Read new fields
  - Handle null/default values
  - Monitor processing

Week 3: All systems on v2
  - Verify all working correctly
  - No rollback needed

Week 4+: Cleanup
  - Remove v1 handling code if needed
  - Update documentation
```

**7. Consumer handling multiple versions:**
```java
@KafkaListener(topics = "orders")
public void process(ConsumerRecord<String, GenericRecord> record) {
    GenericRecord order = record.value();

    // Handle both v1 and v2
    String id = order.get("id").toString();
    Double amount = (Double) order.get("amount");
    String status = order.get("status").toString();

    // New fields (v2) - handle absence
    String currency = order.hasField("currency") ?
        order.get("currency").toString() : "USD";

    String customerId = order.hasField("customer_id") && order.get("customer_id") != null ?
        order.get("customer_id").toString() : null;

    // Process order
    processOrder(id, amount, status, currency, customerId);
}
```

**8. Version-specific consumers:**
```java
// V1 consumer (legacy)
@KafkaListener(topics = "orders", groupId = "orders-v1-processor")
public void processV1(OrderV1 order) {
    // Old logic
}

// V2 consumer (new)
@KafkaListener(topics = "orders", groupId = "orders-v2-processor")
public void processV2(OrderV2 order) {
    // New logic with additional fields
}

// Both consume same topic during transition
```

**9. Schema migration for breaking changes:**
```bash
# When breaking change needed, create new topic
kafka-topics.sh --create --topic orders-v2

# Dual-write period
# Write to both orders and orders-v2
producer.send(new ProducerRecord<>("orders", orderV1));
producer.send(new ProducerRecord<>("orders-v2", orderV2));

# Migrate consumers
# 1. Deploy consumers for orders-v2
# 2. Verify working correctly
# 3. Stop consumers for orders
# 4. Stop producers to orders
# 5. Deprecate orders topic
```

**10. Monitoring schema evolution:**
```java
// Track schema versions in use
@KafkaListener(topics = "orders")
public void process(ConsumerRecord<String, GenericRecord> record) {
    GenericRecord order = record.value();
    int schemaId = // Extract from headers or Schema Registry

    metrics.recordSchemaVersion(schemaId);

    // Alert if old schema versions still in use
    if (isOldVersion(schemaId)) {
        alerts.sendAlert("Old schema version detected: " + schemaId);
    }

    process(order);
}

// Schema Registry metrics
// - Active schema versions per subject
// - Compatibility check failures
// - Schema registration rate
```

**Best practices:**
- Always use Schema Registry in production
- Set appropriate compatibility mode (BACKWARD or FULL)
- Test compatibility before deploying
- Phased rollouts (producers first, then consumers)
- Handle missing fields gracefully
- Monitor schema versions in use
- Document schema changes
- Use semantic versioning for schemas
- Never make breaking changes without new topic
- Keep old schemas for troubleshooting

Sources
1. [20 Kafka Interview Questions for Data Engineers](https://www.datacamp.com/blog/kafka-interview-questions)
2. [Top Kafka Interview Questions and Answers (2025)](https://www.interviewbit.com/kafka-interview-questions/)
3. [Kafka Deep Dive for System Design Interviews](https://www.hellointerview.com/learn/system-design/deep-dives/kafka)
4. [Top 70 Kafka Interview Questions and Answers for 2025](https://www.geeksforgeeks.org/apache-kafka/kafka-interview-questions/)
5. [Preparing for a Kafka interview? A comprehensive list ...](https://www.linkedin.com/posts/ashishmisal_preparing-for-a-kafka-interview-a-comprehensive-activity-7259256309445775360-BGwY)
6. [100+ Kafka Interview Questions and Answers for 2025](https://www.projectpro.io/article/kafka-interview-questions-and-answers/438)
7. [15 Kafka Interview Questions for Hiring Kafka Engineers](https://www.terminal.io/blog/15-kafka-interview-questions-for-hiring-kafka-engineers)
8. [25 Most Common Kafka Interview Questions You Need to ...](https://www.finalroundai.com/blog/kafka-interview-questions)
9. [Top 50 Kafka Interview Questions And Answers for 2025](https://www.simplilearn.com/kafka-interview-questions-and-answers-article)
10. [Top 35 Apache Kafka Interview Questions](https://360digitmg.com/blog/data-engineer-apache-kafka-interview-questions)
11. [50 Apache Kafka Interview Questions and Answers for all ...](https://gist.github.com/bansalankit92/9414ef3614229cdca6053464fedf5038)
12. [Interview Questions & Answers](https://www.ctanujit.org/uploads/2/5/3/9/25393293/data_engineering_interviews.pdf)