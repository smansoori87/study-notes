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
- https://thorben-janssen.com/key-jpa-hibernate-annotations/
- https://www.baeldung.com/jpa-entities
- JPA Relationships 
	- https://www.tutorialspoint.com/jpa/jpa_entity_relationships.htm
	- https://dzone.com/articles/introduction-to-spring-data-jpa-part-4-bidirection
	- @JoinColumn(): used to define the foreign key from One to Many Relationship in other table.
	- Many to One(Multiple Employees can have same Department)
	- One to Many(One Department can have List of Employees)
		```java
		//In below example Role table is creating foreign key in User table with column name role_id.
		@OneToMany(targetEntity = User.class, cascade = CascadeType.ALL)
		@JoinColumn(name = "role_id")
		private List<User> users;
		```
	- One to One(Only one Employee is present in each department)
	- Many to Many(Multiple Teachers can be in one class, and One Teacher can be in multiple class)
	
- Transactions
	- If using, spring-boot-starter-data-jpa then spring-tx by default will be available in class path.
	- Use @EnableTransactionManagement in main class.
	- Use @Transacctional at Method or at Bean level.
	- There are multiple properties like rollbackFor, noRollbackFor, Isolation, Propagation, readOnly available.
	- PROPAGATION: https://www.youtube.com/watch?v=zVRO-LELoSw
		- REQUIRED: Outer and Inner Transaction will run in same physical txn. if Inner fail, outer will also fail and rollbacked.
		- REQUIRED_NEW: Outer and Inner will be independent and run in 2 different physical txn. Inner fail will not affect the outer one.
		- SUPPORT: If Outer method in transaction, inner will support in same txn. If Outer not in txn, Inner will also run without txn.
		- NOT_SUPPORTED: In case if outer in txn or not in txn, inner will not be part of any txn.
		- NEVER: Never run in txn. even if outer in txn,  inner will throw exceptoin.
		- MANDATORY: Inner always needs to call from txn. if Outer not in txn, inner will throw an exception.
	- ISOLATION: https://www.javainuse.com/spring/boot-transaction-isolation
		- SERIALIZABLE: At a time only one txn will take in place.
		- REPEATABLE_READ: 2 txn can run in parallel, where first txn will lock the table and second can't modify same records. but can insert new records.
		- READ_COMMITTED:  before the first transaction is committed the existing records can be changed as well as new records can be changed by second transaction.
		- READ_UNCOMMITTED:  before the first transaction is committed the existing records can be changed as well as new records can be changed by second transaction. Even if the second transaction is not committed the newly added and also updated records get reflected in first transaction which is still not committed.
		- DIRTY_READS: If A and B in txn. A did some changes and before commit B read through records. later A rollbacked the changes. so overall it show wrong value with B.
		- NON_REPEATABLE_READ: A read throw in txn, and B wrote before A commit the changes. So A will be different in case of 2 read.
		- Phantom: 	A and B in txn. A read through few records, B wrote new records bf A commit. A further reads then there is gap. 

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