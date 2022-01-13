<h2>Kafka notes</h2>

**Kafka** is an event streaming platform.

<a href="https://www.youtube.com/watch?v=-AZOi3kP9Js">Link to the video</a>

```bash
EVENTS -> PRODUCERS -> BROKERS -> CONSUMERS
```
- Schema above is a better choice compared to direct delivery from PRODUCERS to CONSUMERS

- we need to send events by producers to consumers
- in our case we use *Kafka* as Brokers

  * Benefits of using BROKERS:
    ** reliability & garantee of delivery
    ** new CONSUMERS can be added easily
    ** PRODUCERS don't know CONSUMERS
    ** tech-support
    ** integration of various tech-stacks
    
Kafka consists of:
  - Broker
  - Zookeeper
  - Message (Record)
  - Topic/Partition
  - Producer
  - Consumer
  
  * Broker (aka Kafka node, server etc): pickup, storage, delivery of messages
    ** we have many BROKERS for fault tolerance which make KAFKA CLUSTER
    ** from all of the BROKERS there is one CONTROLLER, which ensures CONSISTENCY
    ** *Kafka* is a master-slave system
   
  * Zookeeper: condition of the cluster, configuration, address book (data). **Screen1**
    ** i.e. to choose a CONTROLLER we use Zookeeper

  * Message consists of:
    ** Key
    ** Value: content of the message (bytes)
    ** Timestamp
    ** Headers
  
  * Topic/Partition: **Screen2**
    ** Topic is a stream of data (like *table*). It has a queue where we have our MESSAGES (which can be from various sources)
    ** *Kafka* uses FIFO for TOPIC
    ** MESSAGES are not deleted from TOPIC (after having being read by one CONSUMER) which makes it possible for MESSAGES to be shared accross various CONSUMERS
    ** <b>Partitions</b> are for boosting the reading/writing of data (parallelism) **Screen3**
      - FIFO per partition
      - I.e. we can send MESSAGES of one user to one partition and hence we can get them in order
      - Broker 1, 2, n -> Topic A, B n -> Partitions per Broker & per Topic.
        - Why sometimes one Broker can hold all partitions of some Topic? -> Kafka makes balance of all partitions on the Broker, not of the particular Topic. But keep in mind that some Topic can be more weighty
      - Where is data from TOPIC in the BROKER? -> In `Log files` **Screen4**
        - Let's look at screenshot of Broker-File-System *15 minute of the video*
    ** we can't delete TOPIC directly. But there is **TTL** aka time-to-live which deletes *Segments*. If Segment timestamp expired -> to delete (based on the latest timestamp)
      + *Segment* timestamp is defined by the biggest timestamp in it
  
  * Producer: MESSAGE sender which writes to `BROKER -> TOPIC -> LEADER partition`
    * `acks`: 0 - send once and don't wait for response; 1 - response of success only from **Leader**; -1 (aka `all`) - producer waits for response from ALL ISR + Leader
    * `Delivery semantic support`: at most once, at least once, exactly once (idempotence)<br>
    * Steps of `send message`:
      + fetch metadata: who is **Leader** in Partitions, what is the CLUSTER etc. Who will give response? -> `Zookeeper`. It's a blocking && expensive operation => create one PRODUCER which will load in cache all the metadata and will update it
      + serialize message
      + define partition: a) explicit definition b) round-robin c) key-defined (key_hash & n) *34 minute of the video*
      + compress message
      + accumulate batch

  * Consumer: MESSAGE reader which reads from `BROKER -> TOPIC -> LEADER partition`
    ** Steps of `poll messages`:
      + fetch metadata: resembles one for PRODUCER
      + start polling LEADER-replicas of all TOPIC partitions
        - make polling asynchronous. It's named **Consumer Group**: create `group.id`, specify this `id` for CONSUMER, they'll unite under one group. Now, each CONSUMER reads various partitions **Screen5**
    ** `__consumer_offsets`: if Consumer in Consumer Group reads batch (with i.e. 3 messages) and then falls, next Consumer should know that first 3 messages were read => Consumer **commits** to `__consumer_offsets` **Screen6**
      - Auto commit (at most once): bad as Consumer can receive batch, send commit, then fall => doesn't read
      - Manual commit (at least once): after batch was read, `commit` will happen. But here can be duplicates if 2/3 of batch was processed and then Consumer falls -> hence we start reading of this batch by another Consumer from the start. Think about idempotence
      - Custom offset management: **exactly once** if such is required

Data replication: **Screen7**
  - `replication-factor` if set > 1 => partition will reside in another BROKER as well (but in the same TOPIC). So, if one BROKER falls, data persists on another one.
    + After one BROKER has fallen, Kafka automatically will create new copies of lost partitions so that number of replications matches `replication-factor`
  - Issues with replication: 1. Main replica 2. Data in **followers** can be behind **leaders**
    + 1. Throughout all replicas of partitions there is one that is named **Leader** && others are named **Follower**. CONTROLLER names **Leader** && reading/writing operations are done only with **Leader** replica
    + 2. ISR (in-sync replicas) is a solution. We write simultaneously to **Leader** && **Follower** which is marked ISR. Other followers *poll* Leader periodically. => ISR follower is a variant for new **Leader** if old one cripples

Screenshots:
1. ![Alt text](img/kafka_cluster.jpg?raw=true)
2. ![Alt text](img/kafka_topic.jpg?raw=true)
3. ![Alt text](img/partition.jpg?raw=true)
4. ![Alt text](img/log_file.jpg?raw=true)
5. ![Alt text](img/consumer.jpg?raw=true)
6. ![Alt text](img/consumer_offsets.jpg?raw=true)
7. ![Alt text](img/replication.jpg?raw=true)
