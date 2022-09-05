# To Reduce use of @Autowired

1. PreReq:

```xml

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency> 
```

2. In your class

```java
// Use @RequiredArgsConstructor
@RequiredArgsConstructor
public class YourComponent implements IComponent {
	private final @NonNull RestTemplate restTemplate;
	/* // The Generated code
	public YourComponent(@NonNull RestTemplate restTemplate) {
		this.restTemplate = restTemplate;
	}
	*///
}
```

3. If using with @Qualifier

- create lombok.config in `src`
- add `lombok.copyableAnnotations += org.springframework.beans.factory.annotation.Qualifier` to the file
- then inside code-base

```java
// Use @RequiredArgsConstructor
@RequiredArgsConstructor
public class YourComponent implements IComponent {
	private final @NonNull
	@Qualifier("name") RestTemplate restTemplate;
	
	/* // The Generated code
	public YourComponent(@Qualifier("name") @NonNull RestTemplate restTemplate) {
		this.restTemplate = restTemplate;
	}
	*///
}
```