# Junit-Jupiter 5 testing with Spring Boot

## PART 1, Unit Testing with JUnit 5 and Mockito
- https://www.youtube.com/watch?v=--nQfs67zCM

### Dependencies
```xml
Junit-Jupiter, Mockito-All, AsertCore, Mockito-junit-jupiter
```
- @Mockito
- @Test
- @BeforeEach
- Mockito.when().thenReturn()
- AssertThat().isFalse()
- AssertThrownBy().instanceOf()
- 

## PART 2, Testing Data Access Layer using TestContainers
- https://www.youtube.com/watch?v=9UmwBfZ98U8

### Annotations:
- @DataJPATest: From Spring test framework.
- @ActiveProfile("test"): To load data from test props.
- assertThat(Object).usingResursiveComparision().ignoringField("userid").isEqualTo(Object2)
- @sql: to execute sql file before test execution to supply data in table.

```
//should avoid as test container will start with respect to the each test class execution.
- @TestContainer: at test class level.
- @Container: it is basically to run the docker container to support with TestContainers like mySql and all.
}
```

- To avoid the above issue there should be used SingeltonContainer pattern and can extend the class to each test class. avoiding above 2 annotation and creating a class with static start.



## PART 3, Testing REST APIs using MockMvc and Mockito
- https://www.youtube.com/watch?v=vcRFkp8jHJ8

### Dependencies

### Annotations:
- @WebMvcTest(controller class )
- @MockBean: Spring annotation to mock the entire bean dependencies.
- MockMvc: from Spring to provide mock container.
- MockMvcRequestBuilder: to prepare the request object and specify the URI
- Can use list of objects as well to evalute multiple test cases in one go.
