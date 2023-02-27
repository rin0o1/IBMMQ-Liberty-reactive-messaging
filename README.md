# IBMMQ-Liberty-reactive-messaging

### Running the producer
cd ./app-producer
mvn liberty:dev

### Running the consumer
cd ./app-consumer
mvn liberty:dev


### Put a message from producer
curl http://localhost:9080/app-producer/system/producer
