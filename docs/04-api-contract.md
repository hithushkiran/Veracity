# Veracity API Contract

**Document:** 04-api-contract.md  
**Project:** Veracity  
**Source:** Final Veracity SRS v1.0  
**Purpose:** Backend/frontend agreement before controller implementation

---

## 1. API Conventions

### Base URL

```text
/api
```

### Authentication

Protected endpoints require a JWT access token:

```http
Authorization: Bearer <access_token>
```

### Roles

| Role | API Meaning |
|---|---|
| Guest | No token required. Public endpoints only. |
| CONSUMER | Can submit reviews and manage own profile/reviews. |
| BUSINESS_OWNER | Can create/manage own businesses and respond to reviews. |
| ADMIN | Can approve businesses, moderate reviews, manage users/categories, and view statistics. |

### Common Success Response Style

For single resources, return the DTO directly:

```json
{
  "id": 1,
  "name": "Example"
}
```

For paginated lists:

```json
{
  "content": [],
  "page": 0,
  "size": 10,
  "totalElements": 0,
  "totalPages": 0,
  "last": true
}
```

### Common Error Response

```json
{
  "timestamp": "2026-06-29T10:30:00",
  "status": 400,
  "error": "Bad Request",
  "message": "Validation failed",
  "path": "/api/auth/register",
  "details": {
    "email": "Invalid email format"
  }
}
```

### Status Codes

| Code | Meaning |
|---:|---|
| 200 | Successful read/update/action. |
| 201 | Resource created. |
| 204 | Resource deleted or no-content success. |
| 400 | Validation or business rule error. |
| 401 | Missing/invalid authentication. |
| 403 | Authenticated but not authorized. |
| 404 | Resource not found. |
| 409 | Conflict such as duplicate email or duplicate review. |
| 500 | Unexpected server error. |

---

## 2. Authentication APIs

### POST `/api/auth/register`

Registers a consumer or business owner.

**Access:** Guest

Request:

```json
{
  "fullName": "Kasun Perera",
  "email": "kasun@example.com",
  "password": "Password123!",
  "role": "CONSUMER"
}
```

Validation:

| Field | Rule |
|---|---|
| fullName | Required, 2-100 characters. |
| email | Required, valid email, unique. |
| password | Required, minimum 8 characters. |
| role | `CONSUMER` or `BUSINESS_OWNER`. Admin cannot self-register. |

Response `201`:

```json
{
  "id": 1,
  "fullName": "Kasun Perera",
  "email": "kasun@example.com",
  "role": "CONSUMER",
  "status": "ACTIVE"
}
```

---

### POST `/api/auth/login`

Authenticates user and returns JWT.

**Access:** Guest

Request:

```json
{
  "email": "kasun@example.com",
  "password": "Password123!"
}
```

Response `200`:

```json
{
  "accessToken": "jwt-token-here",
  "tokenType": "Bearer",
  "user": {
    "id": 1,
    "fullName": "Kasun Perera",
    "email": "kasun@example.com",
    "role": "CONSUMER"
  }
}
```

Errors:

| Case | Status |
|---|---:|
| Invalid credentials | 401 |
| Suspended/deleted user | 403 |

---

### GET `/api/auth/me`

Returns authenticated user's identity.

**Access:** Authenticated

Response `200`:

```json
{
  "id": 1,
  "fullName": "Kasun Perera",
  "email": "kasun@example.com",
  "role": "CONSUMER",
  "status": "ACTIVE",
  "profileImageUrl": null
}
```

---

## 3. User APIs

### GET `/api/users/me`

**Access:** Authenticated

Returns own profile.

---

### PUT `/api/users/me`

**Access:** Authenticated

Request:

```json
{
  "fullName": "Kasun A. Perera",
  "profileImageUrl": "https://example.com/profile.jpg"
}
```

Rules:

1. User cannot update own role.
2. User cannot update another user's profile.

---

### GET `/api/admin/users`

**Access:** ADMIN

Query parameters:

| Parameter | Description |
|---|---|
| query | Search by name or email. |
| role | Filter by role. |
| status | Filter by account status. |
| page | Page number. |
| size | Page size. |

---

### PATCH `/api/admin/users/{id}/suspend`

**Access:** ADMIN

Request:

```json
{
  "reason": "Policy violation"
}
```

---

### PATCH `/api/admin/users/{id}/activate`

**Access:** ADMIN

Reactivates suspended user.

---

## 4. Category APIs

### GET `/api/categories`

**Access:** Public

Returns active categories.

---

### GET `/api/categories/tree`

**Access:** Public

Returns parent-child category structure.

Example response:

```json
[
  {
    "id": 1,
    "name": "Food & Beverages",
    "slug": "food-beverages",
    "children": [
      { "id": 2, "name": "Coffee Store", "slug": "coffee-store" }
    ]
  }
]
```

