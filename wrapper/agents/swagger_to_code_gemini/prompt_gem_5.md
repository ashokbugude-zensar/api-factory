# Enterprise API Generation Prompt for Gemini

**Objective**: Generate a complete, production-ready, cloud-native Spring Boot application based on a detailed technical specification (`*.md` or similar) and an OpenAPI 3.0 specification (`*.yaml`). The generated code must be enterprise-grade, fully compliant with all specified requirements, and delivered as a single JSON object containing all project files.

---
## 1. Core Inputs

You will be provided with two primary documents:
1.  **Technical Specification (`.md` file):** This is the **primary source of truth**. It contains detailed requirements for every aspect of the project. You must parse this document to find all specifications, including but not limited to:
    *   Architecture, Technology Stack, and specific library Versions
    *   Project Structure, Base Package Name, and File Paths
    *   Security (Secret Management, mTLS)
    *   Resilience Patterns (Circuit Breakers, Retries, Fallbacks)
    *   Logging (Format, Fields), Metrics, Tracing, and Observability
    *   Caching Strategy (Library, TTLs)
    *   Testing Requirements (Frameworks, Coverage goals, Exclusions)
    *   Deployment (CI/CD pipeline definitions)
    *   A pre-defined list of Maven Dependencies
    *   A list of Notes and Assumptions for any missing details
    *   Code Formatting and Style Guides
2.  **OpenAPI 3.0 Specification (`.yaml` file):** This defines the public REST contract, including:
    *   Endpoints, HTTP Methods, and Paths
    *   Request/Response DTOs and Schemas
    *   Parameters (Path, Header, Query)
    *   Status Codes and Example Payloads

> **Golden Rule:** If there is a conflict between the two documents, the **Technical Specification (`.md` file) always overrides** the OpenAPI spec. If a requirement is missing from both, implement a sensible, modern enterprise default and document it in the `README.md`. **Do not leave any placeholders or "TODO" comments.**
> **Assumption Rule:** If the technical specification includes a "Notes and Assumptions" section, you **MUST** follow the guidance provided there when implementing features with missing details.

---
## 2. Strict Output Format

Your entire response **MUST** be a single, raw JSON object.
-   **Keys** must be the full, relative file paths (e.g., `src/main/java/com/macys/iwm/controller/v1/DirectToStoreController.java`).
-   **Values** must be the complete file content as a string.
-   **Do not** wrap the JSON in ```json ... ``` code fences or add any explanatory text outside the JSON structure.

**Example Structure:**
```json
{
  "pom.xml": "<project>...</project>",
  "README.md": "# Project Title\n...",
  "src/main/java/com/macys/iwm/Application.java": "package com.macys.iwm;\n...",
}
```

---
## 3. Core Implementation Requirements

### 3.1. Project & Build
-   **Project Name & Base Package:** Use the exact names specified in the `.md` spec. Do not change the base package name 
-   **Build Tool:** Generate a `pom.xml` (or other build file as specified). Use the explicit list of dependencies from the "Maven Dependencies" section of the `.md` spec. Ensure all plugins for build, testing, code coverage, and formatting are also included as defined.
-   **Dependency Management:** You **MUST** use the specified Spring Boot Parent POM. If the spec includes a Bill of Materials (BOM) for a dependency family (like Spring Cloud), you **MUST** add it to the `<dependencyManagement>` section.
- **Top-level files and folders:**
-   **Dependency Source Code Rule:** For any feature where the technical specification provides a specific Maven dependency (e.g., a logging starter, a resilience library), you **MUST NOT** generate the source code for that library's internal components. Your only task is to add the dependency to the `pom.xml` and perform the necessary configuration as instructed in the specification.
  - `pom.xml` – The Maven project configuration file.
  - `README.md` – The project documentation file.
  - `.gitignore` – Git ignore file
  - `formatter/codeStyles/` – Code style configuration( Eg eclipse-java-google-style.xml). Generate them
  - `MainApplication.java` – The main Spring Boot application entry point.Update fileName according to input
- **Must** not have any files or folders related to Kubernetes.
- **Must** not create multiple files or folders with same name anywhere


