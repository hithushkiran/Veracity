# Veracity Test Plan

**Document:** 06-test-plan.md  
**Project:** Veracity  
**Source:** Final Veracity SRS v1.0  
**Purpose:** Testing strategy and acceptance checklist for the MVP

---

## 1. Testing Goals

The goal of testing Veracity is to prove that the critical business workflows work correctly and securely.

Testing priority:

1. Authentication and role-based authorization.
2. Business creation and admin approval.
3. Review submission and admin moderation.
4. AI/fallback moderation reliability.
5. Rating recalculation correctness.
6. Business owner response authorization.
7. Public visibility rules.
8. Docker/CI readiness.

Frontend tests are useful, but backend tests are more important for this project because most interview-worthy logic is in the backend.

---

## 2. Test Types

| Test Type | Purpose | Priority |
|---|---|---|
| Unit tests | Test isolated service methods and utility logic. | Must |
| Service-layer tests | Test business rules without full HTTP stack. | Must |
| Repository tests | Test custom queries and persistence behavior. | Should |
| Controller/security integration tests | Test API endpoints and role restrictions. | Must |
| End-to-end manual tests | Verify complete user journeys through frontend. | Must |
| Frontend component tests | Validate key forms/pages. | Should |
| API collection tests | Manual/automated Postman or Bruno requests. | Should |
| Performance smoke tests | Basic response-time check for demo data. | Could |

---

## 3. Test Environment

### Local Development

```text
Backend: Spring Boot test profile
Database: H2 for unit/integration tests or Testcontainers MySQL if time allows
Frontend: Vite local dev server
API testing: Postman/Bruno/cURL
```

### Recommended Test Profiles

```text
application-test.properties
```

Test database options:

| Option | Pros | Cons | Recommendation |
|---|---|---|---|
| H2 | Fast and simple. | Some MySQL behavior differs. | Good for 10-day MVP. |
| Testcontainers MySQL | Realistic MySQL tests. | Slower and more setup. | Add only if time allows. |
| Local MySQL | Real environment. | Can pollute dev data. | Use for manual testing. |

For the MVP, use H2 or test profile first. If time permits, add Testcontainers later.

---

## 4. Backend Unit and Service Tests

### 4.1 AuthServiceTest

| Test Case | Expected Result |
|---|---|
| Register valid consumer | User saved with CONSUMER role and BCrypt password. |
| Register valid business owner | User saved with BUSINESS_OWNER role. |
| Register duplicate email | Throws conflict/business exception. |
| Register invalid admin role | Public registration rejects ADMIN role. |
| Login valid credentials | Returns JWT/auth response. |
| Login invalid password | Throws unauthorized exception. |
| Login suspended user | Throws forbidden exception. |
| Password hashing check | Stored password is not plain text. |

---

### 4.2 UserServiceTest

| Test Case | Expected Result |
|---|---|
| Get own profile | Returns safe user DTO. |
| Update own profile | Full name/profile image updated. |
| Attempt role update through profile | Role is unchanged/rejected. |
| Admin suspends user | User status becomes SUSPENDED. |
| Admin activates user | User status becomes ACTIVE. |

---

### 4.3 CategoryServiceTest

| Test Case | Expected Result |
|---|---|
| Create parent category | Category saved with unique slug. |
| Create child category | Child references parent. |
| Get category tree | Parent contains children. |
| Deactivate category | Category hidden from public results. |
| Deactivate/delete in-use category | Blocked or safely handled. |

---

### 4.4 BusinessServiceTest

| Test Case | Expected Result |
|---|---|
| Business owner creates business | Business saved as PENDING_VERIFICATION. |
| Consumer creates business | Forbidden. |
| Guest creates business | Unauthorized. |
| Business owner edits own business | Update succeeds. |
| Business owner edits another owner's business | Forbidden. |
| Duplicate business check | Duplicate name/city/website rejected or warned. |
| Public search excludes pending/rejected/suspended | Only approved businesses returned. |
| Search by city/category/rating | Filters work correctly. |

