# Amazon API Gateway for Serverless Applications

## Course Overview

**Level:** Fundamental  
**Duration:** 2 hours  
**Language:** English  
**Rating:** 4.6/5 (908 reviews)

> This README is a study and revision guide based on the course description provided. It is not official AWS training material and should be supplemented with current AWS documentation.

---

# 1. Introduction

**Amazon API Gateway** is a managed AWS service for creating, publishing, securing, monitoring, and managing APIs.

It can act as the front door for applications and connect clients to backend services such as:

- AWS Lambda
- Amazon DynamoDB
- AWS services
- HTTP endpoints
- Other application backends

A common serverless architecture is:

```text
Client
   |
   v
Amazon API Gateway
   |
   v
AWS Lambda
   |
   v
AWS Service / Database
```

API Gateway allows developers to expose application functionality through APIs without managing API servers themselves.

---

# 2. What Is an API?

An **Application Programming Interface (API)** defines how software components communicate.

For example:

```text
Client
   |
   | GET /users/123
   v
API
   |
   v
Backend
   |
   v
User Information
```

An API defines things such as:

- Endpoints
- HTTP methods
- Request parameters
- Request bodies
- Responses
- Authentication
- Authorization
- Error behavior

---

# 3. Why Use API Gateway?

API Gateway can provide a managed entry point for APIs.

Common capabilities include:

- API creation
- API deployment
- Request routing
- Backend integration
- Authentication and authorization
- Throttling
- Usage management
- Monitoring
- Logging
- Metrics
- API lifecycle management

It can help separate clients from backend implementation details.

---

# 4. API Gateway and Serverless Architecture

API Gateway is frequently used with AWS Lambda.

Typical architecture:

```text
Web / Mobile Client
        |
        v
   API Gateway
        |
        v
      Lambda
        |
        v
   DynamoDB / S3 / Other AWS Service
```

The client communicates with API Gateway instead of directly invoking Lambda.

This creates an API abstraction layer between the client and backend.

---

# 5. API Gateway Request Flow

A simplified request flow is:

```text
1. Client sends request
          |
          v
2. API Gateway receives request
          |
          v
3. API Gateway identifies route
          |
          v
4. Authorization / validation
          |
          v
5. Backend integration
          |
          v
6. Backend processes request
          |
          v
7. API Gateway returns response
```

The exact flow depends on the API type and configuration.

---

# 6. API Types

Amazon API Gateway supports multiple API approaches.

The major API types to understand include:

- REST APIs
- HTTP APIs
- WebSocket APIs

Each serves different requirements.

---

# 7. REST APIs

REST APIs provide a feature-rich API management model.

They can support capabilities such as:

- Resource-based API design
- HTTP methods
- Authorization
- API keys
- Usage plans
- Request/response transformation
- Monitoring
- Integration with AWS services

REST APIs can be appropriate when advanced API management features are required.

---

# 8. HTTP APIs

HTTP APIs are designed to provide a simpler and lower-cost approach for many HTTP-based use cases.

A common pattern is:

```text
Client
  |
  v
HTTP API
  |
  v
Lambda
```

They are useful when the application primarily needs straightforward HTTP routing and integration.

When choosing between HTTP APIs and REST APIs, evaluate the exact features required by the application.

---

# 9. WebSocket APIs

WebSocket APIs support persistent, two-way communication between clients and servers.

Conceptually:

```text
Client <=================> API Gateway
              |
              v
           Backend
```

They can be useful for applications requiring real-time communication.

Examples include:

- Chat applications
- Live dashboards
- Real-time notifications
- Interactive applications

---

# 10. Resources and Routes

An API exposes paths that clients can call.

Example:

```text
/users
/users/{id}
/orders
/orders/{id}
```

HTTP methods define the operation.

Examples:

```text
GET    /users
POST   /users
GET    /users/{id}
PUT    /users/{id}
DELETE /users/{id}
```

A well-designed API uses predictable resource names and appropriate HTTP methods.

---

# 11. HTTP Methods

Common methods include:

| Method | Typical Purpose |
|---|---|
| GET | Retrieve data |
| POST | Create or submit data |
| PUT | Replace/update a resource |
| PATCH | Partially update a resource |
| DELETE | Delete a resource |

The exact semantics should follow the API's contract.

