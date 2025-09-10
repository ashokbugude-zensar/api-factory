# Technical Specification: Carton API Wrapper

**Version:** 1.0

**Date:** September 9, 2025

**Author:** GitHub Copilot (API Architect)

---

## 1. System Architecture & Technology Stack

### 1.1. High-Level Architecture

The Carton API Wrapper service functions as a facade between modern RESTful clients and a legacy SOAP-based backend service. The request lifecycle is as follows:

`Client Application -> Apigee (API Gateway) -> Carton API Wrapper Service -> WEDIE010 (Legacy SOAP Service)`

1.  **Client Application:** Any internal service that needs carton details.
2.  **Apigee API Gateway:** Enforces cross-cutting concerns such as Rate Limiting (per-client), Authorization (RBAC), and potentially some authentication policies.
3.  **Carton API Wrapper Service:** The core component detailed in this document. It handles protocol translation, data transformation, authentication mediation, caching, and resilience.
4.  **WEDIE010 SOAP Service:** The legacy backend system of record for carton information.

### 1.2. Technology Stack

| Category | Technology | Version / Details | Purpose |
| :--- | :--- | :--- | :--- |
| **Language/Framework** | Java | 21 | Core programming language. |
| | Spring Boot | 3.x | Application framework for building the REST API. |
| **Resilience** | Resilience4j | | Circuit Breaker and Retry patterns. |
| **Build & CI/CD** | Maven | | Dependency management and build automation. |
| | Jenkins | | Continuous Integration server. |
| | SonarQube | | Static code analysis and quality checks. |
| **Deployment** | Docker | | Containerization of the application. |
| **API Management** | Apigee | | API Gateway for exposure, security, and rate limiting. |
| **Security** | Google Cloud Secret Manager | | Secure storage of application secrets (e.g., downstream credentials). |
| **Monitoring** | Logback | | Structured JSON logging. |
| | Micrometer | | Application metrics facade. |
| | Dynatrace | | Application Performance Monitoring (APM) and dashboarding. |
| **Testing** | JUnit 5 | | Unit testing framework. |
| | Spring Boot Test | | Integration testing. |
| | WireMock | | Mocking the downstream SOAP service for tests. |
| | JaCoCo | | Code coverage analysis. |

---

## 2. API Design & Endpoints

### 2.1. Endpoint Definition

The service exposes a single, read-only RESTful endpoint.

*   **HTTP Method:** `GET`
*   **Path:** `/v1/carton/{cartonId}`

**Path Parameters:**

| Name | Data Type | Description |
| :--- | :--- | :--- |
| `cartonId` | `String` | The unique identifier for the carton. |

**Required Request Headers:**

The client must provide the following headers, which are essential for processing by the downstream service.

| Header | Description |
| :--- | :--- |
| `location` | The location context for the request. |
| `colleagueId` | The ID of the colleague making the request. |
| `applicationId` | The ID of the consuming application. |
| `division` | The business division, used to dynamically route to the correct downstream URL. |
| `store` | The store context for the request. |
| `device_type` | The type of device making the request. |
| `device_id` | The unique ID of the device. |
| `traceId` | (Optional) A correlation ID for distributed tracing. If not provided, one should be generated. |

### 2.2. Response Data Transfer Object (DTO)

The successful response will be a JSON object with the following structure.

**DTO Name:** `CartonDetailResponse`

```java
// In package: com.macys.carton.dto
public class CartonDetailResponse {
    private Integer returnCode;
    private String returnMessage;
    private String cartonNumber;
    private Long nafship;
    private String bolstatus;
    private String bol;
    // Getters and Setters
}
```

**Field Definitions:**

| Field | Data Type | Description |
| :--- | :--- | :--- |
| `returnCode` | Integer | Status code from the downstream operation (0 for success). |
| `returnMessage` | String | A message describing the result. |
| `cartonNumber` | String | The carton identifier. |
| `nafship` | Long | The NAF ship number. |
| `bolstatus` | String | The Bill of Lading status. |
| `bol` | String | The Bill of Lading number. |

### 2.3. Standardized Error Response

All error responses will conform to a standard JSON format to ensure consistency for clients.

```json
{
  "timestamp": "2025-09-09T12:00:00.000+00:00",
  "status": 400,
  "error": "Bad Request",
  "message": "Validation failed for parameter 'cartonId': must not be null or empty."
}
```

---

## 3. Authentication and Security