---

### 4.5 AdminBusinessServiceTest

| Test Case | Expected Result |
|---|---|
| Admin approves pending business | Status becomes APPROVED and public search includes it. |
| Admin rejects pending business | Status becomes REJECTED and reason saved. |
| Admin suspends business | Status becomes SUSPENDED and public search excludes it. |
| Non-admin approves business | Forbidden. |
| Owner approves own business | Forbidden. |

---

### 4.6 ReviewServiceTest

| Test Case | Expected Result |
|---|---|
| Consumer submits valid review | Review saved as PENDING. |
| Review for pending business | Rejected. |
| Business owner submits consumer review | Rejected unless dual-role support is implemented. |
| Guest submits review | Unauthorized. |
| Rating below 1 or above 5 | Validation error. |
| Short title/content | Validation error. |
| Duplicate active review | Conflict error. |
| Edit own review | Update succeeds. |
| Edit another user's review | Forbidden. |
| Delete own review | Status becomes DELETED. |

---

### 4.7 AdminReviewServiceTest

| Test Case | Expected Result |
|---|---|
| Admin approves pending review | Status becomes APPROVED; approvedAt set. |
| Admin rejects pending review | Status becomes REJECTED; reason saved. |
| Admin flags review | Status becomes FLAGGED. |
| Non-admin moderates review | Forbidden. |
| Approved review appears publicly | Business profile includes review. |
| Rejected/flagged/deleted review hidden publicly | Public profile excludes review. |

---

### 4.8 RatingCalculationServiceTest

| Test Case | Expected Result |
|---|---|
| No approved reviews | Average rating is 0/null based on implementation decision. |
| One approved review | Average equals rating. |
| Multiple approved reviews | Average calculated correctly and rounded to one decimal. |
| Pending review ignored | Average does not include pending review. |
| Rejected review ignored | Average does not include rejected review. |
| Previously approved review rejected | Average recalculated. |
| Star distribution | Counts and percentages are accurate. |

---

### 4.9 BusinessResponseServiceTest

| Test Case | Expected Result |
|---|---|
| Owner responds to review on own business | Response saved. |
| Owner responds to review on another business | Forbidden. |
| Response to pending/rejected review | Rejected. |
| Duplicate response for same review | Conflict error. |
| Owner edits own response | Update succeeds. |
| Owner deletes own response | Delete succeeds. |

---

### 4.10 AiReviewServiceTest

| Test Case | Expected Result |
|---|---|
| Analyze normal review | Low risk analysis stored. |
| Analyze review with banned words | Toxicity/profanity risk detected. |
| Analyze promotional/spam review | Spam risk detected. |
| External AI unavailable | Rule-based fallback result stored. |
| AI failure during review submission | Review submission still succeeds. |
| Admin reanalysis | Existing analysis updated/replaced. |

---

### 4.11 AiBusinessSummaryServiceTest

| Test Case | Expected Result |
|---|---|
| Fewer than 3 approved reviews | Fallback message returned. |
| 3+ approved reviews | Summary generated and cached. |
| Pending/rejected reviews exist | Summary uses approved reviews only. |
| Regenerate summary | Cached summary updated. |
| Unauthorized regeneration | Forbidden. |

---

## 5. Integration Test Flows

### Flow 1 — Register and Login

1. Register consumer.
2. Login with credentials.
3. Receive JWT.
4. Call `/api/auth/me` with token.
5. Confirm safe user response.

Expected: authentication flow works end-to-end.

---

### Flow 2 — Business Approval

1. Register business owner.
2. Business owner logs in.
3. Business owner creates business.
4. Business status is PENDING_VERIFICATION.
5. Public search does not show business.
6. Admin logs in.
7. Admin approves business.
8. Public search shows business.

Expected: only approved businesses are public.

---

### Flow 3 — Review Moderation

1. Register/login consumer.
2. Consumer submits review for approved business.
3. Review status is PENDING.
4. AI/fallback analysis exists.
5. Public business profile does not show pending review.
6. Admin approves review.
7. Public business profile shows review.
8. Business rating statistics update.

