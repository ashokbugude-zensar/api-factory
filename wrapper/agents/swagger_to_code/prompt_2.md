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