### 3.1. End-to-End Authentication Flow

The wrapper service mediates authentication between the API consumer and the downstream SOAP service.

1.  The client authenticates to the Apigee gateway (details out of scope for this document).
2.  The API Wrapper service receives the request from the trusted gateway.
3.  For each call to the downstream WEDIE010 service, the wrapper retrieves the required `X-Client-Id` and `X-Client-Secret` from Google Cloud Secret Manager.
4.  The wrapper constructs the SOAP request and injects these credentials as custom HTTP headers (`X-Client-Id`, `X-Client-Secret`, `X-Message-Id`).
5.  The SOAP service authenticates the request using these custom headers.

### 3.2. Credential Management

*   Credentials for the downstream service (`X-Client-Id`, `X-Client-Secret`) will be stored in **Google Cloud Secret Manager**.
*   The Spring Boot application will integrate with Secret Manager using the `spring-cloud-gcp-starter` dependency. Secrets will be loaded into the Spring `Environment` during application startup.

**Configuration (`application.yml`):**

```yaml
spring:
  cloud:
    gcp:
      secretmanager:
        project-id: <your-gcp-project-id>
        # Secrets will be accessible via property names like ${sm://secret-name}
```

### 3.3. Spring Security Configuration

A `SecurityFilterChain` will be configured to secure the application. While primary authentication is at the gateway, Spring Security will be used for defense-in-depth and to establish a security context.

```java
// In package: com.macys.carton.config
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable()) // Disable CSRF as this is a stateless service API
            .authorizeHttpRequests(authz -> authz
                .requestMatchers("/v1/carton/**").permitAll() // Example: permit all, assuming auth is at gateway
                .anyRequest().authenticated()
            );
        return http.build();
    }
}
```

### 3.4. Input Validation and Sanitization

*   **Validation:** JSR-380 (Bean Validation) will be used. Annotations (`@NotNull`, `@Size`, `@Pattern`) will be applied to request DTOs and controller method parameters. A `@Validated` annotation on the controller class will enable validation.
*   **Sanitization:** To prevent Cross-Site Scripting (XSS), any string inputs that might be logged or reflected in error messages will be sanitized. The OWASP Java HTML Sanitizer library is recommended for this purpose.

---

## 4. Data Transformation and Processing

### 4.1. Request Transformation (REST to SOAP)

The service layer will be responsible for creating the SOAP request.

1.  A `WebServiceTemplate` (from `spring-boot-starter-web-services`) will be configured as the SOAP client.
2.  The service will receive the `cartonId` and other header values from the controller.
3.  It will dynamically construct the SOAP XML request body based on the WSDL definition of the WEDIE010 service. Key-value pairs from the incoming REST request will be mapped to the appropriate elements in the SOAP envelope's body.
4.  The custom authentication headers will be added via a `WebServiceMessageCallback`.

### 4.2. Response Transformation (SOAP to DTO)

**MapStruct** will be used for high-performance, type-safe mapping from the parsed SOAP response object to the `CartonDetailResponse` DTO.

```java
// In package: com.macys.carton.mapper
@Mapper(componentModel = "spring")
public interface CartonMapper {
    CartonDetailResponse toCartonDetailResponse(Wedie010SoapResponse source);
    // The Wedie010SoapResponse is the JAXB-generated class from the WSDL
}
```

The service layer will invoke this mapper to transform the data before returning it to the controller.

---

## 5. Error Handling and Resilience

### 5.1. Global Exception Handling

A centralized exception handler will be implemented using `@ControllerAdvice`.

```java
// In package: com.macys.carton.exception
@ControllerAdvice
public class GlobalExceptionHandler extends ResponseEntityExceptionHandler {

    @ExceptionHandler(UpstreamServiceException.class)
    public ResponseEntity<Object> handleUpstreamServiceException(UpstreamServiceException ex, WebRequest request) {
        // Create and return standard error response DTO
    }

    @ExceptionHandler(ConstraintViolationException.class)
    public ResponseEntity<Object> handleConstraintViolation(ConstraintViolationException ex, WebRequest request) {
        // Create and return standard error response for validation failures
    }
    // ... other handlers
}
```

### 5.2. Resilience Patterns (Resilience4j)

Resilience will be implemented in the client class that communicates with the downstream SOAP service.

