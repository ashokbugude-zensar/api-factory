Given a detailed OpenAPI 3.0 YAML, a .md file containing business and technical specifications for the apis, generate a complete, production-ready, cloud-native Java Spring application with all implementations that meets the following requirements:

**Input Extraction**
  Treat the input document as source of truth for:
  - Technologies & versions, architectural style, package naming
  - Endpoints, headers, methods, parameters, DTOs, validation rules
  - Security (authN/Z, headers, mTLS), secret management
  - Logging/observability/metrics/tracing
  - Resilience (timeouts, retries, circuit breakers, fallbacks)
  - Caching, performance, rate limiting expectations
  - Profiles, environment-specific configuration and deployment targets
  - Testing frameworks, tools, coverage thresholds
  - Documentation depth and delivery

**Output**
- The generated code should include:
   - Controller Layer: REST endpoints that expose the wrapper API.
   - Service Layer: Logic to invoke SOAP operations using the WSDL.
   - DTOs: Data Transfer Objects for request and response mapping.
   - SOAP Client Configuration(if mentioned in inputs): Using JAX-WS, Spring Web Services, or another suitable library.
   - Exception Handling: Standardized error responses and SOAP fault translation.
   - Logging: Integration with a reusable logging component.
   - Health, Liveliness, and Readiness Endpoints: For observability and Kubernetes compatibility.
   - API Documentation: Swagger/OpenAPI annotations.
   - Environment Profiles: Support configurations as described below.
   - Unit Tests: Basic test cases for controller and service layers.

**Selected Technology and Configuration (all are mandatory):**
- Identify all the technologies, integrations, frameworks, configurations, versions from inputs
    - For each of the technologies, integrations, frameworks, configurations, versions identified
    - create all the configuration files, util files.
    - From inputs based on techinal, business requirements, identify the files where these need to be integrated.
        - For each of the files implemented above
        - Integrate the files with the identified technologies, integrations, frameworks, configurations, versions
        - Ensure implementation is complete with actual logic implemented.

**Output Format:**
- Identify the value of **Base Package Name** from the input file as <base_package_name>
- Output the entire application as a single JSON object.
- Each key in the JSON object must be the relative file path (e.g., "<base_package_name>/controller/v1/<controller_file_name>.java").
- Each value must be the complete content of that file as a string.
- Do not include any extra text, explanations, or markdown—only the JSON object.
- **MUST generate every file and implement every requirement described in inputs**
- **MUST include all top-level files such as .gitignore and pom.xml in the output.**
- Example:
  {
    "src/main/java/<base_package_name>/controller/v1/<controller_file_name>.java": "package com.<base_package_name>.controller.v1;\n// ...rest of the code...",
    "pom.xml": "<project>...</project>",
    "docker/Dockerfile": "FROM openjdk:21-jdk-slim\n..."
  }


**Project Structure:**
- Identify the value of **Base Package Name** from the input file as <base_package_name>
- All Java source code must be organized under <base_package_name> from the input file 
- It should follow the project structure as descibed user **Package Structure** section from the input file.

- Indentify all the profiles listed in the **Deployment** section in input.(Eg dev, qa)
- **Generate files in `application-`<profile_item>`.properties` format in `src/main/resources/` folder for each of the profiles identified above.(Mandatory)**. (Eg application-dev.properties)
- Identify Code style configuration xml files from the **Code Formatting** section in the input as <xml_files>(Eg eclipse-java-google-style.xml)
- Top-level files and folders:
  - `pom.xml` – Maven configuration file (Spring Boot, Java, all dependencies as per selections below)
  - `k8s-manifests/` – Kubernetes deployment yamls (Eg iwm-items-list-eapi-deployment.yaml).
  - `docker/` – Docker files (Base: openjdk:21-jdk-slim, Port: 8080, Health: /actuator/health, Memory: 1Gi, CPU: 500m)
  - `README.md` – Project documentation with Swagger url
  - `.gitignore` – Git ignore file
  - `formatter/codeStyles/` – Code style configuration( Eg eclipse-java-google-style.xml). Generate them
  - `MainApplication.java` – Main entry point

**Requirements:**
1. **No Placeholders:**
   - All code must be fully implemented.
   - Do not use any placeholders, stubs, or "TODO" comments.
   - Provide actual logic for all endpoints, services, and integrations, inferring reasonable behavior where not specified.

