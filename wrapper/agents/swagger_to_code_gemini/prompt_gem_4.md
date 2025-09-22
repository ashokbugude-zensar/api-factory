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

### Project & Build
-   **Project Name & Base Package:** Use the exact names specified in the `.md` spec. Do not change the base package name 
-   **Build Tool:** Generate a `pom.xml` (or other build file as specified). Use the explicit list of dependencies from the "Maven Dependencies" section of the `.md` spec. Ensure all plugins for build, testing, code coverage, and formatting are also included as defined.
-   **Dependency Management:** You **MUST** use the specified Spring Boot Parent POM. If the spec includes a Bill of Materials (BOM) for a dependency family (like Spring Cloud), you **MUST** add it to the `<dependencyManagement>` section.
- **Top-level files and folders:**
  - `pom.xml` – The Maven project configuration file.
  - `README.md` – The project documentation file.
  - `.gitignore` – Git ignore file
  - `formatter/codeStyles/` – Code style configuration( Eg eclipse-java-google-style.xml). Generate them
  - `MainApplication.java` – The main Spring Boot application entry point.Update fileName according to input
- **Must** not have any files or folders related to Kubernetes.
- **Must** not create multiple files or folders with same name anywhere

### Deployment
-   **Dockerfile Source of Truth Rule:** The input `Dockerfile` contains the exact content for the `dockerfile`. You **MUST** find this content and copy it verbatim into the output file named `dockerfile` in the project root. Generating a new Dockerfile from a base image or modifying the specified content in any way is a failure to follow instructions. The content in the `Dockerfile` is the absolute and only source of truth.
---
## 4. Final Checklist

Before generating the JSON, ensure your output will satisfy these final checks:

-   **Dockerfile Content Check:** Have you verified that the content of the generated `dockerfile` is a verbatim, unmodified copy of the input `Dockerfile` as required by the "Dockerfile Source of Truth Rule"?
-   Are all other deployment artifacts present and complete as specified?
-   Is the `README.md` detailed and helpful?

You are now ready to generate the complete application.