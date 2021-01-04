# ZUUL - API Gateway

- Front Controller to MS arc. All the calls and request will go through the same gateway.
- Enable the Nameserver configuration as client.

- Add below dependency into pom.xml to start with ZUUL.
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-zuul</artifactId>
</dependency>
```

- Add below annotation enable Zuul.

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

- Create Logging Filter

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

- It's must to use the URIs in below format to work with Zuul API GATEWAY.
```
/[application-name]/URI
original : /exchange-rate/api/v1/from/{from}/to/{to}
with API:/currency-exchange-rate-service/exchange-rate/api/v1/from/{from}/to/{to}

//@FeignClient(name = "currency-exchange-rate-service", url = "localhost:8000")
//@FeignClient(name = "currency-exchange-rate-service")
```

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
- The Final URI that will be used to outer world is like below.

http://localhost:8765/currency-conversion-service/currency-conversion-feign/api/v1/from/USD/to/INR/quantity/22

# Spring API Gateway
- While Zuul is in maintanance mode and no longer in support from Spring, It is recommanded to use Spring API gateway.

- Below are the dependencies while creating Spring-Api-Gateway project.
- Spring-Reactive-Web is the dependency to load gateway UI as spring gateway uses react for UI.

![spring-api-gateway-dependency](https://github.com/smansoori87/study-notes/blob/master/images/spring-api-gateway/spring-api-gateway-dependency.jpg)

- Below are the properties to enable gateway and register itself with name/discovery server.
- lower-case-service-id need to set true in case using client service instance name in lower case. without this property it is must to use in capital letter which is very uncommon in case of any URI.

![spring-api-gateway-config](https://github.com/smansoori87/study-notes/blob/master/images/spring-api-gateway/spring-api-gateway-config.jpg)

- To add URL rewriting or route configuration using predicates, filters, below is the configuration.

![spring-api-gateway-uri_config](https://github.com/smansoori87/study-notes/blob/master/images/spring-api-gateway/spring-api-gateway-uri_config.jpg)

- To call configured URI from gateway below is the path.
- http://localhost:8082 is API gateway address.

![spring-api-gateway-uri_call](https://github.com/smansoori87/study-notes/blob/master/images/spring-api-gateway/spring-api-gateway-uri_call.jpg)

- URI rewriting manual

![spring-api-gateway-uri_re-write_1](https://github.com/smansoori87/study-notes/blob/master/images/spring-api-gateway/spring-api-gateway-uri_re-write_1.jpg)

- URI rewriting with regular expression, seggment based.

![spring-api-gateway-uri_re-write_2](https://github.com/smansoori87/study-notes/blob/master/images/spring-api-gateway/spring-api-gateway-uri_re-write_2.jpg)

### Build-In Predicate Factories

- Built-in Route Predicate Factories in Spring Cloud API Gateway
- Below is a list of built-in predicates that you can use when creating routes in Spring Cloud API Gateways. These are built-in predicates and you do not need to write any additional Java code for them to work.

> Note: The description of the following built-in predicates is taken from the Spring Cloud API Gateway documentation page.

#### 1. The After Route Predicate Factory

The After route predicate factory takes one parameter, a datetime (which is a java ZonedDateTime). This predicate matches requests that happen after the specified datetime. The following example configures an after route predicate:

```properties
spring.cloud.gateway.routes[0].id = after_route
spring.cloud.gateway.routes[0].uri = https://example.org
spring.cloud.gateway.routes[0].predicates[0] = After=2017-01-20T17:42:47.789-07:00[America/Denver]
This route matches any request made after Jan 20, 2017 17:42 Mountain Time (Denver).
```

#### 2. The Before Route Predicate Factory

The Before route predicate factory takes one parameter, a datetime (which is a java ZonedDateTime). This predicate matches requests that happen before the specified datetime. The following example configures a before route predicate:
```properties
spring.cloud.gateway.routes[0].id = before_route
spring.cloud.gateway.routes[0].uri = https://example.org
spring.cloud.gateway.routes[0].predicates[0] = Before=2017-01-20T17:42:47.789-07:00[America/Denver]
This route matches any request made before Jan 20, 2017 17:42 Mountain Time (Denver).
```

#### 3. The Between Route Predicate Factory

The Between route predicate factory takes two parameters, datetime1 and datetime2 which are java ZonedDateTimeobjects. This predicate matches requests that happen after datetime1 and before datetime2. The datetime2 parameter must be after datetime1. The following example configures a between route predicate:
```properties
spring.cloud.gateway.routes[0].id = between_route
spring.cloud.gateway.routes[0].uri = https://example.org
spring.cloud.gateway.routes[0].predicates[0] = Between=2017-01-20T17:42:47.789-07:00[America/Denver], 2017-01-21T17:42:47.789-07:00[America/Denver]
This route matches any request made after Jan 20, 2017 17:42 Mountain Time (Denver) and before Jan 21, 2017 17:42 Mountain Time (Denver). This could be useful for maintenance windows.
```

#### 4. The Cookie Route Predicate Factory

The Cookie route predicate factory takes two parameters, the cookie name and a regexp (which is a Java regular expression). This predicate matches cookies that have the given name and whose values match the regular expression. The following example configures a cookie route predicate factory:

```properties
spring.cloud.gateway.routes[0].id = cookie_route
spring.cloud.gateway.routes[0].uri = https://example.org
spring.cloud.gateway.routes[0].predicates[0] = Cookie=chocolate, ch.p
This route matches requests that have a cookie named chocolate whose value matches the ch.p regular expression.
```

#### 5. The Header Route Predicate Factory

The Header route predicate factory takes two parameters, the header name and a regexp (which is a Java regular expression). This predicate matches with a header that has the given name whose value matches the regular expression. The following example configures a header route predicate:

```properties
spring.cloud.gateway.routes[0].id = header_route
spring.cloud.gateway.routes[0].uri = https://example.org
spring.cloud.gateway.routes[0].predicates[0] = Header=X-Request-Id, \d+
This route matches if the request has a header named X-Request-Id whose value matches the \d+ regular expression (that is, it has a value of one or more digits).
```

#### 6. The Host Route Predicate Factory

The Host route predicate factory takes one parameter: a list of host name patterns. The pattern is an Ant-style pattern with . as the separator. This predicates matches the Host header that matches the pattern. The following example configures a host route predicate:

```properties
spring.cloud.gateway.routes[0].id = host_route
spring.cloud.gateway.routes[0].uri = https://example.org
spring.cloud.gateway.routes[0].predicates[0] = Host=**.somehost.org,**.anotherhost.org
URI template variables (such as {sub}.myhost.org) are supported as well.

