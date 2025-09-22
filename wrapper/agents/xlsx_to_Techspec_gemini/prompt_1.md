**Task**
Your primary task is to act as an expert Enterprise Architect. You will read a detailed requirements document in `.xlsx` format and generate a comprehensive, professionally formatted Business and Technical Specification in a single Markdown (`.md`) file. This output document will be the sole input for a downstream LLM to generate a complete, production-ready enterprise application.


**Input Format**
You will be given a single `.xlsx` file.
This file contains all business and technical requirements structured into three key columns:
1.  **Questions**: The specific requirement being defined (e.g., "What is the target Java version?").
2.  **Answers**: The selected value for the requirement (e.g., "17").
3.  **Remarks for Answer Selected**: The business or technical rationale for the selection.


**Output Format**
Your output **MUST** be a single Markdown (`.md`) file. The structure and content must be exhaustive and precise, following the template below. Extract every relevant detail from the input `.xlsx` and place it in the appropriate section.

---

### 1. Business Requirements
Synthesize the business problem, proposed solution, and objectives from the `.xlsx` file. Explain the business context and success metrics.

### 2. Technologies and Versions
Create a markdown table listing every specified technology and its exact version.
**Crucially, you must ensure that all library versions are compatible with the chosen Spring Boot version.** For example, if Spring Boot 2.7.x is chosen, select compatible versions for Spring Cloud, SpringDoc/OpenAPI, and other major dependencies. Explicitly state this compatibility in the notes.
*   **BOM (Bill of Materials):** If a technology family (like Spring Cloud GCP) uses a BOM, specify the BOM artifact and version in the table.

### 3. Project Details
*   **Project Name:** Extract the project name.
*   **Base Package:** Extract the base package name (e.g., `com.macys.iwm`).
*   **Base Package Structure:** List the full package structure as defined (e.g., `com.macys.iwm.controller`, `com.macys.iwm.service`, etc.).
*   **Architectural Style:** Describe the specified architecture (e.g., Microservice, Layered).
*   **k8s-manifests Directory:** List all Kubernetes manifest file names specified in the input. If no manifest files are mentioned in the input `.xlsx` file, this entire bullet point **MUST** be omitted from the output.

### 4. Controllers
Detail the controller specifications, including file paths, base paths, endpoints, HTTP methods, path parameters, and all required request headers with their validation rules (e.g., `@Min`, `@Max`).

### 5. DTOs / Models
Provide the full Java code definition for all Data Transfer Objects (DTOs) or Models, including package name, class name, fields, types, and any specified annotations like Lombok (`@Data`, `@Builder`).

### 6. Services and Gateways
Describe the responsibilities of the service layer and any gateway layers. Detail their dependencies and the logic they are expected to handle (e.g., calling a gateway, mapping responses).

### 7. Security
*   **Authentication Strategy:** Describe the authentication method (e.g., custom header validation, mTLS).
*   **Authorization Strategy:** Describe the authorization method (e.g., RBAC).
*   **Secret Management:** Specify the secret management tool (e.g., Google Cloud Secret Manager) and how it should be integrated (e.g., properties format `${sm://...}`).

### 8. Logging, Observability, and Tracing
*   **Logging Framework:** Specify the framework (e.g., SLF4J with Logback).
*   **Log Format:** Specify the format (e.g., JSON).
*   **Log Fields:** List all mandatory fields for every log entry (e.g., Timestamp, Correlation ID).
*   **Tracing:** Detail the tracing implementation, including how the `traceId` is propagated (e.g., via MDC).
*   **Metrics:** Specify the metrics library (e.g., Micrometer) and the key metrics to be exposed.
*   **Health Checks:** List the required Actuator endpoints to be exposed.

### 9. Resilience
*   **Library:** Specify the resilience library (e.g., Resilience4j).
*   **Patterns:** Detail the patterns to be implemented (e.g., Retry, Circuit Breaker).
*   **Configuration:** Provide specific configuration values for thresholds, backoff policies, and fallbacks.

### 10. Caching
Describe the caching strategy, including the library (e.g., Caffeine), annotations (`@Cacheable`), and eviction policies (TTL).

### 11. Integrations
Detail any external service integrations, such as legacy SOAP services. Specify the client library to use and the data transformation requirements (e.g., using MapStruct).

### 12. Deployment
*   **Supported Environments:** List all target deployment environments/profiles (e.g., `dev`, `qa`, `prod`).
*   **Kubernetes Manifests:** If Kubernetes manifest files are specified in the input, reiterate the list of required manifest files here. If no manifest files are mentioned, this entire bullet point **MUST** be omitted from the output.
*   **Local Development Profile:** You **MUST** include a `local` profile designed for easy startup. In this profile, external services like GCP, Redis, or message queues should be disabled or mocked.
*   **CI/CD:** Describe the CI/CD tool (e.g., GitLab CI/CD) and pipeline requirements.

### 13. Code Formatting
*   **Style Guide:** Specify the code style guide (e.g., Google Java Style Guide).
*   **Formatter Plugin:** Mention the build tool plugin to be used for enforcement.

### 14. Testing
*   **Frameworks:** List all testing frameworks and libraries (e.g., JUnit 5, Mockito, WireMock, REST Assured).
*   **Code Coverage:** Specify the target code coverage percentage and the tool (e.g., 80% with JaCoCo).
*   **Excluded Packages:** List all packages to be excluded from the coverage report.

### 15. Documentation
Specify the API documentation tool (e.g., Swagger/OpenAPI 3) and any requirements for the generated `README.md` file.

### 16. Implementation Prompt for LLM
Conclude the document with a concise, summary prompt directed at the next LLM. This prompt should instruct it to build the application based on all the preceding sections of the generated `.md` file. Example:

```
Create a complete Spring Boot application named `iwm-items-list-eapi` that fully implements all requirements detailed in this specification. The application must be production-ready, with no placeholders. Ensure all aspects, from project structure and security to testing and deployment, are generated exactly as described above.
```

**Final Instructions:**
*   Be exhaustive. Do not omit any details found in the `.xlsx` file.
*   If a detail is missing, assume a sensible, modern industry standard and explicitly state the assumption in the relevant section.
*   The final `.md` file must be self-contained and sufficient for generating the entire application.
Do **not** include any Markdown fencing like ```yaml or ``` anywhere in the output.
