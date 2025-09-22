### 1. Business Requirements

This document specifies the business and technical requirements for the **Carton API Wrapper**, an enterprise service designed to modernize access to a legacy backend system.

**Business Problem:** The primary business driver is the need to modernize a legacy SOAP/XML service named `XEDIE010`. This service is critical for order processing workflows, specifically for retrieving carton status based on a UCCID for direct-to-store operations. Modern client applications cannot easily or efficiently integrate with this legacy SOAP endpoint, creating significant development overhead and slowing down innovation.

**Proposed Solution:** A new REST/JSON wrapper API will be developed to act as a facade for the legacy `XEDIE010` service. This wrapper will expose a modern, standardized, and easy-to-use RESTful interface. It will handle the protocol translation from REST/JSON to SOAP/XML, abstracting away the complexity of the downstream legacy system from all consuming clients. This approach hides the upstream dependency and simplifies the integration landscape.

**Business Objectives & Success Metrics:**
*   **Simplify Integration:** Drastically reduce the complexity and development time for modern clients needing to access carton status information.
*   **Modernize Legacy Systems:** Provide a modern access layer for a critical legacy system without requiring an immediate, high-risk replacement of the core backend service.
*   **Enhance Developer Experience:** Create a consistent, well-documented REST API that follows modern standards, improving ease of use for internal development teams.
*   **Success Measurement:** The primary success metric will be **Integration Efficiency**, measured by the reduction in lines of code and overall development time required for new applications to integrate with the carton status functionality. A decrease in API-related support tickets will also be a key indicator of success.

---

### 2. Technologies and Versions

The application will be built using the following technologies. All library versions have been selected for compatibility with **Spring Boot 3.2.5**.

| Category                  | Technology / Library                 | Version / Specification                               | Notes                                                                                             |
| ------------------------- | ------------------------------------ | ----------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **Platform**              | **Java**                             | **21**                                                | Project JDK.                                                                                      |
| **Core Framework**        | **Spring Boot**                      | **3.2.5**                                             | Provides core application functionality, web server, and dependency management.                   |
| **Cloud Integration**     | **Spring Cloud GCP BOM**             | **5.2.1**                                             | Bill of Materials for Google Cloud Platform libraries. `com.google.cloud:spring-cloud-gcp-dependencies` |
|                           | Spring Cloud GCP Logging             | `spring-cloud-gcp-starter-logging`                    | Handled by the BOM. For integration with Google Cloud Logging.                                    |
|                           | Spring Cloud GCP Secret Manager      | `spring-cloud-gcp-starter-secretmanager`              | Handled by the BOM. For secure credential management.                                             |
|                           | Spring Cloud GCP Trace               | `spring-cloud-gcp-starter-trace`                      | Handled by the BOM. For distributed tracing with Google Cloud Trace.                              |
| **Web & API**             | Spring Web (MVC)                     | `spring-boot-starter-web`                             | For building RESTful APIs using traditional synchronous processing.                               |
|                           | Jackson                              | `spring-boot-starter-json`                            | Default JSON serializer/deserializer.                                                             |
|                           | Jackson Dataformat XML               | `com.fasterxml.jackson.dataformat:jackson-dataformat-xml` | For supporting XML request/response formats. Version managed by Spring Boot.                        |
| **Data Validation**       | Jakarta Bean Validation              | `spring-boot-starter-validation`                      | For request DTO and header validation using annotations.                                          |
| **Code Generation**       | Lombok                               | **1.18.32**                                           | For reducing boilerplate code (getters, setters, etc.).                                           |
| **SOAP Integration**      | **Apache CXF**                       | **4.0.4**                                             | For generating Java client from WSDL and handling SOAP communication.                             |
|                           | CXF Codegen Maven Plugin             | `org.apache.cxf:cxf-codegen-plugin`                   | Maven plugin to generate JAXB classes from the WSDL during the build phase.                       |
|                           | JAXB API & Runtime                   | `jakarta.xml.bind:jakarta.xml.bind-api`, `org.glassfish.jaxb:jaxb-runtime` | Required for JAXB marshalling/unmarshalling since Java 11+.                                 |
| **HTTP Client**           | OkHttp                               | **4.12.0**                                            | High-performance HTTP client used by the SOAP gateway for underlying transport.                   |
| **Resilience**            | **Resilience4j**                     | `io.github.resilience4j:resilience4j-spring-boot3`      | For implementing Circuit Breaker and Retry patterns.                                              |
|                           | Spring AOP                           | `spring-boot-starter-aop`                             | Required for Resilience4j annotation-based integration.                                           |
| **Observability**         | Spring Boot Actuator                 | `spring-boot-starter-actuator`                        | For health checks, metrics, and application monitoring endpoints.                                 |
|                           | Micrometer Prometheus Registry       | `io.micrometer:micrometer-registry-prometheus`        | For exposing metrics in Prometheus format.                                                        |
| **API Documentation**     | SpringDoc OpenAPI                    | `org.springdoc:springdoc-openapi-starter-webmvc-ui:2.5.0` | For generating OpenAPI 3.0 specification and Swagger UI.                                          |
| **Build Tool**            | **Maven**                            | **3.9+**                                              | Project build and dependency management.                                                          |
| **Testing**               | JUnit 5                              | `spring-boot-starter-test`                            | Core testing framework.                                                                           |
|                           | Mockito                              | `spring-boot-starter-test`                            | For mocking dependencies in unit tests.                                                           |
|                           | REST Assured                         | **5.4.0**                                             | For fluent API integration testing.                                                               |
|                           | WireMock                             | `org.wiremock:wiremock-standalone:3.5.4`              | For mocking the downstream SOAP service in integration tests.                                     |
| **Containerization**      | **Docker**                           | **-**                                                 | For containerizing the application for deployment.                                                |
| **CI/CD**                 | GitLab CI/CD                         | **-**                                                 | Continuous Integration and Deployment pipeline.                                                   |

