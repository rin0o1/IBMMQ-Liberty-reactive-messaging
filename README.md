# IBMMQ-Liberty-reactive-messaging

This project shows how reactive messaging can be achieved between two Open Liberty applications which produce/consume messages to/from a queue.
The communication between these applications occurs using SmallRye for reactive messaging and the AMQP protocol.

The producer is a Open Liberty application which on triggering its endpoint /app-producer/system/producer will produce a message to DEV.QUEUE.1. If the consumer is running and configured to listening for incoming messages on the channel DEV.QUEUE.1, this message will be automatically consume. 
For each Consumer's incoming message a reply is produced to DEV.QUEUE.2 where the producer is listening for incoming messages. 

To create a IBM MQ Queue Manager with AMQP enabled, which will be used as AMQP broker, you can follow this tutorial: 

You have to configure your AMQP HOST, PORT, USERNAME, and PASSWORD for both consumer and producer by setting these four line within the resources/META-INF/macroprofile-config.properties file:
```
- amqp-host= your MQ Queue Manager host
- amqp-port= the port used fro the MQ Queue Manger for AQMP (5672 default)
- amqp-username= username for putting/consuming messages 
- amqp-password= yourAMQPPassword
```


### Producer
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

### Put a message from producer
Once both the consumer and producer are running you can produce a message by triggering the following GET endpoint of the producer: 
```
  curl http://localhost:9080/app-producer/system/producer
```
If this message is sent to DEV.QUEUE.1 successful "Message Sent!" should appear in your console. In the same way, a reply "This is a message from consumer" should be promped to prove that the reactive messaging flow worked correctly.
