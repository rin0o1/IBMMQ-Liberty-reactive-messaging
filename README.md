# IBMMQ-Liberty-reactive-messaging

This project demonstrates how to achieve reactive messaging between two Open Liberty applications that produce and consume messages from a queue. The communication between these applications is facilitated by SmallRye for reactive messaging and the AMQP protocol.

The producer is an Open Liberty application that sends a message to DEV.QUEUE.1 upon triggering its endpoint /app-producer/system/producer. If the consumer is running and configured to listen for incoming messages on the channel DEV.QUEUE.1, it will automatically consume the message. For each incoming message from the consumer, a reply is produced to DEV.QUEUE.2 where the producer is listening for incoming messages.

To create a IBM MQ Queue Manager with AMQP enabled, which will be used as AMQP broker, you can follow this tutorial: 

To enable communication with the AMQP broker for both the consumer and producer, the following properties in the resources/META-INF/macroprofile-config.properties file must be set:
```
- amqp-host= your MQ Queue Manager host
- amqp-port= the port used for MQ AMQP (5672 default)
- amqp-username= username for putting/consuming messages 
- amqp-password= yourAMQPPassword
```


### Running the producer
The app-producer folder contains all the files needed for the Producer.
The Producer can be started with the following commands:
```
  cd ./app-producer
  mvn liberty:dev
```

### Running the consumer
The app-consumer folder contains all the files needed for the Consumer.
The consumer can be started with the following commands:
```
  cd ./app-consumer
  mvn liberty:dev
```

### Put a message from the producer and receive a reply from the consumer with reactive messaging
Once both the consumer and producer are running you can produce a message by triggering the following GET endpoint of the producer: 
```
  curl http://localhost:9080/app-producer/system/producer
```
If the message is successfully sent to DEV.QUEUE.1, "Message Sent!" should appear in your console. Similarly, a reply "received (my-topic): This is a message from the consumer from queue DEV.QUEUE.2" should be prompted to confirm that the consumer reply has been received and that the reactive messaging flow has succeeded.