---

### 3. Project Details

*   **Project Name:** `iwm-items-list-eapi`
*   **Base Package:** `com.macys.iwm.carton`
*   **Base Package Structure:** The application will follow a standard layer-based architecture.
    *   `com.macys.iwm.carton.config`: Spring configuration classes (e.g., AppConfig, SecurityConfig, CXFClientConfig).
    *   `com.macys.iwm.carton.controller.v1`: Version 1 REST controllers.
    *   `com.macys.iwm.carton.dto`: Data Transfer Objects for API requests and responses.
    *   `com.macys.iwm.carton.exception`: Custom exception classes and global exception handlers.
    *   `com.macys.iwm.carton.gateway`: Integration layer for communicating with external services (e.g., the SOAP service).
        *   `com.macys.iwm.carton.gateway.webservice`: JAXB classes generated from the WSDL.
    *   `com.macys.iwm.carton.mapper`: Mappers for converting between DTOs and internal/gateway models.
    *   `com.macys.iwm.carton.service`: Business logic layer.
    *   `com.macys.iwm.carton.util`: Utility classes.
*   **Architectural Style:** Layered Monolith (Microservice Style). The application is self-contained but follows a clear separation of concerns into presentation (controller), business (service), and integration (gateway) layers.

---

### 4. Controllers

**File Path:** `com.macys.iwm.carton.controller.v1.DirectToStoreController.java`

This controller handles all operations related to direct-to-store carton information.

**Base Path:** `/api/v1/directToStore`

#### Endpoints

**1. Get Carton Details by UCCID**

*   **Purpose:** Retrieves carton status based on its unique UCCID.
*   **HTTP Method:** `GET`
*   **Endpoint URL:** `/carton/{UCCID}`
*   **Path Parameters:**
    *   `UCCID` (String): The UCCID of the carton. Must be a non-empty string.
*   **Produces:** `application/json`, `application/xml`
*   **Required Request Headers:**
    *   `location` (String): Store location identifier.
    *   `colleagueId` (String): Employee/Associate ID.
    *   `applicationId` (String): Application identifier.
    *   `division` (Integer): Division number. Validation: `@Min(0) @Max(99999999)`
    *   `store` (Integer): Store number. Validation: `@Min(0) @Max(99999999)`
    *   `device_id` (String): Device identifier.
    *   `device_type` (String): Device type.
