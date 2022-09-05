# To add @ConfigutationProperties to your app

1. Edit pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
<!--VALIDATION IS NOT NECESSARY BUT PREFERRED-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

2. New class to your application

```java
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;

@Validated
@Getter
@Setter
@ToString
// necessary I think for spring to pick it up and scan it
// maybe some support was added in some version
// but intellij was complaining so added this too
@Configuration
@ConfigurationProperties(prefix = "prefix")
public class NameYourConfig {

	@NotBlank
	@ToString.Exclude
	private String password;

	// the class names do not matter  
	// the variable's name matters as they are what are used
	// to pick the properties
	@NotNull
	private AnotherNestedObject nested;

	@Getter
	@Setter
	@ToString
	public static class AnotherNestedObject {
		// spring binds
		// some-property
		// someProperty
		// some other variations too to the same variable
		@NotBlank
		private String someProperty;
	}
}

```
3. Example Config
```properties
# as properties
prefix.password=terces
prefix.nested.someProperty=value
prefix.nested.some-property=value
# both are same
```
```yaml
# as yaml properties
prefix:
  password: terces
  nested:
    someProperty: value
    some-property: value
    # both are same
```