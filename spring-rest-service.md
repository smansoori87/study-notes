# Spring Restful Service

### @RestController
Enable class as a controller to accept http requests.

### @RequestMapping("/path")
Enable Controller to accept default uri(uniform resource identifier).

**Ex:** 
if URI is : localhost:8080/users/1
@RequestMapping("users")

### @GetMapping
To get data.

### @PostMapping
To submit user data.

### @PutMapping
To update data.

### @DeleteMapping
To delete data.

### @PathParam()
To read data from URI path.

**Ex:**
URI: /user/1

```java
@GetMappin(/{user})
User getUser(@PathVariable("user") String user){
 return null;
}
```

### @RequestParam()
- To get parameter from request or query.
- @RequestParam always look for value to bind else give exception.

```java
@GetMappin()
User getUser(@RequestParam("user") String user){
 return null;
}
```
	
### @QueryParam()
- To read query data.
- not mandatory to be part of request and supply is optional.

```java
@GetMappin()
User getUser(@QueryParam("user") String user){
 return null;
}
```

### Produces:
To define the content type as per accept type in header.
While adding the XML response support, its need to add **jackson-dataformat-xml** dependency.
```java
@GetMapping(value="/user", produces={MediaType.APPLICATION_XML_VALUE, MediaType.APPLICATION_JSON_VALUE)
```

### ResponseEntity<T>
Class to return the HTTPStatus along with response object.

```java
ResponseEntity<User> getUser(){

	return new ResponseEntity<User>(null, HttpStatus.Ok);
}
```

#### ConstraintValidation
- add below dependency in pom.
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

- use @valid annotation in postmethod along with @RequestBody.
- use respective validation annotation in model like @Notnull, @Pattern, @Size

#### how to execute service jar from cmd
- Clean build project from mavan using command line.
- use java -jar "jar path" to run the service.


