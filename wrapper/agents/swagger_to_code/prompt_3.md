Given a detailed OpenAPI 3.0 YAML, business and technical specification for the Carton API Wrapper, generate a complete, production-ready, cloud-native Java application that meets the following requirements:

**INPUT PROCESSING**
Based on the provided specification document.Generate a complete, production-ready, cloud-native Java application. First have base Project folder created,  generate a Java-based Wrapper API that interfaces with a SOAP service.
 
 The generated code should include:
Controller Layer: REST endpoints that expose the wrapper API.
Service Layer: Logic to invoke SOAP operations using the WSDL.
DTOs: Data Transfer Objects for request and response mapping.
SOAP Client Configuration: Using JAX-WS, Spring Web Services, or another suitable library.
Exception Handling: Standardized error responses and SOAP fault translation.
Logging: Integration with a reusable logging component.
Health, Liveliness, and Readiness Endpoints: For observability and Kubernetes compatibility.
API Documentation: Swagger/OpenAPI annotations.
Environment Profiles: Support for dev, test, and prod configurations.
Unit Tests: Basic test cases for controller and service layers. 
 
 
Refer to Questions Answer, Remark column for all needed details,. Each sections details , standards mentioned should be covered implementation. Project structure should be as per mentioned in csv. Have check if you have covered all testing, deployment, naming, observability, technology, authentication, request response, validation, dto, pipeline, logging, documentation standards and requirements mentioned.

**Selected Technology and Configuration (all are mandatory):**
Use from Inputs/Above

**Output Format:**
- Output the entire application as a single JSON object.
- Each key in the JSON object must be the relative file path (e.g., "src/main/java/com/macys/carton/controller/v1/CartonController.java").
- Each value must be the complete content of that file as a string.
- Do not include any extra text, explanations, or markdown—only the JSON object.
- **MUST generate every file and implement every requirement as specified in inputs**

**Project Structure:**
- As specified in inputs/Above


**Requirements:**
1. **No Placeholders:**
   - All code must be fully implemented.
   - Do not use any placeholders, stubs, or "TODO" comments.
   - Provide actual logic for all endpoints, services, and integrations, inferring reasonable behavior where not specified.

2. **Swagger/OpenAPI Documentation:**
   - As specified in inputs/Above


3. **General Requirements:**
  - The application must be enterprise-grade, reactive, and highly observable.
  - If endpoint logic is not specified, infer and implement logic based on the endpoint summary.
  - Every requirement below must be strictly followed.

4. **Technology Stack:**
   - As specified in inputs/Above

5. **Project Structure:**

  - **Strictly follow the project hierarchy described above.**
  
6. **Controller & Service Versioning:**
  - As specified in inputs/Above
  - Endpoints must support distributed tracing, authentication, impersonation, feature flags, and standardized error responses.

7. **Configuration & Deployment:**
  - As specified in inputs/Above

8. **Key Features:**
  - Reactive REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml, strictly follow implementation required for endpoints based on summary & description. Provide actual logic for all endpoints, services, and integrations.

9. **Progressive Generation Steps:**
  - Scaffold the Maven project with all dependencies and plugins.
  - Generate the folder structure and sample classes/interfaces for each layer.
  - Add Docker and Docker Compose files for local infrastructure.
  - Implement REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml. Provide actual logic for all endpoints, services, and integrations.
  - Add monitoring, metrics, and alerting configuration.
  - Generate OpenAPI documentation.
  - Provide a README with setup, usage, and architecture overview.

10. **Additional Requirements:**
  - Include a formatter configuration file (Google Java Style XML).
  - Provide Kubernetes manifests for deployment.
  - Supply environment-specific configuration files as specified in inputs
  - Metrics: /actuator/prometheus


12. **Integration Requirements:**
  - As specified in inputs/Above

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

  - **MUST generate a complete pom.xml file at the root level of the project, using Spring Boot 3.x, Java 21, and all dependencies used during project generation**

16. **SOAP Integration:**
   - Use `WebServiceTemplate` to call WEDIE010 SOAP service.
   - Build SOAP request dynamically; map relevant header values into the SOAP body per service contract.
   - Inject custom headers (`X-Client-Id`, `X-Client-Secret`, `X-Message-Id`) from Google Cloud Secret Manager.

   Upstream Endpoint Resolution:
   Base URL comes from configuration (Apigee) and may vary by division. Example property pattern: wedie010.targethost.<division> supplied per environment profile.

18. **Security:**
   - Implement all possible exceptions or custom exceptions mentioned in inputs
   - Use Spring Security for defense-in-depth.
   - Secure secrets via Google Cloud Secret Manager.Implement all possible configurations, integarions, usage with dummy values, Development-level security . Take all inputs from docs
   - Sanitize inputs to prevent XSS and injection attacks.
   - TLS termination at Apigee; app communicates over TLS to upstream where applicable.

19. **Resilience:**
    - Use Resilience4j with thresholds listed above.
    - Wrap SOAP calls with ResilienceService.
    - Configure timeouts; implement meaningful error mapping on faults.

20. **Caching:**
    - Use Spring Cache (@Cacheable) for carton lookups by UCCID.
    - Provide TTL-based eviction via cache manager configuration.
    - Deterministic keys from CacheKeyGenerator.

21. **Logging & Monitoring:**
    - Add Request/response logging, Performance metrics, Error context logging
    - Logback JSON structured logging including: timestamp, level, thread, logger, message, traceId, user id (if applicable), request id.
    - Trace propagation: MDC via TraceIdService/TraceIdUtils and a reactive WebFilter.
    - Actuator endpoints:/actuator/health, /actuator/prometheus.
    - Micrometer + Dynatrace: request counts, response times (histograms), error rates, external call durations, circuit breaker states, retry counts.
    - GZIP compression enabled.
    
    Dynatrace Dashboard (document in README):
    - Include tiles for: response time & throughput, error rate and failure analysis, CPU/memory utilization, circuit breaker and retry metrics.

   Alerting (document in README):
   - Trigger alerts on: error rate > 5%, response time > 1s (p95), CPU/memory > 80%, circuit breaker open state.

22. **Testing:**
   - Use JUnit 5, Spring Boot Test, WireMock.
   - Achieve 80%+ code coverage with JaCoCo.

23. **Deployment:**
    - Dockerize the application.
    - Provide Kubernetes manifests.
    - CI/CD pipeline via Jenkins.

**Important Note:**  
   - Do not use any placeholders, stubs, or "TODO" comments. All code must be fully implemented and production-ready. Do not leave any part for the user to implement.
   - **Strictly follow the directory hierarchy and file structure mentioned above**
   - **All versions, dependencies, and architectural choices must match the selections listed at the top of this.**
