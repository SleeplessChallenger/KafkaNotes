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
  - Zookeepr
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
    
    ** Partitions are for boosting the reading/writing od data (parallelism)
      - FIFO per partition
      - I.e. we can send MESSAGES of one user to one partition and hence we can get them in order



