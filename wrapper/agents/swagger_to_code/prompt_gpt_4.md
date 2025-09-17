Given a detailed OpenAPI 3.0 YAML and xlsx file with technical anf business requirements for the API mentioned in requirements, generate a complete, production-ready, cloud-native Java application.

**Input**
- OpenAPI Spec 3.0 Yaml file.
- Xlsx file with technical and business requirements.

**Input Xlsx Data Processing**
- Refer to 'Questions', 'Answer', 'Remarks for Answer Selected' columns column for all needed details. 
- The 'Questions' column contains the question for a particular reuqirement
- The 'Answer' column contains the requirement that should be implemented
- The 'Remarks for Answer Selected' contains the remarks/reason for selected requirement

**Rquirements**
The application **MUST** meet the following requirements

1. **General Requirements:**
  - The application must be enterprise-grade, reactive, and highly observable.
  - It should support integrations with external systems as per inputs and implement all business logic.
  - If endpoint logic is not specified, infer and implement logic based on the endpoint summary.
  - Every requirement below must be strictly followed.
  - Use file paths, names from the inputs.
  - Indetify and implement all required integrations and provide suitable config files by referring the input docs.

2. **Output Format**
   - Output the entire application as a single JSON object.
   - Each key in the JSON object must be the relative file path with paths as mentioned in input doc.
   - Each value must be the complete content of that file as a string.
   - Do not include any extra text, explanations, or markdown—only the JSON object.
   - **MUST generate every file and implement every requirement described in input doc and all points below.**
   - **MUST include all top-level files such as DockerFile, .gitignore, pom.xml, Readme.md, .gitlab-ci.yml in the output with contents as described in input files.**

3. **Selected Technology and Configuration (all are mandatory):**
   - The application **must** be impelemented only using Technologies and versions mentioned in the input doc.

4. **Project Structure:**
  - All Java source code must be organized under path as mentioned in input as follows:
    - `config/` – Configuration classes
    - `controller/v1/` – REST controllers (versioned)
    - `exception/` – Error handling
    - `model` – Domain entities
    - `repository/` – Repository interfaces
    - `service/v1/` – Business logic and use cases (versioned)
    - `common/` – Shared utilities
    - `utils/` – Utility classes (All utility classes)
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
    - `pom.xml` – Maven configuration file with all configurations as mentioned in input
    - `k8s-manifests/` – Kubernetes deployment yamls
    - `docker/` – Docker files with all the info as mentioned in input
    - `README.md` – Project documentation and analytics (generate) related to codebase
    - `.gitignore` – Git ignore file
    - `formatter/codeStyles/` – Code style configurations as mentioned in input

5. **No Placeholders:**
   - All code must be fully implemented as per inputs.
   - If implementation details not metioned in input doc, provide a sutiable implementations with required integrations.
   - Do not use any placeholders, stubs, or "TODO" comments.
   - Provide actual logic for all endpoints, services, and integrations, inferring reasonable behavior where not specified.

6. **Swagger/OpenAPI Documentation:**
   - Ensure the application includes complete Swagger/OpenAPI documentation.  
   - Configure Springdoc OpenAPI (2.1.0) so that the generated API is fully documented and accessible at `/swagger-ui.html`.

7. **Controller & Service Versioning:**
  - Place versioned controllers in controller/v1/
  - Place versioned services in service/v1/
  - Unversioned controllers/services (e.g., MainController, MainService) remain in their respective root folders.
  - Versioning is managed via URI path (e.g., /api/v1/).
  - Endpoints must support input validations, distributed tracing, authentication, impersonation, feature flags, and standardized error responses.

8. **Configuration & Deployment:**
  - Provide docker-compose.yml for local infrastructure
  - Include a Dockerfile for the application
  - Use Maven plugins for code formatting, coverage, and packaging.
  - Provide Kubernetes manifests for cloud deployment - generate required yaml files for images

