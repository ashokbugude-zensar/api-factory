# Business Requirements Document: Carton API Wrapper

**Version:** 1.0

**Date:** September 9, 2025

**Author:** GitHub Copilot

---

## 1. Introduction

### 1.1. Business Problem

Internal development teams face significant challenges when integrating with legacy backend services for order processing and inventory management. The primary service for retrieving carton details is a SOAP-based web service (WEDIE010) that uses complex, non-standard protocols and authentication mechanisms. This complexity increases integration time, creates inconsistencies across consuming applications, and makes it difficult for modern, REST-based applications to access critical business data efficiently.

### 1.2. Proposed Solution

This document outlines the business requirements for creating a **Carton API Wrapper**. This new service will act as a facade, providing a modern, standardized, and easy-to-use RESTful/JSON interface for the underlying legacy SOAP/XML service.

The wrapper will abstract away the complexities of the legacy system, including its protocol, data format, and custom authentication. By doing so, it will accelerate development, improve developer experience, and ensure consistent and secure access to carton information.

### 1.3. Goals and Objectives

| Goal | Objective | How Success Will Be Measured |
| :--- | :--- | :--- |
| Simplify Integration | Abstract the complexity of the legacy SOAP service. | Reduction in lines of code and development time for new integrations. |
| Modernize Access | Provide a modern REST/JSON API for a legacy backend. | Successful adoption of the new API by internal service consumers. |
| Improve Developer Experience | Offer a standardized, well-documented, and consistent API. | Positive developer feedback and a decrease in API-related support tickets. |
| Enhance Performance & Reliability | Reduce load on the legacy system and ensure service stability. | Meeting defined performance (response time) and availability (uptime) SLAs. |

---

## 2. Scope

### 2.1. In Scope

*   **Wrapping the 'Get Carton' functionality:** The API will exclusively wrap the functionality for retrieving carton details from the downstream SOAP service.
*   **Protocol Translation:** Convert REST/JSON requests from consumers into SOAP/XML requests for the backend service.
*   **Data Transformation:** Map the XML response from the legacy service to a clean, standardized JSON response (DTO).
*   **Authentication Handling:** Mediate the custom authentication required by the legacy service, abstracting it from the end consumer.
*   **Endpoint Definition:** Provide a single, read-only GET endpoint to retrieve carton details by its identifier.
*   **Caching:** Implement caching to improve response times and reduce the load on the downstream service.
*   **Error Handling:** Standardize error responses and map upstream errors to a consistent, modern format.
*   **Security:** Implement security best practices, including input validation, secure credential management, and role-based access.
*   **Documentation:** Provide detailed OpenAPI 3.0 specification with examples.
*   **Operational Readiness:** Implement comprehensive logging, monitoring, and alerting.

### 2.2. Out of Scope

*   **Direct Database Access:** The service will not interact directly with any database.
*   **Data Persistence:** The service is a pass-through proxy and will not store any transactional data.
*   **Data Aggregation:** The service will not aggregate data from multiple upstream services. It is a 1:1 wrapper.
*   **Service Orchestration:** The service will not orchestrate or coordinate calls to multiple services.
*   **Write Operations:** The API is read-only. It will not support `POST`, `PUT`, `DELETE`, or `PATCH` operations to modify data.

---

## 3. Functional Requirements

### 3.1. API Endpoints

| Method | Path | Description |
| :--- | :--- | :--- |
| `GET` | `/v1/carton/{cartonId}` | Retrieves details for a specific carton. |

### 3.2. Request Parameters

| Parameter Type | Name | Data Type | Description | Required |
| :--- | :--- | :--- | :--- | :--- |
| Path | `cartonId` | String | The unique identifier for the carton. | Yes |
| Header | `location` | String | The location context for the request. | Yes |
| Header | `colleagueId` | String | The ID of the colleague making the request. | Yes |
| Header | `applicationId` | String | The ID of the consuming application. | Yes |
| Header | `division` | String | The business division for the request. | Yes |
| Header | `store` | String | The store context for the request. | Yes |
| Header | `device_type` | String | The type of device making the request. | Yes |
| Header | `device_id` | String | The unique ID of the device. | Yes |
| Header | `traceId` | String | A unique identifier for tracing the request. | No |

### 3.3. Response Payload

*   The API MUST return data in `JSON` format.
*   The successful response payload MUST conform to the `CartonDetailResponse` DTO structure.

**`CartonDetailResponse` DTO Structure:**

```json
{
  "returnCode": 0,
  "returnMessage": "success message",
  "cartonNumber": "00607809940059948981",
  "nafship": 4184778,
  "bolstatus": "",
  "bol": "BL004950661110120002"
}
```

**Field Definitions:**

| Field | Data Type | Description |
| :--- | :--- | :--- |
| `returnCode` | Integer | Status code of the operation (e.g., 0 for success). |
| `returnMessage` | String | A human-readable message describing the result. |
| `cartonNumber` | String | The carton identifier. |
| `nafship` | Long | The NAF ship number. |
| `bolstatus` | String | The Bill of Lading status. |
| `bol` | String | The Bill of Lading number. |

