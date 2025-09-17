# Business and Technical Specification: Carton Status API

This document outlines the business and technical specifications for a RESTful API that wraps a legacy SOAP service (XEDIE010) to retrieve carton status based on UCCID for direct-to-store operations.  The primary goal is to simplify API access for modern applications while maintaining backward compatibility with the legacy system.

## 1. Business Requirements

The core business objective is to accelerate direct-to-store operations by providing quick and reliable access to carton status information. Currently, accessing this information through the legacy XEDIE010 SOAP service is complex and requires significant integration effort for modern applications.  The new RESTful wrapper API addresses these pain points by:

* **Simplifying Integration:** Providing a modern RESTful interface that is easier to consume by modern applications (web, mobile, and backend services).
* **Improving Developer Experience:** Reducing integration time and effort for developers using clear and concise documentation.
* **Enhancing Performance:** Implementing caching, connection pooling, and other optimizations to improve response times and throughput.
* **Increasing Reliability:** Utilizing fault tolerance patterns (retries, circuit breakers, fallbacks) to ensure high availability and resilience.
* **Improving Security:** Implementing robust authentication and authorization mechanisms, input validation, and secure communication protocols (mTLS).
* **Enabling Observability:** Providing comprehensive logging, metrics, and tracing to monitor the API's health and performance and ensure efficient troubleshooting.

The success of this initiative will be measured by:

* **Faster Time-to-Market:** Reduced development time for applications integrating with carton status information.
* **Improved Order Processing Efficiency:** Streamlined order processing workflows due to easier and faster API access.
* **Reduced Support Costs:** Fewer support tickets related to API integration complexities.
* **Increased Developer Satisfaction:** Improved ease of use and integration process.


## 2. Technologies and Versions

| Technology          | Version      | Notes                                                                 |
|----------------------|---------------|----------------------------------------------------------------------|
| Java                 | 17            | Core programming language                                             |
| Spring Boot          | 2.7.0         | Application framework                                                |
| Spring Cloud GCP      | 3.4.2         | For Google Cloud Platform integration (Logging, Secret Manager, Trace) |
| Spring MVC            |               | For REST controller implementation                                  |
| Lombok               | 1.18.20       | For code reduction (DTO builders, etc.)                             |
| MapStruct            |               | For DTO mappings                                                     |
| JUnit 5              |               | For unit testing                                                    |
| Mockito              |               | For mocking                                                        |
| Maven                |               | For build automation                                                |
| Docker               |               | For containerization                                                 |
| Kubernetes           |               | For deployment                                                      |
| GitLab CI/CD         |               | For continuous integration and deployment                            |
| Google Cloud Logging  |               | For centralized logging                                             |
| Google Cloud Secret Manager |               | For secure secret management                                         |
| Google Cloud Trace     |               | For distributed tracing                                              |
| Resilience4j         |               | For circuit breakers and retries                                    |
| WireMock             | 2.27.2        | For mocking external services during testing                         |
| REST Assured         | 4.5.0         | For REST API testing                                                 |
| JaCoCo               | 0.8.8         | For code coverage                                                   |
| Swagger/OpenAPI      | 3             | For API documentation                                                |
| Logback              | 2.11.0        | For logging                                                        |
| JMX Exporter         |               | For Prometheus metrics                                               |


## 3. Project Details

* **Project Name:** iwm-items-list-eapi
* **Base Package:** `com.macys.iwm`
* **Base Package Structure:**  A layer-based structure is recommended:
    * `com.macys.iwm.controller` - REST controllers
    * `com.macys.iwm.service` - Business logic and service layer
    * `com.macys.iwm.gateway` - External service integration
    * `com.macys.iwm.model` - Data models and DTOs
    * `com.macys.iwm.config` - Configuration classes
    * `com.macys.iwm.exception` - Custom exceptions
    * `com.macys.iwm.utils` - Utility classes
    * `com.macys.iwm.dto` - Data Transfer Objects
