# Veracity Product Backlog

**Document:** 02-product-backlog.md  
**Project:** Veracity  
**Scope status:** Frozen MVP scope  
**Source:** Final Veracity SRS v1.0  
**Planning mode:** 10-day implementation plan with Day 1 reserved for planning and design

---

## 1. Backlog Strategy

The backlog is organized around the MVP requirements in the SRS. The goal is not to build every possible review-platform feature, but to deliver a complete, explainable, internship-ready system with a strong Spring Boot backend, React frontend, MySQL database, authentication, role-based workflows, review moderation, AI-assisted analysis, testing, Docker, CI/CD, and documentation.

### Priority Levels

| Priority | Meaning |
|---|---|
| Must | Required for the final MVP demo and CV claim. |
| Should | Valuable if time allows after MVP is stable. |
| Could | Nice extension, not required for first release. |
| Out of Scope | Explicitly excluded from the 10-day implementation. |

### Main User Roles

| Role | Description |
|---|---|
| Guest | Can search and view public business profiles and approved reviews. |
| Consumer | Can register, log in, submit reviews, and manage own reviews. |
| Business Owner | Can create/manage business profiles and respond to reviews. |
| Admin | Can approve businesses, moderate reviews, manage users/categories, and view platform statistics. |
| AI System Service | An internal backend service that assists with review analysis and business summaries. |

---

## 2. MVP Epic Breakdown

| Epic ID | Epic | Priority | Target Day |
|---|---|---:|---:|
| E01 | Project Planning and Design | Must | Day 1 |
| E02 | Authentication and Authorization | Must | Day 2 |
| E03 | Category and Business Module | Must | Day 3 |
| E04 | Business Verification Workflow | Must | Day 3 |
| E05 | Search and Public Business Profiles | Must | Day 3-4 |
| E06 | Review Submission and Moderation | Must | Day 4 |
| E07 | Business Responses | Must | Day 5 |
| E08 | AI-Assisted Review Moderation | Must | Day 5 |
| E09 | AI Business Review Summary | Must | Day 6 |
| E10 | Admin Dashboard | Must | Day 8 |
| E11 | Business Owner Dashboard | Must | Day 8 |
| E12 | Testing and Quality Assurance | Must | Day 6-9 |
| E13 | Docker, CI/CD, and Documentation | Must | Day 9-10 |
| E14 | Frontend Public Pages | Must | Day 7 |
| E15 | Frontend Protected Pages | Must | Day 8 |

---

## 3. Detailed Product Backlog

### E01 — Project Planning and Design

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-001 | Save finalized SRS as `docs/01-srs.md`. | Must | Markdown SRS exists in docs folder and matches frozen scope. | No scope expansion after this. |
| PB-002 | Create product backlog as `docs/02-product-backlog.md`. | Must | Backlog contains epics, stories, priorities, and target implementation days. | This file. |
| PB-003 | Create ERD as `docs/03-erd.md`. | Must | ERD includes users, categories, businesses, reviews, responses, AI analysis, AI summaries, claims, votes/reports. | Mermaid ERD format. |
| PB-004 | Create API contract as `docs/04-api-contract.md`. | Must | API endpoints, roles, request/response notes, and common response formats are documented. | Used before coding controllers. |
| PB-005 | Create architecture notes as `docs/05-architecture.md`. | Must | Backend/frontend architecture, package structure, security model, AI strategy, DB strategy, and deployment notes are documented. | Used to explain in interviews. |
| PB-006 | Create test plan as `docs/06-test-plan.md`. | Must | Backend, integration, security, frontend, and manual acceptance testing are planned. | Backend tests prioritized. |
| PB-007 | Create GitHub repository. | Must | Repository exists with README, docs folder, and initial planning commit. | Manual GitHub step. |
| PB-008 | Create GitHub Projects board. | Must | Board contains backlog columns and MVP tasks. | Manual GitHub step. |