---

### POST `/api/admin/categories`

**Access:** ADMIN

Request:

```json
{
  "name": "Coffee Store",
  "parentId": 1,
  "description": "Coffee shops and cafes",
  "displayOrder": 1
}
```

---

### PUT `/api/admin/categories/{id}`

**Access:** ADMIN

Updates category.

---

### PATCH `/api/admin/categories/{id}/deactivate`

**Access:** ADMIN

Deactivates category if allowed.

---

## 5. Business APIs

### GET `/api/businesses`

**Access:** Public

Returns paginated approved businesses.

Query parameters:

| Parameter | Description |
|---|---|
| page | Page number. |
| size | Page size. |
| sort | Sort option. |

---

### GET `/api/businesses/search`

**Access:** Public

Searches approved businesses.

Query parameters:

| Parameter | Description |
|---|---|
| query | Search by name, website, description, category, or city. |
| categoryId | Filter by exact category. |
| parentCategoryId | Filter by parent category including child categories. |
| city | Filter by city. |
| district | Filter by district. |
| ratingMin | Minimum average rating. |
| claimedStatus | `CLAIMED`, `UNCLAIMED`, or all. |
| reviewCountMin | Minimum approved review count. |
| sort | `RELEVANCE`, `HIGHEST_RATED`, `MOST_REVIEWED`, `NEWEST`, `RECENTLY_REVIEWED`. |
| page | Page number. |
| size | Page size. |

Response item:

```json
{
  "id": 1,
  "name": "Ceylon Brew House",
  "slug": "ceylon-brew-house",
  "website": "https://example.com",
  "logoUrl": null,
  "shortDescription": "A local coffee shop...",
  "city": "Colombo",
  "district": "Colombo",
  "primaryCategory": {
    "id": 2,
    "name": "Coffee Store"
  },
  "verificationStatus": "APPROVED",
  "claimStatus": "CLAIMED",
  "averageRating": 4.4,
  "veracityScore": 4.4,
  "approvedReviewCount": 18
}
```

---

### GET `/api/businesses/{id}`

**Access:** Public, approved businesses only unless owner/admin.

Returns business profile details, rating summary, AI summary, approved reviews preview, and similar businesses.

---

### POST `/api/businesses`

**Access:** BUSINESS_OWNER

Request:

```json
{
  "name": "Ceylon Brew House",
  "description": "A Sri Lankan coffee shop serving local blends and snacks.",
  "primaryCategoryId": 2,
  "secondaryCategoryIds": [3, 4],
  "website": "https://example.com",
  "email": "hello@example.com",
  "phone": "+94771234567",
  "address": "123 Main Street",
  "city": "Colombo",
  "district": "Colombo",
  "country": "Sri Lanka",
  "logoUrl": "https://example.com/logo.png",
  "coverImageUrl": "https://example.com/cover.png"
}
```

Response `201`:

```json
{
  "id": 1,
  "name": "Ceylon Brew House",
  "verificationStatus": "PENDING_VERIFICATION",
  "claimStatus": "CLAIMED"
}
```

---

### PUT `/api/businesses/{id}`

**Access:** BUSINESS_OWNER for own business, ADMIN if needed

Updates allowed business fields.

Rules:

1. Business owner can update only own businesses.
2. Public verification status cannot be changed through this endpoint.
3. Admin approval endpoints handle verification status.

---

### GET `/api/businesses/{id}/reviews`

**Access:** Public

Returns approved reviews for a business.

Query parameters:

| Parameter | Description |
|---|---|
| rating | Filter by 1-5 star rating. |
| keyword | Search inside review title/content. |
| sort | `NEWEST`, `HIGHEST_RATING`, `LOWEST_RATING`. |
| page | Page number. |
| size | Page size. |

---

### POST `/api/businesses/{id}/claim`

**Access:** BUSINESS_OWNER

Request:

```json
{
  "contactEmail": "owner@example.com",
  "evidenceText": "I own this business and can verify by email.",
  "evidenceUrl": "https://example.com/evidence.png"
}
```

---

### GET `/api/business-owner/businesses`

**Access:** BUSINESS_OWNER

Returns businesses owned by logged-in business owner.

---

## 6. Admin Business APIs

### GET `/api/admin/businesses`

**Access:** ADMIN

Query parameters:

| Parameter | Description |
|---|---|
| status | Filter by verification status. |
| query | Search name/city/owner. |
| page | Page number. |
| size | Page size. |

---

### GET `/api/admin/businesses/pending`

**Access:** ADMIN

Returns PENDING_VERIFICATION businesses.

---

### PATCH `/api/admin/businesses/{id}/approve`

