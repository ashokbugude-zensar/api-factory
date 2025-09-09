Given a detailed OpenAPI 3.0 YAML specification and Node-red flows json for user management APIs, generate a complete, production-ready, cloud-native Java application for all flows that meets the following requirements:

**Selected Technology and Configuration (all are mandatory):**
- **Java Version:** 21
- **Spring Boot Version:** 3.3.5
- **Build Tool:** Maven
- **Target Environment:** Google Cloud Platform (Multi-region)
- **Base Package:** `com.demo.user`
- **API Version:** v1
- **Production Ready Version:** 1.0.151

**Output Format:**
- Output the entire application as a single JSON object.
- Each key in the JSON object must be the relative file path (e.g., "src/main/java/com/demo/user/controller/v1/UsersysController.java").
- Each value must be the complete content of that file as a string.
- Do not include any extra text, explanations, or markdown—only the JSON object.
- **MUST generate every file and implement every requirement described in all points below.**
- **Must implement all flows end to end from Node-Red flows json file, override the openapi spec file if needed accordingly**
- **MUST include all top-level files such as .gitignore and pom.xml in the output.**
- Example:
  {
    "src/main/java/com/demo/user/controller/v1/UsersysController.java": "package com.demo.user.controller.v1;\n// ...rest of the code...",
    "pom.xml": "<project>...</project>",
    "docker/Dockerfile": "FROM openjdk:21-jdk-slim\n..."
  }

**Project Structure:**
- All Java source code must be organized under `src/main/java/com/demo/user/` as follows:
  - `model/v1/` – Domain entities (versioned)
  - `dto/v1/` – Domain entities (versioned)
  - `repository/` – Repository interfaces
  - `service/v1/` – Business logic and use cases (versioned)
  - `config/` – Configuration classes
  - `controller/v1/` – REST controllers (versioned)
  - `exception/v1/` – Error handling (versioned)
  - `common/` – Shared utilities
  - `utils/` – Utility classes (e.g., JwtUtils etc.)
  - `MainApplication.java` – Main entry point

- All resource/configuration files must be under `src/main/resources/` (Mandatory):
  - **Generation of all the following properties files is mandatory.**
  - `application-ci.properties`
  - `application-dev.properties`
  - `application-perf-central1.properties`
  - `application-perf-east4.properties`
  - `application-perf.properties`
  - `application-pilot-prod-east4.properties`
  - `application-prod-central1.properties`
  - `application-prod-east4.properties`
  - `application-prod.properties`
  - `application-qa.properties`
  - `application-uat.properties`
  - `application.properties`
  - `logback-access-spring.xml`
  - `logback.xml`

- Top-level files and folders:
  - `pom.xml` – Maven configuration file (Spring Boot 3.3.5, Java 21, all dependencies as per selections below)
  - `k8s-manifests/` – Kubernetes deployment yamls
  - `docker/` – Docker files (Base: openjdk:21-jdk-slim, Port: 8080, Health: /actuator/health, Memory: 1Gi, CPU: 500m)
  - `README.md` – Project documentation
  - `.gitignore` – Git ignore file
  - `formatter/codeStyles/` – Code style configuration (Google Java Style via formatter plugin)

**Requirements:**
1. **No Placeholders:**  
   - All code must be fully implemented.  
   - Do not use any placeholders, stubs, or "TODO" comments.  
   - Provide actual logic for all endpoints, services, and integrations, inferring reasonable behavior where not specified.

2. **Swagger/OpenAPI Documentation:**  
   - Ensure the application includes complete Swagger/OpenAPI documentation.  
   - Configure Springdoc OpenAPI (2.1.0) so that the generated API is fully documented and accessible at `/swagger-ui.html`.

3. **General Requirements:**
  - The application must be enterprise-grade, reactive, and highly observable.
  - It should support user data management and integration with external systems (e.g., Postgres).
  - If endpoint logic is not specified, infer and implement logic based on the endpoint summary.
  - Every requirement below must be strictly followed.