### E02 — Authentication and Authorization

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-009 | As a guest, I want to register as a consumer or business owner. | Must | Valid registration succeeds; duplicate email rejected; weak password rejected; password stored with BCrypt; response does not expose password hash. | FR-01 |
| PB-010 | As a registered user, I want to log in with email and password. | Must | Valid login returns JWT; invalid login returns generic unauthorized error. | FR-02 |
| PB-011 | As the system, I want JWT authentication for protected APIs. | Must | Missing/invalid token rejected; valid token identifies user and role. | Security requirement. |
| PB-012 | As the system, I want role-based authorization. | Must | Admin endpoints require ADMIN; business-owner endpoints require BUSINESS_OWNER; consumer review endpoints require CONSUMER. | Tests required. |
| PB-013 | As an authenticated user, I want to view/update my profile. | Must | User can view own profile and update allowed fields only. | FR-03 |
| PB-014 | As an admin, I want seeded admin credentials. | Must | Admin user exists for demo; admin cannot be self-created from public registration. | Seed with Flyway or startup runner. |

### E03 — Category and Business Module

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-015 | As an admin, I want to create/update/deactivate categories. | Must | Active categories appear publicly; inactive categories hidden; in-use categories cannot be deleted. | FR-04 |
| PB-016 | As a guest, I want to browse categories. | Must | Category list/tree endpoint works. | Public endpoint. |
| PB-017 | As a business owner, I want to create a business profile. | Must | Business is saved as PENDING_VERIFICATION; only business owners can create. | FR-05 |
| PB-018 | As a business owner, I want to edit my submitted business. | Must | Owner can edit own business; cannot edit another owner's business. | Ownership test required. |
| PB-019 | As the system, I want primary and secondary category assignments. | Must | One primary category required; up to three secondary categories supported. | BusinessCategory join table. |

### E04 — Business Verification Workflow

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-020 | As an admin, I want to view pending businesses. | Must | Pending queue shows PENDING_VERIFICATION businesses. | Admin dashboard/API. |
| PB-021 | As an admin, I want to approve businesses. | Must | Approved business becomes publicly searchable. | FR-07 |
| PB-022 | As an admin, I want to reject businesses with reason. | Must | Rejected business is hidden publicly; owner can view rejection reason. | FR-07 |
| PB-023 | As an admin, I want to suspend businesses. | Must | Suspended business hidden from public listing/profile. | FR-17 |
| PB-024 | As a business owner, I want to claim an unclaimed business. | Should | Claim request can be submitted and approved/rejected by admin. | Can be simplified if time is tight. |

### E05 — Search and Public Business Profiles

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-025 | As a guest, I want to search businesses by keyword. | Must | Search returns approved businesses by name/description/category/city. | FR-08 |
| PB-026 | As a guest, I want to filter businesses. | Must | Category, city/district, rating minimum, claimed status, and review count filters work. | Paginated. |
| PB-027 | As a guest, I want sorted search results. | Must | Sort by relevance/basic, highest rated, most reviewed, newest. | Keep query simple first. |
| PB-028 | As a guest, I want to view a public business profile. | Must | Shows details, rating, review count, AI summary if available, approved reviews only. | FR-09 |
| PB-029 | As a guest, I want to see trust/safety information. | Must | Business profile includes moderation and AI summary disclaimers. | FR-22 |
| PB-030 | As the system, I want similar businesses. | Should | Similar businesses by category/city shown when available. | Keep optional. |

### E06 — Review Submission and Moderation

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-031 | As a consumer, I want to submit a review. | Must | Only authenticated consumers can review approved businesses; rating 1-5; title/content validation; status PENDING. | FR-10 |
| PB-032 | As the system, I want to prevent duplicate active reviews. | Must | One active review per user per business. | DB constraint + service check. |
| PB-033 | As an admin, I want to view pending reviews. | Must | Pending reviews visible in admin queue with AI result. | FR-18 |
| PB-034 | As an admin, I want to approve/reject/flag reviews. | Must | Approved reviews become public; rejected/flagged hidden publicly unless policy allows. | Rating recalculation after approval. |
| PB-035 | As a guest, I want to view approved reviews only. | Must | Public profile displays approved reviews, filters, sorting, and rating distribution. | FR-11 |
| PB-036 | As a consumer, I want to view my review history. | Should | User can view own reviews. | SH-02 |

