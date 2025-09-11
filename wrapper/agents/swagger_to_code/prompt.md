Given a detailed OpenAPI 3.0 YAML, input files for the Carton API Wrapper, generate a complete, production-ready, cloud-native Java application that meets the following requirements:

**Selected Technology and Configuration (all are mandatory):**
As specified in input

**Output Format:**
- Output the entire application as a single JSON object.
- Each key in the JSON object must be the relative file path.
- Each value must be the complete content of that file as a string.
- Do not include any extra text, explanations, or markdown—only the JSON object.
- **MUST generate every file and implement every requirement described in inputs.**
- **MUST include all top-level files**

**Project Structure:**
As specified in input

**Requirements:**
1. **No Placeholders:**
   - All code must be fully implemented.
   - Do not use any placeholders, stubs, or "TODO" comments.
   - Provide actual logic for all endpoints, services, and integrations, inferring reasonable behavior where not specified.

2. **Swagger/OpenAPI Documentation:**
   - Ensure the application includes complete Swagger/OpenAPI documentation.  
   - Configure Springdoc OpenAPI  so that the generated API is fully documented and accessible at `/swagger-ui.html`.


3. **General Requirements:**
  - The application must be enterprise-grade, reactive, and highly observable.
  - If endpoint logic is not specified, infer and implement logic based on the endpoint summary.
  - Every requirement below must be strictly followed.

4. **Technology Stack:**
    - As specified in input

5. **Project Structure:**

  - **Strictly follow the project hierarchy as specified in input.**
    - k8s-manifests/ - Organize deployment yamls at the top level of the repository
    - docker/ - Organize all docker files at the top level of the repository 
    - ReadMe.md at the top level of the repository
    - DockerFile at the top level of the repository
    
6. **Controller & Service Versioning:**
  - Endpoints must support distributed tracing, authentication, impersonation, feature flags, and standardized error responses.


7. **Key Features:**
  - Reactive REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml, strictly follow implementation required for endpoints based on summary & description. Provide actual logic for all endpoints, services, and integrations.


8. **Progressive Generation Steps:**
  - Scaffold the Maven project with all dependencies and plugins.
  - Generate the folder structure and sample classes/interfaces for each layer.
  - Add Docker and Docker Compose files for local infrastructure.
  - Implement REST API for all endpoints, headers, resource model, response code as listed in input swagger yaml. Provide actual logic for all endpoints, services, and integrations.
  - Add monitoring, metrics, and alerting configuration.
  - Generate OpenAPI documentation.
  - Provide a README with setup, usage, and architecture overview.

9. **Additional Requirements:**
  - Include a formatter configuration file (Google Java Style XML).


10. **Exception Handling:**
  - Use custom exceptions  extending RuntimeException for domain-specific errors.
  - Throw exceptions in service/utility classes to signal error conditions.
  - Implement a global exception handler using @RestControllerAdvice and @ExceptionHandler to return structured error responses (fields: timestamp, status, error, message, path).
  - Handle integration errors with meaningful messages and propagate to the global handler.
  - Wrap checked exceptions in runtime exceptions for reactive flows.
  - Log exception details in custom JSON logs for traceability.
  - Return appropriate HTTP status codes in error responses (200, 201, 400, 401, 403, 404, 500).
  - Generate custom exception classes, a global exception handler, and ensure all service/utility classes throw and handle exceptions as described.



11. **.gitignore, pom.xml, dockerFile:**
  - **MUST generate a complete .gitignore file at the root level of the project.**
  - **MUST generate a complete .pom.xml file at the root level of the project.**
  - **MUST generate a complete .dockerFile file at the root level of the project.**

**Important Note:**  
   - Do not use any placeholders, stubs, or "TODO" comments. All code must be fully implemented and production-ready. Do not leave any part for the user to implement.
   - **Strictly follow the directory hierarchy and file structure mentioned above**
   - **All versions, dependencies, and architectural choices must match the selections listed at the top of this.**