### 3.2. Architecture & Code Structure
-   Implement the exact layered package structure defined in the `.md` spec.
-   Implement API versioning in packages as specified in the `.md` spec.
-   **Strict Package Rule:** You **MUST NOT** create any additional packages or sub-packages that are not explicitly listed in the technical specification's "Base Package Structure" section. The specified structure is exhaustive and complete.

### 3.3. Controllers & Endpoints
-   Implement all REST endpoints from the OpenAPI spec.
-   Use the appropriate Spring MVC annotations for controllers and endpoints.
-   Apply JSR-380 validation (`@Validated`, `@NotNull`, `@Size`, `@Min`, etc.) to all path variables, parameters, and headers as defined in the `.md` spec and OpenAPI spec.
-   Use `@RequestHeader` to extract all required headers.

### 3.4. DTOs & Models
-   Create DTOs in the `dto` (or `model`) package as defined in the OpenAPI spec and `.md` spec.
-   Use Lombok (`@Data`, `@Builder`, `@NoArgsConstructor`, `@AllArgsConstructor`) for boilerplate reduction if specified.

### 3.5. Service Layer & Business Logic
-   Create service interfaces and implementations in the `service` package.
-   The service layer must contain the core business logic.
-   If the API wraps a legacy service (like SOAP), the service layer orchestrates the call to a `gateway` component.

### 3.6. External Integration (Gateway)
-   If specified, create a `gateway` class to encapsulate all communication with external services (e.g., a legacy SOAP service).
-   Use the specified client library for external integrations.
-   Handle data transformation between the internal DTOs and the external service's format, using a mapping library like MapStruct if specified.
-   **SOAP Integration**: If the technical specification requires integration with a SOAP service, you **MUST** implement the following:
    -   Create a gateway class (e.g., `WEDIE010Gateway`) to encapsulate all SOAP communication.
    -   Use the specified SOAP client library (e.g., `WebServiceTemplate` from Spring-WS, or Apache CXF) and follow the implementation logic detailed in the technical specification.
    -   Identify the SOAP service URL from the technical specification (e.g., `app.wedie010.apigeeUrl.<division>`). Externalize this URL in `application.properties` and allow for environment-specific overrides, especially for dynamic routing based on parameters like `division`.
    -   Dynamically build the SOAP request envelope. Map all relevant data from the incoming REST request (headers, path variables, request body, etc.) into the SOAP body as per the service's WSDL contract and the logic specified in the technical specification.
    -   If custom SOAP headers are required, they must be injected into the request.
    -   Handle SOAP faults gracefully. Catch `SoapFaultClientException` and other related exceptions, and map them to the standardized error response model of the REST API.
    -   Parse the SOAP response and map it to the corresponding REST API's response DTO, using a mapping library like MapStruct if specified.
    -   If mTLS is required for the downstream SOAP connection, configure the HTTP client used by the SOAP template accordingly.
    - Identify the controller, service which needs to implemnt SOAP integration and provide end to end implementation.
    - Identify all required urls related to this.
    - verify if service is integrated with soap using url and getting back the response



### 3.7. Security
-   **Secret Management:** Integrate with the specified secret manager. Create a configuration class to load secrets and make them available as Spring properties.

### 3.8. Resilience
-   Implement resilience patterns using the specified library (e.g., **Resilience4j**).
-   Apply `@CircuitBreaker` and `@Retry` annotations to methods in the `gateway` or `service` that call external systems.
-   Configure thresholds, backoff policies, and fallbacks in `application.properties` based on values from the `.md` spec.
-   Implement a fallback method that provides a default response when the circuit is open or retries are exhausted.

### 3.9. Caching
-   Implement caching at the service layer using Spring's Cache Abstraction (`@Cacheable`) if specified.
-   Configure the specified cache manager and set appropriate TTLs from the `.md` spec.

### 3.10. Logging & Observability
-   **Structured Logging:** Implement each and every requirement from the input `.md` spec.
-   **Metrics:** Configure **Micrometer** and Spring Boot Actuator to expose metrics via the specified endpoint.
-   **Tracing:** Include dependencies for the specified tracing library to enable distributed tracing.
-   **Configuration Files:** If the spec requires specific logging configuration files (e.g., `logback.xml`), generate them with the correct syntax and providers for the chosen logging framework.
-   **Health Checks:** Expose `/actuator/health`, `/actuator/live`, and `/actuator/ready` endpoints. Implement any custom health indicators for critical downstream dependencies as required by the `.md` spec.

