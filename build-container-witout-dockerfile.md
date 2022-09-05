# Build Container Image without Manually writing a Dockerfile

1. edit pom.xml

```xml

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <excludes>
                    <exclude>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                    </exclude>
                </excludes>
                <!--add to plugin if already present-->
                <imageName>your-image-name</imageName>
                <!--prevents pulling of buildpack each time-->
                <pullPolicy>IF_NOT_PRESENT</pullPolicy>
                <layers>
                    <!--enable layers to make subsequent builds faster-->
                    <enabled>true</enabled>
                </layers>
            </configuration>
        </plugin>
    </plugins>
</build>
```

then

2. run

```shell
mvn -T 1C spring-boot:build-image -DskipTests
# skip test, (-T 1C) are optional
```

3. There is another approach to add **custom layering**
   useful when working with 2 types of dependencies
   e.g. external and internal
    - internal might change frequently and thus make the **dependency layer built again** and again which can be **slow**

4. Another way to do using **JIB**
   - Does not need docker installed
   - May sometimes produce sub-par imags as not specifically for spring apps