* **Architectural Style:**  Microservice architecture, layered architecture within the microservice.
* **Versioning Folders:**  Version-specific packages within the `controller` and `model` layers (e.g., `com.macys.iwm.controller.v1`, `com.macys.iwm.model.v1`).
* **k8s-manifests Directory:** This directory will contain:
    * `iwm-items-list-eapi-deployment.yaml`
    * `iwm-items-list-eapi-service.yaml`
    * `iwm-items-list-eapi-ingress.yaml`
    * `iwm-items-list-eapi-env.yaml` (for environment-specific variables)
    * `iwm-items-list-eapi-hpa.yaml` (for Horizontal Pod Autoscaler)
    * `iwm-items-list-eapi-configmap.yaml` (for non-sensitive configurations)
    * `iwm-items-list-eapi-secret.yaml` (for sensitive configurations)


## 4. Controllers

* **Controller File:** `com.macys.iwm.controller.v1.DirectToStoreController.java`
* **Base Path:** `/v1/directToStore`
* **Endpoint:** `/carton/{UCCID}`
* **HTTP Method:** GET
* **Request Parameters:**
    * Path Parameter: `{UCCID}` (String, required, no validation beyond length constraints from WSDL)
* **Request Headers:**
    * `X-Client-Id` (String, required)
    * `X-Client-Secret` (String, required)
    * `X-Message-Id` (String, required)
    * `X-Location-Number` (Integer, required, `@Min(0) @Max(99999999)`)
    * `X-Colleague-Id` (String, required)
    * `X-Application-Id` (String, required)
    * `X-Division` (Integer, required, `@Min(0) @Max(99999999)`)
    * `X-Store` (Integer, required, `@Min(0) @Max(99999999)`)
    * `X-Device-Id` (String, required)
    * `X-Device-Type` (String, required)
    * `X-Trace-Id` (String, optional)
* **Request Body:** None
* **Response DTO:** `com.macys.iwm.model.v1.CartonDetailResponse.java`
* **Response Payload:**  (See sample JSON below)
* **Error Handling:**  Uses `GeneralResponse` wrapper with appropriate HTTP status codes (400, 404, 500, 429).  See detailed error handling section below.
* **Input Validation:**  Validation annotations (`@NotNull`, `@Size`, custom validators as needed) on DTOs.  Header validation is performed using custom validation annotations and a `@Validated` controller.

{
  "returnCode": 0,
  "returnMessage": "success message",
  "cartonNumber": "00607809940059948981",
  "nafship": 4184778,
  "bolstatus": "",
  "bol": "BL004950661110120002"
}

## 5. Models

* **Model File:** `com.macys.iwm.model.v1.CartonDetailResponse.java`
* **Fields:** `returnCode`, `returnMessage`, `cartonNumber`, `nafship`, `bolstatus`, `bol` (Types and annotations defined in section 7).


## 6. Services

* **Service File:** `com.macys.iwm.service.v1.DirectToStoreService.java`
* **Responsibilities:**
    * Handles the core business logic for retrieving carton status.
    * Calls the `WEDIE010Gateway`.
    * Maps the SOAP response to the DTO.
    * Implements retry logic using Resilience4j.
    * Implements circuit breaker using Resilience4j.
    * Handles exceptions and logs errors.

* **Dependencies:** `WEDIE010Gateway`, `CartonMapper`, Resilience4j.


## 7. DTOs

* **DTO File:** `com.macys.iwm.dto.v1.CartonDetailResponse.java`

```java
package com.macys.iwm.dto.v1;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class CartonDetailResponse {
    private int returnCode;
    private String returnMessage;
    private String cartonNumber;
    private Long nafship;
    private String bolstatus;
    private String bol;
}
```

## 8. Utils

* **Utility Class:** `com.macys.iwm.utils.JsonUtils.java`
* **Responsibilities:**  Provides methods for JSON serialization and deserialization, potentially using Jackson.  Includes `readFile`, `readFileAsString`, and `marshal` methods.
* **Utility Class:** `com.macys.iwm.utils.CustomRequestHeaders.java`  -  holds the constants for Custom Headers.

## 9. Configs

