# How to use this logging starter

This Spring Boot starter provides a standardized way to generate JSON logs that conform to our organization's logging standards.

## 1. Add the Dependency

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

## 2. Automatic Configuration

This section(2) is for information only and no changes should be done on the services where this jar is injected


This starter is designed to work with zero configuration. Once you add the dependency, it will automatically:

- **Configure Logback**: The included `logback-spring.xml` will be used to set up logging.
- **Enable JSON Logging**: All logs will be formatted as JSON using the `CustomJsonEncoder`.
- **Capture Request Headers**: The `MdcInterceptor` will automatically capture all the required headers from incoming HTTP requests and add them to the SLF4J Mapped Diagnostic Context (MDC). This makes them available to the logger.

## 3. How It Works

This section(3) is for information only and no changes should be done on the services where this jar is injected

The starter works by leveraging Spring Boot's auto-configuration capabilities. Here's a breakdown of the key components:

- **`pom.xml`**: Defines the dependencies for the starter, including `spring-boot-starter-web` and `logstash-logback-encoder`.
- **`CustomJsonEncoder.java`**: This is a custom Logback encoder that formats log messages as JSON, including all the mandatory fields specified in the logging standard.
- **`MdcInterceptor.java`**: This is a Spring `HandlerInterceptor` that intercepts all incoming requests. It extracts the required headers and other information and puts them into the MDC.
- **`WebConfig.java`**: This configuration class registers the `MdcInterceptor`.
- **`logback-spring.xml`**: This is the Logback configuration file that sets up the `CustomJsonEncoder` and defines the logging levels.

## 4. Using the Logger in Your Application
  ### 4.1 
      If application is implementing WebMvcConfigurer under config folder, check if there is any interceptor added or not something like this "addInterceptors(InterceptorRegistry registry)". If it is present add one more interceptor "addInterceptor(new MdcInterceptor()".
  ### 4.2
      If logback-spring.xml is present in application, override entire file with below code.
      ```
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

      If logback-spring.xml is not present in application, create entire file with below code
     ```
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

You can use the standard SLF4J logger in your application code. The logging starter will automatically handle the formatting and enrichment of the log messages.

Here is an example of how to use the logger in a Spring Boot service:

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;

@Service
public class MyService {

    private static final Logger logger = LoggerFactory.getLogger(MyService.class);

    public void doSomething() {
        logger.info("This is an informational message.");
        
        try {
            // some logic that might throw an exception
            int result = 1 / 0;
        } catch (Exception e) {
            logger.error("An error occurred", e);
        }
    }
}
```

When you run your application and call the `doSomething` method, the following will happen:

1. The `MdcInterceptor` will capture the request headers and populate the MDC.
2. The `logger.info` and `logger.error` calls will trigger the `CustomJsonEncoder`.
3. The encoder will create a JSON log message that includes:
    - All the fields from the MDC (request headers, etc.).
    - The log message itself.
    - The exception details (if any).
    - Other mandatory fields like timestamp, thread name, etc.

The resulting log output will be a stream of JSON objects, which can be easily parsed and analyzed by a log management system.
