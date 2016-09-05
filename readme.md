# quindar-mqclient
Updated: Jul 20, 2016 by Ray Lai

## Overview
This project is a helper (utility) class for Quindar platform. It acts as a RabbitMQ client to listen and subscribe to pre-defined RabbitMQ topics (e.g. audacy.telemetry.position), and then write telemetry data from the RabbitMQ server to the backend MongoDB database. The objective is to use multiple RabbitMQ clients to share the load to persist the telemetry data to the backend database.

The information flow is depicted as follows:
* Input:  Satellite or spacecraft to generate telemetry data points via Quindar-platform REST API to write to RabbitMQ
* Middleware: RabbitMQ server will persist telemetry data in one of the three topic names (audacy.telemetry.*)
* Output: quindar-mqclient will listen and subscribe to the three topics, and writes to MongoDB database via Quindar-platform REST API

## Assumptions
* You want reliability to persist telemetry data (that is the reason to use RabbitMQ)
* You want the capability to load balance large data volume when writing to the backend database (that is the reason to use multiple message queue clients to write to MongoDB)

## Pre-requisites
* This quindar-mqclient is an add-on to quindar-platform project. It depends on Quindar platform REST API Web services to write telemetry data to database
* RabbitMQ client
* NodeJS server 

## Installation
* Create a copy of the quindar-mqclient, e.g. git clone https://github.com/audacyDevOps/quindar-mqclient.git
* Install NodeJS dependencies, e.g.
```
./buildme.sh
```

On Windows machine, you can run the following commands as an alternative:
```
npm install
```

* Start the RabbitMQ client
```
node rmq-client audacy.telemetry.position position
``` 

Alternatively, if you would prefer nodemon to node, you can run:
```
nodemon rmq-client audacy.telemetry.position position
```

This will start a NodeJS process to listen to the message queue topic "audacy.telemetry.position" and write to the MongoDB collection "position".

* If you need to run multiple threads for load balancing purpose, you can run one docker container for each process, e.g.
  - docker container 1 for "nodemon rmq-client audacy.telemetry.position position"
  - docker container 2 for "nodemon rmq-client audacy.telemetry.position position"
  - docker container 3 for "nodemon rmq-client audacy.telemetry.position position"

* If you need to write telemetry data for other telemetry data types such as attitude or vehicle, you may run something like:
  - docker container 4 for "nodemon rmq-client audacy.telemetry.attitude attitude"
  - docker container 5 for "nodemon rmq-client audacy.telemetry.vehicles vehicle"

Please note that the topic name for vehicle is "audacy.telemetry.vehicles" (in plural) but the database collection name is "vehicle" (singular form).

## Reference
* For architecture and deployment, you can refer to the Quindar mission operations design documents under /docs folder of quindar-angular project (https://github.com/audacyDevOps/quindar-angular/tree/master/docs)