*   **Optional Request Headers:**
    *   `traceId` (String): Request tracing identifier.
    *   `MockResponse` (String): Flag for testing/mocking.
    *   `X-Client-Id` (String): Identifies the calling application.
    *   `X-Client-Secret` (String): Authenticates the calling application.
    *   `X-Message-Id` (String): Unique identifier for tracking and idempotency.

---

### 5. DTOs / Models

**File Path:** `com.macys.iwm.carton.dto.CartonDetailResponse.java`

This DTO represents the response payload for the Get Carton Details endpoint. It supports both JSON and XML serialization.

```java
package com.macys.iwm.carton.dto;

import com.fasterxml.jackson.annotation.JsonInclude;
import com.fasterxml.jackson.annotation.JsonProperty;
import com.fasterxml.jackson.annotation.JsonRootName;
import jakarta.xml.bind.annotation.XmlAccessType;
import jakarta.xml.bind.annotation.XmlAccessorType;
import jakarta.xml.bind.annotation.XmlElement;
import jakarta.xml.bind.annotation.XmlRootElement;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@JsonInclude(JsonInclude.Include.NON_NULL)
@XmlRootElement(name = "DirectToStoreResponse", namespace = "http://localhost:8080/v1/directtostore")
@XmlAccessorType(XmlAccessType.FIELD)
public class CartonDetailResponse {

    @JsonProperty("returnCode")
    @XmlElement(name = "returnCode")
    private Integer returnCode;

    @JsonProperty("returnMessage")
    @XmlElement(name = "returnMessage")
    private String returnMessage;

    @JsonProperty("cartonNumber")
    @XmlElement(name = "cartonNumber")
    private String cartonNumber;

    @JsonProperty("nafship")
    @XmlElement(name = "nafship")
    private Long nafship;

    @JsonProperty("bolstatus")
    @XmlElement(name = "bolstatus")
    private String bolstatus;

    @JsonProperty("bol")
    @XmlElement(name = "bol")
    private String bol;

    // Private constructor for the builder
    private CartonDetailResponse(Builder builder) {
        this.returnCode = builder.returnCode;
        this.returnMessage = builder.returnMessage;
        this.cartonNumber = builder.cartonNumber;
        this.nafship = builder.nafship;
        this.bolstatus = builder.bolstatus;
        this.bol = builder.bol;
    }

    // Static method to get a new builder instance
    public static Builder builder() {
        return new Builder();
    }

    // Manual Builder Class
    public static class Builder {
        private Integer returnCode;
        private String returnMessage;
        private String cartonNumber;
        private Long nafship;
        private String bolstatus;
        private String bol;

        public Builder returnCode(Integer returnCode) {
            this.returnCode = returnCode;
            return this;
        }

        public Builder returnMessage(String returnMessage) {
            this.returnMessage = returnMessage;
            return this;
        }

        public Builder cartonNumber(String cartonNumber) {
            this.cartonNumber = cartonNumber;
            return this;
        }

        public Builder nafship(Long nafship) {
            this.nafship = nafship;
            return this;
        }

        public Builder bolstatus(String bolstatus) {
            this.bolstatus = bolstatus;
            return this;
        }

        public Builder bol(String bol) {
            this.bol = bol;
            return this;
        }

        public CartonDetailResponse build() {
            // Perform null safety checks or custom validation during build
            if (returnCode == null) {
                throw new IllegalStateException("returnCode cannot be null");
            }
            if (returnMessage == null) {
                this.returnMessage = ""; // Default value
            }
            return new CartonDetailResponse(this);
        }
    }
}
```

---

### 6. Services and Gateways

**Service Layer (`DirectToStoreService.java`):**
*   **Responsibility:** Contains the core business logic. It orchestrates calls to the gateway layer and maps the results to the response DTOs.
*   **Dependencies:** `Wedie010Gateway`.
*   **Logic:**
    1.  Receives the UCCID and required headers from the controller.
    2.  Calls the `Wedie010Gateway` to execute the SOAP request.
    3.  Handles any exceptions thrown by the gateway, translating them into business-specific exceptions.
    4.  Maps the response from the gateway into the `CartonDetailResponse` DTO.

