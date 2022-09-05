# Reduce Maven Test Logging

1. create file in src/test/resources/ as `logback-test.xml`(assuming still using logback in spring)
```xml
<configuration>

    <statusListener class="ch.qos.logback.core.status.NopStatusListener" />

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <Pattern>
                %d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n
            </Pattern>
        </layout>
    </appender>

    <root level="error">
        <appender-ref ref="STDOUT"/>
    </root>

</configuration>
```


2. Other options
```shell
mvn -q test
mvn test | grep 'INFO'
mvn test -Dorg.slf4j.simpleLogger.defaultLogLevel=WARN
```