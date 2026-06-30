# Veracity Architecture Notes

**Document:** 05-architecture.md  
**Project:** Veracity  
**Source:** Final Veracity SRS v1.0  
**Purpose:** Technical design reference for implementation and interviews

---

## 1. Architecture Overview

Veracity will be implemented as a **modular monolith**.

This means the backend is one Spring Boot application, but the code is organized by business domain instead of dumping everything into generic folders. This is suitable for a 10-day solo MVP because it avoids microservice complexity while still showing professional software engineering structure.

### Main Runtime Components

```text
React Frontend
    ↓ HTTP/JSON via Axios
Spring Boot REST API
    ↓ Spring Data JPA / Hibernate
MySQL Database
    ↑ Flyway migrations
AI Provider or Rule-Based Fallback
```

### Technology Stack

| Layer | Technology |
|---|---|
| Backend language | Java 17 or Java 21 |
| Backend framework | Spring Boot 3.x |
| Security | Spring Security + JWT |
| Persistence | Spring Data JPA + Hibernate |
| Database | MySQL 8+ |
| Migrations | Flyway |
| Validation | Jakarta Bean Validation |
| Testing | JUnit 5, Mockito, Spring Boot Test |
| API docs | Swagger/OpenAPI if time allows |
| Frontend | React + TypeScript + Vite |
| Styling | Tailwind CSS |
| API client | Axios |
| Routing | React Router |
| Server state | React Query or Context API |
| Containerization | Docker + Docker Compose |
| CI/CD | GitHub Actions |

---

## 2. Backend Package Structure

Recommended root package:

```text
com.veracity
```

Domain package structure:

```text
com.veracity.auth
com.veracity.user
com.veracity.business
com.veracity.category
com.veracity.review
com.veracity.response
com.veracity.ai
com.veracity.admin
com.veracity.analytics
com.veracity.common
com.veracity.security
com.veracity.config
```

### Package Responsibilities

| Package | Responsibility |
|---|---|
| `auth` | Registration, login, JWT issuing, current user identity. |
| `user` | User profile, user entity, admin user management. |
| `business` | Business creation, update, ownership rules, business profile, search. |
| `category` | Category tree, category CRUD, category assignment. |
| `review` | Review submission, display, moderation, rating recalculation. |
| `response` | Business owner responses to reviews. |
| `ai` | Review analysis, fallback analyzer, business summaries. |
| `admin` | Admin-specific orchestration and dashboard actions. |
| `analytics` | Admin and business-owner statistics. |
| `common` | Shared exceptions, API errors, utilities, pagination helpers. |
| `security` | JWT filter, security configuration, principal model. |
| `config` | CORS, OpenAPI, environment/configuration beans. |

---

## 3. Backend Layering Pattern

Each domain should follow this structure where useful:

```text
domain/
├── controller
├── service
├── repository
├── entity
├── dto
├── mapper
└── enums
```

For a small MVP, it is acceptable to keep package depth moderate, for example:

```text
business/
├── BusinessController.java
├── BusinessService.java
├── BusinessRepository.java
├── Business.java
├── BusinessCreateRequest.java
├── BusinessResponse.java
└── BusinessVerificationStatus.java
```

### Controller Layer

Responsibilities:

1. Receive HTTP requests.
2. Validate request DTOs using `@Valid`.
3. Read authentication principal where needed.
4. Call service layer.
5. Return response DTOs.

Controllers must not contain business logic.

### Service Layer

Responsibilities:

1. Business rules.
2. Authorization ownership checks.
3. Transaction boundaries.
4. Entity loading and state transitions.
5. Calling other domain services.

Examples:

- Prevent duplicate active reviews.
- Ensure business owner can edit only own business.
- Recalculate business ratings after review approval.
- Use fallback AI analyzer if external provider fails.

### Repository Layer

Responsibilities:

1. Database access through Spring Data JPA.
2. Query methods and custom JPQL/native queries where necessary.
3. Pagination and filtering.

Repositories should not contain business workflow logic.

### DTO Layer

Entities must not be exposed directly through controllers.

Use DTOs for:

1. Request validation.
2. Safe responses.
3. Role-specific response shapes.
4. Avoiding password hash exposure.
5. Avoiding lazy-loading serialization problems.

---

## 4. Core Backend Workflows