This route matches if the request has a Host header with a value of www.somehost.org or beta.somehost.org or www.anotherhost.org.

This predicate extracts the URI template variables (such as sub, defined in the preceding example) as a map of names and values and places it in the ServerWebExchange.getAttributes() with a key defined in ServerWebExchangeUtils.URI_TEMPLATE_VARIABLES_ATTRIBUTE. Those values are then available for use by GatewayFilterfactories
```
#### 7. The Method Route Predicate Factory

The Method Route Predicate Factory takes a methods argument which is one or more parameters: the HTTP methods to match. The following example configures a method route predicate:
```properties
spring.cloud.gateway.routes[0].id = method_route
spring.cloud.gateway.routes[0].uri = https://example.org
spring.cloud.gateway.routes[0].predicates[0] = Method=GET,POST
This route matches if the request method was a GET or a POST.
```
#### 8. The Path Route Predicate Factory

The Path Route Predicate Factory takes two parameters: a list of Spring PathMatcher patterns and an optional flag called matchOptionalTrailingSeparator. The following example configures a path route predicate:
```properties
spring.cloud.gateway.routes[0].id = path_route
spring.cloud.gateway.routes[0].uri = https://example.org
spring.cloud.gateway.routes[0].predicates[0] = Path=/red/{segment},/blue/{segment}
This route matches if the request path was, for example: /red/1 or /red/blue or /blue/green.

This predicate extracts the URI template variables (such as segment, defined in the preceding example) as a map of names and values and places it in the ServerWebExchange.getAttributes() with a key defined in ServerWebExchangeUtils.URI_TEMPLATE_VARIABLES_ATTRIBUTE. Those values are then available for use by GatewayFilterfactories
```

#### 9. The Query Route Predicate Factory

The Query route predicate factory takes two parameters: a required param and an optional regexp (which is a Java regular expression). The following example configures a query route predicate:

```properties
spring.cloud.gateway.routes[0].id = query_route
spring.cloud.gateway.routes[0].uri = https://example.org
spring.cloud.gateway.routes[0].predicates[0] = Query=green
The preceding route matches if the request contained a green query parameter.
```

#### 10. The RemoteAddr Route Predicate Factory

The RemoteAddr route predicate factory takes a list (min size 1) of sources, which are CIDR-notation (IPv4 or IPv6) strings, such as 192.168.0.1/16 (where 192.168.0.1 is an IP address and 16 is a subnet mask). The following example configures a RemoteAddr route predicate:

```properties
spring.cloud.gateway.routes[0].id = remoteaddress_route
spring.cloud.gateway.routes[0].uri = https://example.org
spring.cloud.gateway.routes[0].predicates[0] = RemoteAddr=192.168.1.1/24
This route matches if the remote address of the request was, for example, 192.168.1.10.
```

#### 11. The Weight Route Predicate Factory

The Weight route predicate factory takes two arguments: group and weight (an int). The weights are calculated per group. The following example configures a weight route predicate:
```properties
spring.cloud.gateway.routes[0].id = weight_high
spring.cloud.gateway.routes[0].uri = https://example.org
spring.cloud.gateway.routes[0].predicates[0] = Weight=group1, 8
 
spring.cloud.gateway.routes[1].id = weight_low
spring.cloud.gateway.routes[1].uri = https://example.org
spring.cloud.gateway.routes[1].predicates[0] = Weight=group1, 2
This route would forward ~80% of traffic to weighthigh.org and ~20% of traffic to weighlow.org
```

## Gateway Filters
A list of API Gateway filters is very long to be included in a single lecture. Below are direct links to gateway filters published in Spring Cloud API Gateway documentation.

### GatewayFilter Factories
https://cloud.spring.io/spring-cloud-gateway/reference/html/#gatewayfilter-factories

### Global Filters
https://cloud.spring.io/spring-cloud-gateway/reference/html/#global-filters

### HTTPHeadersFilters
https://cloud.spring.io/spring-cloud-gateway/reference/html/#httpheadersfilters