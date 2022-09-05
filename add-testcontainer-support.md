# To add test container support to your app

1. Edit pom.xml

```xml
<!--add to pom.xml-->
<dependencyManagement>
    <dependencies>
        <dependency>
            <artifactId>testcontainers-bom</artifactId>
            <groupId>org.testcontainers</groupId>
            <scope>import</scope>
            <type>pom</type>
            <version>${testcontainers.version}</version>
            <!--<version>1.17.3</version>-->
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>{your-module}</artifactId>
        <artifactId>oracle-xe</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>junit-jupiter</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>


```

2. Your Test

```java

// using a test slice is a good idea
@DataJdbcTest
// this is important
@AutoConfigureTestDatabase(replace = Replace.NONE)
// disabledWithoutDocker is also "good IMO", so that when docker is not running, your tests do not crash
@Testcontainers(disabledWithoutDocker = true)
class TheTestClass {

	@Container
	public static final
	// the container type, here we have oracle as example
	// can be changed to anything that is supported
	OracleContainer CONTAINER =
			new OracleContainer(
					// the version you want to use
					"gvenzl/oracle-xe:18.4.0-slim"
			);

	@DynamicPropertySource
	static void setContainer(DynamicPropertyRegistry registry) {
		registry.add("spring.datasource.url", CONTAINER::getJdbcUrl);
		registry.add("spring.datasource.username", CONTAINER::getUsername);
		registry.add("spring.datasource.password", CONTAINER::getPassword);
		// this is important as Testcontainers will config it's own driver
		registry.add("spring.datasource.driver-class-name", CONTAINER::getDriverClassName);
	}
}

```

3. If you have TC cloud running, you won't use your resources
   But it does need Docker to be running still I think