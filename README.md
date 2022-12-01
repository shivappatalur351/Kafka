# Kafka

			POC Of Apache Kafka
Introduction to Kafka
Apache Kafka is publish-subscribe based fault tolerant messaging system. It is fast, scalable and distributed by design.
Terminology:
⦁	Producers send messages to brokers
⦁	Consumers read messages from brokers
⦁	Messages are sent to a topic
⦁	Each topic is broken into one or more ordered partitions of messages
Kafka Architecture

 
fundamentals of kafka
⦁	Producers: They are the one who will public the messages.
⦁	 Topics:  Stream of messages in 1 category.
⦁	Partition: Each topic has 1/multiple partition and have messages in sequence of immutatable(no change) data
⦁	Partition offset: It has own offset.uniqye sequence id
⦁	Replicas of partition: No data loss,backups of partitions
⦁	Brokers:  They are simple system made ty handle/maintain the publish data and has muliple partition.1 broker-1 topic
⦁	Kafka cluster: Any kafka having more than 1 broker
⦁	Consumers: Receivers are the
⦁	Leader: Every single broker will have 1 leader,responsible for reading and writing the data to consumers.
⦁	Followers: They are the one who will follow leaders,leader fails follower will take a initiative.
Why we need kafka?
⦁	Helps to have 0 data loss,0 down time.
Advantages
⦁	High Throughput: It can easily handle a large volume of data when generating at high velocity is an exceptional advantage in Kafka’s favour. This application lacks huge hardware to support message throughput at a frequency of thousands of messages per second.
⦁	Low Latency: Low latency handling this high volume message generation.
⦁	Fault Tolerance: This feature is handy; it has an inherent capability to be restricted by a node built into a cluster.
⦁	Durable: It is very durable in its operation and is so why many MNC’s are preferring to use Kafka. Talking of durability in operations, the messages cannot get lost in the long term.
Installing Apache Kafka:
Step 1:Download the apache Kafka https://kafka.apache.org/downloads.html
step2:Extract the kafka file in C drive
step3:open the kafka file create one folder data 
⦁	create a zookeeper and kafka folder inside the data folder		
step4:go to config and choose zookeeper properties file and open notepad++
⦁	change the dataDir=C:\kafka_2.13-3.3.1\Data\zookeeper
step5: go to config and choose the server properties file and open notepad++
⦁	change the log.dirs=C:\kafka_2.13-3.3.1\Data\kafka
stap6: go to current directory enter cmd Ex: C:\kafka_2.13-3.3.1
⦁	First Run ZooKeeper command:
		 .\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties
⦁	open new command and run Kafka :
    		.\bin\windows\kafka-server-start.bat .\config\server.properties

SpringBoot With Kafka Implementation:
KafkaApplication.java
@SpringBootApplication
public class MessageApplication {

	public static void main(String[] args) {
		SpringApplication.run(KafkaApplication.class, args);
	}

Producer.java
@Service
public class KafkaProducer {

    private static final Logger LOGGER= LoggerFactory.getLogger(KafkaProducer.class);
    @Autowired
    private KafkaTemplate<String,String> kafkaTemplate;
    public void sendMsg(String message){
        LOGGER.info(String.format("message sent -> %s",message));
        kafkaTemplate.send("Kafka_Topic",message);
    }
}

Consumer.java
@Service
public class KafkaConsumer {

    private static final Logger LOGGER= LoggerFactory.getLogger(KafkaConsumer.class);

    @KafkaListener(topics="Kafka_Topic",groupId="myGroup")
    public void consume(String message)throws Exception{
        LOGGER.info(String.format("Message Received -> %s",message));
    }

}

RestApiController.java

@RestController
@RequestMapping("/api/kafka")
public class RestApiController {
    private KafkaProducer kafkaProducer;

    public RestApiController(KafkaProducer kafkaProducer) {
        this.kafkaProducer = kafkaProducer;
    }
    //http://localhost:8080/api/kafka/publish?message=hello world
    @GetMapping("/publish")
    public ResponseEntity<String> publish(@RequestParam("message") String message){
        kafkaProducer.sendMsg(message);
        return ResponseEntity.ok("Message sent to the Topic");
    }
}
