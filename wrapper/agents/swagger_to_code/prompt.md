Given a detailed OpenAPI 3.0 YAML, business and technical specification for the Carton API Wrapper, generate a complete, production-ready, cloud-native Java application that meets the following requirements:

**Selected Technology and Configuration (all are mandatory):**
- **Java Version:** 21
- **Spring Boot Version:** 3.x
- **Build Tool:** Maven
- **Target Environment:** Google Cloud Platform (GKE, Apigee)
- **Base Package:** `com.macys.carton`
- **API Version:** v1
- **Production Ready Version:** 1.0.0

**Output Format:**
- Output the entire application as a single JSON object.
- Each key in the JSON object must be the relative file path (e.g., "src/main/java/com/macys/carton/controller/v1/CartonController.java").
- Each value must be the complete content of that file as a string.
- Do not include any extra text, explanations, or markdown—only the JSON object.
- **MUST generate every file and implement every requirement described in all points below.**
- **MUST include all top-level files such as `.gitignore`, `pom.xml`, `Dockerfile`, `README.md`, and Kubernetes manifests in the output.**
- Example:
  {

    "src/main/java/com/macys/carton/controller/v1/CartonController.java": "package com.macys.carton.controller.v1;\n// ...rest of the code...",

    "pom.xml": "<project>...</project>",

    "docker/Dockerfile": "FROM openjdk:21-jdk-slim\n..."

  }

**Project Structure:**
- All Java source code must be organized under `src/main/java/com/macys/carton/` as follows:
  - `controller/v1/` – REST controllers (versioned)
  - `service/v1/` – Business logic and use cases (versioned)
  - `dto/` – Data transfer objects
  - `mapper/` – MapStruct mappers
  - `config/` – Configuration classes
  - `exception/v1/` – Error handling (versioned)
  - `client/` – SOAP client integration
  - `util/` – Utility classes
  - `MainApplication.java` – Main entry point

- All resource/configuration files must be under `src/main/resources/`:
  - `application.yml`
  - `logback-spring.xml`

- Top-level files and folders:
  - `pom.xml`
  - `docker/Dockerfile`
  - `k8s-manifests/`
  - `.gitignore`
  - `README.md`

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
  - If endpoint logic is not specified, infer and implement logic based on the endpoint summary.
  - Every requirement below must be strictly followed.

4. **Technology Stack:**
   - **Java Version:** 21
   - **Spring Boot Version:** 3.x  
   - **Build Tool:** Maven  
   - **Framework:** Spring Boot Web (non-WebFlux)  
   - **Security:** Spring Security (Header-based, JWT optional for defense-in-depth)  
   - **SOAP Integration:** Spring Web Services (`WebServiceTemplate`)  
   - **DTO Mapping:** MapStruct  
   - **Boilerplate Reduction:** Lombok 1.18.36  
   - **Secrets Management:** Google Cloud Secret Manager  
   - **Logging:** SLF4J + Logback (JSON structured logging with MDC and `traceId`)  
   - **Monitoring & Metrics:** Spring Boot Actuator, Micrometer, Dynatrace  
   - **Resilience:** Resilience4j  
   - Circuit Breaker: Failure Threshold 50%  
   - Minimum Requests: 10  
   - Wait Duration: 30 seconds  
   - Sliding Window Size: 10 calls  
   - Retry: Max Attempts 3, Exponential Backoff  
   - **Testing:**  
   - Unit Testing: JUnit 5  
   - Mocking: Mockito  
   - Integration Testing: Spring Boot Test, WireMock, MockWebServer  
   - Code Coverage: JaCoCo (≥80%)  
   - **Containerization:** Docker  
   - **Orchestration:** Kubernetes (Deployment, Service, ConfigMap, Secret, HPA)  
   - **API Gateway:** Apigee (Rate Limiting, RBAC, TLS termination)  
   - **Validation:** JSR-380 (Bean Validation)  
   - **Caching:** Spring Cache Abstraction (`@Cacheable`, TTL-based)  
   - **CI/CD:** Jenkins + SonarQube  
   - **Documentation:** OpenAPI 3.0 + Swagger UI  

