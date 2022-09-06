# Actuator config for dev env and dev k8s deployments

1. edit pom.xml

```xml

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

2. config for application.yaml/properties

```yaml
# Actuator Config
management:
  endpoint:
    # this is useful for k8s     
    health:
      probes:
        enabled: true
    # this is turing loggers on/off dynamically     
    loggers:
      enabled: true
  # this is useful for k8s     
  health:
    livenessstate:
      enabled: true
    readinessstate:
      enabled: true
  # expose health for k8s is needed
  # loggers mostly only till the dev env
  endpoints:
    web:
      exposure:
        include: loggers,health
```

3. The k8s config
[More info](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#actuator.endpoints.kubernetes-probes)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace:
  name:
  labels:
    app:
spec:
  replicas: 1
  selector:
    matchLabels:
      app: CONTAINER_NAME
  template:
    metadata:
      namespace:
      labels:
        app: CONTAINER_NAME
    spec:
      containers:
        - name: CONTAINER_NAME
          image: IMAGE NAME
          imagePullPolicy: Always
          env:
            - name: POD_IP
              valueFrom:
                fieldRef:
                  fieldPath: status.podIP
            - name: PROPERTY_NAME
              value: VALUE
          # Important    
          # check for initial readiness, restarts if status not 200
          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: 8080
            initialDelaySeconds: 20
            periodSeconds: 10
          # check for initial readiness, restarts if status not 200
          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: 8080
            initialDelaySeconds: 20
            periodSeconds: 10
          resources:
            requests:
              memory:
              cpu:
            limits:
              memory:
              cpu:
          ports:
            - containerPort: 
```

4. The probes are special as in they only come live when inside a k8s cluster,
   which is done by some spring `magic`