### E07 — Business Responses

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-037 | As a business owner, I want to respond to an approved review on my business. | Must | Owner can respond only to own business reviews; one response per review. | FR-12 |
| PB-038 | As a business owner, I want to edit/delete my response. | Must | Response can be updated/deleted by owner only. | FR-12 |
| PB-039 | As a public user, I want to see business responses below reviews. | Must | Approved review card displays response if present. | Public profile. |

### E08 — AI-Assisted Review Moderation

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-040 | As the system, I want AI/fallback analysis to run after review submission. | Must | Review submission succeeds even if external AI fails; fallback result stored. | FR-15 |
| PB-041 | As an admin, I want to see AI moderation output. | Must | Admin sees sentiment, toxicity, spam, risk, suggested action, reason, keywords. | Advisory only. |
| PB-042 | As an admin, I want to manually reanalyze a review. | Must | Admin can trigger analysis again. | POST reanalyze. |
| PB-043 | As the system, I want safe prompt boundaries. | Must | Review text treated as untrusted input; no sensitive auth data sent to AI. | Security note. |

### E09 — AI Business Review Summary

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-044 | As the system, I want to generate business summaries from approved reviews only. | Must | Minimum 3 approved reviews; fallback message if insufficient. | FR-16 |
| PB-045 | As a guest, I want to see AI summary and top mentions on business profile. | Must | Summary text, positive themes, negative themes, top mentions shown when available. | Cached in DB. |
| PB-046 | As an admin/business owner, I want to regenerate summary. | Must | Authorized roles can regenerate; result cached. | Access rules. |

### E10 — Admin Dashboard

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-047 | As an admin, I want dashboard statistics. | Must | Total users, businesses, pending reviews, rating stats, sentiment stats if available. | FR-21 |
| PB-048 | As an admin, I want business management UI. | Must | Pending/approved/rejected/suspended filters; approve/reject/suspend actions. | Frontend Day 8. |
| PB-049 | As an admin, I want review management UI. | Must | Pending/flagged/status/risk filters; approve/reject/flag actions. | Frontend Day 8. |
| PB-050 | As an admin, I want user management. | Must | View/search/filter users; suspend/reactivate. | FR-19 |
| PB-051 | As an admin, I want category management. | Should | Create/update/deactivate category from UI. | Backend must exist. |

### E11 — Business Owner Dashboard

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-052 | As a business owner, I want to view my businesses. | Must | Only owned businesses shown. | FR-20 |
| PB-053 | As a business owner, I want to view business verification status. | Must | Status badge and rejection reason if rejected. | Dashboard. |
| PB-054 | As a business owner, I want to view reviews for my business. | Must | Owner sees approved reviews and can respond. | No moderation controls. |
| PB-055 | As a business owner, I want basic analytics. | Should | Average rating, review count, star distribution, AI summary. | Useful for CV. |

### E12 — Testing and Quality Assurance

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-056 | Write AuthService tests. | Must | Registration, duplicate email, login, password hashing tested. | Unit/service. |
| PB-057 | Write BusinessService tests. | Must | Business creation, update, ownership, approval flow tested. | Service. |
| PB-058 | Write ReviewService tests. | Must | Submission, duplicate prevention, validation, rating recalculation tested. | Service. |
| PB-059 | Write AdminReviewService tests. | Must | Approve/reject/flag workflow tested. | Service. |
| PB-060 | Write AI fallback tests. | Must | External failure does not block review submission; fallback analysis stored. | Critical. |
| PB-061 | Write security integration tests. | Must | Unauthorized user blocked from admin endpoints; owner cannot manage another owner's business. | High-value. |
| PB-062 | Create manual acceptance checklist. | Must | Demo flows can be tested end-to-end. | Use docs/06-test-plan.md. |