4. **Technology Stack:**
  - Java 21
  - Spring Boot 3.3.5
  - Maven
  - Spring WebFlux (Reactive)
  - Spring Security (JWT/OAuth2, RSA256, Auth0 JWT 3.18.2)
  - PostgreSQL (R2DBC optional)
  - Docker & Docker Compose
  - Kubernetes (Deployment, Service, ConfigMap, Secret, HPA)
  - MapStruct for DTO mapping
  - Lombok 1.18.36 for boilerplate reduction
  - Spring Boot Actuator, Micrometer, Prometheus, Google Cloud Monitoring
  - Jackson JSON Processing
  - SLF4J + Logback (JSON structured logging)
  - Resilience4j (Circuit Breaker: Failure Threshold 50%, Min Requests 10, Wait 30s, Sliding Window 20 calls)
  - JUnit 5, Mockito, WireMock, MockWebServer for testing

5. **Project Structure:**
  - **Strictly follow the project hierarchy described above.**
  - Organize code under src/main/java/com/demo/user/ as follows:
    - model/v1 – Domain entities
    - repository/ – Repository interfaces
    - service/v1 – Business logic and use cases
    - config/ – Configuration classes
    - controller/v1 – REST controllers
    - exception/v1 – Error handling
    - common/ – Shared utilities
    - utils/ – Utility classes (e.g., JwtUtils, DbUtils, etc.)
    - MainApplication.java – Main entry point
    - pom.xml - configuration file at the top level of the repository
    - k8s-manifests/ - Organize deployment yamls at the top level of the repository
    - docker/ - Organize all docker files at the top level of the repository 
    - ReadMe.md at the top level of the repository
    - Supply environment-specific configuration files for these profiles:
        ci, dev, perf, perf-central1, perf-east4, pilot-prod-east4, prod, prod-central1, prod-east4, qa, uat, local, test, default
    - .gitignore at the top level of the repository  
    - formatter/codeStyles/ - place it at the top level of the repository for code style configuration (Google Java Style via formatter plugin). Also add required dependencies in pom.xml.

6. **Controller & Service Versioning:**
  - Place versioned controllers in controller/v1/ (e.g., UsersysController.java).
  - Place versioned services in service/v1/ (e.g., UsersysService.java).
  - Unversioned controllers/services (e.g., MainController, MainService) remain in their respective root folders.
  - EmitterController/EmitterService can be versioned or unversioned as needed.
  - Versioning is managed via URI path (e.g., /api/v1/).
  - Endpoints must support distributed tracing, JWT authentication, impersonation, feature flags, and standardized error responses.

7. **Configuration & Deployment:**
  - Provide docker-compose.yml for local infrastructure (Postgres, Prometheus, Grafana).
  - Include a Dockerfile for the application (Base: openjdk:21-jdk-slim, Port: 8080, Health: /actuator/health, Memory: 1Gi, CPU: 500m).
  - Use Maven plugins for code formatting, coverage, and packaging.
  - Provide Kubernetes manifests for cloud deployment - Generate a user-sys-api-api-deployment.yaml for user-sys-api-api image