**Access:** ADMIN

Approves pending business.

---

### PATCH `/api/admin/businesses/{id}/reject`

**Access:** ADMIN

Request:

```json
{
  "reason": "Insufficient business details."
}
```

---

### PATCH `/api/admin/businesses/{id}/suspend`

**Access:** ADMIN

Request:

```json
{
  "reason": "Suspended due to policy concerns."
}
```

---

### GET `/api/admin/business-claims`

**Access:** ADMIN

Returns business claim requests.

---

### PATCH `/api/admin/business-claims/{id}/approve`

**Access:** ADMIN

Approves claim and assigns business owner.

---

### PATCH `/api/admin/business-claims/{id}/reject`

**Access:** ADMIN

Request:

```json
{
  "adminNote": "Evidence was not sufficient."
}
```

---

## 7. Review APIs

### POST `/api/businesses/{businessId}/reviews`

**Access:** CONSUMER

Request:

```json
{
  "rating": 5,
  "title": "Excellent service",
  "content": "The staff were friendly, the response time was fast, and the overall experience was very good."
}
```

Rules:

1. Business must be approved.
2. User must be a consumer.
3. Rating must be 1-5.
4. Title must be 5-120 characters.
5. Content must be 20-3000 characters.
6. Review is saved as PENDING.
7. AI/fallback moderation analysis runs after submission.
8. Duplicate active review by same user for same business is prevented.

Response `201`:

```json
{
  "id": 1,
  "businessId": 1,
  "rating": 5,
  "title": "Excellent service",
  "content": "The staff were friendly...",
  "status": "PENDING",
  "createdAt": "2026-06-29T10:30:00"
}
```

---

### GET `/api/users/me/reviews`

**Access:** CONSUMER

Returns logged-in consumer's reviews.

---

### PUT `/api/reviews/{id}`

**Access:** CONSUMER owner of review

Updates own review.

Rule: editing an approved review may move it back to PENDING.

---

### DELETE `/api/reviews/{id}`

**Access:** CONSUMER owner of review

Soft-deletes review by setting status to DELETED.

---

### POST `/api/reviews/{id}/vote`

**Access:** Authenticated

Should-have endpoint.

Request:

```json
{
  "voteType": "HELPFUL"
}
```

---

### POST `/api/reviews/{id}/report`

**Access:** Authenticated

Should-have endpoint.

Request:

```json
{
  "reason": "SPAM",
  "details": "This review looks promotional."
}
```

---

## 8. Business Response APIs

### POST `/api/reviews/{reviewId}/response`

**Access:** BUSINESS_OWNER owning reviewed business

Request:

```json
{
  "content": "Thank you for your feedback. We are glad you had a good experience."
}
```

Rules:

1. Review must be approved.
2. Business owner must own the reviewed business.
3. One response per review in MVP.

---

### PUT `/api/responses/{id}`

**Access:** BUSINESS_OWNER owner of response

Updates response content.

---

### DELETE `/api/responses/{id}`

**Access:** BUSINESS_OWNER owner of response

Deletes response.

---

## 9. Admin Review APIs

### GET `/api/admin/reviews`

**Access:** ADMIN

Query parameters:

| Parameter | Description |
|---|---|
| status | Filter by review status. |
| risk | Filter by AI moderation risk. |
| businessId | Filter by business. |
| page | Page number. |
| size | Page size. |

---

### GET `/api/admin/reviews/pending`

**Access:** ADMIN

Returns pending review queue.

---

### GET `/api/admin/reviews/flagged`

**Access:** ADMIN

Returns flagged reviews.

---

### PATCH `/api/admin/reviews/{id}/approve`

**Access:** ADMIN

Approves review, sets `approvedAt`, and recalculates business rating statistics.

---

### PATCH `/api/admin/reviews/{id}/reject`

**Access:** ADMIN

Request:

```json
{
  "reason": "Contains abusive language."
}
```

Rejects review and recalculates business rating statistics if needed.

---

### PATCH `/api/admin/reviews/{id}/flag`

**Access:** ADMIN

Request:

```json
{
  "reason": "Needs manual follow-up."
}
```

---

### POST `/api/admin/reviews/{id}/reanalyze`

**Access:** ADMIN

Runs AI/fallback analysis again.

---

## 10. AI APIs

### POST `/api/ai/reviews/{reviewId}/analyze`

**Access:** ADMIN

Runs review analysis and stores result.

Response:

```json
{
  "reviewId": 1,
  "sentiment": "POSITIVE",
  "toxicityScore": 0.02,
  "spamScore": 0.10,
  "profanityDetected": false,
  "personalAttackDetected": false,
  "possibleFakeReview": false,
  "moderationRisk": "LOW",
  "suggestedAction": "APPROVE",
  "reason": "No obvious policy risk detected.",
  "extractedKeywords": ["friendly service", "fast response"],
  "provider": "RULE_BASED"
}
```