### 3.4. Error Handling

*   The API MUST use a global exception handling strategy to ensure consistent error responses.
*   Errors from the upstream service MUST be mapped to a standardized error format.
*   The standard error response MUST be in JSON and include a timestamp, status code, error type, and a descriptive message.
*   When rate limits are exceeded, the API MUST return an `HTTP 429 (Too Many Requests)` status code.

### 3.5. Data Validation

*   The API MUST validate all incoming request parameters and headers.
*   Validation MUST include checks for presence, format, and size (e.g., `NotNull`, `Size`, `Pattern`).
*   Validation errors MUST be reported back to the consumer with detailed, field-specific error messages.

---

## 4. Non-Functional Requirements

### 4.1. Performance

| Requirement | Metric |
| :--- | :--- |
| **Response Time** | 95th percentile response time MUST be **< 500ms**. |
| **Throughput** | The system MUST support an expected load of **10-100 requests per second**. |
| **Concurrent Users** | The system MUST support **100-1,000 concurrent users**. |
| **Caching** | The service MUST implement caching to improve performance and reduce load on the backend. |
| **Compression** | API responses MUST be compressed (GZIP) to reduce payload size. |

### 4.2. Scalability

*   The application MUST be containerized (using Docker) to allow for flexible deployment.
*   The infrastructure MUST support horizontal scaling (e.g., Kubernetes HPA) to automatically adjust the number of instances based on load (CPU/memory).
*   The infrastructure MUST support vertical scaling by allowing for the adjustment of container resource limits (CPU/memory).

### 4.3. Availability & Reliability

| Requirement | Metric |
| :--- | :--- |
| **Availability (Uptime)** | The service MUST have a target availability of **99.9%**. |
| **Mean Time To Recovery (MTTR)** | The service MUST have a target MTTR of **< 30 minutes**. |
| **Circuit Breaker** | The service MUST implement a circuit breaker (using Resilience4j) to prevent cascading failures when the downstream service is unavailable. |
| **Retries** | The service MUST implement an automatic retry mechanism with exponential backoff to handle transient downstream errors. |
| **Fault Injection Testing** | The service MUST be tested against latency and fault injections to ensure resilience mechanisms are effective. |

### 4.4. Security

*   **Exposure:** The API is for **internal** organizational use only and will be exposed via an API Gateway (Apigee).
*   **Authentication:** The wrapper service is responsible for handling the custom header-based authentication (`X-Client-Id`, `X-Client-Secret`) required by the downstream SOAP service.
*   **Authorization:** Access to the API MUST be controlled via Role-Based Access Control (RBAC) configured at the API Gateway level.
*   **Credential Management:** All secrets and credentials (e.g., `X-Client-Secret`) MUST be stored securely in Google Cloud Secret Manager.
*   **Secure Communication:** All communication with the API MUST be over TLS.
*   **Input Sanitization:** All inputs MUST be sanitized to prevent common vulnerabilities such as Cross-Site Scripting (XSS) and SQL Injection.
*   **Rate Limiting:** Per-client rate limiting MUST be enforced at the API Gateway at a rate of **1000 requests per minute**.

### 4.5. Monitoring & Logging

*   **Logging Framework:** The service MUST use a structured logging framework (Logback) to produce JSON-formatted logs.
*   **Log Content:** Every log message MUST include a timestamp, log level, thread name, and a correlation ID (`traceId`) to trace a request's entire lifecycle.
*   **Log Retention:** Logs MUST be retained for a period of **30 days**.
*   **Monitoring:** A comprehensive monitoring dashboard (in Dynatrace) MUST be created, tracking key metrics like response time, throughput, error rates, CPU/memory utilization, and circuit breaker status.
*   **Alerting:** Alerts MUST be configured to trigger on critical events, including:
    *   High error rate (> 5%)
    *   High response time (> 1s)
    *   High resource utilization (> 80%)
    *   Circuit breaker transitioning to an open state.
*   **Health Checks:** The service MUST expose health check endpoints that reflect its ability to connect to the downstream service.

### 4.6. Documentation & Versioning

*   **API Documentation:** The API MUST be documented using the OpenAPI 3.0 specification. The documentation must be detailed, including examples, and be accessible via an interactive UI (e.g., Swagger UI).
*   **API Versioning:** The API MUST use URL-based versioning (e.g., `/v1/...`).

### 4.7. Testability

*   **Code Coverage:** The application code MUST achieve a minimum of **80%** unit and integration test coverage, measured by JaCoCo.
*   **Test Cases:** Testing MUST include positive and negative test cases, as well as boundary value analysis.
*   **Mocking:** Integration tests MUST use a mock service (e.g., WireMock) to simulate the downstream SOAP API, allowing for isolated testing of the wrapper logic.

---

