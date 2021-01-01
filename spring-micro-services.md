   
---
# ************Spring MS************
---

[Best-Practices-For-Better-Restful-api](
https://blog.mwaysolutions.com/2014/06/05/10-best-practices-for-better-restful-api/)

[GitHub URL to read Complete Project.](https://github.com/in28minutes/spring-microservices)

## Topics

- Spring-Cloud-Config-Server
- Config server to client
- Spring JPA
- Spring Feign
- Spring Ribbon [client side load balancing of multiple MS]
- Eureka Naming Server
---
### Spring Cloud Config Server
---
- Centralized place for all the configuration for different micro services at different level like Dev, QA, Prod and so on...
- Atleast use Boot 2.0.0M3[not snapshot]
- give the appropriate artifact id.
- add Devtools, Config Server dependencies while creating service.
 

##### Use below annotation to define the application as Spring-Cloud-Config-Server

```java 

@EnableConfigServer
@SpringBootApplication
public class SpringCloudConfigServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringCloudConfigServerApplication.class, args);
	}
}
```

#####  Make below changes in applicaiton.properties file

```properties 
spring.application.name=spring-cloud-config-server
server.port=8888
```

#####  Add belwo changes in bootstrap.properties file.

```properties 
spring.cloud.config.server.bootstrap=true
spring.cloud.config.server.git.clone-on-start=true
```

##### below change is to read files from git server direct using url

```properties 
#spring.cloud.config.server.git.uri=https://github.com/suhailmansoori87/Git_Micro_Service_Config_Repo.git
#below changes to read it from local git.
spring.cloud.config.server.git.uri=file://${user.home}/git/Git_Micro_Service_Config_Repo
```

##### use [applicationName].[filename].propertyName in respective properties file
#### Ex:
Created currency-exchange-service.properties file:
currency-exchange-service.currency-exchange-model.conversionMultiple=89

---
### Config server to client
---
##### How to read data from Config server to client:
- Add below minimal dependencies to create a microservice that will read data from config-server.
[Devtools, Web, actuator, Config-client]

@ConfigurationProperties("")
- Annotation useful to read data from application.properties/config server respective to profiles.

application.properties
//add below property at client side in application.properties to read the respective config file form config server like DEV/QA/PROD 
spring.profiles.active=dev

bootstrap.propertrties
>Its must to have the same name of application and files in config server.
[name in bootstrap.properties]spring.application.name=currency-exchange-rate-service
Files in git repo[config server]
//currency-exchange-rate-service-qa.properties
//currency-exchange-rate-service.properties

Ex:

```java 

@Component
@ConfigurationProperties(prefix="limit-service")
public class LimitConfiguration {

	private int minimum;
	private int maximum;
	
	//Setters, Getters, Constructor
}
```
```properties

spring.application.name=limit-service
limit-service.minimum=99
limit-service.maximum=9999
```
Other way to read is:
@RefreshScope

Use full property name in place holder to read from config server.

```java 

@Component
@RefreshScope
public class LimitConfiguration {

	@Value("${limit-service.minimum}")
	private int minimum;
}
```	
----------------------------------------------------------------------------------------------------	
--------------------------------------connect with Git Config Repository--------------------------------------------------

How to connect with Git Config Repository.
-Create Repository in Git and sync add the same in Eclipse.
-To Add the same folder in project[CloudConfigServer], Right click on project->buildpath->LinkSource[select the repository location].
-Add respective configuration properties files in the newly added folder in build path.[property file must to have the same name as project name, so it will be easy to distinguish between different projects.]
-To define the files at DEV/QA/UAT/STAGE/PROD use the [filename]-dev/qa/uat/stag/prod.
 Ex: limit-service-qa.properties
-Use below property in client application.properties file to read the respective profile data.
-spring.profiles.active=dev

----------------------------------------------------------------------------------------------------
-------------------------------------Spring JPA:---------------------------------------------------


[imp] https://dzone.com/articles/introduction-to-jpa-using-spring-boot-data-jpa
https://www.petrikainulainen.net/programming/spring-framework/spring-data-jpa-tutorial-introduction/
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```
Internally it will take care of Hibernet-Core, Hikari CP, SpringJdbc, Spring Tx, Spring JPA and others.

>Spring JPA is part of Spring Data project and built on top of Spring Data Common. 
Where Spring Data Exposed 4 different interfaces to connect with underline JPA like Hibernet and all.

>Spring JPA-->Spring Data Common-->JPA Providers[Like Hibernet, Ibaties and all]-->JPA[Java Persistence API]-->DB

>Spriing Data Common exposed 4 different interfaces.
[PagingAndSortingRepository-->CRUD Repository-->Repository][QuerydslPredicateExecutor]

>Moreover Spring JPA simplify this hireachy and came up with only 2 interface.
[JPARespository, JpaSpecificationExecutor]

::::::::::::H2 Changes:::::::::::::::
>If using H2 as dB instance, once app up and running to check the DB use below properties changes in application.properties
	-spring.jpa.show-sql=true
	-spring.h2.console.enabled=true

Use below change in h2-console
Ex URL:http://localhost:8000/h2-console
Change JDBC URL to :>jdbc:h2:mem:testdb

>
@Entity:
To define a class as Entity[table in DB]
Note: if Class Name is in initial upper case, JPA will create table with _ saprate.
EX: If Class name is ExchangeRate[class name]-->exchange_rate[in DB]

>@Id
To Define the primary key column

>@Column:
If there is need to create the coulumn in DB with diff name.
@Column(name="currency_from")

>To start with JPA:
Create an interface extend jpaRepository<Entity, Id>
Ex:
public interface ExchangeRateRepository extends JpaRepository<ExchangeRate, Long>{
	ExchangeRate findByFromAndTo(String from, String to);
}

>findByFromAndTo:
findBy is an standard prefix from JPA where we can add the conditions based on column. as in above example finding ExchangeRate based on from and to column.

>


----------------------------------------------------------------------------------------------------
---------------------------------------RestTemplate:-------------------------------------------------

Use to make the Server to Rest call.
```java 
@RestController
public class CurrencyConversionController {

	@Autowired
	Environment env;

	@GetMapping("/currency-conversion/api/v1/from/{from}/to/{to}/quantity/{quantity}")
	public CurrencyConverterBean currencyConverter(@PathVariable String from, @PathVariable String to, @PathVariable BigDecimal quantity) {

		Map<String, String> uriVar = new HashMap<String, String>();
		uriVar.put("from", from);
		uriVar.put("to", to);
		
		ResponseEntity<CurrencyConverterBean> re = new RestTemplate().getForEntity("http://localhost:8000/exchange-rate/api/v1/from/{from}/to/{to}",
				CurrencyConverterBean.class, uriVar);
		CurrencyConverterBean ccb = re.getBody();
		System.out.println(">>>>>>>>>>>" + ccb);

		return new CurrencyConverterBean(ccb.getId(), from, to, ccb.getConversionMultiple(), quantity, ccb.getConversionMultiple().multiply(quantity),
				Integer.valueOf(env.getProperty("local.server.port")));
	}
}
```
----------------------------------------------------------------------------------------
------------------------------------Spring Feign:---------------------------------------------------

-Its helps and simplify the process to invoke the server to server rest communication.
-Also its provide the important component called Ribbon that is client side load balancer.

-Add below dependency in pom.xml
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```
> Use below annotation to mark your application to read the feign class paths.
```java 
@SpringBootApplication
@EnableFeignClients("com.cs.feign")
public class CurrencyConversionServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(CurrencyConversionServiceApplication.class, args);
	}
}
```
>To read other microservice data use below annotations.
```java 
@FeignClient(name = "currency-exchange-rate-service", url = "localhost:8000")
public interface ExchangeRateControllerProxy {

	@GetMapping("/exchange-rate/api/v1/from/{from}/to/{to}")
	public CurrencyConverterBean currencyExchange(@PathVariable String from, @PathVariable String to);
}
```
>How to use the same.
```java 
@GetMapping("/currency-conversion-feign/api/v1/from/{from}/to/{to}/quantity/{quantity}")
public CurrencyConverterBean currencyConverterFeign(@PathVariable String from, @PathVariable String to, @PathVariable BigDecimal quantity) {

	CurrencyConverterBean ccb =ercp.currencyExchange(from, to);
	
	return new CurrencyConverterBean(ccb.getId(), from, to, ccb.getConversionMultiple(), quantity, ccb.getConversionMultiple().multiply(quantity),
			Integer.valueOf(env.getProperty("local.server.port")));
}
```
----------------------------------------------------------------------------------------
--------------------------------------Spring Ribbon:--------------------------------------------------


As Feign made developer life easier by providing the proxy interface for MS to MS communication, Still there is issue as it cant hadle multiple
instances of same microservice. and the same will become the bottleneck for scalability.
To Overcome the same problem Ribbon have client side load balancing concept and there we can configure multiple instances
of same microservice in property file.

Dependency needs to be added in pom.xml
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
</dependency>
```
Create Proxy class as below:
----------------
```java 
@FeignClient(name = "currency-exchange-rate-service")
@RibbonClient(name="currency-exchange-rate-service")
public interface ExchangeRateControllerProxy {

	@GetMapping("/exchange-rate/api/v1/from/{from}/to/{to}")
	public CurrencyConverterBean curr
	encyExchange(@PathVariable("from") String from, @PathVariable("to") String to);
}
```
Make the below changes in bootstrap.properties file
-----------------
currency-exchange-rate-service.ribbon.listOfServers=localhost:8000,localhost:8001
[service-name].ribbon.listOfServers=[list of servers]

## Eureka Naming Server:

As Ribbon providing client side load balancing but still you would not have control to add or remove the instances of services 
flexibly or without touching the code base. 
Eureka provide flexiblity to hande this situation. Any MS can register it self with Eureka and the other MS can look into naming server to get the
instance of MS using Eureka client.

- Add Below dependency into pom.xml
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

- Add below annotation to enable Eureka server n your main class.

```java 
@SpringBootApplication
@EnableEurekaServer
public class NetflixEurekaNamingServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(NetflixEurekaNamingServerApplication.class, args);
	}
}
```

- Add below configuration into application properties file to test the server first time.
```properties
spring.application.name=netfix-eureka-naming-server
server.port=8761
```
- Below properties to register eureka itself with nameself.
```properties
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
```

- Add Below dependency into client to register it with eureka.[Eureka Discovery]
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

- Add "@EnableDiscoveryClient" to enable the nameserver discovery.

```java 
@SpringBootApplication
@EnableDiscoveryClient
public class CurrencyConversionServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(CurrencyConversionServiceApplication.class, args);
	}
}
```

- Add below configuration in application.property file.
```properties
eureka.client.service-url.default-zone=http://localhost:8761/eureka
```

To talk to other services just use Ribbon and Feign with service name and it will work like pro... :)
Also comment the "currency-exchange-rate-service.ribbon.listOfServers" if there is any in applicaiton.properties.

---------------------------------------------------------------------------------------------------------------------
----------------------Config Server with Name Server------------------------------------------------------------------

How to link Config Server with Name Server and connect with client.
*******************
>To do the same enable config server with EurekaClient dependencies as mentioend in above steps.
>To read data from config server add below property in application/bootstrap.properties.

spring.cloud.config.discovery.enabled=true

---------------------------------------------------------------------------------------------------------------------
----------------------ZUUL - API GateWay------------------------------------------------------------------

>Front Controller to MS arc. All the calls and request will go through the same gateway.
>Enable the Nameserver configuration as client.

>Add below dependency into pom.xml to start with ZUUL.
*******************
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-zuul</artifactId>
</dependency>

>Add below annotation enable Zuul.
*******************
```java 
@EnableZuulProxy
@EnableDiscoveryClient
@SpringBootApplication
public class NetflixZuulApiGatewayServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(NetflixZuulApiGatewayServerApplication.class, args);
	}
}
```
>Create Logging Filter
*******************
```java 
@Component
public class ZuulLoggingFilter extends ZuulFilter {

