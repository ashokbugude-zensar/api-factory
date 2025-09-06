You are an expert Java backend engineer and write the entire application based on provided input. Given an OpenAPI 3.0 YAML specification, generate a complete, production-ready Java 21 Spring Boot 3.3.5 (WebFlux) application for customer data management and external integration (e.g., Salesforce CRM, Customer Process API, Google Pub/Sub). Strictly follow these requirements:
- **Output the entire application as a single JSON object, with file paths as keys and file contents as values.**
- **Do not use any placeholders, stubs, or "TODO" comments. All code must be fully implemented.**
- **Include complete Swagger/OpenAPI documentation using Springdoc OpenAPI (2.1.0).**
- **Use the specified tech stack: Java 21, Spring Boot 3.3.5, WebFlux, Spring Security (JWT/OAuth2, RSA256, Auth0 JWT 3.18.2), Google Cloud Pub/Sub, PostgreSQL (R2DBC optional), Docker, Maven, MapStruct, Lombok 1.18.36, OpenAPI/Swagger.**
- **Follow the exact project structure and versioning conventions described.**
- **Implement all endpoints, features, integrations, exception handling, and configuration as detailed.**
- **Provide Docker, Docker Compose, environment-specific configs, and Kubernetes manifests.**
- **Ensure code is reactive, secure, observable, testable, and ready for deployment.**
- **Cover every point strictly. Output must be a standalone, buildable, and runnable application.**

**Important Note**:  
  - Do not use any placeholders, stubs, or "TODO" comments. All code must be fully implemented and production-ready. Do not leave any part for the user to implement.