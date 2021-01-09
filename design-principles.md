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