5. **Project Structure:**

  - **Strictly follow the project hierarchy described above.**
  - Organize code under src/main/java/com/macys/carton/ as follows:
    - model/v1 – Domain entities
    - repository/ – Repository interfaces
    - service/v1 – Business logic and use cases
    - config/ – Configuration classes
    - controller/v1 – REST controllers
    - exception/v1 – Error handling
    - common/ – Shared utilities
    - `utils/` – Utility classes 
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
  - Place versioned controllers in controller/v1/ (e.g., CartonController.java).
  - Place versioned services in service/v1/ (e.g., CartonService.java).
  - Unversioned controllers/services (e.g., MainController, MainService) remain in their respective root folders.
  - Versioning is managed via URI path (e.g., /api/v1/).
  - Endpoints must support distributed tracing, authentication, impersonation, feature flags, and standardized error responses.

7. **Configuration & Deployment:**
  - Provide docker-compose.yml for local infrastructure
  - Include a Dockerfile for the application (Base: openjdk:21-jdk-slim, Port: 8080, Memory: 1Gi, CPU: 500m).
  - Use Maven plugins for code formatting, coverage, and packaging.
  - Provide Kubernetes manifests for cloud deployment - Generate a wrapper-api-deployment.yaml for wrapper-api image

8. **Key Features:**
  - Reactive REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml, strictly follow implementation required for endpoints based on summary & description. Provide actual logic for all endpoints, services, and integrations.

  - For controller implementation, 
    a. Include OpenAPI annotations (@Operation, @ApiResponses, @ApiResponse) for documentation.
    b. Provide method names based on description of functionality being implemented.
    c. Add RequestMapping based on paths </**>  mentioned in input swagger.
    d. Use @RequestHeader and ensure null-safe handling of optional and default headers for all parameters with in as header and details mentioned in input swagger.
    f. Use reactive programming with Project Reactor (Mono, Flux).
  - Place versioned services in service/v1/ 
  - Place versioned models in model/v1/ 
  - Create required models - in model/ folder

  - CartonService: Use Spring WebFlux/WebClient, authentication, and configuration properties. Implement service and utility classes 
  - Implement required services.
     - CartonService 
        - Purpose: Core business logic for retrieving carton details and interacting with the downstream SOAP service.
        - Definition: CartonDetailResponse getCartonDetails(String cartonId, Map<String, String> headers)
        - Functions:
          - Retrieves carton details from the SOAP service.
          - Applies caching (@Cacheable).
          - Handles header extraction and transformation.
     - SoapClientService
       - Purpose: Encapsulates SOAP communication logic using WebServiceTemplate.
       - Definition: CartonDetailResponse getCartonDetails(String cartonId, Map<String, String> headers)
       - Functions: 
         - Constructs and sends SOAP request.
         - Injects custom headers (X-Client-Id, X-Client-Secret, X-Message-Id).
         - Parses SOAP response.
     - SecretManagerService
       - Purpose: Retrieves secrets from Google Cloud Secret Manager.
       - Definition:         
         - String getClientId()
         - String getClientSecret()
       - Functions: 
         - Fetches credentials securely for downstream authentication.
     - CartonMapper (MapStruct Interface)
       - Purpose : Maps SOAP response to DTO.
       - Definition:
         - CartonDetailResponse toCartonDetailResponse(Wedie010SoapResponse source)
       - Functions: 
         - Converts JAXB-generated SOAP response to CartonDetailResponse.
     - TraceIdService
       - Purpose: Manages trace ID generation and propagation for logging.
       - Functions:
         - String getOrGenerateTraceId(HttpServletRequest request)
         - void setTraceIdInMDC(String traceId)
       - Definition:
         - Ensures traceability across logs and requests.
      - ValidationService:
        - Purpose: Performs manual validation beyond JSR-380 annotations.
        - Functions:
          - void validateHeaders(Map<String, String> headers)
          - void validateCartonId(String cartonId)
        - Definition:
          - Validates presence, format, and constraints of headers and path parameters.
      - ResilienceService
        - Purpose: Applies circuit breaker and retry logic programmatically.
        - Functions: <T> T executeWithResilience(Supplier<T> supplier)
        - Definition:
          - Wraps SOAP call with retry and circuit breaker logic.
   - Implement the following required utilities
     - TraceIdUtils
       - Purpose: Manages trace ID generation and MDC propagation for distributed logging.
       - Functions:         
         - String getOrGenerateTraceId(HttpServletRequest request)
         - void setTraceIdInMDC(String traceId)
         - void clearMDC()
       - Definition:
         - Extracts or generates a traceId.
         - Sets it in the logging context (MDC).
         - Clears MDC after request completion.
     - HeaderUtils
       - Purpose: Extracts and validates required headers from incoming requests.
       - Functions:
         - Map<String, String> extractRequiredHeaders(HttpServletRequest request)
         - void validateRequiredHeaders(Map<String, String> headers)
       - Definition:
         - Ensures all mandatory headers (location, colleagueId, applicationId, etc.) are present and valid.
     - SoapRequestBuilder
       - Purpose: : Constructs the SOAP XML payload for the WEDIE010 service.
       - Functions:
         - String buildSoapRequest(String cartonId, Map<String, String> headers)
       - Definition:
         - Dynamically builds the SOAP envelope using input parameters.     
  





      
      
  

      
            

        
     - SoapHeaderInjector
       - Purpose: Injects custom authentication headers into the SOAP request.
       - Functions: WebServiceMessageCallback getHeaderCallback(String clientId, String clientSecret, String messageId)
       - Definition:
         - Adds X-Client-Id, X-Client-Secret, and X-Message-Id to the SOAP request.
     - ErrorResponseBuilder
       - Purpose: Creates standardized error response DTOs.
       - Functions:
         - ErrorResponse buildErrorResponse(HttpStatus status, String message, String path)
       - Definition:
         Returns a structured error response with timestamp, status, error, message, and path.
     - CacheKeyGenerator
       Purpose: Generates cache keys for carton lookups.
       Functions: String generateCartonCacheKey(String cartonId)
       Definition: Ensures consistent cache key generation for @Cacheable.
     - SanitizationUtils
       Purpose: Sanitizes input strings to prevent XSS and injection attacks.
     - Functions:
       String sanitize(String input)
     - Definition: Applies HTML encoding or uses OWASP Java HTML Sanitizer. 
     


  - Comprehensive JSON logging (GCP compatible, fields: Timestamp, Log Level, Thread Name, Logger Name, Message, Correlation ID/Trace ID, User ID, Request ID).
  - Prometheus metrics and health endpoints via Spring Boot Actuator.
  - Code style enforcement (Google Java Style via formatter plugin). Also add required dependencies in pom.xml.
  - Secure endpoints using JWT or OAuth2 (RSA256, Auth0 JWT, public key validation, token expiration validation).
  - If any Bulk operations, implement them with rate limits and batch size constraints, pagination.
  - Generate utility class for required operations

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
  - Supply environment-specific configuration files for these profiles:
    ci, dev, perf, perf-central1, perf-east4, pilot-prod-east4, prod, prod-central1, prod-east4, qa, uat, local, test, default

11. **Example API Endpoints:**
  - Unversioned: /api/** (MainController, all HTTP methods)
  - Versioned: /api/v1/** (CartonController, all HTTP methods)
  - Health: /actuator/health
  - Metrics: /actuator/prometheus


12. **Integration Requirements:**
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

  - **MUST generate a complete pom.xml file at the root level of the project, using Spring Boot 3.x, Java 21, and all dependencies used during project generation**

16. **Endpoint Implementation:**
   - Implement `GET /v1/carton/{cartonId}` with all required headers.
   - Validate headers and path parameters using JSR-380 annotations.
   - Return a fully populated `CartonDetailResponse` DTO.

17. **SOAP Integration:**
   - Use `WebServiceTemplate` to call WEDIE010 SOAP service.
   - Inject custom headers (`X-Client-Id`, `X-Client-Secret`, `X-Message-Id`) from Google Cloud Secret Manager.

18. **Security:**
   - Use Spring Security for defense-in-depth.
   - Secure secrets via Google Cloud Secret Manager.
   - Sanitize inputs to prevent XSS and injection attacks.

19. **Resilience:**
   - Use Resilience4j for circuit breaker and retry.
   - Configure thresholds and backoff strategies.

20. **Caching:**
   - Use Spring Cache abstraction with `@Cacheable`.
   - TTL-based eviction policy.

21. **Logging & Monitoring:**
   - Use Logback for structured JSON logging.
   - Include traceId, timestamp, thread name, log level.
   - Use Micrometer + Dynatrace for metrics and alerting.

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
