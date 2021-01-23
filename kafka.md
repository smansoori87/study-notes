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
- similarly there can be multiple consumer for each topic and partition. 
- To control the duplicate read in case of multiple consumer processing the same work, consumers needs to be present in same group.
- Multiple consumer can be in same group using GroupId. and Kafka mill make sure about not to assign same partition to more then one Kafka Listener in same Group.
- There is Raplication factor at Partition level and it can be define at the time of topic creation. 

## Configuration

- Dependency
```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
    <!--Version As per Spring Boot, Current one is 2.4.1-->
</dependency>
```
- While starting with Kafka Listener or Producers, Kafka topic needs to create manually and Kafka Server must to start.

- While creating Kafka topic it is must to supply Topic name, Partition factor, and Replication Factor.
- Producer Config

```java
@Configuration
public class KafkaProducerConfig {

    @Bean
    public ProducerFactory<String, String> producerFactory() {
        Map<String, Object> configProps = new HashMap<>();
        configProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
        configProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        configProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        //Incase there are Custom Objects use Json Serializer.
        //configProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
        return new DefaultKafkaProducerFactory<>(configProps);
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
}
```
- Producer Logic:
```java
public void sendMessage(String message) {
            
    ListenableFuture<SendResult<String, String>> future = 
      kafkaTemplate.send(topicName, message);
	
    future.addCallback(new ListenableFutureCallback<SendResult<String, String>>() {

        @Override
        public void onSuccess(SendResult<String, String> result) {
            System.out.println("Sent message=[" + message + 
              "] with offset=[" + result.getRecordMetadata().offset() + "]");
        }
        @Override
        public void onFailure(Throwable ex) {
            System.out.println("Unable to send message=[" 
              + message + "] due to : " + ex.getMessage());
        }
    });
}
```

- Consumer Config: Must to use @EnableKafka in configuration class.
```java
@EnableKafka
@Configuration
public class KafkaConsumerConfig {


    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        Map<String, Object> props = new HashMap<>();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
        props.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        return new DefaultKafkaConsumerFactory<>(props);
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, String> 
      kafkaListenerContainerFactory() {
   
        ConcurrentKafkaListenerContainerFactory<String, String> factory =
          new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }
}
``` 

- Consume:
```java
@KafkaListener(topics = "topicName", groupId = "foo", containerFactory ="kafkaListenerContainerFactory")
public void listenGroupFoo(String message) {
    System.out.println("Received Message in group foo: " + message);
}
```

- Imp Link:
> https://www.baeldung.com/spring-kafka 
> Producer: https://www.youtube.com/watch?v=NjHYWEV_E_o
> Consumer: https://www.youtube.com/watch?v=IncG0_XSSBg