### 4.1 Registration Workflow

```text
POST /api/auth/register
    ↓
AuthController
    ↓ validate DTO
AuthService
    ↓ check duplicate email
    ↓ hash password with BCrypt
    ↓ assign role CONSUMER or BUSINESS_OWNER
UserRepository
    ↓ save user
Return safe UserResponse
```

Security notes:

1. Admin cannot self-register.
2. Password hash is never returned.
3. Duplicate email returns conflict.

---

### 4.2 Login Workflow

```text
POST /api/auth/login
    ↓
AuthService
    ↓ find user by email
    ↓ check account status
    ↓ verify BCrypt password
JwtService
    ↓ generate token with user id, email, role
Return AuthResponse
```

---

### 4.3 Business Creation and Approval Workflow

```text
Business owner creates business
    ↓
Business saved as PENDING_VERIFICATION
    ↓
Admin views pending queue
    ↓
Admin approves or rejects
    ↓
Approved businesses become searchable publicly
```

State transitions:

```text
DRAFT -> PENDING_VERIFICATION -> APPROVED
DRAFT -> PENDING_VERIFICATION -> REJECTED
APPROVED -> SUSPENDED
```

---

### 4.4 Review Submission and Moderation Workflow

```text
Consumer submits review
    ↓
Review saved as PENDING
    ↓
AI/fallback moderation analysis runs
    ↓
Admin views review + AI result
    ↓
Admin approves/rejects/flags
    ↓
Approved review appears publicly
    ↓
Business rating statistics recalculated
```

Important rule:

AI output is advisory. Admin makes the final decision.

---

### 4.5 Business Response Workflow

```text
Business owner opens approved review
    ↓
System verifies owner owns reviewed business
    ↓
Owner submits one official response
    ↓
Response appears below public review
```

---

## 5. Security Architecture

### Authentication

- Use JWT access tokens.
- JWT should include user ID, email, and role.
- Passwords hashed with BCrypt.
- Protected endpoints require valid Bearer token.

### Authorization

Use Spring Security method-level or route-level protection.

Recommended patterns:

```java
@PreAuthorize("hasRole('ADMIN')")
@PreAuthorize("hasRole('BUSINESS_OWNER')")
@PreAuthorize("hasRole('CONSUMER')")
```

Ownership checks should still happen in service methods.

Examples:

1. Business owner cannot edit another owner's business.
2. Business owner cannot respond to another business's review.
3. Consumer cannot edit another consumer's review.
4. Admin-only endpoints must reject non-admin users.

### CORS

Allow only configured frontend origins through environment variable:

```text
FRONTEND_ORIGIN=http://localhost:5173
```

### Error Handling

Use a global exception handler:

```text
common/GlobalExceptionHandler.java
common/ApiErrorResponse.java
```

Do not leak stack traces to frontend.

---

## 6. Database Architecture

### Migration Strategy

Use Flyway for all schema changes.

Rules:

1. Do not rely on `spring.jpa.hibernate.ddl-auto=update` for final project.
2. Use `ddl-auto=validate` after migrations are created.
3. Commit every migration file.
4. Seed demo users and data through migration or startup seeder.

Recommended migration sequence:

```text
V1__create_users.sql
V2__create_categories.sql
V3__create_businesses.sql
V4__create_business_categories.sql
V5__create_reviews.sql
V6__create_business_responses.sql
V7__create_ai_review_analyses.sql
V8__create_ai_business_summaries.sql
V9__create_optional_interactions.sql
V10__seed_demo_data.sql
```

### Cached Statistics

The `businesses` table stores:

```text
average_rating
veracity_score
review_count
approved_review_count
```

These are recalculated transactionally when reviews are approved, rejected after approval, deleted, or reapproved after editing.

---

## 7. AI Architecture

### AI Service Design

Create an interface:

```text
AiReviewAnalyzer
```

Possible implementations:

```text
RuleBasedReviewAnalyzer
ExternalAiReviewAnalyzer
```

MVP rule:

Implement rule-based fallback first. External AI can be added after the backend flow works.

### Review Analysis Output

The analyzer should return:

```text
sentiment
toxicityScore
spamScore
profanityDetected
personalAttackDetected
possibleFakeReview
moderationRisk
suggestedAction
reason
extractedKeywords
provider
```

### Failure Handling