Expected: moderation gate works correctly.

---

### Flow 4 — Business Owner Response

1. Approved review exists.
2. Business owner logs in.
3. Owner responds to review.
4. Public business profile shows response below review.
5. Another business owner attempts response.
6. Request is rejected.

Expected: ownership authorization works.

---

### Flow 5 — AI Summary

1. Business has fewer than three approved reviews.
2. Summary endpoint returns fallback message.
3. Add/approve three reviews.
4. Regenerate summary.
5. Business profile displays cached summary.

Expected: AI summary uses approved reviews only and caches result.

---

### Flow 6 — Security Restrictions

1. Guest calls admin endpoint.
2. Consumer calls admin endpoint.
3. Business owner calls admin endpoint.
4. Admin calls admin endpoint.

Expected:

| User Type | Expected |
|---|---|
| Guest | 401 Unauthorized |
| Consumer | 403 Forbidden |
| Business Owner | 403 Forbidden |
| Admin | 200 OK |

---

## 6. Frontend Test Plan

Frontend automated tests are secondary for this MVP, but the following are valuable if time allows.

### Component/Form Tests

| Component/Page | Test Case |
|---|---|
| Login form | Empty fields show validation errors. |
| Login form | Valid submit calls login API. |
| Register form | Role selection works. |
| Register form | Invalid email/weak password rejected. |
| Search page | Renders business result cards. |
| Business profile | Shows business details, rating, reviews, AI summary. |
| Review form | Rating/title/content validation works. |
| Admin reviews page | Pending reviews and AI risk badges render. |
| Admin businesses page | Approve/reject buttons render for pending businesses. |

### Route Guard Tests

| Case | Expected Result |
|---|---|
| Guest opens `/admin` | Redirect to login or access denied. |
| Consumer opens `/admin` | Access denied. |
| Business owner opens `/business/dashboard` | Allowed. |
| Consumer opens review form | Allowed. |
| Guest opens review form | Redirect to login. |

---

## 7. Manual Acceptance Test Checklist

Use this before final submission/demo.

### Public/Guest

- [ ] Guest can open homepage.
- [ ] Guest can search businesses.
- [ ] Guest can filter by rating.
- [ ] Guest can filter by location.
- [ ] Guest can filter by category.
- [ ] Guest can filter by claimed/unclaimed status.
- [ ] Guest can open approved business profile.
- [ ] Guest sees business details, rating, review count, and trust/safety note.
- [ ] Guest sees approved reviews only.
- [ ] Guest sees AI summary or insufficient-review fallback.
- [ ] Guest is redirected to login when trying to write review.

### Consumer

- [ ] Consumer can register.
- [ ] Consumer can login.
- [ ] Consumer can view profile.
- [ ] Consumer can submit review for approved business.
- [ ] Review is pending after submission.
- [ ] Consumer cannot submit duplicate active review for same business.
- [ ] Consumer can view own reviews if implemented.
- [ ] Consumer cannot access admin dashboard.

### Business Owner

- [ ] Business owner can register/login.
- [ ] Business owner can create business.
- [ ] Created business is pending verification.
- [ ] Business owner can view owned businesses.
- [ ] Business owner can edit own business.
- [ ] Business owner cannot edit another owner's business.
- [ ] Business owner can respond to approved review on own business.
- [ ] Business owner cannot respond to review on another business.
- [ ] Business owner can view basic analytics if implemented.

### Admin

- [ ] Admin can login using seeded account.
- [ ] Admin can view dashboard statistics.
- [ ] Admin can view pending businesses.
- [ ] Admin can approve business.
- [ ] Admin can reject business with reason.
- [ ] Admin can suspend business.
- [ ] Admin can view pending reviews.
- [ ] Admin can see AI moderation result.
- [ ] Admin can approve review.
- [ ] Admin can reject review with reason.
- [ ] Admin can flag review.
- [ ] Admin can suspend/reactivate users.
- [ ] Admin can manage categories if implemented.

