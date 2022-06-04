# DESCRIPTION
- A simple application on practice to build a microservice design pattern using Spring Boot , Spring Cloud
dependencies

## Dependencies (OS Level)
- JDK 17
- Docker desktop
- Maven Build Tool

## Docker Dependencies
- All teh otehr dependencies will be pulled from docker hub as images

# MicroServices Overview

a. **Load Balancer/ API GATEWAY**
- An independent microservice sitting in a public network
- This is the entry point of the application. A load balancer is used to control and balance network 
traffic to the microservices,
- In cloud providers eg AWS it s better to use their load balancer than building your own one.
- For this tutorial we will use our own API Gateway which also works as a load balancer.
- API Gateway can manage and balance out network traffic just as a Load Balancer, just in a different way. 
- Instead of distributing requests evenly to a set of backend resources (e.g. a cluster of servers), 
an API Gateway can be configured to direct requests to specific resources based on the endpoints being requested.
- As seen on the diagram, the microservices should be within a private network and should not be accessed to 
the public network
- Aprt form AWS API Gateway other 3rd party API gateways are: Zuul 
- Zuul Server is an API Gateway application. It handles all the 
requests and performs the dynamic routing of microservice applications. It works as a front door for all the 
requests. It is also known as Edge Server.


b. **Client Microservice**
- An independent microservice sitting in a private network that a customer registers to
- It sits as a n independent docker container
- It communicates to other microservices via API calls, once a custoners registers, it checks the payment microservice
- if the client is fraudlent (synchronous communication means it has to wait for a response from payment microservice)
- then later saves into postgresql database
- 

c. **Payment Microservice**
- used for payment purpose
- It communicates with Customer microservice synchronously and Message Queue asynchronously

d. **Message Queue (RABBITMQ)**
- This is a layer/service added between microservices to create Asynchronous communication 
- (meaning the requests are non-blocking) . This means a producer sends a request and continues with other tasks
- before waiting for a response
- Tasks such as sending emails, sms, saving files can be asynchronous
- We are using RabbitMQ tool which has terms like: PRODUCER , QUEUE , CONSUMER
- Producing means nothing more than sending. A program that sends messages is a producer, 
- Consuming has a similar meaning to receiving.
- In the architecture above, Fraud and Customer Microservices are producers while Notification Microservice is a consumer

e. **Notification Microservice**
- This is a microservice whose task is sending SMS/EMAILS or push notifications.
- It can use a 3rd party provider like Twilio, Africas Talking, Infobip, Firebase etc
- It picks them from the queue and hence its a Consumer of the queue

f. **Eureka Server**
- We dont want to be dealing with ports 
- Every Micro service will register into the Eureka server 
- and Eureka server knows all the client applications running on each port and IP address. Eureka Server is also known as Discovery Server.
- To communicate with the clients we just need a service name
- This is usually an independent spring boot application

g. **Config Server**
- Mainly for storing the configurations 
- Whether its development, testing environment, production 

NB ~ There are better ways nowadays, you can use Kubernetes and this replaces Eureka server and config server
Kubernetes is a portable, extensible, open source platform for managing containerized workloads and services, 
that facilitates both declarative configuration and automation.

h. **Sleuth/Distributed Tracing**
- Distributed tracing is a method of observing requests as they propagate through distributed cloud environments.
- In this case we can have a 360 Degrees view of all the requests flowing in all the microservices 
this enables seeing bottleneck while in production.
- In a monolithic system, it's relatively easy to track requests as they move 
through the codebase because all requests can easily be logged to the same log file.
- Zipkin is a very efficient tool for distributed tracing in the microservices ecosystem.
- Spring Cloud Sleuth is used to generate and attach the trace id, span id to the logs so that 
- these can then be used by tools like Zipkin and ELK for storage and analysis.