**Gateway Layer (`Wedie010Gateway.java`):**
*   **Responsibility:** Encapsulates all logic for communicating with the downstream `WEDIE010` SOAP service. It is responsible for building the SOAP request, executing the HTTP call, and parsing the SOAP response.
*   **Dependencies:** `OkHttpClient`, Apache CXF generated client/port (`WEDIE010Port`), and a mapper utility.
*   **Logic:**
    1.  Constructs the JAXB request object (`Xedie010_Input_Data`) using data from the REST request (headers, path parameters).
    2.  Uses the Apache CXF port to invoke the `Xedie010_Input_Data` operation on the SOAP service. The underlying transport will be configured to use `OkHttpClient` for connection pooling and mTLS.
    3.  Receives the JAXB response object (`Xedie010_Input_DataResponse`).
    4.  Parses the JAXB response, performs any necessary type conversions, and returns a simplified gateway-level response object or maps directly to the API DTO.
    5.  Handles SOAP Faults and network errors, mapping them to custom `RestClientException` instances.

---

### 7. Security

*   **Authentication Strategy:**
    1.  **mTLS:** The service will enforce mutual TLS, validating the client's certificate against a trusted authority to establish a secure, authenticated channel. The trust store and key store paths will be configured via application properties.
    2.  **Custom Header Validation:** The service will validate the presence and format of specific enterprise headers (`X-Client-Id`, `X-Client-Secret`, `X-Message-Id`) required by the downstream SOAP service.

*   **Authorization Strategy:**
    *   **Role-Based Access Control (RBAC):** Access to the API will be governed by roles. While the service itself will not implement a full role provider, it will be designed to integrate with an external authorization system (like Apigee) that enforces these roles. The security context will be checked for the required roles.

*   **Secret Management:**
    *   **Google Cloud Secret Manager:** All sensitive configuration values, such as client secrets, keystore passwords, and API keys, will be stored in Google Cloud Secret Manager.
    *   **Integration:** The application will use the `spring-cloud-gcp-starter-secretmanager` library to fetch secrets at startup. Properties will be defined in `bootstrap.yml` using the format: `app.secret.property=${sm://projects/my-project/secrets/my-secret/versions/latest}`.

*   **Actuator Security:**
    *   Actuator endpoints (`/actuator/*`) will be exposed on a separate management port (e.g., `9091`). Access to this port will be restricted at the network level (e.g., via Kubernetes NetworkPolicy or firewall rules) and will not use the same mTLS/header authentication as the main API.

---

### 8. Logging, Observability, and Tracing

**Logging Implementation:**
This service will use a standardized Spring Boot starter to generate structured JSON logs conforming to organizational standards.

*   **Automatic Configuration:**
    *   **Logback:** The `logback-spring.xml` from the starter will be used.
    *   **JSON Logging:** A `CustomJsonEncoder` will format all logs as JSON.
    *   **MDC Enrichment:** A `MdcInterceptor` will automatically capture required headers from incoming requests and add them to the SLF4J Mapped Diagnostic Context (MDC), making them available in every log line.
*   **Usage:** Standard SLF4J loggers (`LoggerFactory.getLogger(...)`) will be used throughout the application. The starter will automatically handle formatting and enrichment.
*   **Logging Framework:** SLF4J with Logback, integrated with `spring-cloud-gcp-starter-logging` to ship logs to Google Cloud Logging.
*   **Log Format:** Single-line JSON.
*   **Mandatory Log Fields:**
    *   `timestamp`: The time the log event occurred.
    *   `level`: Log level (e.g., INFO, ERROR).
    *   `threadName`: Name of the thread that generated the log.
    *   `loggerName`: The fully qualified class name of the logger.
    *   `message`: The log message.
    *   `stackTrace`: The full stack trace for exceptions.
    *   `mdc.location`: Value of the `location` request header.
    *   `mdc.colleagueId`: Value of the `colleagueId` request header.
    *   `mdc.applicationId`: Value of the `applicationId` request header.
    *   `mdc.division`: Value of the `division` request header.
    *   `mdc.store`: Value of the `store` request header.
    *   `mdc.deviceId`: Value of the `device_id` request header.
    *   `mdc.deviceType`: Value of the `device_type` request header.
    *   `mdc.traceId`: Unique ID for tracing the request across services.