8. **Key Features:**
  - Reactive REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml, strictly follow implementation required for endpoints based on summary & description. Provide actual logic for all endpoints, services, and integrations.
  - For controller implementation, 
    a. Include OpenAPI annotations (@Operation, @ApiResponses, @ApiResponse) for documentation.
    b. Provide method names based on description of functionality being implemented.
    c. Add RequestMapping based on paths </**>  mentioned in input swagger.
    d. Use @RequestHeader and ensure null-safe handling of optional and default headers for all parameters with in as header and details mentioned in input swagger.
    f. Use reactive programming with Project Reactor (Mono, Flux).
  - Place versioned services in service/v1/ 
  - Place versioned models in model/v1/ (eg: User)
  - Create required models - Domain entities, event – Domain events in model/ folder
  - UserService: Use JWT authentication. Implement service and utility classes (e.g., UserService, JwtUtils). 
  - Generate a UserService which sets up a SUserService class in Java using Spring implements methods for user CRUD operations.
  - Comprehensive JSON logging (GCP compatible, fields: Timestamp, Log Level, Thread Name, Logger Name, Message, Correlation ID/Trace ID, User ID, Request ID).
  - Prometheus metrics and health endpoints via Spring Boot Actuator.
  - Code style enforcement (Google Java Style via formatter plugin). Also add required dependencies in pom.xml.
  - Secure endpoints using JWT or OAuth2 (RSA256, Auth0 JWT, public key validation, token expiration validation).
  - If any Bulk operations, implement them with rate limits and batch size constraints.
  - Generate utility class for JWT operations

9. **Progressive Generation Steps:**
  - Scaffold the Maven project with all dependencies and plugins.
  - Generate the folder structure and sample classes/interfaces for each layer.
  - Add Docker and Docker Compose files for local infrastructure.
  - Implement REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml. Provide actual logic for all endpoints, services, and integrations.
  - For controller implementation, 
    a. Include OpenAPI annotations (@Operation, @ApiResponses, @ApiResponse) for documentation.
    b. Provide method names based on description of functionality being implemented.
    c. Add RequestMapping based on paths </**>  mentioned in input swagger.
    d. Use @RequestHeader and ensure null-safe handling of optional and default headers for all parameters with in as header and details mentioned in input swagger.
    f. Use reactive programming with Project Reactor (Mono, Flux).
  - Create required models - Domain entities, event – Domain events in model/ folder
  - UserService: Use JWT authentication. Implement service and utility classes (e.g., UserService, JwtUtils). Generate a SalesforceService which sets up a UserService class in Java using Spring and implements crud operations for user.
  - Add monitoring, metrics, and alerting configuration.
  - Generate OpenAPI documentation.
  - Provide a README with setup, usage, and architecture overview.

10. **Additional Requirements:**
  - Include a formatter configuration file (Google Java Style XML).
  - Provide Kubernetes manifests for deployment.
  - Supply environment-specific configuration files for these profiles:
    ci, dev, perf, perf-central1, perf-east4, pilot-prod-east4, prod, prod-central1, prod-east4, qa, uat, local, test, default

11. **Example API Endpoints:**
  - Unversioned: /api/** (MainController, all HTTP methods)
  - Versioned: /api/v1/** (UsersysController, all HTTP methods)
  - Health: /actuator/health
  - Metrics: /actuator/prometheus

12. **Integration Requirements:**
  - Postgres: Use Postgres for all db related implementations for all apis
  - User API: REST API, API Key, JSON, 15s timeout, simple retry 2 attempts.
  - JWT Authentication: Provide utilities for token generation/decoding, private key management, and claims extraction.
  - Distributed Tracing: Add a WebFilter to extract Trace-Id and propagate via MDC for logging/tracing.
  - Custom Logging: Configure Logback for JSON logging and MDC context propagation.
  - Exception Handling: Implement global exception handlers and custom exceptions for integration errors.
  - Configuration Management: Use application properties and environment-specific files for all endpoints, credentials, and feature flags.

13. **Exception Handling:**
  - Use custom exceptions (e.g., BusinessException, ValidationException, ResourceNotFoundException, ExternalServiceException, ForbiddenException) extending RuntimeException for domain-specific errors.
  - Throw exceptions in service/utility classes to signal error conditions.
  - Implement a global exception handler using @RestControllerAdvice and @ExceptionHandler to return structured error responses (fields: timestamp, status, error, message, path).
  - Handle integration errors with meaningful messages and propagate to the global handler.
  - Wrap checked exceptions in runtime exceptions for reactive flows.
  - Log exception details in custom JSON logs for traceability.
  - Return appropriate HTTP status codes in error responses (200, 201, 400, 401, 403, 404, 500).
  - Generate custom exception classes, a global exception handler, and ensure all service/utility classes throw and handle exceptions as described.

14. **Logging & Monitoring Configuration:**
  - Add Logback configuration files (`logback-access-spring.xml`, `logback.xml`) for JSON logging in `src/main/resources/`.
  - Ensure the Logback configuration is included in the Maven build (pom.xml) and supports environment-based configuration management.
  - Metrics: Request count per endpoint, Response time distribution, Error rate by endpoint, External service call duration, Circuit breaker status.

15. **.gitignore and pom.xml:**
  - **MUST generate a complete .gitignore file at the root level of the project.**
  - **MUST generate a complete pom.xml file at the root level of the project, using Spring Boot 3.3.5, Java 21, and all dependencies used during project generation**

**Important Note:**  
- Do not use any placeholders, stubs, or "TODO" comments. All code must be fully implemented and production-ready. Do not leave any part for the user to implement.
- **Strictly follow the directory hierarchy and file structure mentioned above**
- **All versions, dependencies, and architectural choices must match the selections listed at the top of this.**