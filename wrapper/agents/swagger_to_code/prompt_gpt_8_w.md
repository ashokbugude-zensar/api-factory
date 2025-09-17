Given a detailed OpenAPI 3.0 YAML, business and technical specification for the Carton API Wrapper, generate a complete, production-ready, cloud-native Java application that meets the following requirements:

**Input Extraction**
- If provided csv/xlsx file
  - Refer to Questions, Answer, Remark column for all needed details,. Each sections details , standards mentioned should be covered implementation. Project structure should be as per mentioned in csv. Have check if you have covered all testing, deployment, naming, observability, technology, authentication, request response, validation, dto, pipeline, logging, documentation standards and requirements mentioned. 
  - IMplement each and everything mentioned here

**Selected Technology and Configuration (all are mandatory):**
- Use from inputs and implement all of them end to end
- create all required files with implementation

**Output Format:**
- Output the entire application as a single JSON object.
- Each key in the JSON object must be the relative file path (e.g., "src/main/java/com/macys/carton/controller/v1/CartonController.java").
- Each value must be the complete content of that file as a string.
- Do not include any extra text, explanations, or markdown—only the JSON object.
- **MUST generate every file and implement every requirement described in inputs**
- **MUST include all top-level files such as .gitignore and pom.xml in the output.**
- Example:
  {
    "src/main/java/com/demo/user/controller/v1/UsersysController.java": "package com.demo.user.controller.v1;\n// ...rest of the code...",
    "pom.xml": "<project>...</project>",
    "docker/Dockerfile": "FROM openjdk:21-jdk-slim\n..."
  }


**Project Structure:**
- The generated code should include:
   - Controller Layer: REST endpoints that expose the wrapper API.
   -  Service Layer: Logic to invoke SOAP operations using the WSDL.
   -  DTOs: Data Transfer Objects for request and response mapping.
   -  SOAP Client Configuration: Using JAX-WS, Spring Web Services, or another suitable library.
   - Exception Handling: Standardized error responses and SOAP fault translation.
   - Logging: Integration with a reusable logging component.
   - Health, Liveliness, and Readiness Endpoints: For observability and Kubernetes compatibility.
   - API Documentation: Swagger/OpenAPI annotations.
   - Environment Profiles: Support for dev, test, and prod configurations.
   - Unit Tests: Basic test cases for controller and service layers. 

- Top-level files and folders:
  - `pom.xml`
  - `docker/Dockerfile`
  - `k8s-manifests/` (Deployment, Service, ConfigMap, Secret, HPA)
  - `.gitignore`
  - `README.md` (All possible info and analytics about codebase)
  - formatter/codeStyles/ (Google Java Style XML)

- Profiles (supply env-specific configuration files):
ci, dev, perf, perf-central1, perf-east4, pilot-prod-east4, prod, prod-central1, prod-east4, qa, uat, local, test, default

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
   - As mentioned in inputs 

5. **Project Structure:**

  - **Strictly follow the project hierarchy described above.**

6. **Controller & Service Versioning:**
  - Follow the paths as mentioned in inpurs
  - Endpoints must support distributed tracing, authentication, impersonation, feature flags, and standardized error responses.

7. **Configuration & Deployment:**
  - Provide docker-compose.yml for local infrastructure
  - Use Maven plugins for code formatting, coverage, and packaging.
  - Provide Kubernetes manifests for cloud deployment 

