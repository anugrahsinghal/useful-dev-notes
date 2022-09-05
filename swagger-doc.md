# Add minimal swagger doc to your app

1. To pom.xml

```xml

<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-ui</artifactId>
    <version>1.6.11</version>
    <!--<version>${openapi.version}</version>-->
</dependency>
```

2. To application.yaml/properties

```yaml
springdoc:
  packagesToScan: com.other.packages.<controller-package>
```

# Todo

Add some docs about common documentation annotations