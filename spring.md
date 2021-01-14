# All About Spring

## Annotations
 https://www.adevguide.com/all-spring-annotations-cheat-sheet/
 
- 	![spring-life](https://github.com/smansoori87/study-notes/blob/master/images/spring/bean-life-cycle.JPG)


- @Configuration: To define any class as config class in spring to create beans and register them in container.
- @Import: In case of multiple config classes to merge all of them in single context can be used one of the configg class with Import.
- @PropertySource: To define the property location to read the property data.
- @Value: To inject the data from property to field.
- @Profile: Is to tell bean to load for which profile.
- @Bean: to create and register a object with context.
- @Component: to define any class as bean.
- @Qualifier: in case of multiple implementation of same Interface, to unique identify the bean.
- @Lazy: to define if bean want to load during call.
- @PreDistroy:
- @PostConstruct
- @DependesOn: if bean is depend on other bean. and need to execute in sequence.
- 

## Scope
- Singleton
- Prototype
- Session
- Request
- CustomeSccope(Rare)
- WebSocket

## AOP
- Tangling: different concern in class then business logic
- Scattering: same code in multiple class

###: AOP Phase
- Jointpoint: Where to call
- Pointcut: Expresion to select joint point 
- Advice: What to do.
- @Aspect: Enable AOP with @component
- Weavin: 

