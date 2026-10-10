# AnyCompanyRead - Design Clarification Questions

**Version:** 1.3  
**Date:** October 9, 2026  
**AI-DLC Phase:** Design Clarification  
**Approved By:** Human reviewer  
**Approved Date:** October 9, 2026

---

## Overview

This document tracks design clarification questions and approved answers for the AnyCompanyRead project. Questions are answered in batches of 5, covering all major architectural domains.

---

## Question Set 1 - APPROVED

### Q1.1: Frontend Architecture

**Approved Decision:** React + Vite

**Rationale:** Vite provides faster development experience, hot module replacement, and efficient builds compared to Next.js SSR. Since SEO is handled via server-side rendering via Vite plugins (e.g., vite-ssg) or pre-rendering, this meets the SEO requirements while keeping the frontend simpler and more developer-friendly.

---

### Q1.2: Backend Architecture

**Approved Decision:** Node.js + Express with Modular Monolith

**Rationale:** Express is lightweight, well-understood, and pairs naturally with Vite. The modular monolith approach provides clear domain boundaries (separate modules for auth, catalog, cart, etc.) that can evolve into microservices later if needed.

---

### Q1.3: Data Architecture

**Approved Decision:** PostgreSQL

**Rationale:** Aurora PostgreSQL is the right choice for complex queries, transactions, and relational data. This decision covers both the database engine choice (PostgreSQL) and the managed service (Aurora).

---

### Q1.4: Authentication

**Revoked Decision:** Amazon Cognito User Pools (original decision superseded)

**Approved Revision:** Application-managed authentication using secure password hashing and JWT-based sessions/tokens

**Rationale:** For the locally runnable MVP, Amazon Cognito requires AWS infrastructure. Replaced with application-managed authentication that uses:
- bcrypt or argon2 for secure password hashing
- JWT tokens for session management
- Local user management in PostgreSQL
- External integrations (payments, 3PL, DRM) behind provider interfaces/adapters for mock implementations

**Notes:** Architecture remains cloud-portable - AWS Cognito can replace application-managed auth in future deployments. External integrations use adapter pattern to support both production (Stripe, real 3PL, DRM) and local mock implementations.

---

### Q1.5: Payment Gateway

**Approved Decision:** Stripe

**Rationale:** Developer-friendly API, built-in PCI-DSS compliance, guest checkout support (required), and strong US/Canada support.

---

## Question Set 2 - APPROVED

### Q2.1: Service Boundaries - Domain Decomposition

**Approved Decision:** Single module per business domain within the Modular Monolith

**Rationale:** Clear domain boundaries prevent coupling and enable independent development. Each module (auth, catalog, cart, checkout, etc.) will have its own models, services, and routes while being deployed as a single application. This approach provides maintainability for MVP while allowing future decomposition.

**Notes:** Approved by human reviewer with clarification that all modules will be part of a single Node.js/Express modular monolith application.

---

### Q2.2: API Design - REST vs GraphQL

**Approved Decision:** REST API for the MVP

**Rationale:** REST is simpler, well-understood, and sufficient for MVP needs. GraphQL adds complexity that isn't needed for the initial release. Book discovery can be effectively handled with REST endpoints and query parameters for filtering.

**Notes:** Approved by human reviewer. Advanced search capabilities requiring GraphQL may be reconsidered in Phase 2.

---

### Q2.3: Search & Recommendations - Search Provider

**Approved Decision:** PostgreSQL Full-Text Search for the MVP

**Rationale:** Using PostgreSQL full-text search keeps search within the existing PostgreSQL architecture, reducing infrastructure complexity. This approach satisfies MVP requirements (500K+ SKUs, <500ms response) without the operational overhead of OpenSearch.

**Implementation Notes:**
- Use PostgreSQL full-text search with GiST/GIN indexes for performance
- Implement basic relevance ranking based on title, author, and description
- Recommendations remain simple and requirements-driven (popular, new, related products)
- Advanced search infrastructure may be considered after the MVP if scale requires it

---

### Q2.4: 3PL Integration - Inventory Management

**Approved Decision:** REST-based 3PL integration with periodic inventory synchronization

**Rationale:** Real-time synchronization is not required for the MVP. Periodic sync (e.g., every 15 minutes) reduces complexity while still maintaining reasonable inventory accuracy. Order submission and shipment status updates will be supported through the provider API where available.

**Implementation Notes:**
- REST API integration with 3PL for order submission
- Periodic inventory sync (15-minute intervals) via API polling
- Real-time shipment status updates via webhook/API polling where supported
- Graceful handling of sync failures with retry logic

---

### Q2.5: Digital Delivery & DRM

**Approved Decision:** External DRM provider behind an internal DRM abstraction

**Rationale:** Using an abstraction layer provides flexibility to evaluate and switch DRM providers without binding the architecture to a specific solution. The abstraction ensures that DRM requirements (content protection, device limits, expiration) are met while maintaining future flexibility.

**Implementation Notes:**
- Design DRM abstraction interface for provider agnosticism
- Provider must satisfy strict DRM requirements already approved
- Support for e-book and audiobook delivery with DRM
- Integration with digital fulfillment workflow

---

## Question Set 3 - APPROVED (Local-First Architecture)

### Q3.1: Runtime and Deployment

**Approved Decision:** Local-first Node.js runtime

**Rationale:** The MVP must be fully runnable and demonstrable on a local development machine without requiring an AWS account.