*   **Circuit Breaker:** The `@CircuitBreaker` annotation will be applied to the method making the SOAP call. It will be configured to open after a certain number of failures, preventing further calls to the failing service.
*   **Retry:** The `@Retry` annotation will be used on the same method to automatically retry failed calls due to transient issues (e.g., network glitches). It will be configured with an exponential backoff strategy.

**Configuration (`application.yml`):**

```yaml
resilience4j:
  circuitbreaker:
    instances:
      wedie010-service:
        failure-rate-threshold: 50
        sliding-window-size: 10
        wait-duration-in-open-state: 10s
  retry:
    instances:
      wedie010-service:
        max-attempts: 3
        wait-duration: 500ms
        retry-exceptions:
          - java.io.IOException
          - java.util.concurrent.TimeoutException
```

---

## 6. Caching Strategy

*   **Implementation:** Caching will be implemented using Spring's cache abstraction with `@Cacheable`.
*   **Annotation:** The `@Cacheable` annotation will be applied to the service method that retrieves carton details.
*   **Cache Name:** A cache named `cartons` will be used.
*   **Cache Key:** The `cartonId` will be used as the cache key.
*   **Eviction Policy:** A Time-to-Live (TTL) eviction policy will be configured (e.g., 15 minutes) to ensure data freshness. The specific cache provider (e.g., Caffeine, Redis) will be determined during implementation.

```java
// In package: com.macys.carton.service
@Service
public class CartonService {
    @Cacheable(value = "cartons", key = "#cartonId")
    public CartonDetailResponse getCartonDetails(String cartonId, Map<String, String> headers) {
        // ... logic to call downstream service
    }
}
```

---

## 7. Logging, Monitoring, and Alerting

### 7.1. Structured Logging

*   **Framework:** Logback, configured via `logback-spring.xml`.
*   **Format:** Logs will be written in **JSON** format using `LogstashEncoder` to facilitate parsing by log aggregation tools.
*   **Mandatory Fields:** Every log entry must include:
    *   `timestamp`
    *   `log_level`
    *   `thread_name`
    *   `traceId` (Correlation ID, propagated via a Spring `Filter` and Mapped Diagnostic Context - MDC).
    *   `message`

### 7.2. Metrics and Monitoring

**Micrometer** will be used to expose metrics to be scraped by **Dynatrace**. Key metrics to monitor include:
*   **API:** `http.server.requests` (response time, throughput, error rate per endpoint).
*   **JVM:** CPU and Memory utilization.
*   **Resilience4j:** `resilience4j.circuitbreaker.state`, `resilience4j.retry.calls`.
*   **Cache:** `cache.gets`, `cache.puts` (hit/miss ratio).

### 7.3. Alerting

Alerts will be configured in Dynatrace to trigger on the following conditions:
*   **High Error Rate:** API error rate > 5% over a 5-minute window.
*   **High Response Time:** 95th percentile response time > 1s.
*   **High Resource Utilization:** CPU or Memory utilization > 80%.
*   **Circuit Breaker Open:** The `wedie010-service` circuit breaker is stuck in the `OPEN` state.

---

## 8. Testing Strategy

### 8.1. Unit & Integration Tests

*   **Unit Tests (JUnit 5):** Focus on individual classes (mappers, service logic, exception handlers) in isolation. Mocks will be created using frameworks like Mockito.
*   **Integration Tests (`@SpringBootTest`):** Test the interaction between components, from the controller down to the service layer.
*   **Mocking Downstream Service:** **WireMock** will be used to run a mock SOAP service on a local port during integration tests. This allows for simulating various downstream responses, including success, SOAP faults, and network timeouts.

### 8.2. Code Coverage

*   **Tool:** **JaCoCo** will be integrated into the Maven build lifecycle.
*   **Target:** The build will be configured to enforce a minimum of **80%** code coverage.

---

## 9. CI/CD and Deployment

### 9.1. CI/CD Pipeline

A **Jenkins** pipeline will automate the build, test, and deployment process:
1.  **Checkout:** Pull source code from Git.
2.  **Build:** Compile code and run unit/integration tests using `mvn clean install`.
3.  **Analysis:** Run SonarQube analysis to check for code quality and vulnerabilities.
4.  **Package:** Build the Docker image containing the application JAR.
5.  **Deploy:** Push the Docker image to a container registry and deploy it to the target environment (e.g., Kubernetes) using a **Rolling Update** strategy.

### 9.2. Containerization

The application will be packaged as a **Docker** image. A `Dockerfile` will be created to define the image, using a base image with Java 21 and copying the application JAR.

