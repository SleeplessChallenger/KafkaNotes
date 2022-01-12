<h2>Kafka notes</h2>

**Kafka** is an event streaming platform.

TODO: add screenshots

<a href="https://www.youtube.com/watch?v=-AZOi3kP9Js">Link to the video</a>

```bash
EVENTS -> PRODUCERS -> BROKERS -> CONSUMERS
```

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
  
  * Broker (aka Kafka node, server etc): pickup, delivery, storage of messages
    ** we have many BROKERS for fault tolerance which make KAFKA CLUSTER
    ** from all of the BROKERS there is one CONTROLLER, which ensures CONSISTENCY
    ** *Kafka* is a master-slave system
   
  * Zookeeper: condition of the cluster, configuration, address book (data)
    ** i.e. to choose a CONTROLLER we use Zookeeper
  
  * Message consists of:
    ** Key
    ** Value: content of the message (bytes)
    ** Timestamp
    ** Headers
  
  * Topic/Partition:
    ** Topic is a stream of data (like *table*). It has a queue where we have our MESSAGES (which can be from various sources)
    ** *Kafka* uses FIFO for TOPIC
    ** MESSAGES are not deleted from TOPIC which makes it possible for MESSAGES to be shared accross various CONSUMERS
    ** <b>Partitions</b> are for boosting the reading/writing of data (parallelism)
      - FIFO per partition
      - I.e. we can send MESSAGES of one user to one partition and hence we can get them in order
      - Topic A -> Broker 1, 2, n -> Partitions per Broker. **Screenshot!**
        - Why sometimes one Broker can hold all partitions of some Topic? -> Kafka makes balance of all partitions on the Broker, not of the particular Topic. But keep in mind that some Topic can be more weighty
      - Where is data from TOPIC in the BROKER? -> In `Log files`
        - Let's look at screenshot of Broker-File-System
    ** we can't delete TOPIC directly. But there is **TTL** aka time-to-live which deletes *Segments*. If Segment timestamp expired -> to delete (based on the latest timestamp)
  
  * Producer: MESSAGE sender which writes to `BROKER -> TOPIC -> LEADER partition`
    ** acks: 0 - send once and don't wait for response; 1 - response of success only from **Leader**; -1 (aka `all`) - producer waits for response from ALL ISR + Leader

Data replication:
  - `replication-factor` if set > 1 => partition will reside in another BROKER as well. So, if one BROKER falls, data persists on another one.
    + After one BROKER has fallen, Kafka automatically will create new copies of lost partitions so that number of replications matches `replication-factor`
  - Issues with replication: 1. Main replica 2. Data in **followers** can be behind **leaders**
    + 1. Throughout all replicas of partitions there is one that is named **Leader** && others are named **Follower**. CONTROLLER names **Leader** && reading/writing operations are done only with **Leader** replica
    + 2. ISR (in-sync replicas) is a solution. We write simultaneously to **Leader** && **Follower** which is marked ISR. Other followers *pull* Leader periodically. => ISR follower is a variant for new **Leader** if old one cripples