* **Configuration File:** `com.macys.iwm.config.AppConfig.java`
* **Responsibilities:**
    * Loads application properties using `@ConfigurationProperties`.
    * Includes properties for database connection, API Gateway URL,  API keys, and other configurations.
    * Integrates with Google Cloud Secret Manager for secure secret management.
* **Example properties:**
  ```properties
  app.wedie010.apigeeUrl.123=https://my-server:12345/provider/edi/wedie010
  app.wedie010.apigeeUrl.456=https://my-other-server:67890/provider/edi/wedie010
  app.database.url=${spring.datasource.url}
  app.database.username=${spring.datasource.username}
  app.database.password=${spring.datasource.password}
  app.apigee.apiKey=${sm://hss_apigee_key}
  ```

## 10. Security

* **Authentication Strategy:** Custom header-based authentication using `X-Client-Id` and `X-Client-Secret`.  mTLS is used as an additional security layer with the downstream service.
* **Authorization Strategy:** Role-based access control (RBAC) implemented through custom annotations and validation.
* **Secret Management:** Google Cloud Secret Manager.  The `AppConfig` class integrates with Secret Manager to fetch credentials securely.
* **Implementation Steps:**
    1. Create a custom annotation for validating client credentials (`@ValidClient`).
    2. Implement a validator for this annotation (`ValidClientValidator`).
    3. Annotate the controller methods with `@Validated` and `@ValidClient`.
    4. Configure Google Cloud Secret Manager in the application.properties file.
    5. Create config classes (in `com.macys.iwm.config`) to manage  credentials from GCP Secret Manager.


## 11. Logging

* **Logging Framework:** SLF4J with Logback.
* **Log Format:** JSON
* **Log Fields:** Timestamp, Log Level, Thread Name, Correlation ID (X-Trace-Id), Class Name, Method Name, Line Number,  Message.
* **Implementation Details:**
    1. Add `logback-classic` and `logback-json-classic` dependencies.
    2. Configure Logback to use the JSON encoder.
    3. Use `@Slf4j` annotation in service and controller classes.
    4. Include logging statements in relevant methods.
    5. Implement MDC (Mapped Diagnostic Context) using SLF4j to propagate `traceId`.

## 12. Observability

* **Monitoring Tools:** Google Cloud Monitoring (Stackdriver), Dynatrace.
* **Implementation Details:**
    1. Configure Spring Boot Actuator for health checks, metrics, and other monitoring endpoints.
    2. Expose `/actuator/health`, `/actuator/live`, `/actuator/ready`, and `/actuator/prometheus` endpoints.
    3. Implement custom health indicators for database, cache, and external service health.
    4. Configure JMX Exporter for Prometheus metrics.
    5. Integrate with Google Cloud Monitoring (Stackdriver) and Dynatrace.

## 13. Metrics

* **Metrics Library:** Micrometer with Prometheus integration.
* **Metrics:** Response time (average, 95th, 99th percentile), throughput (requests per second), error rate, retry count, circuit breaker state, CPU usage, memory usage.
* **Implementation Steps:**
    1. Add `micrometer-registry-prometheus` dependency.
    2. Configure `MeterRegistry` bean.
    3. Add metrics instrumentation to relevant parts of the code using Micrometer annotations.
    4. Expose metrics via the Prometheus endpoint `/actuator/prometheus`.

## 14. Tracing

* **Tracing Library:** Google Cloud Trace (via Spring Cloud GCP).
* **Implementation Steps:**
    1. Add Spring Cloud GCP Trace dependency.
    2. Propagate `traceId` (from request headers) using MDC.
    3. Enable distributed tracing in Google Cloud Platform.

## 15. Resilience

* **Resilience Library:** Resilience4j.
* **Patterns:** Retry with exponential backoff, Circuit breaker.
* **Implementation Details:**
    1. Add `resilience4j-spring-boot2` dependency.
    2. Annotate service methods with `@Retry` and `@CircuitBreaker` annotations.
    3. Configure retry policies and circuit breaker thresholds.
    4. Implement fallback mechanisms to return a default response when upstream calls fail.



## 16. Caching