---

# 12. API Gateway + Lambda

One of the most common serverless patterns is:

```text
Client
   |
   v
API Gateway
   |
   v
Lambda
   |
   v
Database
```

For example:

```text
GET /products/123
       |
       v
API Gateway
       |
       v
Lambda
       |
       v
DynamoDB
       |
       v
Product Response
```

Lambda handles the application logic while API Gateway manages the API endpoint.

---

# 13. Lambda Integration

API Gateway can invoke Lambda when an API request matches an appropriate route or method.

Conceptually:

```text
HTTP Request
     |
     v
API Gateway
     |
     v
Lambda Function
     |
     v
Application Logic
     |
     v
HTTP Response
```

The Lambda function should validate inputs and handle application errors appropriately.

---

# 14. Integration Types

API Gateway can integrate with different backend types.

Conceptually:

```text
API Gateway
    |
    +----> Lambda
    |
    +----> AWS Service
    |
    +----> HTTP Endpoint
```

Choose the integration based on application architecture and operational requirements.

---

# 15. Stages

An API can have different deployment environments.

Common examples:

```text
dev
test
staging
prod
```

A stage provides an environment in which a deployed API configuration can be accessed.

A common workflow is:

```text
Development
    |
    v
Testing
    |
    v
Staging
    |
    v
Production
```

Separate environments can help reduce the risk of making changes directly to production.

---

# 16. Deployment

Creating an API configuration does not necessarily mean that the configuration is immediately available to clients.

A deployment process typically involves:

```text
Configure API
     |
     v
Test
     |
     v
Deploy
     |
     v
Stage
     |
     v
Client Access
```

Always verify deployment and stage configuration before exposing an API to users.

---

# 17. API Endpoints

An API endpoint represents a URL through which clients access an API.

Conceptually:

```text
https://api.example.com/users
```

Clients send HTTP requests to the endpoint.

API Gateway receives the request and routes it to the appropriate integration.

---

# 18. Custom Domains

Applications may use custom domain names instead of exposing an API only through the default API Gateway endpoint.

Conceptually:

```text
api.example.com
       |
       v
API Gateway
       |
       v
Backend
```

Custom domains can make APIs easier for clients to consume and can support consistent application naming.

---

# 19. Authentication vs. Authorization

These concepts are different.

### Authentication

Answers:

> Who are you?

### Authorization

Answers:

> What are you allowed to do?

Example:

```text
Client
  |
  v
Authentication
  |
  v
Identity
  |
  v
Authorization
  |
  v
API Access
```

A secure API should use an appropriate authentication and authorization strategy.

---

# 20. Authorization Options

API Gateway can integrate with different authorization mechanisms depending on API type and architecture.

Examples include:

- AWS IAM authorization
- Amazon Cognito
- Lambda authorizers
- JWT-based authorization for supported API configurations

Choose authorization based on:

- Client type
- Identity requirements
- Security model
- Token format
- Application architecture

---

# 21. IAM Authorization

AWS Identity and Access Management (IAM) can be used to control access to APIs.

Conceptually:

```text
IAM Principal
     |
     v
API Gateway
     |
     v
Authorized Request
```

IAM-based authorization is particularly useful for AWS-aware applications and workloads that already use IAM identities and policies.

---

# 22. Amazon Cognito

Amazon Cognito can provide user identity and authentication capabilities for applications.

A conceptual flow is:

```text
User
 |
 v
Cognito
 |
 v
Token
 |
 v
API Gateway
 |
 v
Backend
```

This can be useful for applications with end users who authenticate through an identity system.

---

# 23. Lambda Authorizers

A Lambda authorizer can execute custom authorization logic.

Conceptually:

```text
Client
  |
  v
API Gateway
  |
  v
Lambda Authorizer
  |
  +---- Deny
  |
  +---- Allow
          |
          v
       Backend
```

Use custom authorization logic when built-in authorization mechanisms do not meet application requirements.

---

# 24. API Keys

API keys can be used to identify API clients and support usage management.

Conceptually:

```text
Client
  |
  | API Key
  v
API Gateway
  |
  v
API
```

Important:

> API keys should not automatically be treated as a complete authentication or authorization mechanism.

Use proper authentication and authorization for sensitive APIs.

---

# 25. Usage Plans