	private Logger logger = LoggerFactory.getLogger(this.getClass());
	
	@Override
	//to enable or disable the filter
	public boolean shouldFilter() {
		return true;
	}

	@Override
	//business logic at the time of filter
	public Object run() throws ZuulException {
		HttpServletRequest request = RequestContext.getCurrentContext().getRequest();
		logger.info("request->{} request uri->{}", request, request.getRequestURI());
		return null;
	}

	@Override
	//To execute pre post or error condition
	public String filterType() {
		return "pre";
	}

	@Override
	//priority and sequence of filter
	public int filterOrder() {
		return 1;
	}
}
```
>It's must to use the URIs in below format to work with Zuul API GATEWAY.
*******************
/[application-name]/URI
original : /exchange-rate/api/v1/from/{from}/to/{to}
with API:/currency-exchange-rate-service/exchange-rate/api/v1/from/{from}/to/{to}

//@FeignClient(name = "currency-exchange-rate-service", url = "localhost:8000")
//@FeignClient(name = "currency-exchange-rate-service")
```java 
@FeignClient(name = "netflix-zuul-api-gateway-server")
@RibbonClient(name="currency-exchange-rate-service")
public interface ExchangeRateControllerProxy {

	//As using Api gateway, any uri has to append with its application name.
	//@GetMapping("/exchange-rate/api/v1/from/{from}/to/{to}")
	@GetMapping("/currency-exchange-rate-service/exchange-rate/api/v1/from/{from}/to/{to}")	
	public CurrencyConverterBean currencyExchange(@PathVariable("from") String from, @PathVariable("to") String to);
}
```
>The Final URI that will be used to outer world is like below.
*******************
http://localhost:8765/currency-conversion-service/currency-conversion-feign/api/v1/from/USD/to/INR/quantity/22

---------------------------------------------------------------------------------------------------------------------
----------------------Spring Cloud Sleuth------------------------------------------------------------------

>In MS as there will ne n number of components which are intracting to each other its very had to manage the request flow.
>Spring Sleuth use to provide the mechanism to manage the centeralize tracing id in flow of request. so it can be easeley track.

>Add Below dependency in pom.xml
*******************
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>

>Add below code to enable the id generation.
*******************
//To link centralized tracing id.
```java 
@Bean
public Sampler defaultSampler() {
	return Sampler.ALWAYS_SAMPLE;
}
```
>Add the appropriate log in individual modules.
*******************

>Log O/P sample
*******************
2016-06-17 16:12:36.902 INFO [slueth-sample,432943172b958030,432943172b958030,false] 12157 --- [nio-8080-exec-2] com.example.SleuthSampleApplication : calling home 2016-06-17 16:12:36.940 INFO [slueth-sample,432943172b958030,b4d88156bc6a49ec,false] 12157 --- [nio-8080-exec-3] com.example.SleuthSampleApplication : you called home
[slueth-sample,432943172b958030,b4d88156bc6a49ec,false]
[application-name,  trace id, span id,  indicate whether the span should be exported to Zipkin]

---------------------------------------------------------------------------------------------------------------------
----------------------Zipkin------------------------------------------------------------------
>Spring Finchley.M3 onwards, due to compatability issue with Sleuth, Spring-boot removed zipkin from stack.
>There is way to use indipendently the same using zipkin executable jar.
>