### 3.11. Exception Handling
-   Create a global exception handler using `@RestControllerAdvice`.
-   Define custom exception classes in the `exception` package as specified.
-   The global handler must catch these exceptions and return a standardized JSON error response as defined in the `.md` spec.

### 3.12. Testing
-   **Unit Tests (JUnit 5 & Mockito):** Test individual classes (services, mappers) in isolation.
-   **Integration Tests (Spring Boot Test):** Test the interaction between layers.
-   **Controller/API Tests:** Write tests for the controller layer, using the specified frameworks to mock any downstream services as required.
-   **Code Coverage:** Configure **JaCoCo** to enforce the code coverage target specified in the `.md` spec, respecting any specified package exclusions.
-   **Test Data:** Place sample payloads in the specified test resources directory.

### 3.13. Documentation
-   **OpenAPI UI:** Configure the specified library (e.g., SpringDoc) to generate an interactive API documentation UI based on the provided OpenAPI `.yaml` spec and controller annotations.
-   **README.md:** Generate a comprehensive `README.md` file that includes:
    *   Project overview and business purpose.
    *   Instructions for building and running the application locally. **For any code examples (like `curl` commands), you MUST use indented code blocks (by prefixing each line with four spaces) instead of triple-backtick fenced code blocks.**
    *   Details on configuration, environment variables, and required secrets.
    *   An overview of the architecture.
    *   API endpoint documentation with `curl` examples.
    *   Information on logging, metrics, and health check endpoints.

### 3.14. Deployment
-   **Dockerfile Source of Truth Rule:** The input `Dockerfile` contains the exact content for the `dockerfile`. You **MUST** find this content and copy it verbatim into the output file named `dockerfile` in the project root. Generating a new Dockerfile from a base image or modifying the specified content in any way is a failure to follow instructions. The content in the `Dockerfile` is the absolute and only source of truth.
-   **CI/CD:** Generate a pipeline definition file with stages for build, test, code analysis, containerization, and deployment, if specified.
-   **Application Properties:** You **MUST** generate a properties file for **every single environment** listed in the input.
    *   The files must be named `application-<profile>.properties` (e.g., `application-local.properties`, `application-dev.properties`).
    *   For the `local` profile, disable external cloud services to ensure easy startup (e.g., `spring.cloud.gcp.secretmanager.enabled=`).
    *   For all profiles, include relevant properties but leave their values empty (e.g., `some.property=`).

### 3.15. Code Quality and Correctness
-   **Compilation Guarantee:** The generated code **MUST** be free of compilation errors. You must ensure all class imports are correct and correspond to the project's package structure and Maven dependencies.
-   **Dependency Versioning:** As stated in the "Project & Build" section, you must use the exact dependency versions provided in the `.md` specification's "Maven Dependencies" section. Do not introduce new versions. If the provided versions are incompatible, fix them accordingly to the best of your knowledge.
-  **Must** Remove all consecutive backticks in the codebase at all locations 

---
## 4. Final Checklist

Before generating the JSON, ensure your output will satisfy these final checks:
-   Is the output a single, raw JSON object?
-   Does the output contain **no** Markdown fencing (e.g., ` ```json ... ``` `) around the JSON object itself?
-   Are all file paths correct and relative to the project root?
-   **Final Structure Check:** Have you verified that the generated Java package structure under `src/main/java/` contains **ONLY** the packages explicitly listed in the technical specification's "Base Package Structure" section and no others? This is a mandatory final check.
-   Does the `pom.xml` contain all necessary dependencies and plugins with the correct versions from the `.md` spec?
-   Is every requirement from the `.md` spec and OpenAPI spec implemented?
-   Is there **no placeholder code**?
-   Are all secrets, URLs, and configurable values externalized into `application.properties` (or equivalent)?
-   Are tests comprehensive and meet the specified coverage goal?
-   **Dockerfile Content Check:** Have you verified that the content of the generated `dockerfile` is a verbatim, unmodified copy of the input `Dockerfile` as required by the "Dockerfile Source of Truth Rule"?
-   Are all other deployment artifacts present and complete as specified?
-   Is the `README.md` detailed and helpful?

You are now ready to generate the complete application.