### E13 — Docker, CI/CD, and Documentation

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-063 | Add Docker Compose for local development. | Must | MySQL, backend, and frontend can run through compose. | DB first, full stack later. |
| PB-064 | Add backend Dockerfile. | Must | Backend image builds. | Day 9. |
| PB-065 | Add frontend Dockerfile. | Must | Frontend image builds. | Day 9. |
| PB-066 | Add `.env.example`. | Must | Required env vars documented without real secrets. | Security. |
| PB-067 | Add GitHub Actions CI. | Must | Backend tests and frontend build run on push/PR. | Day 9. |
| PB-068 | Write final README. | Must | Setup, architecture, features, API, demo users, screenshots, and testing explained. | Day 10. |
| PB-069 | Add API documentation. | Should | Swagger/OpenAPI available or documented via API contract. | SH-08. |

### E14 — Frontend Public Pages

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-070 | Build homepage. | Must | Hero search, categories, featured businesses, CTA sections. | Day 7. |
| PB-071 | Build search results page. | Must | Query, filters, result cards, pagination/empty/loading states. | Day 7. |
| PB-072 | Build business profile page. | Must | Header, details, ratings, AI summary, review list, write-review CTA. | Day 7. |
| PB-073 | Build login/register pages. | Must | Forms validate and call backend. | Day 7. |
| PB-074 | Build review form page. | Must | Authenticated consumer can submit review. | Day 7. |

### E15 — Frontend Protected Pages

| ID | User Story / Task | Priority | Acceptance Criteria | Notes |
|---|---|---|---|---|
| PB-075 | Implement auth state and protected routes. | Must | Unauthorized users redirected; role-restricted routes blocked. | Context or React Query. |
| PB-076 | Build admin dashboard pages. | Must | Admin can manage businesses/reviews/users. | Day 8. |
| PB-077 | Build business owner dashboard. | Must | Owner can view/edit businesses and respond to reviews. | Day 8. |
| PB-078 | Build profile/my reviews pages. | Should | User can view profile and review history. | Time permitting. |

---

## 4. Out-of-Scope Backlog Items for v1.0

These are intentionally excluded from the 10-day MVP:

- Native mobile applications
- Payment integration
- Subscription tiers
- SMS verification
- Real Google Maps integration
- Appointment scheduling
- Loyalty program
- Trusted reviewer program
- Public third-party API
- Advanced SEO automation
- Social media integration
- Competitive analysis tools
- Blue-green deployment
- Full production monitoring
- 10,000 concurrent user support
- Real-time notifications
- Machine learning model training
- Web scraping external review platforms

---

## 5. GitHub Projects Board Structure

Recommended board name:

```text
Veracity MVP Build
```

Recommended columns/status values:

| Status | Meaning |
|---|---|
| Backlog | Not started yet. |
| Ready | Clear enough to start. |
| In Progress | Currently being implemented. |
| Review/Test | Built but needs testing/review. |
| Done | Meets Definition of Done. |
| Deferred | Should/Could item postponed. |

Recommended custom fields:

| Field | Values |
|---|---|
| Priority | Must, Should, Could |
| Area | Backend, Frontend, Database, AI, DevOps, Docs, Testing |
| Target Day | Day 1 to Day 10 |
| Risk | Low, Medium, High |

---

## 6. Definition of Ready

A backlog item is ready when:

1. The purpose is clear.
2. The user role or system actor is clear.
3. Acceptance criteria are written.
4. Required API/database impact is known.
5. Security or authorization rules are identified.
6. The item is small enough to complete within a focused coding session.

---

## 7. Definition of Done

A feature is done only when:

1. Backend endpoint is implemented if required.
2. DTO validation exists.
3. Authorization rules are enforced.
4. Database migration exists if schema changed.
5. Frontend is connected if UI is required.
6. Happy path is manually tested.
7. Main failure cases are handled.
8. Relevant backend tests are written.
9. No sensitive data is exposed.
10. README/API docs are updated when needed.