Usage plans can help manage API consumption for supported API configurations.

They can be associated with concepts such as:

- API keys
- Stages
- Throttling
- Quotas

Conceptually:

```text
Client
  |
  v
API Key
  |
  v
Usage Plan
  |
  +---- Throttling
  |
  +---- Quota
  |
  v
API
```

---

# 26. Throttling

**Throttling** limits the rate at which requests can be processed.

Example:

```text
Client
  |
  | High request rate
  v
API Gateway
  |
  v
Throttle
  |
  +---- Accepted requests
  |
  +---- Limited / rejected requests
```

Throttling can protect backend systems from excessive traffic.

---

# 27. Why Throttling Matters

Without suitable controls:

```text
Large Traffic Spike
       |
       v
API
       |
       v
Backend Overload
       |
       v
Failures
```

With throttling:

```text
Large Traffic Spike
       |
       v
API Gateway
       |
       v
Controlled Request Rate
       |
       v
Backend
```

Throttling should be configured according to application requirements and service limits.

---

# 28. Quotas

A quota controls usage over a defined period for supported API management configurations.

For example, an organization may want to limit the number of requests available to a particular API consumer.

Throttling and quotas are different:

| Feature | Purpose |
|---|---|
| Throttling | Controls request rate |
| Quota | Controls usage over a longer period |

---

# 29. Request Validation

API Gateway can help validate incoming requests depending on API type and configuration.

Validation can help catch:

- Missing parameters
- Invalid request structures
- Incorrect data formats

Conceptually:

```text
Request
  |
  v
Validation
  |
  +---- Invalid ---> Error
  |
  +---- Valid ----> Backend
```

Validation should also be performed appropriately by the application backend.

---

# 30. Input Validation

Never assume that client input is trustworthy.

Validate:

- Required fields
- Data types
- Length
- Allowed values
- Format
- Business constraints

Example:

```text
Client Input
     |
     v
Validate
     |
     +---- Invalid ---> Reject
     |
     +---- Valid ----> Process
```

Input validation is both a correctness and security concern.

---

# 31. Response Handling

API Gateway can help control how backend responses are returned to clients.

A good API should provide:

- Appropriate HTTP status codes
- Consistent response formats
- Useful error messages
- No unnecessary sensitive information

Example:

```json
{
  "status": "success",
  "data": {}
}
```

The actual response contract should be defined by the API design.

---

# 32. Error Handling

Common API errors include:

- Invalid request
- Unauthorized request
- Forbidden request
- Resource not found
- Too many requests
- Backend failure
- Timeout

A conceptual flow:

```text
Request
   |
   v
API Gateway
   |
   +---- 4xx ---> Client Error
   |
   +---- 5xx ---> Server / Backend Error
   |
   +---- 2xx ---> Success
```

Avoid exposing internal implementation details in error responses.

---

# 33. Caching

API caching can reduce repeated backend requests for suitable workloads.

Conceptually:

```text
Client
  |
  v
API Gateway
  |
  +---- Cache Hit ---> Response
  |
  +---- Cache Miss --> Backend
```

Caching can improve performance and reduce backend load.

However, caching is appropriate only when data freshness and consistency requirements allow it.

---

# 34. Monitoring

API Gateway should be monitored as part of the application.

Useful metrics and signals can include:

- Request count
- Latency
- Error rates
- 4xx responses
- 5xx responses
- Throttled requests
- Integration failures

Monitoring helps identify:

- Performance problems
- Client errors
- Backend failures
- Traffic spikes
- Capacity problems

---

# 35. Logging

Logs can help investigate API behavior.

A useful troubleshooting flow is:

```text
Client Request
      |
      v
API Gateway Logs
      |
      v
Lambda Logs
      |
      v
Backend Logs
```

Use appropriate logging levels and avoid putting secrets or sensitive data into logs.

---

# 36. Tracing and Distributed Systems

A serverless application may contain several components:

```text
Client
  |
  v
API Gateway
  |
  v
Lambda
  |
  v
DynamoDB
```

When troubleshooting, it can be difficult to identify where latency or errors occurred.

Correlation identifiers and distributed tracing can help connect activity across components.

---

# 37. Security Best Practices

Important practices include:

1. Use strong authentication and authorization.
2. Apply least privilege.
3. Validate inputs.
4. Use HTTPS/TLS.
5. Avoid exposing sensitive information.
6. Protect backend integrations.
7. Configure appropriate throttling.
8. Monitor API activity.
9. Protect API keys and credentials.
10. Avoid logging secrets.
11. Review permissions regularly.
12. Keep APIs and dependencies updated.

---

# 38. Least Privilege

Backend Lambda functions should have only the permissions they require.

Example:

```text
Lambda
  |
  +---- Read required DynamoDB table
  |
  +---- Write required S3 location
```

Avoid giving a function broad permissions when a narrower IAM policy is sufficient.

---

# 39. API Gateway + Lambda + DynamoDB

A common serverless application architecture is:

```text
Client
   |
   v
API Gateway
   |
   v
Lambda
   |
   v
DynamoDB
```

Example API:

```text
POST /users
GET  /users/{id}
PUT  /users/{id}
DELETE /users/{id}
```

Lambda contains the application logic while DynamoDB provides persistent data storage.

---

# 40. API Gateway + Lambda + S3

Another common pattern is file-related processing:

```text
Client
   |
   v
API Gateway
   |
   v
Lambda
   |
   v
S3
```

Use appropriate upload patterns for large objects rather than forcing large payloads through Lambda unnecessarily.

---

# 41. API Gateway + Asynchronous Processing

For long-running operations:

```text
Client
   |
   v
API Gateway
   |
   v
SQS
   |
   v
Lambda
   |
   v
Processing
```

This prevents the API request from having to wait for the entire processing operation.

---

# 42. API Gateway as an API Front Door

API Gateway can centralize common API concerns:

```text
                    +----> Lambda A
                    |
Clients ---> API Gateway
                    |
                    +----> Lambda B
                    |
                    +----> HTTP Backend
```

This can provide a consistent interface to clients while allowing backend components to evolve independently.

---

# 43. API Lifecycle

A simplified API lifecycle is:

```text
Design
  |
  v
Develop
  |
  v
Test
  |
  v
Deploy
  |
  v
Monitor
  |
  v
Update
  |
  v
Retire
```

API lifecycle management should include versioning and controlled changes.

---

# 44. API Versioning

APIs evolve over time.

Possible versioning approaches include:

```text
/v1/users
/v2/users
```

or other versioning strategies.

Versioning can allow clients to migrate without immediately breaking existing integrations.

Avoid making breaking changes without a migration strategy.

---

# 45. Backward Compatibility

When changing an API:

Prefer changes that do not break existing consumers.

Examples:

- Adding optional fields
- Adding new endpoints
- Maintaining existing response fields
- Introducing a new API version for breaking changes

Communicate breaking changes clearly.

---

# 46. CORS

**Cross-Origin Resource Sharing (CORS)** controls how browser-based applications can make requests across origins.

Example:

```text
Web App
https://app.example.com
       |
       v
API
https://api.example.com
```

If browser security rules require it, API Gateway must be configured appropriately for the application's CORS requirements.

Do not use overly permissive CORS settings unnecessarily.

---

# 47. Rate Limiting Strategy

Rate limiting should reflect:

- Expected traffic
- Backend capacity
- Client behavior
- Business requirements
- Abuse protection
- Cost considerations

A basic strategy is:

```text
Normal Traffic
      |
      v
Allow

Excessive Traffic
      |
      v
Throttle
```

Throttling should be tested before production use.

---

# 48. Cost Considerations

API Gateway costs depend on factors such as:

- API type
- Number of API calls
- Data processing
- Associated AWS services
- Caching where applicable
- Architecture

Do not evaluate API Gateway cost in isolation.

For a serverless application, consider the complete request path:

```text
API Gateway
     +
Lambda
     +
Database
     +
Storage
     +
Logging
     +
Networking
```

Always check current AWS pricing before making financial decisions.

---

# 49. Performance Considerations

API performance depends on the complete architecture.

Potential latency sources include:

- API Gateway
- Lambda startup and execution
- Database access
- External APIs
- Network calls
- Application logic

Optimize the slowest components rather than focusing on one service in isolation.

---

# 50. Common Mistakes

## Mistake 1: Treating API Keys as Full Authentication

API keys are primarily useful for identifying clients and managing usage in supported configurations.

