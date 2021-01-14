# Kafka, Guide

### Important Links
- [Kafka Tutorial - Core Concepts](https://www.youtube.com/watch?v=dq-ZACSt_gA&list=PLkz1SCf5iB4enAR00Z46JwY9GGkaS2NON&index=4&t=0s)

> Series
- https://www.youtube.com/watch?v=qu96DFXtbG4

> All Theory About
- https://medium.com/@durgaswaroop/a-practical-introduction-to-kafka-storage-internals-d5b544f6925f#:~:text=A%20segment%20is%20simply%20a,them%20into%20chunks%20called%20segments.

> Details or bullet points.
- Broker is a physical server where Kafka instance is running. There can be multiple broker.
- Zookeeper is broker or cluster manager. Each Kafka instance is registerd in zookeper.

- Topics are virtual entities.
- Each topic is divided into multiple partitions. again number of partitions can be define at the time of Topic creations.
- Partitions will be distributed across multiple brokers.
- Each partition further uses internally Segment to write the logs.
```properties
freblogg-2(Partition)
|-- 00.index// Segment-0
|-- 00.log
|-- 00.timeindex
|-- 03.index// Seggment-3
|-- 03.log
|-- 03.timeindex
`--
```
- In above example there are 2 segment. segment message count is configurable. and new segment will be created with first offset value of coming message.

- there can be multiple producers for each topic.
- similarly there can be multiple consumer for each topic and partition. however, there to control the duplicate read in case of multiple consumer processingg the same work, consumers needs to be present in same group.



 