**Metrics:**
*   **Library:** Micrometer with the Prometheus registry (`micrometer-registry-prometheus`).
*   **Endpoint:** Metrics will be exposed in Prometheus format at `/actuator/prometheus`.
*   **Key Metrics:**
    *   HTTP server requests (`http_server_requests_seconds`).
    *   JVM memory and CPU usage.
    *   Logback events.
    *   Resilience4j metrics (circuit breaker state, retry attempts).
    *   Authentication success/failure counts.

**Health Checks:**
*   **Library:** Spring Boot Actuator.
*   **Endpoints:**
    *   `/actuator/health`: Main health endpoint.
    *   `/actuator/health/liveness`: Liveness probe to indicate if the application is running.
    *   `/actuator/health/readiness`: Readiness probe to indicate if the application is ready to serve traffic.
*   **Custom Health Indicators:** A custom health indicator will be implemented to check the availability of the downstream `WEDIE010` SOAP service.

**Tracing:**
*   **Library:** Google Cloud Trace, enabled via `spring-cloud-gcp-starter-trace`.
*   **Functionality:** Automatically instruments incoming and outgoing HTTP requests to propagate trace context and send trace data to Google Cloud, enabling distributed request tracing.

---

### 9. Resilience

*   **Library:** **Resilience4j** will be used to implement fault tolerance patterns.
*   **Patterns:**
    1.  **Circuit Breaker:**
        *   **Implementation:** The gateway method calling the SOAP service will be annotated with `@CircuitBreaker`.
        *   **Configuration:** Configured in `application.yml` with properties for failure rate threshold (e.g., 50%), slow call rate threshold, and wait duration in the open state (e.g., 60s). A fallback method will be provided to return a default error response when the circuit is open.
    2.  **Retry:**
        *   **Implementation:** The same gateway method will be decorated with a `Retry` policy.
        *   **Configuration:** Configured in `application.yml` to retry on specific transient exceptions (e.g., `IOException`, `TimeoutException`). It will use an exponential backoff strategy with a configurable number of attempts (e.g., 3).

---

### 10. Caching

No application-level caching (e.g., `@Cacheable`) is required for this service. The primary performance optimization will be HTTP connection pooling for the outbound SOAP calls, managed by the OkHttp client.

---

### 11. Integrations

**SOAP Service Integration: `XEDIE010`**

The core function of this application is to integrate with the legacy `WEDIE010` (aliased as `XEDIE010`) SOAP service.

*   **Service Endpoint:** The endpoint URL is dynamic and constructed based on the `division` header. The base URL pattern is retrieved from application configuration (e.g., `wedie010.targethost.<division>`).
*   **Client Library:** **Apache CXF** will be used. The `cxf-codegen-plugin` for Maven will be configured to run during the `generate-sources` phase. It will consume the `wedie010.wsdl` file to generate JAXB-annotated Java classes representing the SOAP request, response, and port type.
*   **Implementation Location:** The integration logic will be fully encapsulated within the `com.macys.iwm.carton.gateway.Wedie010Gateway` class.
*   **Controller -> Service -> Gateway Flow:**
    1.  `DirectToStoreController` receives the GET request and extracts the `UCCID` and all required headers.
    2.  It calls `DirectToStoreService.getCartonDetails(uccid, headers)`.
    3.  `DirectToStoreService` invokes `Wedie010Gateway.getCartonStatus(uccid, headers)`.
    4.  `Wedie010Gateway` uses a mapper utility to create the `Xedie010_Input_Data` JAXB object from the provided parameters and headers.
    5.  It invokes the SOAP operation using the CXF-generated port.
    6.  It receives the `Xedie010_Input_DataResponse` JAXB object.
    7.  It uses another mapper method to transform the JAXB response into the `CartonDetailResponse` DTO.
    8.  The DTO is returned up the call stack to the controller, which serializes it to JSON or XML.
*   **Error Handling:** The gateway will catch `SOAPFaultException` and other network-related exceptions. These will be wrapped in a custom `RestClientException` containing details from the fault, which is then handled by the global exception handler to produce a standardized HTTP error response (e.g., 502 Bad Gateway).

---