Use appropriate authentication and authorization for protected resources.

## Mistake 2: Giving Lambda Excessive Permissions

Use least privilege.

## Mistake 3: No Input Validation

Never trust client input.

## Mistake 4: Exposing Sensitive Error Details

Return safe, useful errors without exposing internal implementation details.

## Mistake 5: Ignoring Throttling

Uncontrolled traffic can overload downstream services.

## Mistake 6: No Monitoring

Production APIs require visibility into traffic, errors, latency, and throttling.

## Mistake 7: Making Long Operations Synchronous

Consider asynchronous processing for long-running workloads.

## Mistake 8: Breaking Existing Clients

Use versioning and backward-compatible API evolution.

## Mistake 9: Overly Permissive CORS

Allow only the origins and methods required by the application.

## Mistake 10: Logging Sensitive Data

Do not log passwords, tokens, secrets, or unnecessary personal information.

---

# 51. Best Practices Checklist

## API Design

- [ ] Define clear resources.
- [ ] Use appropriate HTTP methods.
- [ ] Define request and response schemas.
- [ ] Use consistent error responses.
- [ ] Document the API.
- [ ] Plan versioning.

## Security

- [ ] Use HTTPS/TLS.
- [ ] Configure authentication.
- [ ] Configure authorization.
- [ ] Apply least privilege.
- [ ] Validate input.
- [ ] Protect credentials and API keys.
- [ ] Configure appropriate CORS.
- [ ] Avoid sensitive data in logs.

## Performance

- [ ] Monitor latency.
- [ ] Monitor backend performance.
- [ ] Consider caching where appropriate.
- [ ] Configure throttling.
- [ ] Use asynchronous processing for long-running workloads.

## Operations

- [ ] Create development and production stages.
- [ ] Test before deployment.
- [ ] Monitor API metrics.
- [ ] Enable appropriate logs.
- [ ] Define incident response procedures.
- [ ] Review permissions regularly.

## Cost

- [ ] Monitor request volume.
- [ ] Monitor associated Lambda costs.
- [ ] Monitor database costs.
- [ ] Monitor storage costs.
- [ ] Review current AWS pricing.

---

# 52. Architecture Decision Guide

| Requirement | Potential Solution |
|---|---|
| Simple HTTP API | HTTP API |
| Feature-rich API management | REST API |
| Real-time two-way communication | WebSocket API |
| Serverless backend | API Gateway + Lambda |
| Persistent application data | API Gateway + Lambda + DynamoDB |
| File/application object workflow | API Gateway + Lambda + S3 |
| Long-running processing | API Gateway + SQS + Lambda |
| User authentication | Cognito / appropriate identity solution |
| AWS-native authorization | IAM |
| Custom authorization logic | Lambda authorizer |
| Client usage identification | API keys / usage management |
| Protect backend from spikes | Throttling / asynchronous design |
| Repeated read optimization | Caching where appropriate |

---

# 53. Example Architecture — Basic Serverless API

```text
                +----------------+
                |     Client     |
                +-------+--------+
                        |
                        v
                +---------------+
                | API Gateway   |
                +-------+-------+
                        |
                        v
                +---------------+
                |    Lambda     |
                +-------+-------+
                        |
                        v
                +---------------+
                |   DynamoDB    |
                +---------------+
```

### Request flow

1. Client sends an HTTP request.
2. API Gateway receives the request.
3. API Gateway checks configured access controls.
4. API Gateway invokes Lambda.
5. Lambda executes application logic.
6. Lambda interacts with DynamoDB.
7. API Gateway returns the response.

---

# 54. Example Architecture — Protected API

```text
Client
  |
  v
Authentication
  |
  v
API Gateway
  |
  v
Authorization
  |
  v
Lambda
  |
  v
Database
```

Security controls should exist at appropriate layers rather than relying on a single control.

---

# 55. Example Architecture — Asynchronous API

```text
Client
   |
   v
API Gateway
   |
   v
SQS Queue
   |
   v
Lambda
   |
   v
Database / Processing
```

Use this pattern when the client can receive an acknowledgement before the full operation is complete.

---

# 56. Knowledge Check Questions

### Question 1

What is Amazon API Gateway?

**Answer:** A managed AWS service for creating, publishing, securing, monitoring, and managing APIs.