9. **Key Features:**
  - Reactive REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml, strictly follow implementation required for endpoints based on summary & description. Provide actual logic for all endpoints, services, and integrations.
  - For controller implementation, 
    a. Include OpenAPI annotations (@Operation, @ApiResponses, @ApiResponse) for documentation.
    b. Provide method names based on description of functionality being implemented.
    c. Add RequestMapping based on paths </**>  mentioned in input swagger.
    d. Use @RequestHeader and ensure null-safe handling of optional and default headers for all parameters with in as header and details mentioned in input swagger.
  - Place versioned services in service/v1/
  - Create required models 
  - Create all services as mentioned in inputs with naming conventions and provide complete implementations
  - Crete all required utilities in utils folder.
  - Comprehensive JSON logging (GCP compatible, fields: Timestamp, Log Level, Thread Name, Logger Name, Message, Correlation ID/Trace ID, User ID, Request ID).
  - Prometheus metrics and health endpoints via Spring Boot Actuator.
  - Code style enforcement (as mentioned in inputs). Also add required dependencies in pom.xml.
  - If any Bulk operations, implement them with rate limits and batch size constraints, pagination.
  - Impelement all required authentication as mentioned in inputs.

10. **Progressive Generation Steps:**
  - Scaffold the Maven project with all dependencies and plugins.
  - Generate the folder structure and sample classes/interfaces for each layer.
  - Add Docker and Docker Compose files for local infrastructure.
  - Implement REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml. Provide actual logic for all endpoints, services, and integrations.
  - Add monitoring, metrics, and alerting configuration.
  - Generate OpenAPI documentation.
  - Provide a README with setup, usage, and architecture overview. Include all possible analytics about the codebase
    

11. **Additional Requirements:**
  - Include a formatter configuration file as described in inputs
  - Provide Kubernetes manifests for deployment.
  - Supply environment-specific configuration files for all profiles mentioned in input

12. **Example API Endpoints:**
  - Unversioned: /api/** (MainController, all HTTP methods)
  - Versioned: /api/v1/** (all HTTP methods)
  - Health: /actuator/health
  - Metrics: /actuator/prometheus

13. **Integration Requirements:**
  - Distributed Tracing: As described in inputs
  - Custom Logging: As described in inputs
  - Exception Handling: Implement global exception handlers and custom exceptions for integration errors.
  - Configuration Management: Use application properties and environment-specific files for all endpoints, credentials, and feature flags.


14. **Exception Handling:**
  - Use custom exceptions, domain-specific exceptions as mentioned in inputs.
  - Throw exceptions in service/utility classes to signal error conditions.
  - Implement a global exception handler using @RestControllerAdvice and @ExceptionHandler to return structured error responses (fields: timestamp, status, error, message, path).
  - Handle integration errors with meaningful messages and propagate to the global handler.
  - Wrap checked exceptions in runtime exceptions for reactive flows.
  - Log exception details in custom JSON logs for traceability.
  - Return appropriate HTTP status codes in error responses (200, 201, 400, 401, 403, 404, 500).
  - Generate custom exception classes, a global exception handler, and ensure all service/utility classes throw and handle exceptions as described.

15. **Logging & Monitoring Configuration:**
  - Indentify the logging library from input doc, create config files, implementation(request/esponse logging, performance tracking etc).  
  - Add Logback configuration files (`logback-access-spring.xml`, `logback.xml`) for JSON logging in `src/main/resources/`.
  - Ensure the Logback configuration is included in the Maven build (pom.xml) and supports environment-based configuration management.
  - Metrics: Request count per endpoint, Response time distribution, Error rate by endpoint, External service call duration.
  - Use Logging Framework from Input file and implement end to end
  - Create all required classes and ensure its called
  - Logback JSON structured logging including: timestamp, level, thread, logger, message, traceId, user id (if applicable), request id.
  - Trace propagation: MDC via TraceIdService/TraceIdUtils and a reactive WebFilter.
  

16. **.gitignore, pom.xml, Readme.md, DockerFile:**
  - **MUST generate a complete .gitignore file at the root level of the project.**
  - **MUST generate a complete pom.xml file at the root level of the project, using Spring Boot, Java with versions mentioned in input and all dependencies used during project generation**
  - **MUST generate a complete Readme.md file at the root level of the project.It should include all possible analytics about the project**
  -- **MUST generate a complete DockerFile file at the root level of the project.**


17. **Endpoint Implementation:**
   - Validate headers and path parameters using JSR-380 annotations.
   - Return a fully populated response.

18. **SOAP Integration:**
   - Create required service with implementation and ensure its called.
   - Use `WebServiceTemplate` to call SOAP service.
   - Build SOAP request dynamically; map relevant header values into the SOAP body per service contract.
   - Inject custom headers (`X-Client-Id`, `X-Client-Secret`, `X-Message-Id`) from Google Cloud Secret Manager.
   - Parse WSDL input to identify operations, input/output schemas, and endpoints
   - Ensure proper request/response transformation between REST and SOAP formats

   Upstream Endpoint Resolution:
   Base URL comes from configuration (Apigee) and may vary by division. Example property pattern: wedie010.targethost.<division> supplied per environment profile.

19. **Security:**
   - Implement all the Congig, required classes as per inputs and implement it.
   - Use Spring Security for defense-in-depth.
   - Secure secrets via Google Cloud Secret Manager.Create required classes and implement end to end
   - Sanitize inputs to prevent XSS and injection attacks.
   - TLS termination at Apigee; app communicates over TLS to upstream where applicable.

20. **Resilience:**
    - Use Resilience4j Circuit Breaker and Retry with thresholds listed above.
    - Wrap SOAP calls with ResilienceService.
    - Configure timeouts; implement meaningful error mapping on faults.

21. **Caching:**
    - Use Spring Cache (@Cacheable) for carton lookups by UCCID.
    - Provide TTL-based eviction via cache manager configuration.
    - Deterministic keys from CacheKeyGenerator.

    
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