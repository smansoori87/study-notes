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
- Keep the code changes from the code will remain same.
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