2. **General Requirements:**
  - The application must be enterprise-grade, reactive, and highly observable.
  - If endpoint logic is not specified, infer and implement logic based on the endpoint summary.
  - Every requirement below must be strictly followed.

3. **Technology Stack:**
   - As mentioned above

4. **Project Structure:**
   - As mentioned above

5. **Controller & Service Versioning:**
  - Place versioned controllers in controller/v1/ 
  - Place versioned services in service/v1/
  - Implement Input validation, Header validation, structured logging, authetication, authorization. Refer input data
  - Unversioned controllers/services (e.g., MainController, MainService) remain in their respective root folders.
  - Versioning is managed via URI path (e.g., /api/v1/).
  - Endpoints must support distributed tracing, JWT authentication, impersonation, feature flags, and standardized error responses.
  - Follow the paths as mentioned in inputs

6. **Example API Endpoints:**
  - Unversioned: /api/** (MainController, all HTTP methods)
  - Versioned: /api/v1/** (other Controllers, all HTTP methods)
  - Health: /actuator/health
  - Metrics: /actuator/prometheus

7. **Endpoint Implementation:**
   - Validate headers and path parameters using JSR-380 annotations.
   - Return a fully populated response.

8. **Key Features:**
  - Reactive REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml, strictly follow implementation required for endpoints based on summary & description. Provide actual logic for all endpoints, services, and integrations.
  - For controller implementation, 
    a. Include OpenAPI annotations (@Operation, @ApiResponses, @ApiResponse) for documentation.
    b. Provide method names based on description of functionality being implemented.
    c. Add RequestMapping based on paths </**>  mentioned in input swagger.
    d. Use @RequestHeader and ensure null-safe handling of optional and default headers for all parameters with in as header and details mentioned in input swagger.
  - Place versioned services in service/v1/ 
  - Place versioned models in model/v1/ 
  - Create required models
  - Create all required services
  - Comprehensive JSON logging (GCP compatible, fields: Timestamp, Log Level, Thread Name, Logger Name, Message, Correlation ID/Trace ID, User ID, Request ID).
  - Secure endpoints using technologies mentioned in input.
  - Prometheus metrics and health endpoints via Spring Boot Actuator.
  - Code style enforcement (Google Java Style via formatter plugin). Also add required dependencies in pom.xml.
  - If any Bulk operations, implement them with rate limits and batch size constraints, pagination.
  - Generate utility class for required operations

9. **Swagger/OpenAPI Documentation:**
   - Ensure the application includes complete Swagger/OpenAPI documentation.  
   - Configure Springdoc OpenAPI (2.1.0) so that the generated API is fully documented and accessible at `/swagger-ui.html`.

10. **Integration Requirements:**
  - Distributed Tracing: Add a WebFilter to extract Trace-Id and propagate via MDC for logging/tracing.
  - Custom Logging: Configure Logback for JSON logging and MDC context propagation.
  - Exception Handling: Implement global exception handlers and custom exceptions for integration errors.
  - Configuration Management: Use application properties and environment-specific files for all endpoints, credentials, and feature flags.

11. **Exception Handling:**
  - Use custom exceptions, domain exceptions (e.g.,ValidationException, ResourceNotFoundException, ExternalServiceException, ForbiddenException) extending RuntimeException for domain-specific errors and other exceptions as mentioned in input.
  - Implement all the exceptions identified in the input.
  - Throw exceptions in service/utility classes to signal error conditions.
  - Implement a global exception handler using @RestControllerAdvice and @ExceptionHandler to return structured error responses (fields: timestamp, status, error, message, path).
  - Handle integration errors with meaningful messages and propagate to the global handler.
  - Wrap checked exceptions in runtime exceptions for reactive flows.
  - Log exception details in custom JSON logs for traceability.
  - Return appropriate HTTP status codes in error responses (200, 201, 400, 401, 403, 404, 500).
  - Generate custom exception classes, a global exception handler, and ensure all service/utility classes throw and handle exceptions as described.

12. **Logging & Monitoring Configuration:**
  - Add Logback configuration files (`logback-access-spring.xml`, `logback.xml`) for JSON logging in `src/main/resources/`.
  - Ensure the Logback configuration is included in the Maven build (pom.xml) and supports environment-based configuration management.
  - Metrics: Request count per endpoint, Response time distribution, Error rate by endpoint, External service call duration, Circuit breaker status.


13. **Logging & Monitoring:**
    - Use Logging Framework from Input file and implement end to end by creating required config, util files
    - Implement Request/Response Logging with performance tracking
    - Logback JSON structured logging including: timestamp, level, thread, logger, message, traceId, user id (if applicable), request id, correlation Id
    - Trace propagation: MDC via TraceIdService/TraceIdUtils and a reactive WebFilter.

14. **Security:**
   - Implement all the required classes and implement it.
   - Use Spring Security for defense-in-depth.
   - Secure secrets via Google Cloud Secret Manager.Create required classes and implement end to end
   - Sanitize inputs to prevent XSS and injection attacks.
   - TLS termination at Apigee; app communicates over TLS to upstream where applicable.

15. **Secret Management:**
    - Identify the secret mangements from the input file
    - For the identified technology, implement required files , conig, utils, envs etc
    - Use secret management with end to end implementation using inputs from the file 

16. **Google Cloud Secret Manager:** (if mentioned in input)
    - Provide the following
     - Bootstrap property configurations
     - Multi-environment secret handling
     - Production-ready security

    - Create required config, util, ev files etc
    - Implement in codebase wherever applicable

17. **SOAP Integration:** (if mentioned in input)
   - Create required service with implementation and ensure its called.
   - Use `WebServiceTemplate` to call SOAP service.
   - Build SOAP request dynamically; map relevant header values into the SOAP body per service contract.
   - Inject custom headers (`X-Client-Id`, `X-Client-Secret`, `X-Message-Id`) from Google Cloud Secret Manager.
   - Parse WSDL input to identify operations, input/output schemas, and endpoints
   - Ensure proper request/response transformation between REST and SOAP formats
   Upstream Endpoint Resolution:
   Base URL comes from configuration (Apigee) and may vary by division. Example property pattern: wedie010.targethost.<division> supplied per environment profile.
   - Ensure Soap Service is called from one of the services based on business/tech requirements from input.

18. **Resilience:**
    - Use Resilience4j Circuit Breaker and Retry with thresholds listed in input or above.
    - Wrap SOAP calls with ResilienceService.
    - Configure timeouts; implement meaningful error mapping on faults.

19. **Caching:**
    - Use caching if mentioned in inputs
    - Provide TTL-based eviction via cache manager configuration.
    - Deterministic keys from CacheKeyGenerator.

20. **Configuration & Deployment:**
  - Provide docker-compose.yml for local infrastructure 
  - Include a Dockerfile for the application (params mentioned in input).
  - Use Maven plugins for code formatting, coverage, and packaging.
  - Provide Kubernetes manifests for cloud deployment - Generate a yaml for corresponding  image

21. **k8s-manifests Generation**
   - Identify all the **Kubernetes Manifests** files listed in the **Deployment** section in input. (Eg iwm-items-list-eapi-deployment.yaml) including non-yaml files
   - Generate the identified Kubernetes Manifest Files in the `k8s-manifests/` folder in root directory

22. **Deployment:**
    - Dockerize the application.
    - Provide Kubernetes manifests.
    - CI/CD pipeline via Jenkins.

23. **Progressive Generation Steps:**
  - Scaffold the Maven project with all dependencies and plugins.
  - Generate the folder structure and sample classes/interfaces for each layer.
  - Add Docker and Docker Compose files for local infrastructure.
  - Implement REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml. Provide actual logic for all endpoints, services, and integrations.
  - Add monitoring, metrics configuration.
  - Generate OpenAPI documentation.
  - Provide a README with setup, usage, and architecture overview. 

24. **Testing:**
   - Use JUnit 5, Spring Boot Test, WireMock.
   - Achieve 80%+ code coverage with JaCoCo.

25. **.gitignore, pom.xml, Readme.md, DockerFile:**
  - **MUST generate a complete .gitignore file at the root level of the project.**

  - **MUST generate a complete pom.xml file at the root level of the project, using Spring Boot, Java with versions mentioned in input and all dependencies used during project generation**

  - **MUST generate a complete Readme.md file at the root level of the project.It should include all possible analytics about the project**
  
  -- **MUST generate a complete DockerFile file at the root level of the project.**

26. **Additional Requirements:**
  - Include a formatter configuration file (Google Java Style XML).
  - Provide Kubernetes manifests for deployment.

**Important Note:**  
   - Do not use any placeholders, stubs, or "TODO" comments. All code must be fully implemented and production-ready. Do not leave any part for the user to implement.
   - **Strictly follow the directory hierarchy and file structure mentioned above**
   - **All versions, dependencies, and architectural choices must match the selections listed at the top of this.**