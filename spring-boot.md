# spring-boot
---
We use the @SpringBootApplication annotation in our Application or Main class to enable a host of features, e.g. Java-based Spring configuration, component scanning, and in particular for enabling Spring Boot's auto-configuration feature.

If you have been using Spring Boot for a long time, then you know that we need to annotate our Application class or Main class with quite a lot of annotations to start with, for example:

1. @Configuration to enable Java-based configuration
2. @ComponentScan to enable component scanning.
3. @EnableAutoConfiguration to enable Spring Boot's auto-configuration feature.

But now you can do all that by just annotating your Application class with @SpringBootApplication.

By the way, this annotation is available from Spring 1.2 onwards, which means that if you are running on lower Spring Boot versions, then you still need to use @Configuration, @CompnentScan, and @EnableAutoConfiguration if you need those features.

## @SpringBootApplication Example
Here is a simple example of how to write a Spring Boot application using the @SpringBootApplication annotation.

![Spring-Boot](https://github.com/smansoori87/study-notes/blob/master/images/spring-boot/spring-boot.JPG)

The Main class serves two purposes in a Spring Boot application: configuration and bootstrapping. First, it's the main Spring configuration class and second, it enables the auto-configuration feature of a Spring Boot application.

![Spring-Boot](https://github.com/smansoori87/study-notes/blob/master/images/spring-boot/spring-boot-configuration.JPG)

### @SpringBootApplication = @Configuration + @ComponentScan + @EnableAutoConfiguration
The @SpringBootApplication annotation is a combination of following three Spring annotations and provides the functionality of all three with just one line of code.

### @Configuration
This annotation marks a class as a Configuration class for Java-based configuration. This is particularly important if you favor Java-based configuration over XML configuration. 

### @ComponentScan
This annotation enables component-scanning so that the web controller classes and other components you create will be automatically discovered and registered as beans in Spring's Application Context. All the@Controller classes you write are discovered by this annotation.

### @EnableAutoConfiguration
This annotation enables the magical auto-configuration feature of Spring Boot, which can automatically configure a lot of stuff for you.

For example, if you are writing a Spring MVC application and you have Thymeleaf JAR files on the application classpath, then Spring Boot auto-configuration can automatically configure the Thymeleaf template resolver, view resolver, and other settings automatically.

So, you can say that @SpringBootApplication is a 3-in-1 annotation that combines the functionality of @Configuration, @ComponentScan, and @EnableAutoConfiguration.

It also marks the class as a BootStrap class, which means you can run it as a normal Java class, e.g. by running its JAR file from the command prompt as shown here, or just right-clicking and running it as a Java program in Eclipse IDE.

![Spring-Boot-App-Run](https://github.com/smansoori87/study-notes/blob/master/images/spring-boot/spring-boot-app-run.JPG)

This will start the embedded server that comes along with Spring Boot and runs your web application inside it. Once you see the log without any errors, you can go to the browser and open the localhost with the server port to access your Spring Boot application.

That's all about the @SpringBootApplication annotation and a simple application to demonstrate how to use it. As I said, this nice little annotation packs quite a lot of punch. You can just write this one line of code to enable Java-based configuration, component scanning, and to enable the auto-configuration feature of Spring Boot. It makes your code more readable.