### Question 2

What is a common serverless architecture using API Gateway?

**Answer:** API Gateway receives client requests and integrates with AWS Lambda, which performs application logic.

### Question 3

What are the major API approaches associated with API Gateway?

**Answer:** REST APIs, HTTP APIs, and WebSocket APIs.

### Question 4

When is a WebSocket API useful?

**Answer:** When applications require persistent, two-way, real-time communication.

### Question 5

What is authentication?

**Answer:** The process of determining who a client or user is.

### Question 6

What is authorization?

**Answer:** Determining what an authenticated identity is allowed to access or perform.

### Question 7

What is throttling?

**Answer:** Limiting the rate at which requests can be processed.

### Question 8

What is the purpose of an API key?

**Answer:** It can identify API consumers and support usage management in supported API configurations; it should not automatically be treated as full authentication.

### Question 9

What is a usage plan?

**Answer:** A mechanism for managing API consumer usage through features such as throttling and quotas for supported configurations.

### Question 10

Why is input validation important?

**Answer:** It prevents invalid or unexpected data from reaching backend systems and supports security and application correctness.

### Question 11

Why use API Gateway with Lambda?

**Answer:** API Gateway provides the API entry point and request management while Lambda provides serverless application processing.

### Question 12

When should asynchronous processing be considered?

**Answer:** When operations are long-running, resource-intensive, or do not require the final result in the initial HTTP response.

### Question 13

Why are stages useful?

**Answer:** They provide controlled deployment environments such as development, testing, and production.

### Question 14

Why is least privilege important for Lambda?

**Answer:** It limits the impact of a compromised or misconfigured function by granting only the permissions it requires.

### Question 15

Why should APIs be versioned?

**Answer:** Versioning can allow existing clients to continue functioning while new API contracts are introduced.

---

# 57. Quick Revision Summary

| Concept | Key Point |
|---|---|
| API Gateway | Managed API front door |
| API | Contract for software communication |
| REST API | Feature-rich API management option |
| HTTP API | Simplified HTTP API option |
| WebSocket API | Two-way real-time communication |
| Lambda | Serverless backend compute |
| Stage | Deployment environment |
| Endpoint | URL used to access an API |
| Authentication | Determines identity |
| Authorization | Determines permissions |
| IAM | AWS identity and access control |
| Cognito | User identity capabilities |
| Lambda Authorizer | Custom authorization logic |
| API Key | Client identification/usage management |
| Usage Plan | Usage controls for supported API configurations |
| Throttling | Limits request rate |
| Quota | Controls usage over a defined period |
| CORS | Browser cross-origin access control |
| Caching | Reduces repeated backend requests |
| Monitoring | Visibility into API behavior |
| Versioning | Supports controlled API evolution |
| Least Privilege | Grant only required permissions |

---

# 58. Final Takeaway

The key idea is:

> **Amazon API Gateway provides a managed front door for APIs and can connect clients to serverless backends such as AWS Lambda.**

A typical serverless flow is:

```text
Client
  |
  v
API Gateway
  |
  v
Lambda
  |
  v
AWS Service / Database
```

When designing an API, think about:

1. **Which API type fits the requirement?**
2. **How should clients authenticate?**
3. **How should access be authorized?**
4. **How should API consumers be identified and metered?**
5. **What throttling and quota controls are required?**
6. **Should processing be synchronous or asynchronous?**
7. **How should requests be validated?**
8. **How will errors be handled?**
9. **How will the API be monitored?**
10. **How will the API evolve without breaking clients?**
11. **How will backend permissions follow least privilege?**
12. **How will cost and performance be monitored?**

The most important architecture pattern to remember is:

```text
                 +------------------+
                 |      Client      |
                 +--------+---------+
                          |
                          v
                 +------------------+
                 |  API Gateway     |
                 +--------+---------+
                          |
                 +--------+---------+
                 |                  |
                 v                  v
              Lambda             Other
                 |               Backend
                 v
          AWS Services
```

API Gateway is most valuable when it provides a controlled, secure, observable API layer between clients and backend services.

---

## Disclaimer

This document is a learning aid based on the course description supplied by the user. It is not official AWS training material. AWS service features, limits, pricing, security recommendations, and terminology can change. Always consult the latest AWS documentation before implementing a production solution.