---

### GET `/api/ai/businesses/{businessId}/summary`

**Access:** Public through approved business profile; direct endpoint can also be public for approved businesses

Returns cached summary or fallback message.

---

### POST `/api/ai/businesses/{businessId}/summary/regenerate`

**Access:** ADMIN or BUSINESS_OWNER owning business

Regenerates summary using approved reviews only.

---

## 11. Analytics APIs

### GET `/api/admin/stats`

**Access:** ADMIN

Response:

```json
{
  "totalUsers": 50,
  "totalConsumers": 35,
  "totalBusinessOwners": 14,
  "totalBusinesses": 20,
  "pendingBusinesses": 3,
  "approvedBusinesses": 15,
  "totalReviews": 80,
  "pendingReviews": 6,
  "flaggedReviews": 2,
  "averagePlatformRating": 4.1
}
```

---

### GET `/api/business-owner/businesses/{id}/stats`

**Access:** BUSINESS_OWNER owning business

Response:

```json
{
  "businessId": 1,
  "averageRating": 4.4,
  "veracityScore": 4.4,
  "approvedReviewCount": 18,
  "starDistribution": {
    "5": 10,
    "4": 5,
    "3": 2,
    "2": 1,
    "1": 0
  },
  "sentimentDistribution": {
    "POSITIVE": 12,
    "NEUTRAL": 3,
    "NEGATIVE": 2,
    "MIXED": 1
  }
}
```

---

## 12. DTO Naming Plan

Recommended backend DTO names:

### Auth

```text
RegisterRequest
LoginRequest
AuthResponse
CurrentUserResponse
```

### User

```text
UserResponse
UpdateProfileRequest
AdminUserResponse
```

### Category

```text
CategoryRequest
CategoryResponse
CategoryTreeResponse
```

### Business

```text
BusinessCreateRequest
BusinessUpdateRequest
BusinessResponse
BusinessSearchResponse
BusinessProfileResponse
BusinessApprovalRequest
BusinessRejectionRequest
BusinessClaimRequestDto
BusinessClaimResponse
```

### Review

```text
ReviewCreateRequest
ReviewUpdateRequest
ReviewResponse
ReviewModerationResponse
ReviewRejectRequest
ReviewFlagRequest
```

### Business Response

```text
BusinessResponseCreateRequest
BusinessResponseUpdateRequest
BusinessOwnerReplyResponse
```

### AI

```text
AiReviewAnalysisResponse
AiBusinessSummaryResponse
```

### Analytics

```text
AdminStatsResponse
BusinessStatsResponse
RatingDistributionResponse
```

---

## 13. Security Rules Summary

| Endpoint Area | Guest | Consumer | Business Owner | Admin |
|---|---:|---:|---:|---:|
| Public search/profile | Yes | Yes | Yes | Yes |
| Register/login | Yes | Yes | Yes | Yes |
| Submit review | No | Yes | No | No |
| Own profile | No | Yes | Yes | Yes |
| Create business | No | No | Yes | No |
| Own business dashboard | No | No | Yes | No |
| Respond to review | No | No | Own business only | No |
| Admin businesses | No | No | No | Yes |
| Admin reviews | No | No | No | Yes |
| Admin users | No | No | No | Yes |
| Admin stats | No | No | No | Yes |

---

## 14. Frontend Route to API Mapping

| Frontend Route | Main APIs Used |
|---|---|
| `/` | `GET /api/categories/tree`, `GET /api/businesses/search` |
| `/login` | `POST /api/auth/login` |
| `/register` | `POST /api/auth/register` |
| `/search` | `GET /api/businesses/search` |
| `/categories` | `GET /api/categories/tree` |
| `/businesses/:id` | `GET /api/businesses/{id}`, `GET /api/businesses/{id}/reviews` |
| `/businesses/:id/review` | `POST /api/businesses/{businessId}/reviews` |
| `/profile` | `GET /api/users/me`, `PUT /api/users/me` |
| `/profile/reviews` | `GET /api/users/me/reviews` |
| `/business/dashboard` | `GET /api/business-owner/businesses` |
| `/business/create` | `POST /api/businesses` |
| `/business/:id/edit` | `PUT /api/businesses/{id}` |
| `/admin` | `GET /api/admin/stats` |
| `/admin/businesses` | `GET /api/admin/businesses`, approve/reject/suspend endpoints |
| `/admin/reviews` | `GET /api/admin/reviews`, approve/reject/flag/reanalyze endpoints |
| `/admin/users` | `GET /api/admin/users`, suspend/activate endpoints |
| `/admin/categories` | category admin endpoints |