* **Caching Strategy:** Cache responses at the service layer using Spring's `@Cacheable` annotation with a configurable caching strategy (e.g., Caffeine).
* **Performance Expectations:** Significant reduction in response times and load on the legacy backend service.
* **Rate Limiting Expectations:**  Rate limiting is handled primarily at the Apigee API Gateway level.

## 17. Integrations

* **External Integration:** Legacy XEDIE010 SOAP service.
* **Implementation Details:**
    1. Create a gateway class (`WEDIE010Gateway`) to handle SOAP calls.
    2. Use a SOAP client library (e.g., Apache CXF) to make SOAP requests.
    3. Handle SOAP faults and map them to appropriate API error responses.
    4.  The `DirectToStoreService` will use the `WEDIE010Gateway` to interact with the SOAP service.

## 18. Deployment

* **Supported Environments:** dev, qa, uat, ci, perf, perf-central, perf-east, pilot-prod-east, prod, prod-central, prod-east
* **Kubernetes Manifests:** (See Section 3 for a list of YAML files and their descriptions)
* **Environment-Specific Configuration:**  Values are loaded from environment variables (using `$` notation in properties) or from configmaps and secrets referenced in deployment.yaml (`envFrom`).

## 19. Code Formatting

* **Style Guide:** Google Java Style Guide
* **XML Style Sheet:** `eclipse-java-google-style.xml` (or equivalent)
* **Formatter Plugin:** Maven formatter plugin (or equivalent).


## 20. Testing

* **Unit Testing Framework:** JUnit 5
* **Mocking Frameworks:** Mockito, WireMock
* **Integration Testing Framework:** Spring Boot Test
* **Code Coverage Tool:** JaCoCo
* **Target Code Coverage:** 80% (excluding excluded packages defined below)
* **Excluded Packages:** `com.macys.iwm.domain`, `com.macys.iwm.model`, `com.macys.iwm.config`, `com.macys.iwm.exception`, `com.macys.iwm.utils`, `com.macys.iwm.common`, `com.macys.iwm.dto`
* **Test Organization:**  `src/test/resources/sample-payloads/` directory contains sample data organized by feature.


## 21. Documentation

The API will be documented using Swagger/OpenAPI 3.  The documentation will include:

* A detailed API reference with request/response examples in JSON and XML format.
* An interactive API explorer (Swagger UI/Redoc).
* Comprehensive documentation for authentication and authorization.
* A troubleshooting guide.
* A migration guide for upgrading between API versions.
* Business context documentation describing the purpose and usage of each endpoint.



## 22. Implementation Prompt for LLM

Create a Spring Boot application named `iwm-items-list-eapi` with the package structure and technologies specified above. The application should expose a RESTful API at `/v1/directToStore/carton/{UCCID}` that retrieves carton status from the legacy XEDIE010 SOAP service.

The API should use custom header-based authentication, role-based authorization, and mTLS to communicate with the downstream SOAP service. All response data should be formatted as JSON. Use Resilience4j for circuit breaker and retry implementations. Implement comprehensive logging using SLF4J and Logback, and expose metrics via Prometheus. Integrate with Google Cloud Secret Manager for secure credentials and Google Cloud Monitoring for observability.  Use a multi-stage docker build.  Implement robust error handling, input validation, and a versioning strategy. Generate comprehensive unit, integration, and controller tests, aiming for 80% code coverage. Produce detailed Swagger/OpenAPI 3 documentation. The API should be deployed on Google Kubernetes Engine (GKE), with horizontal scaling managed by HPA.  The API should support multiple environments including `dev, qa, uat, ci, perf, perf-central, perf-east, pilot-prod-east, prod, prod-central, prod-east`. Test data should be organized in `src/test/resources/sample-payloads/`.  Use Lombok for annotations.  Enforce the Google Java Style Guide.  The health endpoints should be restricted.


This prompt provides a concise and comprehensive set of instructions for an LLM to generate the code, configuration files, and documentation for this API.  The detailed breakdown of the specifications in the preceding sections will serve as a reference for error-free and high-quality code generation. Remember to provide the WSDL for the XEDIE010 service to the LLM.
