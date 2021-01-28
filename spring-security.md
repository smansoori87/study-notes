# Spring-Boot-Security

##
- https://www.youtube.com/watch?v=rBNOc4ymd1E

![spring-security-arch](images/spring-security/spring-security-arch.JPG)

## Dependency:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
    <version>2.4.2</version>
</dependency>

<!-- Dependency to support JWT -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```

## Classes in use and flow:
- Enabling the web security and binding the User details object to fetch the user details.
 
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter{
	
	//Class must implement UserDetailsService
	//This class to fetch the user details based on user name from DB Repo.
	@Autowired
	CustomUserDetailService customUserDetailService;
	
	@Override
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		auth.userDetailsService(customUserDetailService);
	}

}



```

- CustomUserDetailsService extends the UserDetailsService.

```java
@Repository
public class CustomUserDetailService implements UserDetailsService {

//This one can be linked to any JPA repo to fetch the user details from DB. or LDAP or any other service directory.

	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		return new User("Suhail", "Suhail", new ArrayList<>());
	}
}
```