8. **Key Features:**
  - Reactive REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml, strictly follow implementation required for endpoints based on summary & description. Provide actual logic for all endpoints, services, and integrations.


  - For controller implementation, 
    a. Include OpenAPI annotations (@Operation, @ApiResponses, @ApiResponse) for documentation.
    b. Provide method names based on description of functionality being implemented.
    c. Add RequestMapping based on paths </**>  mentioned in input swagger.
    d. Use @RequestHeader and ensure null-safe handling of optional and default headers for all parameters with in as header and details mentioned in input swagger.
  - Prometheus metrics and health endpoints via Spring Boot Actuator.
  - Code style enforcement (Google Java Style via formatter plugin). Also add required dependencies in pom.xml.
  - If any Bulk operations, implement them with rate limits and batch size constraints, pagination.
  - Generate utility class for required operations

9. **Progressive Generation Steps:**
  - Scaffold the Maven project with all dependencies and plugins.
  - Generate the folder structure and sample classes/interfaces for each layer.
  - Add Docker and Docker Compose files for local infrastructure.
  - Implement REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml. Provide actual logic for all endpoints, services, and integrations.
  - Add monitoring, metrics, and alerting configuration.
  - Generate OpenAPI documentation.
  - Provide a README with setup, usage, and architecture overview. Include all possible analytics about the codebase
    

10. **Additional Requirements:**
  - Include a formatter configuration file (Google Java Style XML).
  - Provide Kubernetes manifests for deployment.
  - Supply environment-specific configuration files for profiles in input

11. **Integration Requirements:**
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
  - Ensure the Logback configuration is included in the Maven build (pom.xml) and supports environment-based configuration management.
  

15. **.gitignore, pom.xml, Readme.md, DockerFile:**
  - **MUST generate a complete .gitignore file at the root level of the project.**

  - **MUST generate a complete pom.xml file at the root level of the project, using Spring Boot, Java with versions mentioned in input and all dependencies used during project generation**

  - **MUST generate a complete Readme.md file at the root level of the project.It should include all possible analytics about the project**
  
  -- **MUST generate a complete DockerFile file at the root level of the project.**


16. **Endpoint Implementation:**
   - Validate headers and path parameters using JSR-380 annotations.
   - Return a fully populated response.

17. **SOAP Integration:**
   - Create required service with implementation and ensure its called.
   - Use `WebServiceTemplate` to call SOAP service.
   - Build SOAP request dynamically; map relevant header values into the SOAP body per service contract.
   - Inject custom headers (`X-Client-Id`, `X-Client-Secret`, `X-Message-Id`) from Google Cloud Secret Manager.
   - Parse WSDL input to identify operations, input/output schemas, and endpoints
   - Ensure proper request/response transformation between REST and SOAP formats



   Upstream Endpoint Resolution:
   Base URL comes from configuration (Apigee) and may vary by division. Example property pattern: wedie010.targethost.<division> supplied per environment profile.

18. **Security:**
   - Implement all the required classes and implement it.
   - Use Spring Security for defense-in-depth.
   - Secure secrets via Google Cloud Secret Manager.Create required classes and implement end to end
   - Sanitize inputs to prevent XSS and injection attacks.
   - TLS termination at Apigee; app communicates over TLS to upstream where applicable.

19. **Resilience:**
    - Use Resilience4j Circuit Breaker and Retry with thresholds listed above.
    - Wrap SOAP calls with ResilienceService.
    - Configure timeouts; implement meaningful error mapping on faults.

20. **Caching:**
    - Use Spring Cache (@Cacheable) for carton lookups by UCCID.
    - Provide TTL-based eviction via cache manager configuration.
    - Deterministic keys from CacheKeyGenerator.

21. **Logging & Monitoring:**
    - Use Logging Framework from Input file and implement end to end
    - Create all required classes and ensure its called
    - Logback JSON structured logging including: timestamp, level, thread, logger, message, traceId, user id (if applicable), request id.
    - Trace propagation: MDC via TraceIdService/TraceIdUtils and a reactive WebFilter.
    
22. **Testing:**
   - Use JUnit 5, Spring Boot Test, WireMock.
   - Achieve 80%+ code coverage with JaCoCo.

23. **Deployment:**
    - Dockerize the application.
    - Provide Kubernetes manifests.
    - CI/CD pipeline via Jenkins.

24. **Secret Management:**
    - Use secret management with end to end implementation using inputs from the file 

**Important Note:**  
   - Do not use any placeholders, stubs, or "TODO" comments. All code must be fully implemented and production-ready. Do not leave any part for the user to implement.
   - **Strictly follow the directory hierarchy and file structure mentioned above**
   - **All versions, dependencies, and architectural choices must match the selections listed at the top of this.**