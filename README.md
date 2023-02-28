# IBMMQ-Liberty-reactive-messaging

This project demonstrates how to achieve IBM MQ reactive messaging between two Open Liberty applications that produce/consume messages to/from a queue. The communication between these applications is facilitated by SmallRye for reactive messaging and the AMQP protocol.

To create and setup a IBM MQ Queue Manager with AMQP enabled, which will be used as AMQP broker, you can follow this tutorial: 
https://developer.ibm.com/tutorials/mq-setting-up-amqp-with-mq/?mhsrc=ibmsearch_a&mhq=MQ%20AMQP

To enable communication with the AMQP broker for both the consumer and producer, the following properties in the resources/META-INF/macroprofile-config.properties file must be set:
```
- amqp-host= your MQ Queue Manager host
- amqp-port= the port used for MQ AMQP (5672 default)
- amqp-username= username for putting/consuming messages 
- amqp-password= yourAMQPPassword
```

The producer is an Open Liberty application that sends a message to DEV.QUEUE.1 upon triggering its endpoint /app-producer/system/producer. If the consumer is running and configured to listen for incoming messages on the channel DEV.QUEUE.1, it will automatically consume the message. For each  Consumer's incoming message a reply is produced to DEV.QUEUE.2 where the producer is listening for incoming messages. 

The SmallRye version used is 4.3.0 which includes in its dependecy tree MicroProfile Reactive Messaging 3.0.
The microprofile configuration can be found in ./resources/META-INF/macroprofile-config.properties for both the producer and consumer.

The reactive message flow occurs as follow: 
1- The producer put a message by emitting into the channel 'data-out' which is configured as follow: 
```
mp.messaging.outgoing.data-out.connector=smallrye-amqp
mp.messaging.outgoing.data-out.address=DEV.QUEUE.1
mp.messaging.outgoing.data-out.use-anonymous-sender=false
mp.messaging.outgoing.data-out.capabilities=queue 
```

2- The consumer consumes the message by listening at channel 'data-in' which is configured as follow:
```
mp.messaging.incoming.data-in.connector=smallrye-amqp
mp.messaging.incoming.data-in.address=DEV.QUEUE.1
mp.messaging.incoming.data-in.use-anonymous-sender=false
mp.messaging.incoming.data-in.capabilities=queue
```

3- The consumer is replying by putting message into the channel 'data-out' which is configure as follow:
```
mp.messaging.outgoing.data-out.connector=smallrye-amqp
mp.messaging.outgoing.data-out.address=DEV.QUEUE.2
mp.messaging.outgoing.data-out.use-anonymous-sender=false
mp.messaging.outgoing.data-out.capabilities=queue
```

4- The producer is listening for reply messages from the channel 'data-in' which is configured as follow:
```
mp.messaging.incoming.data-in.connector=smallrye-amqp
mp.messaging.incoming.data-in.address=DEV.QUEUE.2
mp.messaging.incoming.data-in.use-anonymous-sender=false
mp.messaging.incoming.data-in.capabilities=queue
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