TIP: In production, always pay someone else to manage your load balancer, it should be available always, multi-region, SSL certs are done
for you so that you focus on your business logic
Same tip applies to database, use a managed service eg AWS RDS
- for configurations (ports) and configurations kubernetes works well


# DEPENDENCIES
- The following dependencies are used by each of the microservice as seen in each pom.xml
- In this architecture, the parent module holds all the dependencies taht are shared by children modules
- Check pom.xml file
- 
### Parent Module
- lombok  - Reduces Boilerplate code by generating getters and setters
- spring-boot-starter - Spring Boot provides a number of starters that allow us to add jars in the classpath. 
Spring Boot built-in starters make development easier and rapid. Spring Boot Starters are the dependency descriptors.


### Clients Microservice
- spring-web -  It is used for building the web application, including RESTful applications using Spring MVC. It uses Tomcat as the default embedded container.
- Note: It inherits all other dependencies from parent module

### Eureka MicroService 
- spring-cloud-starter-netflix-eureka-server - A dependency that allows this application to be Eureka Server (where Eureka clients can register)
- Note: It inherits all other dependencies from parent module

### Notification Microservice
- spring-cloud-starter-netflix-eureka-client - A dependency that allows this microservice to be Eureka Client
- spring-boot-starter-web -  It is used for building the web application, including RESTful applications using Spring MVC. 
It uses Tomcat as the default embedded container.
- spring-cloud-starter-sleuth -Spring Cloud Sleuth allows you to aggregate and track log entries as requests move 
through a distributed software system.
- spring-boot-starter-data-jpa -  connect Spring application with relational database efficiently.
- postgresql - JDBC driver that  allows to connect to Postgresql databases
- spring-boot-starter-amqp - Spring AMQP provides us a “template” as a high-level abstraction for sending and receiving messages
to and from RabbitMQ. This protocol is used by RabbitNQ
- spring-cloud-sleuth-zipkin - Helps usage of Sleuth and Zipkin together for distributed tracing
- Note: It inherits all other dependencies from parent module

## Fraud Microservice
- spring-boot-starter-web -  It is used for building the web application, including RESTful applications using Spring MVC.
  It uses Tomcat as the default embedded container.
- spring-boot-starter-data-jpa - connect Spring application with relational database efficiently.
- postgresql - JDBC driver that  allows to connect to Postgresql databases
- spring-cloud-starter-netflix-eureka-client - A dependency that allows this microservice to be Eureka Client
- Note: It inherits all other dependencies from parent module


## Customer Microservice
- spring-boot-starter-web -  It is used for building the web application, including RESTful applications using Spring MVC.
  It uses Tomcat as the default embedded container.
- spring-boot-starter-data-jpa - connect Spring application with relational database efficiently.
- postgresql - JDBC driver that  allows to connect to Postgresql databases
- spring-cloud-starter-netflix-eureka-client - A dependency that allows this microservice to be Eureka Client
- Note: It inherits all other dependencies from parent module

## Apigw Microservice
- spring-cloud-starter-gateway - Spring Cloud Gateway aims to provide a simple, yet effective way to route to 
APIs and provide cross cutting concerns to them such as: security, monitoring/metrics, and resiliency.
- spring-cloud-starter-netflix-eureka-client - A dependency that allows this microservice to be Eureka Client
- spring-cloud-starter-sleuth  - allows you to aggregate and track log entries as requests move through a 
distributed software system.
- spring-cloud-sleuth-zipkin - Helps usage of Sleuth and Zipkin together for distributed tracing
- Note: It inherits all other dependencies from parent module


### Microservices Overview
- The containers inside docker daemon communicate with each other via a network
- Configuration of containers is done im docker-compose.yml

## DEPLOYMENT AFTER DEVELOPMENT
- To deploy these microservices, we have to build the containers into an image
- The image is then pushed to image repository such as docker hub or AWS ECR
- This is done via execution of commands in dockerfile
- In the prod server the image is then build into running containers