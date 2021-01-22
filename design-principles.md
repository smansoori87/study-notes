# Design Principles

## SOLID

### S - Single Responsibility
- There should be only single reason to modify the class. 
- In depth, while designing the application, classes needs to be fine grained in terms of functionality and responsibility. 
- It should not be too small that each class is only holding one method and acting more as utility class rather then functional.
- It should not be too big that changing or adding new functionality forcing developers to make frequent changes in same class.

### O - Open Close Principle
- Code should be open for enhancement and close for any modification.
- Writing or designing a application in a way that any code will not change in future is very hard and again depend on context.
- So our approach should be while designing the application that, in future if there are any change request or 
	enhancement needed it can be done with minimal or no changes.

- Sorting Algorithm Example. 
-- Using Enum and Extending abstract class with conditional manager.
-- Solution is with replacing abstract class with interface.
> Ex:
1. Strategy Design Pattern
2. Template Design Pattern	

### L - Liskov substitution principle
- It is all about Inheritance. Child classes must inharit all the property/behaviour of parent class. If not, then it is the violation of the LSP principal.
- Child class must not change the behaviour of super class.
- If T is the interface and X is the subtype, then X should inherit all the property of T and should remain same behaviour.
- In such case your interfaces should be fine grained, when classes are extending the interface, in any situation it should not be forced to inherit the behaviour.
- It is Runtime polymorphism, where Parent class will be in the position to hold the instance of child class.

> Ex:
- If there is interface Animal, holding behaviour as Speak.
- But All animal can't speak, in place, Cat is implementing Animal can Speak, but Snail is animal but can't Speak.

https://www.youtube.com/watch?v=Z_ySH41NmUQ

1. Template Design Pattern

### I - Interface Segregation Principle
- There should be distinct interfaces with respect to the functionality rather than keep all the functionality in single interface
 which may lead the classes to provide implementation for the behavioud which they are not even looking for.
 
>Ex:
- List and Set are the interfaces, extending Collection as an interface. 
- List holds, Collection + List features.
- Set holds, Collection + Set features.
 
### D - Dependency Inversion
- High level modules should not be depends on low level modules.
- Should use with Has a relationship.

> Ex:
- Spring uses IOC to inject the dependency on modules.

--

# Behavioral Design Patterns

## Strategy Pattern:
- Keep the code changes away from the code will remain same.
- Program through interfaces or abstract classes not through concrete implementations.
- Use compositions over inheritance consume the behaviors or has a relationship over is-a relationship.

Ex: In web applications, Service layer is build on interfaces and integrated with controller in has-a relationship.

## Observer Pattern:
- When making changes in one object and need to update all the other dependencies.
- to enable loose coupling between objects.
- While updating state of object and want to update all the dependent objects, This is suitable fit for such requirement.

> Ex:
- WeatherStation application
- One good example is Angular State and Observable Store. Any changes in store will give signal to update all the dependent objects.

https://www.tutorialspoint.com/design_pattern/observer_pattern.htm
https://howtodoinjava.com/design-patterns/behavioral/observer-design-pattern/

## Command Pattern
- Design pattern allow to wrap the multiple commands to execute on object.
- Object executing the logic knows nothing about the implementation or business logic.

- Command : Command Interface, holding execute method.
- Receiver: Light class, with turnOn and turnOff methods.
- Invoker : Switcher class, Holding the list of commands and executing the interface execute method without bothering about the implementation.
- Client  : Source of request.

Ex: Dispatcher Servlet serving request.

## Template Pattern
- Abstract class exposes the defined way / template to execute its methods.
- Subclasses can override the methods as per need but the invocation will remain in same way as defined in template.

>Ex:
- Abstract class holding 3 abstract methods,

public abstract class Algo{
 public abstract void init();
 public abstract void sorting();
 public abstract void print();
 
 public void sort(){
  init();
  sorting();
  print();
 }
} 

## Null Object Pattern
- design to validate null objects.

## Visitor Pattern

#

--
# ACID:

## Atomic: 
- Either all the steps involved in transaction executes or none of them will execute.

## Consistency:
- While dealing with multiple transactions, if 2 transactions are related to each other in such case there should be any differences in relative data after and before the transaction.
- While doing the development as part of developer responsibility if A/I/D is managged in correct manner then C is auto achivable.
-Ex: If T1 and T2 in transaction and updating or transferring fund to each other then the sum of both account post and pre transaction should remain same.

## Isolation
- Transactions are in isolation when they are not related to each other. It means T1 and T2 not impacting each other when they are running in parallel.

## Durability
- Changes or data in system should be durable, irrespective of Hardware or Software failure.
---