### 12. Deployment

*   **Supported Environments:** The application must support multiple deployment profiles. Configuration for each will be managed via `application-{profile}.yml` files.
    *   `local`
    *   `dev`
    *   `qa`
    *   `uat`
    *   `ci`
    *   `perf`, `perf-central`, `perf-east`
    *   `pilot-prod-east`
    *   `prod`, `prod-central`, `prod-east`
*   **Local Development Profile (`local`):**
    *   A dedicated `local` profile must be created to facilitate easy local development.
    *   In this profile, integration with Google Cloud services (Secret Manager, Logging, Trace) **must be disabled**.
    *   The application should fall back to using local property files for configuration instead of fetching from Secret Manager.
    *   The downstream SOAP service should be mocked (e.g., using a configurable WireMock instance).
*   **CI/CD:**
    *   **Tool:** GitLab CI/CD.
    *   **Pipeline Stages:** The pipeline will consist of the following primary stages: `build -> test -> scan -> package -> deploy`. The `scan` stage will run SonarQube analysis.
*   **Containerization (`Dockerfile`):**
    *   A multi-stage `Dockerfile` will be used for optimization and security.
    *   **Build Stage:** Uses an enterprise-certified OpenJDK 21 base image.
    *   **Runtime Stage:** Uses a minimal distroless Java 21 image to reduce the attack surface.
    *   **Configuration:** The entrypoint will enable the JMX Prometheus exporter on port 9091, force TLSv1.2, and prefer the IPv4 stack.
    *   **Health Check:** A `HEALTHCHECK` instruction will be included, pointing to the actuator health endpoint.

---

### 13. Code Formatting

*   **Style Guide:** **Google Java Style Guide**.
*   **Formatter Plugin:** The `formatter-maven-plugin` will be configured in `pom.xml` to automatically format source code during the build process, ensuring compliance with the style guide. The configuration will reference an `eclipse-java-google-style.xml` file.

---

### 14. Testing

*   **Frameworks & Libraries:**
    *   **Unit & Integration Testing:** JUnit 5.
    *   **Mocking:** Mockito.
    *   **External Service Mocking:** WireMock will be used to mock the downstream `WEDIE010` SOAP service during integration tests.
    *   **API Testing:** REST Assured for writing fluent and descriptive API tests.
*   **Code Coverage:**
    *   **Tool:** JaCoCo.
    *   **Target:** **80%** line coverage.
    *   **Excluded Packages:** The following packages will be excluded from the code coverage report to focus on business logic:
        *   `**/domain/**/*`
        *   `**/model/**/*`
        *   `**/config/**/*`
        *   `**/exception/**/*`
        *   `**/common/**/*`
        *   `**/dto/**/*`
*   **Test Organization:**
    *   **Unit Tests:** Focus on individual services and utilities in isolation.
    *   **Integration Tests:** Test the full application stack from the controller down to the (mocked) gateway. These will run with the `@SpringBootTest` annotation and an active profile of `test`.
    *   **Test Data:** Sample JSON/XML payloads and WireMock stubs will be located in `src/test/resources/`.

---

### 15. Documentation

*   **API Documentation:**
    *   **Tool:** **SpringDoc OpenAPI 3**. The application will automatically generate an OpenAPI 3.0 specification.
    *   **UI:** The `springdoc-openapi-starter-webmvc-ui` dependency will provide an embedded **Swagger UI**, accessible at `/swagger-ui.html`.
    *   **Content:** Controllers and DTOs will be heavily annotated (`@Operation`, `@ApiResponse`, `@Schema`, etc.) to produce detailed and user-friendly documentation with clear descriptions and examples.
*   **Project Documentation:**
    *   A comprehensive `README.md` file will be generated at the root of the project. It will include:
        *   A project overview.
        *   Instructions on how to build and run the application locally.
        *   Details on available Spring profiles.
        *   Instructions for running tests.
        *   A summary of key API endpoints.

---

### 16. Implementation Prompt for LLM

Create a complete Spring Boot application named `iwm-items-list-eapi` that fully implements all requirements detailed in this specification. The application must be production-ready, with no placeholders. Ensure all aspects, from project structure and security to testing and deployment, are generated exactly as described above.