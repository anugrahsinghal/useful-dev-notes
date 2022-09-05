# Spring DataSource Proxy for easier viewing of queries

1. Edit pom.xml

```xml

<dependency>
    <groupId>com.github.gavlyukovskiy</groupId>
    <artifactId>datasource-proxy-spring-boot-starter</artifactId>
    <version>1.8.0</version>
    <!--<version>${datasource-proxy.version}</version>-->
</dependency> 
```

2. To your application.yaml/properties

```yaml
logging:
  level:
    net:
      ttddyy:
        dsproxy:
          listener: debug
# OR
#logging.level.net.ttddyy.dsproxy.listener=debug
```