# CAP:

## Consistency:
- In distributed system, If there are multiple nodes connected to each other, at any point of time writing data at one node and reading data from other node should give the same result. 

## Availability:
- For each request from client system should respond in defined time limit irrespective of any conditions.

## Partition Tolerance:
- If any node is failed in system, it should not fail the system and the remaining nodes in system should be able to manage all the client request.

### CA, CP, AP

## CA: 
- System will be in CA if it support Consistency and Availability at same time.
- It is the case when there is one node system and only responsible for read and write operations.
- Ex: All RDMS like mySql, MariaDb, Db2 runs in CA mode.

## CP: 
- If nodes in cluster are arranged in Master slave pattern and only one node will take the responsibility of Read and write operations.
- 'A' is not possible in above arrangement because if Master node failed then cluster will take some time to elect new master node and system will be unavailable for that period of time.
- Ex: Redis

## AP:
- If nodes in cluster are arranged in a way that each node can participate in read and write operations.
- 'C' is missing because in above arrangement, there is surely a time difference between nodes to get in sync with data. and during that period read immediate after write may give inconsistent behaviour. 
- Ex: Cassandra



--
# Microservice Design Pattern

https://dzone.com/articles/design-patterns-for-microservices

## Domain Driven Design:
- Divide the whole application into logical domains arround the bounded context. 
- An example there is Wallet Applicaiton like Phonepe or Paytm
	- Scan and process QR
	- Pay to wallet
	- Pay to Merchant linked bank account
	- accounts(CC/DB/net banking)
	- Adding money in wallet
	- Billing service(recharge, electric bill, gas bill, water bill
	- Wallet: Balance(), Add Money(), Payment
	- Merchants: merchant related 
	- User: User details, update
	- 

## Strangler:
- Best suit to support the monolith to microservice journey. 
- This solution works well with web applications, where a call goes back and forth, and for each URI call, a service can be broken into different domains and hosted as separate services. The idea is to do it one domain at a time.
- passthrough services. from main application to individual domains.

## API Gateway Pattern:
- basic pillers of microservice architecture, where API Gateway will act as front controller for any incoming request and sharing the responsibility of cross cutting concerns like  Authentication, Authraziation, protocol conversion.

## Aggregator Pattern:
- 1. A composite microservice will make calls to all the required microservices, consolidate the data, and transform the data before sending back.
- 2. An API Gateway can also partition the request to multiple microservices and aggregate the data before sending it to the consumer.
- It is recommended if any business logic is to be applied, then choose a composite microservice. Otherwise, the API Gateway is the established solution.

## Client-Side UI Composition Pattern
- With microservices, the UI has to be designed as a skeleton with multiple sections/regions of the screen/page. Each section will make a call to an individual backend microservice to pull the data.
- These screens are known as Single Page Applications (SPA). This enables the app to refresh a particular region of the screen instead of the whole page.

## Database per Service
- To solve the above concerns, one database per microservice must be designed; it must be private to that service only. 
	It should be accessed by the microservice API only. It cannot be accessed by other services directly. For example,
 	for relational databases, we can use private-tables-per-service, schema-per-service, or database-server-per-service
 	
## Shared Database per Service
- if the application is a monolith and trying to break into microservices, denormalization is not that easy. What is the suitable architecture in that case?
- Most people consider this an anti-pattern for microservices, but for brownfield applications, this is a good start to break the application into smaller logical pieces.
- but it has to be restricted to 2-3 maximum, otherwise scaling, autonomy, and independence will be challenging to execute.

## CQRS (Command Query Responsibility Segregation )
- Mainly to segregate the responsibility of Command(Create, Update, Delete) and Query(Reading) the data.
- Segregate the data for Read and Write operations.
- Ex: https://www.youtube.com/watch?v=hrmeeCEqzo8

## SAGA
- way to manage transactions in distributed system. 
- In case there are multiple services and way they are related/depend on each other process, Saga gives the flexibility as implementation.

> there are 2 different way in Saga to handle the above scenarios.
- 1. Choreography: All service will connect throughmessage brokers and listin based on event.
		- Only suitable if there are minimal service.
- 2. Orchestration: It is required a cordinator service which will take the responsibilty to manage all the services in transaction.
		- in case if one service fail, manager service will take the responsibility to give instruction to other services to rollback there previos changes.
		
## Observability Patternr
- Log Agrigation
- Distributed Tracing
- Performance Monitor
- Health Check

## Cross Cutting Concern Pattern
- Circit Breaker
- Name Server or Registry Server
- External Config
- Blue Green Deployment
- 
