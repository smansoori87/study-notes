# Spring Boot Redis
- redis can be used as a DB, Cache and MQ.
- in below example we are seeing how to use Redis as cache

- Below is the anotaion to define entity object in case of Redis using as a DB storage.
```java
@RedisHash("Entity Name which will be used as a table object in Redis").
```
## How to use Redis inside Spring Boot Project?

### Dependencies
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
- Jedis is the Redis implementation for Java
- spring-boot-starter-data-redis
	- Internally parent used to manage "spring-data-redis" and "jedis" as dependency as part of parent project.

- Redis Template setup
```java

@Bean
public LettuceConnectionFactory factory(){
	RedisStandaloneConfiguration config = new RedisStandaloneConfiguration();
	config.setHost();
	config.setPort();
	config.setPassword(Password.of());
	return new LettuceConnectionFactory(config);
}

@Bean
public RedisTemplate redisTemplate(LettuceConnectionFactory redisConnectionFactory) {
  	RedisTemplate template = new RedisTemplate();

//All Serializers are in place to store data in readable format in Redis.
    template.setKeySerializer(new PrefixRedisSerializer());
    template.setValueSerializer(new StringRedisSerializer());
    template.setConnectionFactory(redisConnectionFactory);
    template.setHashKeySerializer(new StringRedisSerializer());
    template.setHashValueSerializer(new StringRedisSerializer());
    template.afterPropertiesSet();
    return template;
}
``` 
- Use case:
- Use @EnableCaching at Spring Applicaiton, along with @SpringBootApplcaiton
- Use below annotations to cache, update or delete the data.
- Insert data in cache while returning the result. where Users is the Group or Cache Table. and userId is Key to access User Object.
```java
@Cacheable(value = "users", key = "#userId", unless = "#result.followers < 12000")
```
- To Update cached data entry. 
```java
@CachePut(value = "users", key = "#user.id")
```
- To Delete Complete Group or respective entry.
```java
@CacheEvict(value = "users", allEntries=true)
```
- Imp Links:
	- https://www.baeldung.com/spring-boot-evict-cache
	- https://www.journaldev.com/18141/spring-boot-redis-cache