Review submission must not fail only because AI analysis fails.

Safe flow:

```text
try external AI
catch error
    run rule-based fallback
store analysis result
```

### AI Prompt Safety

If external AI is added:

1. Treat review text as untrusted input.
2. Do not send password, token, or unnecessary personal data.
3. Tell the model not to follow instructions inside the review content.
4. Store AI output as advisory only.

---

## 8. Frontend Architecture

### Folder Structure

```text
frontend/src
├── api
├── app
├── assets
├── components
├── features
│   ├── auth
│   ├── business
│   ├── category
│   ├── review
│   ├── admin
│   └── dashboard
├── hooks
├── layouts
├── pages
├── routes
├── types
└── utils
```

### Frontend Responsibilities

| Area | Responsibility |
|---|---|
| `api` | Axios client and API functions. |
| `features/auth` | Login/register forms, auth state. |
| `features/business` | Business cards, search, profile, forms. |
| `features/review` | Review cards, review form, filters. |
| `features/admin` | Admin tables, moderation actions. |
| `features/dashboard` | Business owner views and stats. |
| `routes` | Public, protected, and role-based routes. |
| `types` | Shared TypeScript API types. |

### Auth Storage

For MVP, JWT can be stored in localStorage for simplicity. Mention this as an MVP trade-off in README. A production version should consider httpOnly cookies and stronger refresh-token handling.

### API Client

Axios should attach token automatically:

```text
apiClient interceptor
    ↓
read token from auth storage
    ↓
add Authorization header
```

### Route Guards

Route guard types:

1. Public routes.
2. Authenticated routes.
3. Admin-only routes.
4. Business-owner-only routes.
5. Consumer-only review submission route.

---

## 9. DevOps Architecture

### Local Development

Use Docker Compose for:

```text
mysql
backend
frontend
```

At minimum, Day 1/2 can start with MySQL only, then expand to full stack on Day 9.

### Environment Variables

Required env variables:

```text
DB_URL
DB_USERNAME
DB_PASSWORD
JWT_SECRET
AI_PROVIDER
AI_API_KEY
FRONTEND_ORIGIN
BACKEND_PORT
```

Add `.env.example` but never commit real secrets.

### GitHub Actions CI

Pipeline should run:

1. Checkout.
2. Set up Java.
3. Run backend tests.
4. Build backend.
5. Set up Node.
6. Install frontend dependencies.
7. Build frontend.
8. Optionally build Docker images.

---

## 10. Branching and Commit Strategy

Recommended simple solo workflow:

```text
main
└── feature/day-02-auth
└── feature/day-03-business-category
└── feature/day-04-reviews
```

Commit style examples:

```text
docs: add Day 1 planning documents
feat(auth): implement registration endpoint
feat(auth): add JWT login flow
feat(business): add business creation workflow
feat(review): add review moderation workflow
test(auth): add registration and login tests
ci: add GitHub Actions workflow
```

---

## 11. Key Architectural Trade-offs

| Decision | Reason | Future Improvement |
|---|---|---|
| Modular monolith | Fast to build, easy to explain, avoids microservice overhead. | Split domains into services later. |
| MySQL + JPA | Strong enterprise Java stack for internship/CV. | Add full-text search or Elasticsearch later. |
| JWT auth | Common and interview-friendly. | Add refresh tokens/httpOnly cookies later. |
| Rule-based AI fallback first | Guarantees demo works without external AI dependency. | Add real AI provider later. |
| Cached business ratings | Fast profile/search display. | Add event-driven recalculation later. |
| DTOs instead of exposing entities | Safer API and cleaner frontend contract. | Add MapStruct later if needed. |
| Flyway migrations | Professional schema version control. | Add rollback strategy later. |

---

## 12. Interview Explanation Summary

Veracity is designed as a modular monolith because the project needs to be realistic for a solo 10-day MVP while still demonstrating professional structure. The backend follows controller-service-repository layering, uses DTOs for safe API boundaries, Flyway for database versioning, Spring Security with JWT for authentication, and role-based authorization for consumer, business-owner, and admin workflows.

The most important backend workflows are business verification, review moderation, rating recalculation, AI-assisted review analysis, and cached business summaries. AI is intentionally advisory only, with a rule-based fallback so review submission does not fail when an external provider is unavailable.