**Implementation Notes:**
- React frontend and Express backend run locally (npm run dev)
- Docker may be used for supporting infrastructure (PostgreSQL)
- Cloud deployment uses the same codebase with different environment configuration
- Local .env files for development, environment-specific configs for production

---

### Q3.2: Caching Strategy

**Approved Decision:** No dedicated caching infrastructure for MVP

**Rationale:** Keep MVP simple and local-runnable. Use application-level caching only if demonstrated to be necessary.

**Implementation Notes:**
- In-memory caching via node-cache or simple LRU cache for request-level optimization
- No Redis, CloudFront, or dedicated caching services for MVP
- Add caching infrastructure in Phase 2 if performance requirements demand it

---

### Q3.3: Database

**Approved Decision:** PostgreSQL running locally or through Docker

**Rationale:** Keep MVP local-runnable while maintaining production scalability path.

**Implementation Notes:**
- PostgreSQL via Docker Compose for local development
- Direct PostgreSQL connection for production (AWS RDS or other managed service)
- No RDS Proxy or AWS-managed database dependencies for MVP

---

### Q3.4: Logging and Observability

**Approved Decision:** Structured application logging and local development logs

**Rationale:** Keep MVP local-runnable while maintaining production observability path.

**Implementation Notes:**
- Structured JSON logging (e.g., pino, winston) for both local and production
- Local: logs to console/file
- Production: logs to CloudWatch or external logging service
- No CloudWatch or X-Ray required for MVP

---

### Q3.5: Environment and Configuration

**Approved Decision:** Environment variables and local .env files

**Rationale:** Keep MVP local-runnable without AWS service dependencies.

**Implementation Notes:**
- Environment variables for all configuration (database, API keys, feature flags)
- Local .env files for development
- Production uses environment-specific configs (AWS Secrets Manager or equivalent)
- No AWS Parameter Store or Secrets Manager required for MVP

---

## Question Set 4 (To Be Answered)

...

---

## Architecture Revisions (October 9, 2026)

### Revision 1: Authentication (Q1.4) - Superseded
**Original Decision:** Amazon Cognito User Pools  
**Revised Decision:** Application-managed authentication using secure password hashing and JWT-based sessions/tokens

**Rationale:** For the locally runnable MVP, Amazon Cognito requires AWS infrastructure. The revised approach uses:
- bcrypt or argon2 for secure password hashing
- JWT tokens for session management
- Local user management in PostgreSQL
- External integrations behind adapter interfaces for mock implementations

**Notes:** Architecture remains cloud-portable - AWS Cognito can replace application-managed auth in future deployments.

### Revision 2: Question Set 3 - Local-First Architecture
**Original Decisions:** AWS-centric services (Lambda, ElastiCache, RDS Proxy, CloudWatch, Parameter Store/Secrets Manager)  
**Revised Decisions:** Local-first approach with Docker for supporting infrastructure

**Rationale:** MVP must be fully runnable and demonstrable on a local development machine without requiring an AWS account. Cloud deployment uses the same codebase with different environment configuration.

**Revised Decisions Summary:**
| Original | Revised |
|----------|---------|
| Lambda + Fargate hybrid | Local Node.js runtime |
| CloudFront + ElastiCache Redis | Application-level caching only |
| RDS Proxy | Direct PostgreSQL connection |
| CloudWatch + X-Ray | Structured JSON logging |
| Parameter Store + Secrets Manager | Environment variables + .env files |

---

## Approved Answers Log

| Question | Date | Answer | Decision Maker | Status |
|----------|------|--------|----------------|--------|
| Q1.1 | Oct 9, 2026 | React + Vite | Human | APPROVED |
| Q1.2 | Oct 9, 2026 | Node.js + Express + Modular Monolith | Human | APPROVED |
| Q1.3 | Oct 9, 2026 | PostgreSQL | Human | APPROVED |
| Q1.4 | Oct 9, 2026 | Application-managed auth (JWT) | Human | APPROVED - REVISED |
| Q1.5 | Oct 9, 2026 | Stripe | Human | APPROVED |
| Q2.1 | Oct 9, 2026 | Single module per domain | Human | APPROVED |
| Q2.2 | Oct 9, 2026 | REST API for MVP | Human | APPROVED |
| Q2.3 | Oct 9, 2026 | PostgreSQL Full-Text Search | Human | APPROVED |
| Q2.4 | Oct 9, 2026 | REST + periodic sync | Human | APPROVED |
| Q2.5 | Oct 9, 2026 | DRM abstraction layer | Human | APPROVED |
| Q3.1 | Oct 9, 2026 | Local-first Node.js runtime | Human | APPROVED - REVISED |
| Q3.2 | Oct 9, 2026 | No dedicated caching | Human | APPROVED - REVISED |
| Q3.3 | Oct 9, 2026 | PostgreSQL via Docker | Human | APPROVED - REVISED |
| Q3.4 | Oct 9, 2026 | Structured JSON logging | Human | APPROVED - REVISED |
| Q3.5 | Oct 9, 2026 | Environment variables + .env | Human | APPROVED - REVISED |

---

## Next Steps

1. Design Clarification complete - all 15 questions answered
2. Architecture revisions documented and approved
3. Ready to generate Application Design artifacts:
   - `application-design.md`
   - `components.md`
   - `component-methods.md`
   - `component-dependency.md`
   - `services.md`

---

*End of Design Clarification Questions*


---

*End of Design Clarification Questions*
