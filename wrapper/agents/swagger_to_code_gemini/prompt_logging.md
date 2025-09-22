# Prompt for Standardized JSON Logging Implementation

As a world-class software engineering coding assistant, your task is to apply standardized JSON logging configurations to the application. You must follow these rules precisely to ensure that all log output is structured correctly.

---

### 1. Add the Dependency

To use this starter, add it as a dependency to your microservice's `pom.xml`:

```xml
<dependency>
  <groupId>com.macys.logging</groupId>
  <artifactId>macys-logging-starter</artifactId>
  <version>1.0.0-SNAPSHOT</version>
</dependency>
```

If you are using a local JAR file, you can add it like this:

```xml
<dependency>
  <groupId>com.macys.logging</groupId>
  <artifactId>macys-logging-starter</artifactId>
  <version>1.0.0-SNAPSHOT</version>
  <scope>system</scope>
  <systemPath>${project.basedir}/lib/my-org-logging-starter-1.0.0-SNAPSHOT.jar</systemPath>
</dependency>
```


### 2. Configure the MDC Interceptor

You must ensure that the `MdcInterceptor` is registered with Spring's `InterceptorRegistry`.

1.  **Locate the Configuration Class**: Find any class within the `config` package that implements the `WebMvcConfigurer` interface.

2.  **Modify or Add the Interceptor**:
    *   If a `WebMvcConfigurer` class is found and it already has an `addInterceptors(InterceptorRegistry registry)` method, you **MUST** add the following line to it:
        ```java
        registry.addInterceptor(new MdcInterceptor());
        ```
    *   If the class exists but does not have the `addInterceptors` method, you **MUST** add the complete method with the new interceptor.

---

### 3. Configure Logback for JSON Output

You must configure Logback to use the custom JSON encoder.

1.  **Check for `logback-spring.xml`**: Look for the file at the path `src/main/resources/logback-spring.xml`.

2.  **Replace or Create the File**:
    *   If the file already exists, you **MUST completely replace** its contents with the XML provided below.
    *   If the file does **not** exist, you **MUST create** it at `src/main/resources/logback-spring.xml` and populate it with the XML content provided below.

#### `logback-spring.xml` Content:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <springProperty scope="context" name="appName" source="spring.application.name"/>

    <appender name="jsonConsoleAppender" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="com.macys.logging.CustomJsonEncoder">
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="jsonConsoleAppender" />
    </root>

</configuration>
```

---

After applying these changes, ensure that standard SLF4J loggers (`org.slf4j.Logger`) used anywhere in the application will automatically produce structured JSON logs containing all required fields from the MDC, without needing any further changes to the application code.