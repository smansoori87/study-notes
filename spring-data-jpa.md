# Spring Data
- Project from Spring to connect Database.

-![spring-data-pic](images/spring-data-jpa/spring-data-pic.JPG)

## Dependencies:
```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-jpa</artifactId>
   <!--Recommanded to use default version from Boot, Latest available is 2.4.1-->
</dependency>

// In case of H2:
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
</dependency>

//With Different DB like MariaDB it needs specific dependency.
<dependency>
   <groupId>org.mariadb.jdbc</groupId>
   <artifactId>mariadb-java-client</artifactId>
   <scope>runtime</scope>
</dependency>
```

## ConnectionPool/ DataSource
- Default DataSourceBuilder reads required properties like 
	- jdbcUrl, User, Pwd, PoolSize, Ideltimeout, MinPool, MaxPool from config file.
	- Also it can be assigned programatically while creating the DataSource.

```java
@Bean
@Primary
@ConfigurationProperties(prefix = "datasource")
public DataSource dataSource() {
    return DataSourceBuilder.create().build();
}
```

## Entity

```java

> @Entity
Class is an Entity.

> @Table
To Map Actual DB table with Entity Class, and can give name and multiple other paratameters in inout.

> @Id
Mark field in Entity as primary column.

> @Column
Define a field as DB column. Can give name inside @Column to map actual Table Column with field name.

> @Transient
We can use this annotation to mark a field in a model class as transient. Hence the data store engine won't read or write this field's value.



```

## Repository
```java
- Create @Repository and Extend JpaRepository<<EntityClass>, <PrimaryKeyType>>

- findOne(String id): Will return uniq record based on primary key
- findByName(String name):

- findByNameAndLastName(String name, String lastname)
- findByNameOrLastName(String name, String lastname)

- findByAliveTrue(), findByAliveFalse(): This is boolean check and no need to supply any parameter.

- findByNameNull(): No need any parameter
- findBtNameNotNull()

- findByAgeLessThan(int age)
- findByAgeLessThanEqual(int age)
- findByAgeGreaterThan(int age)
- findByAgeGreaterThanEqual(int age)

- findByNameLike(String name): In either case like queries, developer need to supply the wild cards alongg with data.
- findByNameNotLike(String name)
- findByNameContains(String name)

- findByCompanyIn(List<String> companies);
- findByCompanyNotIn(List<String> companies);

- findByFirstNameOrderByLastNameAsc(String firstName);
- findByFirstNameOrderByLastNameDesc(String firstName);

- findFirstByFirstName(String firstName);
- findTop10ByFirstName(String firstName);
- findDistinctByFirstName(String firstName);


 
``` 