### AI/Fallback

- [ ] Review submission triggers moderation analysis.
- [ ] Analysis result is stored.
- [ ] Admin can view sentiment, toxicity, spam, risk, suggested action, and reason.
- [ ] External AI failure does not block review submission.
- [ ] Rule-based fallback works.
- [ ] Business summary uses approved reviews only.
- [ ] Business summary is cached.

### DevOps/Documentation

- [ ] Backend tests pass.
- [ ] Frontend builds successfully.
- [ ] Docker Compose starts required services.
- [ ] GitHub Actions pipeline passes.
- [ ] README explains setup.
- [ ] README includes demo users.
- [ ] README includes architecture summary.
- [ ] README includes screenshots/GIF if available.

---

## 8. API Manual Testing Order

Use this order in Postman/Bruno/cURL:

1. Register consumer.
2. Register business owner.
3. Login consumer.
4. Login business owner.
5. Login admin.
6. Create category as admin.
7. Create business as business owner.
8. Confirm public search excludes pending business.
9. Approve business as admin.
10. Confirm public search includes approved business.
11. Submit review as consumer.
12. Confirm review is pending.
13. Confirm AI analysis exists.
14. Approve review as admin.
15. Confirm review appears publicly.
16. Confirm rating statistics updated.
17. Respond to review as business owner.
18. Confirm response appears publicly.
19. Regenerate business summary.
20. Confirm summary appears on profile.

---

## 9. Test Data Plan

### Demo Users

| Role | Email | Password | Notes |
|---|---|---|---|
| Admin | admin@veracity.local | Admin123! | Seeded only. |
| Consumer | consumer@veracity.local | Consumer123! | Demo review writer. |
| Business Owner | owner@veracity.local | Owner123! | Demo business owner. |

Never use these passwords in production. They are demo-only.

### Demo Categories

1. Coffee Store
2. Grocery Store
3. Restaurant
4. Software Company
5. Website Designer
6. Gym
7. Auto Insurance Agency

### Demo Businesses

Minimum:

1. 3 approved claimed businesses.
2. 2 approved unclaimed businesses.
3. 2 pending businesses.
4. 1 rejected business.

### Demo Reviews

Minimum:

1. At least 20 reviews.
2. Mix of 1-star to 5-star ratings.
3. Some pending reviews.
4. Some approved reviews.
5. Some flagged reviews.
6. Reviews with positive and negative sentiment.
7. Reviews that demonstrate AI/fallback moderation.

---

## 10. CI Quality Gates

GitHub Actions should fail if:

1. Backend tests fail.
2. Backend build fails.
3. Frontend install fails.
4. Frontend build fails.
5. Docker build fails if Docker build is included.

Recommended first CI command set:

```bash
cd backend
./mvnw test
./mvnw package -DskipTests

cd ../frontend
npm ci
npm run build
```

---

## 11. Risk-Based Testing Priorities

| Risk | Impact | Required Test Focus |
|---|---|---|
| Admin endpoints accessible by normal users | High | Security integration tests. |
| Business owner edits another owner's business | High | Ownership tests. |
| Reviews become public without approval | High | Review moderation tests. |
| Rating calculation wrong | Medium | Rating service tests. |
| AI failure blocks review submission | Medium | AI fallback tests. |
| Pending/rejected businesses visible publicly | Medium | Business search tests. |
| Password exposed in API | High | DTO/response tests. |
| Frontend route guards fail | Medium | Manual and component tests. |

---

## 12. Minimum Testing Completion Target

The project can be considered test-ready for MVP submission when these exist:

1. AuthServiceTest
2. BusinessServiceTest
3. ReviewServiceTest
4. AdminBusinessServiceTest
5. AdminReviewServiceTest
6. AiReviewServiceTest
7. RatingCalculationServiceTest
8. At least one controller/security integration test proving non-admin users cannot access admin endpoints
9. Manual acceptance checklist completed
10. GitHub Actions passing
