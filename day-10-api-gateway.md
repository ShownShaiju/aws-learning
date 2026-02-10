# AWS – Day 10: API Gateway

## What is an API

An API is:
>An API is a contract exposed by a server that defines:
- What requests are allowed
- How to ask
- What response you get

Client uses API. 
Server exposes API.


An API hides:
- Database details
- Business logic
- Internal structure
- Clients never talk to internals directly.
---
### HTTP API basics

Common HTTP methods:
- GET → read
- POST → create
- PUT → update
- DELETE → delete

APIs exist to provide abstraction.
---
## **API Gateway**

Api gateways is a aws managed management layer that sits between the clinet and the server (its actually server side) and recieve `APIs` handling: 
- HTTPS
- URLs
- Authentication
- Rate limiting
- Validation
- Logging

for serverless functions like `lambda`.

---
## API Gateway vs ALB

**EC2-based architecture (stateful servers)**

>Browser
 → DNS
 → Load Balancer (ALB)
 → EC2
   → Python app (FastAPI / Flask / Django)

Routing such as /login or /home is handled by:
- The application framework
- Optionally by ALB using path-based routing

> API Gateway is NOT required here

---

**Lambda-based architecture (serverless)**

API Gateway is used when the backend is Lambda.

>Browser
 → DNS
 → API Gateway
 → Lambda

What happens:
1) Browser requests: `GET /login`
2) DNS resolves to API Gateway
3) API Gateway:
    - Accepts HTTPS
    - Validates request
    - Applies rate limits
4) API Gateway invokes Lambda
5) Lambda runs code
6) Response returned

Lambda cannot listen on ports hence api gateway is mandatory.

>**API Gateway + Lambda replaces ALB + EC2**

---

### Who handles routing ?

Depends on architecture:

EC2-based:
- Application framework handles routing
- ALB can optionally do coarse routing

Lambda-based:
- API Gateway defines routes
- Each route triggers a Lambda function (or same function)

---
## What i learned today:

**Does API Gateway has 1:1 dedicated public IP like NAT?**

    API gateway like ALB dosent have a dedicated IP rather it uses AWS-managed public ips (Aws Edge infrastructure) that are exposed via DNS



