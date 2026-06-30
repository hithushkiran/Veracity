# Software Requirements Specification

## Veracity

### AI-Assisted Consumer Review Platform for Sri Lankan Businesses

**Document Version:** 1.0  
**Prepared For:** Veracity Web Application Project  
**Prepared By:** Hithush Mohanaraja  
**Primary Source Reference:** TrustMark Lanka SRS v1.0  
**Reference Inspiration:** Trustpilot-style consumer review platform
behavior  
**Target Implementation Period:** 10 Days  
**Technology Direction:** Spring Boot, React, MySQL, Docker, CI/CD  
**Document Status:** Implementation-ready MVP SRS

# 1. Introduction

## 1.1 Purpose

This Software Requirements Specification defines the functional,
non-functional, technical, testing, deployment, and acceptance
requirements for **Veracity**, a full-stack consumer review web
application.

Veracity is a review platform where consumers can discover Sri Lankan
businesses, read customer reviews, submit their own reviews, and view
AI-generated summaries of customer sentiment. Business owners can create
or claim business profiles, manage their business information, respond
to reviews, and view basic feedback analytics. Administrators can verify
businesses, moderate reviews, manage users, and use AI-assisted review
analysis to identify inappropriate, abusive, spam-like, or suspicious
review content.

The purpose of this SRS is to define a realistic but strong
implementation scope that can be completed within approximately 10 days
while still demonstrating professional software engineering practices,
including requirements refinement, layered architecture, secure
authentication, role-based authorization, database design, testing,
Dockerization, and CI/CD.

## 1.2 Project Background

The original TrustMark Lanka project was designed as a Sri Lankan
consumer review platform that connects consumers and businesses through
transparent ratings and feedback. Veracity is a rebuilt and re-scoped
version of that concept, implemented from scratch as a Spring Boot-based
web application.

The original SRS includes broad long-term features such as mobile
applications, payment integration, business subscriptions, trusted
reviewer programs, advanced analytics, SMS integration, and ecosystem
expansion. Veracity intentionally limits the first release to a
practical, high-quality MVP with selected advanced features that
strengthen the project for internship and CV purposes.

## 1.3 Product Vision

Veracity aims to become a trusted platform where Sri Lankan consumers
can make better decisions by reading authentic business reviews, and
where businesses can improve their reputation by responding to customer
feedback transparently.

The system should not simply store reviews. It should support trust,
moderation, discoverability, accountability, and insight.

## 1.4 Product Objectives

The main objectives of Veracity are:

1.  Allow consumers to search, filter, and compare businesses.
2.  Allow consumers to read and write reviews with star ratings.
3.  Allow business owners to create, claim, and manage business
    profiles.
4.  Allow business owners to respond to reviews.
5.  Allow administrators to approve businesses and moderate reviews.
6.  Provide AI-assisted review moderation for admins.
7.  Provide AI-generated review summaries for business profile pages.
8.  Demonstrate strong Spring Boot backend design with proper layers,
    DTOs, validation, security, and testing.
9.  Demonstrate SDLC practices through clear requirements, scoped
    delivery, testing, CI/CD, documentation, and deployment readiness.

## 1.5 Intended Audience

This document is intended for:

1.  The developer implementing Veracity.
2.  Academic evaluators or project reviewers.
3.  Internship recruiters reviewing the project.
4.  Future contributors who may extend the project.
5.  Technical interviewers who may ask about design decisions,
    architecture, testing, and implementation scope.

## 1.6 Definitions

| Term               | Definition                                                                                                                     |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------|
| Consumer           | A user who reads and writes reviews about businesses.                                                                          |
| Business Owner     | A user who owns or represents a business profile.                                                                              |
| Admin              | A privileged user who manages platform moderation, business verification, categories, and users.                               |
| Review             | A written customer opinion about a business, usually paired with a 1–5 star rating.                                            |
| Rating             | A numerical score from 1 to 5 given by a consumer to a business.                                                               |
| Business Profile   | A public page containing business details, rating information, reviews, AI summary, and contact details.                       |
| Claimed Business   | A business profile managed by a verified or approved business owner.                                                           |
| Unclaimed Business | A public business profile that exists on the platform but is not yet managed by a business owner.                              |
| Verification       | Admin approval process for confirming a business or claim request.                                                             |
| Moderation         | The process of approving, rejecting, or flagging submitted review content.                                                     |
| AI Review Analysis | AI-assisted evaluation of review text to detect sentiment, toxicity, spam signals, inappropriate wording, or moderation risks. |
| AI Review Summary  | A generated summary of common positive and negative themes found in approved reviews for a business.                           |
| MVP                | Minimum Viable Product; the first complete implementation scope.                                                               |
| VeracityScore      | The public rating score displayed for a business based on approved reviews.                                                    |

# 2. Overall Description

## 2.1 Product Perspective

Veracity is a full-stack web application consisting of:

1.  A React frontend for consumers, business owners, and administrators.
2.  A Spring Boot REST API backend.
3.  A MySQL relational database.
4.  Optional AI provider integration for review analysis and review
    summarization.
5.  Dockerized local development environment.
6.  GitHub Actions CI pipeline for automated builds and tests.

The system will be implemented as a **modular monolith** rather than
microservices. This keeps the project realistic for a 10-day solo build
while still maintaining clean package-level separation.

Suggested backend package modules:

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

## 2.2 Product Scope

The first version of Veracity shall include:

1.  User registration and login.
2.  JWT-based authentication.
3.  Role-based access control.
4.  Consumer, business owner, and admin roles.
5.  Business creation and profile management.
6.  Business claim/verification status.
7.  Business search and filtering.
8.  Category-based business browsing.
9.  Public business profile pages.
10. Review submission with star rating.
11. Review moderation workflow.
12. Business owner responses to reviews.
13. AI-assisted moderation for review content.
14. AI-generated review summaries for business pages.
15. Admin dashboard for business and review management.
16. Basic business analytics.
17. Backend tests for critical workflows.
18. Docker Compose setup.
19. GitHub Actions CI pipeline.
20. Implementation documentation and API documentation.

## 2.3 User Classes and Characteristics

### 2.3.1 Guest User

A guest user is not logged in.

Guest users can:

1.  Search businesses.
2.  Browse categories.
3.  View public business profiles.
4.  View approved reviews.
5.  View business ratings and review summaries.
6.  Register or log in.
7.  Click external business website links.

Guest users cannot:

1.  Write reviews.
2.  Vote on reviews.
3.  Flag reviews.
4.  Respond to reviews.
5.  Access dashboards.

### 2.3.2 Consumer

A consumer is a registered user who can interact with businesses through
reviews.

Consumers can:

1.  Log in and manage their profile.
2.  Search and filter businesses.
3.  Write reviews for businesses.
4.  Edit or delete their own reviews subject to system rules.
5.  View their review history.
6.  Flag inappropriate reviews.
7.  Mark reviews as helpful or unhelpful.
8.  View AI-generated business review summaries.

Consumers cannot:

1.  Approve businesses.
2.  Moderate reviews.
3.  Respond as a business owner.
4.  Manage business profiles unless they also register as a business
    owner.

### 2.3.3 Business Owner

A business owner is a registered user who manages one or more business
profiles.

Business owners can:

1.  Create a business profile.
2.  Submit a business for admin verification.
3.  Claim an existing unclaimed business.
4.  Edit approved business details.
5.  Upload or update business logo/image URL.
6.  View reviews on their business.
7.  Respond to approved reviews.
8.  View basic analytics such as average rating, review count, star
    distribution, and sentiment summary.
9.  Request AI review summary regeneration for their business.

Business owners cannot:

1.  Approve their own business.
2.  Delete consumer reviews.
3.  Modify consumer review text.
4.  Hide negative reviews.
5.  Access admin-only moderation controls.

### 2.3.4 Admin

An admin manages platform integrity.

Admins can:

1.  View all users.
2.  Manage user roles and account status.
3.  Approve or reject business submissions.
4.  Approve or reject business claim requests.
5.  Approve, reject, or flag reviews.
6.  View AI moderation results for reviews.
7.  Manually trigger AI analysis for reviews.
8.  Manage categories.
9.  View platform statistics.
10. Suspend businesses or users if required.
11. View reports submitted by users.

Admins cannot:

1.  Modify review text to change consumer opinion.
2.  Publish AI-generated claims as verified facts.
3.  Use AI output as the only reason for permanent enforcement without
    manual confirmation.

### 2.3.5 AI Assistant/System Service

The AI assistant is not a human user. It is a backend service used by
the application.

The AI assistant can:

1.  Analyze review text.
2.  Estimate sentiment.
3.  Detect possible toxicity or abusive language.
4.  Detect possible spam or low-quality review patterns.
5.  Generate short review summaries for business profiles.
6.  Extract top positive and negative themes.

The AI assistant cannot:

1.  Automatically delete reviews.
2.  Automatically ban users.
3.  Make final moderation decisions.
4.  Override admin decisions.
5.  Access passwords or sensitive authentication data.

# 3. Scope Prioritization

## 3.1 MVP Scope: Must Build

The following features are mandatory for the first release:

| ID     | Feature                           | Priority |
|--------|-----------------------------------|----------|
| MVP-01 | User registration and login       | Must     |
| MVP-02 | JWT authentication                | Must     |
| MVP-03 | Role-based authorization          | Must     |
| MVP-04 | Business profile creation         | Must     |
| MVP-05 | Admin business approval/rejection | Must     |
| MVP-06 | Public business search            | Must     |
| MVP-07 | Category and location filtering   | Must     |
| MVP-08 | Business profile page             | Must     |
| MVP-09 | Review submission                 | Must     |
| MVP-10 | Admin review moderation           | Must     |
| MVP-11 | Business response to reviews      | Must     |
| MVP-12 | AI-assisted review moderation     | Must     |
| MVP-13 | AI-generated review summary       | Must     |
| MVP-14 | Admin dashboard                   | Must     |
| MVP-15 | Docker Compose setup              | Must     |
| MVP-16 | Backend unit/integration tests    | Must     |
| MVP-17 | GitHub Actions CI                 | Must     |

## 3.2 Should-Have Scope

The following features should be implemented if time allows:

| ID    | Feature                                      | Priority |
|-------|----------------------------------------------|----------|
| SH-01 | Helpful/unhelpful review voting              | Should   |
| SH-02 | User review history                          | Should   |
| SH-03 | Business owner analytics dashboard           | Should   |
| SH-04 | Review keyword search inside business page   | Should   |
| SH-05 | Top mentions from reviews                    | Should   |
| SH-06 | Business logo/image support using URL upload | Should   |
| SH-07 | Seed data for demo                           | Should   |
| SH-08 | Swagger/OpenAPI documentation                | Should   |

## 3.3 Could-Have Scope

The following features are useful but not essential:

| ID    | Feature                       | Priority |
|-------|-------------------------------|----------|
| CH-01 | Email verification simulation | Could    |
| CH-02 | In-app notifications          | Could    |
| CH-03 | Saved businesses              | Could    |
| CH-04 | Review report management      | Could    |
| CH-05 | Review editing history        | Could    |
| CH-06 | Admin audit log               | Could    |

## 3.4 Out of Scope for 10-Day Build

The following features shall be excluded from the first implementation:

1.  Native mobile applications.
2.  Payment integration.
3.  Subscription tiers.
4.  SMS verification.
5.  Real Google Maps integration.
6.  Business appointment scheduling.
7.  Loyalty program.
8.  Trusted reviewer program.
9.  Third-party public API.
10. Advanced SEO automation.
11. Social media integration.
12. Competitive analysis tools.
13. Blue-green deployment.
14. Full production monitoring.
15. 10,000 concurrent user support.
16. Real-time notifications.
17. Machine learning model training.
18. Web scraping from external review platforms.

These features may be considered future enhancements.

# 4. User Journeys

## 4.1 Guest Search and Discovery Journey

1.  Guest visits homepage.
2.  Guest enters a search term such as business name, category, or city.
3.  System displays matching businesses.
4.  Guest applies filters such as rating, location, category, claimed
    status, or number of reviews.
5.  Guest opens a business profile.
6.  System displays business details, score, review count, contact
    details, rating distribution, AI review summary, and approved
    reviews.
7.  Guest is prompted to log in if they attempt to write a review.

## 4.2 Consumer Review Journey

1.  Consumer logs in.
2.  Consumer searches for a business.
3.  Consumer opens the business profile.
4.  Consumer clicks “Write a Review”.
5.  Consumer selects star rating from 1 to 5.
6.  Consumer enters review title and content.
7.  System validates the review.
8.  System submits the review with `PENDING` status.
9.  AI moderation service analyzes the review.
10. Admin reviews the moderation result.
11. Admin approves, rejects, or flags the review.
12. Approved review becomes visible on the business page.
13. Business owner may respond to the review.

## 4.3 Business Owner Journey

1.  Business owner registers or logs in.
2.  Business owner creates a new business profile or claims an existing
    unclaimed business.
3.  System stores the business as `PENDING_VERIFICATION`.
4.  Admin reviews and approves/rejects the business.
5.  Approved business becomes publicly searchable.
6.  Business owner manages profile details.
7.  Business owner views customer reviews.
8.  Business owner responds to approved reviews.
9.  Business owner views basic rating analytics and AI summary.

## 4.4 Admin Moderation Journey

1.  Admin logs in.
2.  Admin opens admin dashboard.
3.  Admin views pending businesses and pending reviews.
4.  Admin opens a review moderation item.
5.  System displays review text, rating, reviewer, business, AI
    sentiment, toxicity score, spam score, and moderation reason.
6.  Admin approves, rejects, or flags the review.
7.  System updates review status.
8.  If approved, the review becomes visible publicly.
9.  Business score and rating distribution are recalculated.
10. AI business summary can be regenerated.

# 5. Functional Requirements

# FR-01 User Registration

## Description

The system shall allow users to create an account using name, email,
password, and role.

## User Roles

Consumer, Business Owner.

## Functional Rules

1.  Email must be unique.
2.  Password must be securely hashed using BCrypt.
3.  Password must satisfy minimum strength rules.
4.  The default role shall be `CONSUMER` unless the user explicitly
    registers as a business owner.
5.  Admin accounts shall not be self-created through the public
    registration form.
6.  Admin accounts shall be seeded or created through an admin-only
    process.

## Input Fields

| Field     | Required | Validation                 |
|-----------|----------|----------------------------|
| Full name | Yes      | 2–100 characters           |
| Email     | Yes      | Valid email format, unique |
| Password  | Yes      | Minimum 8 characters       |
| Role      | Yes      | CONSUMER or BUSINESS_OWNER |

## Acceptance Criteria

1.  A valid user can register successfully.
2.  Duplicate emails are rejected.
3.  Passwords are not stored in plain text.
4.  Invalid email format is rejected.
5.  Weak passwords are rejected.
6.  Registration returns a safe user response without password hash.

# FR-02 User Authentication

## Description

The system shall allow registered users to log in using email and
password.

## Functional Rules

1.  User must provide valid credentials.
2.  On successful login, the system returns a JWT access token.
3.  JWT must contain user ID, email, and role claims.
4.  Invalid credentials must return a generic error message.
5.  Disabled or suspended accounts must not be allowed to log in.

## Acceptance Criteria

1.  Valid credentials return a JWT token.
2.  Invalid credentials return unauthorized response.
3.  JWT-protected endpoints reject missing or invalid tokens.
4.  Role-specific endpoints validate the user role.

# FR-03 User Profile Management

## Description

The system shall allow authenticated users to view and update their own
profile.

## Functional Rules

1.  Users can view their name, email, role, profile image URL, and
    account status.
2.  Users can update their full name and profile image URL.
3.  Users cannot update their role.
4.  Users cannot update another user’s profile.
5.  Admins can view user lists and suspend users.

## Acceptance Criteria

1.  Authenticated user can view own profile.
2.  Authenticated user can update allowed profile fields.
3.  User cannot change role through profile API.
4.  Admin can view all users.
5.  Suspended users cannot perform protected actions.

# FR-04 Category Management

## Description

The system shall support hierarchical business categories for browsing
and filtering.

## Category Logic

1.  A category may have a parent category.
2.  A parent category can contain multiple child categories.
3.  A business must have one primary category.
4.  A business may have up to three secondary categories.
5.  Public search must support filtering by parent or child category.
6.  If a parent category is selected, businesses under all child
    categories must be included.
7.  Category results must show category name and parent category name
    where applicable.

## Example Category Structure

    Food & Beverages
      - Coffee Store
      - Grocery Store
      - Restaurant
      - Bakery

    Technology
      - Software Company
      - Website Designer
      - IT Services

    Money & Insurance
      - Auto Insurance Agency
      - Finance Consultant

    Health & Wellness
      - Gym
      - Pharmacy
      - Medical Clinic

    Education
      - Private Tutor
      - Training Institute
      - University Service

## Admin Category Rules

1.  Admin can create, update, and deactivate categories.
2.  Categories cannot be deleted if businesses are assigned to them.
3.  Deactivated categories must not appear in public filters.
4.  Category slugs must be unique.

## Acceptance Criteria

1.  Public users can browse active parent categories.
2.  Public users can browse child categories.
3.  Search results can be filtered by category.
4.  Admin can create and update categories.
5.  Deleting an in-use category is prevented.

# FR-05 Business Registration

## Description

The system shall allow business owners to create a business profile.

## Functional Rules

1.  Only users with `BUSINESS_OWNER` role can create businesses.
2.  Business profile is created with `PENDING_VERIFICATION` status.
3.  Business is not fully public until approved by admin.
4.  Business owner can edit submitted business details before approval.
5.  Business owner can manage only businesses they own.
6.  System shall prevent duplicate businesses based on name, website,
    and city combination where possible.

## Required Business Fields

| Field                | Required | Validation               |
|----------------------|----------|--------------------------|
| Business name        | Yes      | 2–150 characters         |
| Description          | Yes      | 20–2000 characters       |
| Primary category     | Yes      | Existing active category |
| Secondary categories | No       | Maximum 3                |
| Website              | No       | Valid URL                |
| Email                | No       | Valid email              |
| Phone                | No       | Valid phone format       |
| Address              | No       | Maximum 255 characters   |
| City                 | Yes      | 2–100 characters         |
| District             | No       | 2–100 characters         |
| Country              | Yes      | Default: Sri Lanka       |
| Logo URL             | No       | Valid URL                |
| Business image URL   | No       | Valid URL                |

## Acceptance Criteria

1.  Business owner can create business profile.
2.  Created business has pending verification status.
3.  Guest users cannot create businesses.
4.  Consumers cannot create businesses unless they register as business
    owners.
5.  Admin can view pending businesses.
6.  Business owner cannot approve their own business.

# FR-06 Business Claiming

## Description

The system shall allow business owners to claim an existing unclaimed
business profile.

## Functional Rules

1.  A business may have `claimStatus` as `UNCLAIMED`, `CLAIM_PENDING`,
    or `CLAIMED`.
2.  If a business is unclaimed, a business owner can submit a claim
    request.
3.  Claim request must include evidence text, contact email, and
    optional document/image URL.
4.  Admin approves or rejects claim request.
5.  Once approved, the business is assigned to the claiming owner.
6.  Public business page shall display claimed/unclaimed status.

## Acceptance Criteria

1.  Business owner can request to claim an unclaimed business.
2.  Admin can approve claim.
3.  Admin can reject claim with reason.
4.  Claimed business displays claimed badge.
5.  Unclaimed business displays unclaimed status.

# FR-07 Business Verification

## Description

The system shall allow admins to verify submitted businesses.

## Business Verification Statuses

    DRAFT
    PENDING_VERIFICATION
    APPROVED
    REJECTED
    SUSPENDED

## Functional Rules

1.  Pending businesses appear in admin dashboard.
2.  Admin can approve a business.
3.  Admin can reject a business with rejection reason.
4.  Approved businesses become publicly searchable.
5.  Rejected businesses remain visible only to owner and admin.
6.  Suspended businesses are hidden from public search and profile
    pages.

## Acceptance Criteria

1.  Admin can approve pending business.
2.  Admin can reject pending business.
3.  Public users can see only approved businesses.
4.  Business owner can see their own rejected business and rejection
    reason.
5.  Suspended business is removed from public listing.

# FR-08 Business Search and Discovery

## Description

The system shall allow users to search and discover businesses using
keyword search, category filters, rating filters, location filters,
claimed status filters, and review count filters.

## Search Inputs

| Parameter        | Description                                                      |
|------------------|------------------------------------------------------------------|
| query            | Search by business name, website, description, category, or city |
| categoryId       | Filter by category                                               |
| parentCategoryId | Filter by parent category including child categories             |
| city             | Filter by city                                                   |
| district         | Filter by district                                               |
| ratingMin        | Minimum rating threshold                                         |
| claimedStatus    | Claimed, unclaimed, or all                                       |
| reviewCountMin   | Minimum number of reviews                                        |
| sort             | Sort order                                                       |
| page             | Page number                                                      |
| size             | Page size                                                        |

## Rating Filter Options

    All
    3.0+
    4.0+
    4.5+

## Review Count Filter Options

    Any
    1+
    5+
    10+
    25+
    50+
    100+

## Claimed Status Filter Options

    All
    Claimed
    Unclaimed

## Sort Options

    Relevance
    Highest Rated
    Most Reviewed
    Newest Businesses
    Recently Reviewed

## Search Result Card Requirements

Each search result card shall display:

1.  Business logo or placeholder.
2.  Business name.
3.  Website/domain.
4.  VeracityScore.
5.  Star display.
6.  Review count.
7.  Primary category.
8.  Location.
9.  Claimed/unclaimed badge where applicable.
10. Short description snippet.

## Search Page UI Requirements

The search page shall contain:

1.  Top search bar.
2.  Filter chips/buttons for all filters.
3.  Results count.
4.  List of business result cards.
5.  Category suggestion panel.
6.  All-filters side panel or modal.
7.  Pagination or load-more behavior.
8.  Empty state when no results match.

## Acceptance Criteria

1.  Search by business name returns matching approved businesses.
2.  Search by category returns businesses in that category.
3.  Parent category search includes child category businesses.
4.  Rating filter returns only businesses above selected threshold.
5.  Location filter returns businesses in selected location.
6.  Claimed filter works correctly.
7.  Results are paginated.
8.  Search does not expose pending/rejected/suspended businesses.

# FR-09 Business Profile Page

## Description

The system shall provide a detailed public profile page for each
approved business.

## Business Profile Header

The header shall display:

1.  Business logo or placeholder.
2.  Business name.
3.  Claimed/unclaimed badge.
4.  Primary category.
5.  Secondary category tags.
6.  VeracityScore.
7.  Star rating display.
8.  Total review count.
9.  “Write a Review” button.
10. “Visit Website” button if website exists.

## Business Details Section

The company details section shall display:

1.  Business description.
2.  Category tags.
3.  Location.
4.  Address.
5.  Website.
6.  Email.
7.  Phone.
8.  Business hours if available.
9.  Claimed status.
10. Verification status badge.

## Score Summary Section

The score summary section shall display:

1.  VeracityScore.
2.  Rating label.
3.  Star distribution bars.
4.  Total review count.
5.  Explanation link or text explaining how the score is calculated.
6.  Count of reviews within the last 12 months if available.

## Trust and Safety Section

The business profile shall include a trust message such as:

> Veracity uses automated checks and manual moderation to protect
> platform integrity. Reviews are moderated for policy violations, but
> Veracity does not fact-check individual customer experiences.

## Related Businesses Section

The system should display “People also looked at” or “Similar
businesses” based on:

1.  Same primary category.
2.  Same city or district.
3.  Similar rating.
4.  Recent review activity.

## Review Section

The review section shall display:

1.  Overall score.
2.  Total approved reviews.
3.  Star distribution.
4.  Keyword search within reviews.
5.  Review filters by rating.
6.  Sort by newest, highest rating, lowest rating.
7.  Top mentions.
8.  Review cards.
9.  Business responses below reviews.
10. Helpful/unhelpful buttons if implemented.
11. Report/flag button if implemented.

## Acceptance Criteria

1.  Approved business has public profile page.
2.  Profile displays rating and review count.
3.  Profile displays contact and category information.
4.  Profile displays only approved reviews.
5.  AI summary is displayed if available.
6.  Similar businesses are displayed when available.
7.  Write review button redirects unauthenticated users to login.

# FR-10 Review Submission

## Description

The system shall allow authenticated consumers to submit reviews for
approved businesses.

## Functional Rules

1.  Only authenticated consumers can submit reviews.
2.  A consumer can submit only one active review per business.
3.  Rating must be between 1 and 5.
4.  Review title is required.
5.  Review content is required.
6.  Review is initially stored as `PENDING`.
7.  AI analysis should run after submission.
8.  Review becomes public only after admin approval.
9.  Consumer may edit their own pending or approved review.
10. Editing an approved review may move it back to `PENDING` depending
    on implementation decision.

## Review Fields

| Field       | Required | Validation                      |
|-------------|----------|---------------------------------|
| Rating      | Yes      | Integer 1–5                     |
| Title       | Yes      | 5–120 characters                |
| Content     | Yes      | 20–3000 characters              |
| Business ID | Yes      | Must refer to approved business |

## Review Statuses

    PENDING
    APPROVED
    REJECTED
    FLAGGED
    DELETED

## Acceptance Criteria

1.  Consumer can submit valid review.
2.  Review is not public until approved.
3.  Duplicate active review for same business is prevented.
4.  Rating outside 1–5 is rejected.
5.  Short or empty review content is rejected.
6.  Review triggers AI analysis.
7.  Approved review updates business rating and review count.

# FR-11 Review Display and Filtering

## Description

The system shall display approved reviews on business profile pages with
filtering and sorting.

## Review Card Requirements

Each review card shall display:

1.  Reviewer name.
2.  Review date.
3.  Star rating.
4.  Review title.
5.  Review content.
6.  Business response if available.
7.  Helpful/unhelpful count if implemented.
8.  Report button if implemented.
9.  Review status only for admin/business owner views.

## Review Filter Options

1.  All ratings.
2.  5-star reviews.
3.  4-star reviews.
4.  3-star reviews.
5.  2-star reviews.
6.  1-star reviews.
7.  Keyword search.
8.  Most recent.
9.  Highest rating.
10. Lowest rating.

## Rating Distribution

The system shall calculate and display:

1.  Number of 5-star reviews.
2.  Number of 4-star reviews.
3.  Number of 3-star reviews.
4.  Number of 2-star reviews.
5.  Number of 1-star reviews.
6.  Percentage of each rating group.

## Acceptance Criteria

1.  Business profile displays approved reviews.
2.  Review search by keyword works within a business.
3.  Star rating filters work.
4.  Sort by most recent works.
5.  Rating distribution percentages are accurate.
6.  Pending, rejected, deleted, and flagged reviews are not shown
    publicly unless policy allows flagged-but-approved reviews.

# FR-12 Business Response to Reviews

## Description

The system shall allow business owners to respond to approved reviews on
their own businesses.

## Functional Rules

1.  Only the owner of the business can respond.
2.  Admins may view all responses.
3.  One official business response is allowed per review in MVP.
4.  Business owner can edit their response.
5.  Business owner can delete their response.
6.  Response content must be professional and must pass validation.
7.  Responses may be subject to moderation if needed.

## Acceptance Criteria

1.  Business owner can respond to a review on their own business.
2.  Business owner cannot respond to reviews on another business.
3.  Response appears below the review.
4.  Response can be edited by the business owner.
5.  Response can be deleted by the business owner.

# FR-13 Helpful and Unhelpful Review Voting

## Description

The system should allow authenticated users to vote whether a review is
helpful or unhelpful.

## Functional Rules

1.  A user can vote once per review.
2.  User can change their vote.
3.  User cannot vote on their own review.
4.  Voting counts must be displayed publicly if enabled.

## Acceptance Criteria

1.  Authenticated user can mark review helpful.
2.  Authenticated user can mark review unhelpful.
3.  Duplicate vote is prevented.
4.  Vote count updates correctly.

# FR-14 Review Reporting and Flagging

## Description

The system should allow users to report reviews that may violate
platform guidelines.

## Functional Rules

1.  Authenticated users can report a review.
2.  Report must include reason.
3.  Report appears in admin dashboard.
4.  Admin can accept or dismiss report.
5.  Reported review may be marked `FLAGGED`.
6.  Flagged reviews are reviewed manually by admin.

## Report Reasons

    Spam
    Abusive language
    Hate or harassment
    False information
    Personal information exposed
    Conflict of interest
    Other

## Acceptance Criteria

1.  User can report a review.
2.  Report appears in admin dashboard.
3.  Admin can resolve report.
4.  Review status updates according to admin decision.

# FR-15 AI-Assisted Review Moderation

## Description

The system shall analyze review content using an AI-assisted moderation
service to help admins identify problematic reviews.

## Functional Rules

1.  AI analysis runs when a review is submitted.
2.  AI analysis can also be manually triggered by admin.
3.  AI must return structured results.
4.  AI output is advisory only.
5.  Admin makes the final moderation decision.
6.  If external AI provider fails, system must fall back to rule-based
    moderation.
7.  Review text must be treated as untrusted input.
8.  AI prompt must instruct the model not to follow instructions
    contained inside the review text.
9.  AI analysis should avoid sending unnecessary personal data.

## AI Analysis Output

The AI moderation result shall include:

| Field                  | Description                           |
|------------------------|---------------------------------------|
| sentiment              | POSITIVE, NEUTRAL, NEGATIVE, or MIXED |
| toxicityScore          | Number from 0.00 to 1.00              |
| spamScore              | Number from 0.00 to 1.00              |
| profanityDetected      | Boolean                               |
| personalAttackDetected | Boolean                               |
| possibleFakeReview     | Boolean                               |
| moderationRisk         | LOW, MEDIUM, HIGH                     |
| suggestedAction        | APPROVE, REVIEW_MANUALLY, REJECT      |
| reason                 | Short explanation                     |
| extractedKeywords      | List of important terms               |

## Rule-Based Fallback Checks

If AI provider is unavailable, the system shall check:

1.  Banned word list.
2.  Excessive uppercase text.
3.  Repeated links.
4.  Very short low-effort content.
5.  Repeated characters.
6.  Suspicious promotional phrases.
7.  Spam-like URL count.

## Admin Moderation UI Requirements

Admin shall see:

1.  Review content.
2.  Rating.
3.  Reviewer name.
4.  Business name.
5.  AI sentiment.
6.  Toxicity score.
7.  Spam score.
8.  Profanity warning.
9.  Suggested action.
10. AI reason.
11. Buttons to approve, reject, or flag.

## Acceptance Criteria

1.  Review submission triggers AI analysis.
2.  AI result is stored with review.
3.  Admin can view AI result.
4.  Admin decision is not automated solely by AI.
5.  System works even if AI provider is unavailable.
6.  AI errors do not block review submission.

# FR-16 AI-Generated Business Review Summary

## Description

The system shall generate a summary of approved reviews for each
business.

## Functional Rules

1.  AI summary uses only approved reviews.
2.  Summary should be balanced and mention both positive and negative
    themes when present.
3.  Summary must not invent facts.
4.  Summary must not reveal private user information.
5.  Summary can be regenerated by admin or business owner.
6.  Summary should be cached in database to avoid repeated AI calls.
7.  If a business has too few reviews, system should show “Not enough
    reviews to generate a reliable summary.”

## Minimum Review Requirement

The default minimum number of approved reviews required for AI summary
shall be 3.

## AI Summary Output

The AI summary shall include:

| Field           | Description                          |
|-----------------|--------------------------------------|
| summaryText     | 2–5 sentence balanced review summary |
| positiveThemes  | List of common positive themes       |
| negativeThemes  | List of common negative themes       |
| topMentions     | List of frequently mentioned topics  |
| reviewCountUsed | Number of reviews used               |
| generatedAt     | Timestamp                            |

## Example Summary

> Customers commonly praise the business for friendly service, fast
> responses, and good product quality. Some reviewers mention delays
> during busy periods and inconsistent communication. Overall, recent
> reviews suggest a generally positive customer experience with a few
> service consistency concerns.

## Acceptance Criteria

1.  Summary is generated using approved reviews only.
2.  Summary appears on business profile page.
3.  Top mentions appear as tags.
4.  Summary is cached.
5.  Summary can be regenerated after new approved reviews.
6.  Businesses with insufficient reviews display a fallback message.

# FR-17 Admin Business Management

## Description

The system shall provide admin tools to manage businesses.

## Admin Business Features

1.  View all businesses.
2.  Filter businesses by status.
3.  View pending verification queue.
4.  Approve business.
5.  Reject business with reason.
6.  Suspend business.
7.  View business owner.
8.  View business review statistics.
9.  Edit category assignment if needed.
10. View claim requests.

## Acceptance Criteria

1.  Admin can view pending businesses.
2.  Admin can approve business.
3.  Admin can reject business.
4.  Admin can suspend business.
5.  Public listing updates based on admin decision.

# FR-18 Admin Review Management

## Description

The system shall provide admin tools to moderate reviews.

## Admin Review Features

1.  View pending reviews.
2.  View flagged reviews.
3.  Filter reviews by AI moderation risk.
4.  Filter reviews by status.
5.  Approve review.
6.  Reject review with reason.
7.  Flag review.
8.  Delete review if necessary.
9.  View review reports.
10. Manually trigger AI re-analysis.

## Acceptance Criteria

1.  Admin can view pending review queue.
2.  Admin can approve review.
3.  Admin can reject review.
4.  Admin can flag review.
5.  Admin can filter by AI risk level.
6.  Business rating updates after approval/rejection.

# FR-19 Admin User Management

## Description

The system shall provide admin tools to manage users.

## Admin User Features

1.  View users.
2.  Search users by name or email.
3.  Filter users by role.
4.  Suspend user.
5.  Reactivate user.
6.  View user review count.
7.  View business owner’s businesses.

## Acceptance Criteria

1.  Admin can view user list.
2.  Admin can suspend user.
3.  Suspended user cannot log in or submit reviews.
4.  Admin can reactivate user.

# FR-20 Business Owner Dashboard

## Description

The system shall provide business owners with a dashboard for managing
their businesses and reviewing feedback.

## Dashboard Features

1.  List owned businesses.
2.  View business verification status.
3.  Edit business profile.
4.  View approved reviews.
5.  Respond to reviews.
6.  View average rating.
7.  View review count.
8.  View star distribution.
9.  View AI summary.
10. Regenerate AI summary if allowed.

## Acceptance Criteria

1.  Business owner can view only their own businesses.
2.  Business owner can edit business profile.
3.  Business owner can view reviews for own business.
4.  Business owner can respond to approved reviews.
5.  Business owner cannot moderate reviews.

# FR-21 Platform Statistics

## Description

The system shall provide basic statistics for admins.

## Admin Statistics

1.  Total users.
2.  Total consumers.
3.  Total business owners.
4.  Total businesses.
5.  Pending businesses.
6.  Approved businesses.
7.  Total reviews.
8.  Pending reviews.
9.  Flagged reviews.
10. Average platform rating.
11. Reviews by rating.
12. Reviews by sentiment if AI data is available.

## Acceptance Criteria

1.  Admin dashboard displays statistics.
2.  Statistics update after relevant actions.
3.  Statistics are accessible only to admins.

# FR-22 Public Trust and Transparency Information

## Description

The system shall display simple trust and safety information to users.

## Requirements

1.  Business page shall show a platform integrity message.
2.  Business page shall show claimed/unclaimed status.
3.  Business page shall show whether the business can respond to
    reviews.
4.  Review page shall show that reviews are moderated for policy
    violations.
5.  System shall not claim that every review is fact-checked.
6.  System shall explain that AI summaries are generated from approved
    reviews.

## Acceptance Criteria

1.  Public business page contains trust/safety note.
2.  Claimed/unclaimed status is visible.
3.  AI summary includes disclaimer or tooltip.
4.  Platform does not misrepresent moderation as factual verification.

# 6. Data Requirements

## 6.1 Main Entities

The system shall use a relational database with the following main
entities:

1.  User
2.  Category
3.  Business
4.  BusinessCategory
5.  Review
6.  BusinessResponse
7.  ReviewVote
8.  ReviewReport
9.  AiReviewAnalysis
10. AiBusinessSummary
11. BusinessClaimRequest
12. AdminActionLog

## 6.2 User Entity

| Field           | Type        | Notes                           |
|-----------------|-------------|---------------------------------|
| id              | UUID / Long | Primary key                     |
| fullName        | String      | Required                        |
| email           | String      | Required, unique                |
| passwordHash    | String      | Required                        |
| role            | Enum        | CONSUMER, BUSINESS_OWNER, ADMIN |
| profileImageUrl | String      | Optional                        |
| emailVerified   | Boolean     | Default false or true for MVP   |
| status          | Enum        | ACTIVE, SUSPENDED, DELETED      |
| createdAt       | DateTime    | Auto-generated                  |
| updatedAt       | DateTime    | Auto-updated                    |

## 6.3 Category Entity

| Field        | Type        | Notes            |
|--------------|-------------|------------------|
| id           | UUID / Long | Primary key      |
| name         | String      | Required         |
| slug         | String      | Required, unique |
| parentId     | FK          | Optional         |
| description  | String      | Optional         |
| displayOrder | Integer     | Optional         |
| active       | Boolean     | Default true     |
| createdAt    | DateTime    | Auto-generated   |
| updatedAt    | DateTime    | Auto-updated     |

## 6.4 Business Entity

| Field               | Type        | Notes                                                      |
|---------------------|-------------|------------------------------------------------------------|
| id                  | UUID / Long | Primary key                                                |
| ownerId             | FK User     | Nullable for unclaimed businesses                          |
| name                | String      | Required                                                   |
| slug                | String      | Required, unique                                           |
| description         | Text        | Required                                                   |
| website             | String      | Optional                                                   |
| email               | String      | Optional                                                   |
| phone               | String      | Optional                                                   |
| address             | String      | Optional                                                   |
| city                | String      | Required                                                   |
| district            | String      | Optional                                                   |
| country             | String      | Default Sri Lanka                                          |
| logoUrl             | String      | Optional                                                   |
| coverImageUrl       | String      | Optional                                                   |
| verificationStatus  | Enum        | DRAFT, PENDING_VERIFICATION, APPROVED, REJECTED, SUSPENDED |
| claimStatus         | Enum        | UNCLAIMED, CLAIM_PENDING, CLAIMED                          |
| averageRating       | Decimal     | Cached                                                     |
| veracityScore       | Decimal     | Cached                                                     |
| reviewCount         | Integer     | Cached                                                     |
| approvedReviewCount | Integer     | Cached                                                     |
| rejectedReason      | String      | Optional                                                   |
| createdAt           | DateTime    | Auto-generated                                             |
| updatedAt           | DateTime    | Auto-updated                                               |

## 6.5 BusinessCategory Entity

| Field        | Type        | Notes                |
|--------------|-------------|----------------------|
| businessId   | FK Business | Required             |
| categoryId   | FK Category | Required             |
| categoryType | Enum        | PRIMARY or SECONDARY |

## 6.6 Review Entity

| Field           | Type        | Notes                                         |
|-----------------|-------------|-----------------------------------------------|
| id              | UUID / Long | Primary key                                   |
| userId          | FK User     | Required                                      |
| businessId      | FK Business | Required                                      |
| rating          | Integer     | 1–5                                           |
| title           | String      | Required                                      |
| content         | Text        | Required                                      |
| status          | Enum        | PENDING, APPROVED, REJECTED, FLAGGED, DELETED |
| rejectionReason | String      | Optional                                      |
| edited          | Boolean     | Default false                                 |
| createdAt       | DateTime    | Auto-generated                                |
| updatedAt       | DateTime    | Auto-updated                                  |
| approvedAt      | DateTime    | Nullable                                      |

Unique constraint:

    One active review per user per business.

## 6.7 BusinessResponse Entity

| Field      | Type        | Notes          |
|------------|-------------|----------------|
| id         | UUID / Long | Primary key    |
| reviewId   | FK Review   | Required       |
| businessId | FK Business | Required       |
| ownerId    | FK User     | Required       |
| content    | Text        | Required       |
| createdAt  | DateTime    | Auto-generated |
| updatedAt  | DateTime    | Auto-updated   |

Constraint:

    One business response per review in MVP.

## 6.8 AiReviewAnalysis Entity

| Field                  | Type        | Notes                              |
|------------------------|-------------|------------------------------------|
| id                     | UUID / Long | Primary key                        |
| reviewId               | FK Review   | Required                           |
| sentiment              | Enum        | POSITIVE, NEUTRAL, NEGATIVE, MIXED |
| toxicityScore          | Decimal     | 0.00–1.00                          |
| spamScore              | Decimal     | 0.00–1.00                          |
| profanityDetected      | Boolean     | Required                           |
| personalAttackDetected | Boolean     | Required                           |
| possibleFakeReview     | Boolean     | Required                           |
| moderationRisk         | Enum        | LOW, MEDIUM, HIGH                  |
| suggestedAction        | Enum        | APPROVE, REVIEW_MANUALLY, REJECT   |
| reason                 | Text        | Optional                           |
| extractedKeywords      | JSON/Text   | Optional                           |
| provider               | String      | AI provider or RULE_BASED          |
| createdAt              | DateTime    | Auto-generated                     |

## 6.9 AiBusinessSummary Entity

| Field           | Type        | Notes                     |
|-----------------|-------------|---------------------------|
| id              | UUID / Long | Primary key               |
| businessId      | FK Business | Required                  |
| summaryText     | Text        | Required                  |
| positiveThemes  | JSON/Text   | Optional                  |
| negativeThemes  | JSON/Text   | Optional                  |
| topMentions     | JSON/Text   | Optional                  |
| reviewCountUsed | Integer     | Required                  |
| provider        | String      | AI provider or RULE_BASED |
| generatedAt     | DateTime    | Required                  |

## 6.10 BusinessClaimRequest Entity

| Field        | Type        | Notes                       |
|--------------|-------------|-----------------------------|
| id           | UUID / Long | Primary key                 |
| businessId   | FK Business | Required                    |
| requesterId  | FK User     | Required                    |
| contactEmail | String      | Required                    |
| evidenceText | Text        | Required                    |
| evidenceUrl  | String      | Optional                    |
| status       | Enum        | PENDING, APPROVED, REJECTED |
| adminNote    | String      | Optional                    |
| createdAt    | DateTime    | Auto-generated              |
| resolvedAt   | DateTime    | Nullable                    |

## 6.11 AdminActionLog Entity

| Field      | Type        | Notes          |
|------------|-------------|----------------|
| id         | UUID / Long | Primary key    |
| adminId    | FK User     | Required       |
| actionType | String      | Required       |
| targetType | String      | Required       |
| targetId   | String      | Required       |
| reason     | Text        | Optional       |
| createdAt  | DateTime    | Auto-generated |

This entity is optional for MVP but recommended because it demonstrates
auditability.

# 7. Rating and Score Requirements

## 7.1 Rating Scale

All reviews shall use a 1–5 star rating system.

| Rating | Meaning   |
|--------|-----------|
| 5      | Excellent |
| 4      | Good      |
| 3      | Average   |
| 2      | Poor      |
| 1      | Bad       |

## 7.2 Average Rating

The system shall calculate the average rating using approved reviews
only.

    averageRating = sum(approved review ratings) / number of approved reviews

The displayed value shall be rounded to one decimal place.

## 7.3 VeracityScore

For MVP, VeracityScore may equal the average rating of approved reviews.

Recommended implementation:

    VeracityScore = rounded average of approved review ratings

Future enhancement may include recency weighting and Bayesian adjustment
to avoid over-ranking businesses with very few reviews.

## 7.4 Rating Distribution

The system shall calculate star distribution using approved reviews
only.

Example:

    5-star: 80%
    4-star: 10%
    3-star: 5%
    2-star: 0%
    1-star: 5%

## 7.5 Recalculation Rules

Business rating statistics must be recalculated when:

1.  A review is approved.
2.  A review is rejected after previously being approved.
3.  A review is deleted.
4.  A review rating is edited and reapproved.

# 8. API Requirements

## 8.1 Authentication APIs

    POST /api/auth/register
    POST /api/auth/login
    GET  /api/auth/me

## 8.2 User APIs

    GET  /api/users/me
    PUT  /api/users/me
    GET  /api/admin/users
    PATCH /api/admin/users/{id}/suspend
    PATCH /api/admin/users/{id}/activate

## 8.3 Category APIs

    GET    /api/categories
    GET    /api/categories/tree
    POST   /api/admin/categories
    PUT    /api/admin/categories/{id}
    PATCH  /api/admin/categories/{id}/deactivate

## 8.4 Business APIs

    GET    /api/businesses
    GET    /api/businesses/search
    GET    /api/businesses/{id}
    POST   /api/businesses
    PUT    /api/businesses/{id}
    GET    /api/businesses/{id}/reviews
    POST   /api/businesses/{id}/claim
    GET    /api/business-owner/businesses

## 8.5 Admin Business APIs

    GET   /api/admin/businesses
    GET   /api/admin/businesses/pending
    PATCH /api/admin/businesses/{id}/approve
    PATCH /api/admin/businesses/{id}/reject
    PATCH /api/admin/businesses/{id}/suspend
    GET   /api/admin/business-claims
    PATCH /api/admin/business-claims/{id}/approve
    PATCH /api/admin/business-claims/{id}/reject

## 8.6 Review APIs

    POST   /api/businesses/{businessId}/reviews
    GET    /api/businesses/{businessId}/reviews
    GET    /api/users/me/reviews
    PUT    /api/reviews/{id}
    DELETE /api/reviews/{id}
    POST   /api/reviews/{id}/vote
    POST   /api/reviews/{id}/report

## 8.7 Business Response APIs

    POST   /api/reviews/{reviewId}/response
    PUT    /api/responses/{id}
    DELETE /api/responses/{id}

## 8.8 Admin Review APIs

    GET   /api/admin/reviews
    GET   /api/admin/reviews/pending
    GET   /api/admin/reviews/flagged
    PATCH /api/admin/reviews/{id}/approve
    PATCH /api/admin/reviews/{id}/reject
    PATCH /api/admin/reviews/{id}/flag
    POST  /api/admin/reviews/{id}/reanalyze

## 8.9 AI APIs

    POST /api/ai/reviews/{reviewId}/analyze
    GET  /api/ai/businesses/{businessId}/summary
    POST /api/ai/businesses/{businessId}/summary/regenerate

Access rules:

1.  Review analysis endpoint shall be admin-only.
2.  Business summary regeneration shall be admin or business owner only.
3.  Public users can view existing business summary through the business
    profile endpoint.

## 8.10 Analytics APIs

    GET /api/admin/stats
    GET /api/business-owner/businesses/{id}/stats

# 9. User Interface Requirements

## 9.1 General UI Requirements

1.  The UI shall be responsive.
2.  The UI shall support desktop and mobile screen widths.
3.  The UI shall use consistent spacing, typography, and buttons.
4.  The UI shall display loading, empty, success, and error states.
5.  The UI shall not expose raw technical errors to users.
6.  Forms shall show validation errors clearly.
7.  Protected pages shall redirect unauthenticated users to login.
8.  Role-specific pages shall block unauthorized users.

## 9.2 Main Pages

The frontend shall include the following pages:

| Page                     | Route                                      |
|--------------------------|--------------------------------------------|
| Homepage                 | `/`                                        |
| Login                    | `/login`                                   |
| Register                 | `/register`                                |
| Search Results           | `/search`                                  |
| Category Browse          | `/categories`                              |
| Business Profile         | `/businesses/:id`                          |
| Write Review             | `/businesses/:id/review`                   |
| User Profile             | `/profile`                                 |
| My Reviews               | `/profile/reviews`                         |
| Business Owner Dashboard | `/business/dashboard`                      |
| Business Form            | `/business/create` or `/business/:id/edit` |
| Admin Dashboard          | `/admin`                                   |
| Admin Businesses         | `/admin/businesses`                        |
| Admin Reviews            | `/admin/reviews`                           |
| Admin Users              | `/admin/users`                             |
| Admin Categories         | `/admin/categories`                        |

## 9.3 Homepage Requirements

Homepage shall include:

1.  Hero section with search bar.
2.  Popular categories.
3.  Featured/top-rated businesses.
4.  Recent reviews.
5.  Call-to-action for writing a review.
6.  Call-to-action for business owners.
7.  Short explanation of Veracity.

## 9.4 Search Results Page Requirements

Search results page shall include:

1.  Header navigation.

2.  Large search bar with current query.

3.  Filter buttons/chips:

    - All filters
    - Rating
    - Location
    - Category
    - Claimed status

4.  Results count.

5.  Business result list.

6.  Category suggestion panel.

7.  All filters side panel.

8.  Pagination/load more.

The all-filters panel shall include:

1.  Rating selection.
2.  Location search.
3.  Company status.
4.  Number of reviews.
5.  Category selection.
6.  Reset filters button.
7.  Show results button.

## 9.5 Business Profile Page Requirements

Business profile page shall include:

1.  Business header.
2.  Rating and review count.
3.  Claimed/unclaimed status.
4.  Write review button.
5.  Visit website button.
6.  Trust/safety notice.
7.  Company details.
8.  Contact info.
9.  Score card.
10. Rating distribution.
11. Similar businesses carousel.
12. Review section.
13. Review filters.
14. AI review summary.
15. Top mentions.
16. Business responses.

## 9.6 Admin Dashboard UI Requirements

Admin dashboard shall include:

1.  Summary statistics cards.
2.  Pending business table.
3.  Pending review table.
4.  AI risk badges.
5.  Quick action buttons.
6.  Filtering by status.
7.  Search functionality.
8.  Modal/details page for reviewing business/review.
9.  Confirmation dialogs for approve/reject/suspend actions.

## 9.7 Business Owner Dashboard UI Requirements

Business owner dashboard shall include:

1.  Owned business list.
2.  Business verification status.
3.  Edit business profile button.
4.  Review list.
5.  Respond button.
6.  Rating statistics.
7.  AI summary card.
8.  Regenerate summary button.
9.  Basic analytics chart or table.

# 10. Non-Functional Requirements

## 10.1 Performance

| ID      | Requirement                                                                                                             |
|---------|-------------------------------------------------------------------------------------------------------------------------|
| NFR-P01 | Public business search API should respond within 500ms for demo-scale data.                                             |
| NFR-P02 | Business profile API should respond within 500ms for demo-scale data.                                                   |
| NFR-P03 | Frontend initial page load should be under 3 seconds on a normal connection after deployment.                           |
| NFR-P04 | List endpoints must use pagination.                                                                                     |
| NFR-P05 | Search should use database indexes on business name, city, category, status, rating, and review count where applicable. |
| NFR-P06 | AI calls may take longer but must not block page rendering if cached data exists.                                       |

## 10.2 Security

| ID      | Requirement                                                                |
|---------|----------------------------------------------------------------------------|
| NFR-S01 | Passwords must be hashed using BCrypt.                                     |
| NFR-S02 | Protected APIs must require JWT authentication.                            |
| NFR-S03 | Role-based access control must protect admin and business owner endpoints. |
| NFR-S04 | Users must not access another user’s private data.                         |
| NFR-S05 | Business owners must not manage businesses they do not own.                |
| NFR-S06 | Input validation must be applied on backend DTOs.                          |
| NFR-S07 | Global exception handler must prevent leaking stack traces.                |
| NFR-S08 | CORS must allow only configured frontend origins.                          |
| NFR-S09 | AI prompts must treat review content as untrusted input.                   |
| NFR-S10 | Sensitive environment variables must not be committed to GitHub.           |

## 10.3 Usability

| ID      | Requirement                                                                |
|---------|----------------------------------------------------------------------------|
| NFR-U01 | UI must be clean and understandable for first-time users.                  |
| NFR-U02 | Forms must show validation feedback.                                       |
| NFR-U03 | Search filters must be easy to reset.                                      |
| NFR-U04 | Business rating and review count must be visible without scrolling deeply. |
| NFR-U05 | Admin moderation decisions must require confirmation.                      |
| NFR-U06 | UI must work on desktop and mobile widths.                                 |

## 10.4 Reliability

| ID      | Requirement                                                     |
|---------|-----------------------------------------------------------------|
| NFR-R01 | System must handle AI provider failure gracefully.              |
| NFR-R02 | Review submission must not fail only because AI analysis fails. |
| NFR-R03 | Rating recalculation must be transactional.                     |
| NFR-R04 | Database migrations must be version-controlled.                 |
| NFR-R05 | CI pipeline must detect build/test failures.                    |

## 10.5 Maintainability

| ID      | Requirement                                                                   |
|---------|-------------------------------------------------------------------------------|
| NFR-M01 | Backend must follow controller-service-repository structure.                  |
| NFR-M02 | Request and response DTOs must be used instead of exposing entities directly. |
| NFR-M03 | Business logic must be placed in service layer.                               |
| NFR-M04 | Common error responses must be standardized.                                  |
| NFR-M05 | Code must be organized by domain modules.                                     |
| NFR-M06 | README must explain setup, architecture, API, and demo users.                 |

## 10.6 Scalability

| ID       | Requirement                                                                       |
|----------|-----------------------------------------------------------------------------------|
| NFR-SC01 | APIs must use pagination for lists.                                               |
| NFR-SC02 | Business rating values may be cached in business table.                           |
| NFR-SC03 | AI summaries must be cached instead of regenerated on every page load.            |
| NFR-SC04 | Search design should allow future migration to Elasticsearch or full-text search. |
| NFR-SC05 | Modular monolith structure should allow future service separation.                |

# 11. Technical Requirements

## 11.1 Backend Stack

The backend shall use:

    Java 17 or Java 21
    Spring Boot 3.x
    Spring Web
    Spring Security
    Spring Data JPA
    Hibernate
    MySQL
    Flyway
    Jakarta Bean Validation
    JWT
    JUnit 5
    Mockito
    Spring Boot Test
    Swagger/OpenAPI

## 11.2 Frontend Stack

The frontend shall use:

    React
    TypeScript
    Vite
    Tailwind CSS
    Axios
    React Router
    React Query or Context API
    Vitest and React Testing Library if time permits

## 11.3 Database

The database shall use:

    MySQL 8+

Database migrations shall be handled using Flyway.

## 11.4 Containerization

The project shall include Docker support.

Required files:

    backend/Dockerfile
    frontend/Dockerfile
    docker-compose.yml
    .env.example

Docker Compose shall run:

    mysql
    backend
    frontend

## 11.5 CI/CD

The project shall include GitHub Actions workflow.

Pipeline steps:

1.  Checkout repository.
2.  Set up Java.
3.  Run backend tests.
4.  Build backend.
5.  Set up Node.
6.  Install frontend dependencies.
7.  Build frontend.
8.  Build Docker images if configured.

Workflow file:

    .github/workflows/ci.yml

## 11.6 API Documentation

The backend should expose Swagger/OpenAPI documentation.

Suggested route:

    /swagger-ui/index.html

# 12. Testing and Quality Assurance

## 12.1 Testing Strategy

The system shall include:

1.  Unit tests.
2.  Service-layer tests.
3.  Repository tests where useful.
4.  Controller/security integration tests.
5.  API testing collection.
6.  Manual UI acceptance testing.
7.  Basic performance checks if time allows.

## 12.2 Backend Unit Tests

Required unit tests:

| Test Class                   | Purpose                                                    |
|------------------------------|------------------------------------------------------------|
| AuthServiceTest              | Registration, login, password hashing                      |
| BusinessServiceTest          | Business creation, update, ownership rules                 |
| ReviewServiceTest            | Review submission, duplicate prevention, rating validation |
| AdminBusinessServiceTest     | Approve/reject business                                    |
| AdminReviewServiceTest       | Approve/reject/flag review                                 |
| AiReviewServiceTest          | AI fallback behavior                                       |
| RatingCalculationServiceTest | Average rating and distribution                            |

## 12.3 Integration Tests

Required integration test flows:

1.  Register and login user.
2.  Business owner creates business.
3.  Admin approves business.
4.  Consumer submits review.
5.  AI analysis is generated or fallback analysis is stored.
6.  Admin approves review.
7.  Public business profile displays approved review.
8.  Business owner responds to review.
9.  Unauthorized user cannot access admin endpoint.
10. Business owner cannot manage another owner’s business.

## 12.4 Frontend Testing

Frontend tests should include:

1.  Login form validation.
2.  Register form validation.
3.  Business search page rendering.
4.  Business profile page rendering.
5.  Review form validation.
6.  Admin review moderation page rendering.

If time is limited, frontend testing may be reduced, but backend tests
must be prioritized.

## 12.5 Manual Acceptance Testing

Manual test scenarios:

1.  Guest can search businesses.
2.  Guest can filter by rating.
3.  Guest can filter by location.
4.  Guest can filter by category.
5.  Guest can open business profile.
6.  Consumer can register.
7.  Consumer can login.
8.  Consumer can write review.
9.  Review is pending after submission.
10. Admin can approve review.
11. Approved review appears publicly.
12. Business owner can respond to review.
13. AI summary appears on business profile.
14. Admin can approve business.
15. Admin can reject inappropriate review.
16. Unauthorized access is blocked.

## 12.6 Definition of Done

A feature is considered done only when:

1.  Backend endpoint is implemented.
2.  DTO validation is implemented.
3.  Authorization rules are implemented.
4.  Database changes are migrated.
5.  Frontend UI is connected.
6.  Happy path is manually tested.
7.  Main failure cases are handled.
8.  Relevant tests are written.
9.  No sensitive data is exposed.
10. README or API documentation is updated if needed.

# 13. Deployment Requirements

## 13.1 Local Deployment

The system shall run locally using:

    docker compose up

The local setup shall include:

1.  MySQL database.
2.  Backend API.
3.  Frontend application.

## 13.2 Environment Variables

The project shall use environment variables for:

    DB_URL
    DB_USERNAME
    DB_PASSWORD
    JWT_SECRET
    AI_PROVIDER
    AI_API_KEY
    FRONTEND_ORIGIN
    BACKEND_PORT

An `.env.example` file shall be provided without real secrets.

## 13.3 Production Deployment

For MVP, deployment may use:

1.  Frontend: Vercel, Netlify, or similar.
2.  Backend: Render, Railway, Azure App Service, or similar.
3.  Database: Managed MySQL or cloud database service.

Production deployment is recommended but not mandatory if Docker setup
and documentation are complete.

# 14. Constraints

## 14.1 Time Constraint

The implementation target is approximately 10 days with around 5 hours
per day.

Therefore, the project must prioritize:

1.  Core review platform features.
2.  Admin workflow.
3.  AI moderation and summary.
4.  Testing and CI/CD.
5.  Clean documentation.

The project must avoid overbuilding low-impact features.

## 14.2 Technical Constraint

The original SRS referenced a Node.js/MongoDB stack. Veracity shall use
Spring Boot and MySQL to demonstrate enterprise Java backend
development, relational database design, and layered architecture.

## 14.3 AI Constraint

AI output shall be used only as assistance. The system shall not rely on
AI as the sole decision-maker for review rejection, business suspension,
user suspension, or public trust claims.

## 14.4 Data Constraint

The application shall use fictional or manually seeded business and
review data. The system shall not scrape real reviews or business data
from external review platforms.

# 15. Assumptions and Dependencies

## 15.1 Assumptions

1.  Users have access to a modern web browser.
2.  Admin account will be seeded during application startup or database
    migration.
3.  Business and review data used for demo will be fictional.
4.  AI provider may be unavailable during development, so fallback
    moderation is required.
5.  Email verification may be simulated for MVP.
6.  The system targets demonstration and internship portfolio use, not
    immediate production launch.

## 15.2 Dependencies

1.  Java and Spring Boot ecosystem.
2.  MySQL database.
3.  Node.js and npm.
4.  Docker.
5.  GitHub Actions.
6.  Optional external AI API.
7.  Optional deployment provider.

# 16. Risk Analysis

| Risk                            | Impact | Mitigation                                    |
|---------------------------------|--------|-----------------------------------------------|
| Scope too large for 10 days     | High   | Prioritize MVP features only                  |
| AI integration takes too long   | Medium | Implement rule-based fallback first           |
| Frontend consumes too much time | Medium | Use simple reusable components                |
| Security bugs in role handling  | High   | Write authorization tests                     |
| Database design changes late    | Medium | Finalize ERD early                            |
| Deployment issues               | Medium | Dockerize locally first                       |
| Weak README reduces CV value    | High   | Document architecture and features clearly    |
| Incomplete tests                | Medium | Prioritize backend service and security tests |

# 17. Acceptance Criteria for Final Project

The project shall be accepted as complete when:

1.  Users can register and log in.
2.  JWT authentication works.
3.  Role-based access control works.
4.  Business owners can create businesses.
5.  Admins can approve/reject businesses.
6.  Public users can search and filter approved businesses.
7.  Public users can view business profile pages.
8.  Consumers can submit reviews.
9.  Reviews require admin approval before public visibility.
10. AI moderation analysis is stored for submitted reviews.
11. Admins can view AI moderation results.
12. Admins can approve/reject/flag reviews.
13. Approved reviews appear on business profile pages.
14. Business scores and rating distributions update correctly.
15. Business owners can respond to reviews.
16. AI business review summary is displayed.
17. Backend has meaningful tests for critical workflows.
18. Docker Compose runs the system locally.
19. GitHub Actions CI runs backend tests and frontend build.
20. README explains setup, architecture, features, API, and demo
    credentials.

# 18. Traceability Matrix

| Requirement Area     | Related Features                  |
|----------------------|-----------------------------------|
| User Management      | FR-01, FR-02, FR-03               |
| Business Listings    | FR-04, FR-05, FR-06, FR-07        |
| Search and Discovery | FR-08, FR-09                      |
| Review System        | FR-10, FR-11, FR-12, FR-13, FR-14 |
| AI Features          | FR-15, FR-16                      |
| Administration       | FR-17, FR-18, FR-19, FR-21        |
| Business Tools       | FR-20                             |
| Trust and Safety     | FR-14, FR-15, FR-22               |
| Testing              | Section 12                        |
| Deployment           | Section 13                        |
| Security             | NFR-S01 to NFR-S10                |

# 19. Future Enhancements

The following features may be added after the MVP:

1.  Real email verification.
2.  Password reset through email.
3.  OAuth login.
4.  Real Google Maps integration.
5.  Business photo gallery.
6.  Business hours editor.
7.  Advanced analytics dashboard.
8.  Subscription tiers.
9.  Review invitation system.
10. Verified purchase reviews.
11. Trusted reviewer badge.
12. Personalized business recommendations.
13. Advanced full-text search using Elasticsearch.
14. Redis caching.
15. In-app notifications.
16. Email notifications.
17. Public business API.
18. Mobile application.
19. Multi-language support.
20. Production monitoring and logging dashboard.
21. Admin audit log expansion.
22. Blue-green deployment.
23. Rate limiting.
24. Advanced fraud detection.
25. Review authenticity scoring.

# 20. Recommended Implementation Order

## Day 1: Planning and Setup

1.  Finalize SRS.
2.  Create GitHub repository.
3.  Create backend and frontend skeleton.
4.  Configure MySQL and Docker Compose.
5.  Create ERD.
6.  Define API contract.

## Day 2: Authentication

1.  User entity.
2.  Register API.
3.  Login API.
4.  JWT security.
5.  Role-based authorization.
6.  Profile API.

## Day 3: Business and Category Module

1.  Category entity.
2.  Business entity.
3.  Business creation.
4.  Business search.
5.  Admin business approval.

## Day 4: Review Module

1.  Review entity.
2.  Submit review.
3.  Admin review moderation.
4.  Public review display.
5.  Rating recalculation.

## Day 5: Business Response and AI Moderation

1.  Business response entity.
2.  Owner response API.
3.  AI review analysis interface.
4.  Rule-based fallback analyzer.
5.  Admin AI moderation display API.

## Day 6: AI Summary and Backend Tests

1.  AI business summary.
2.  Summary caching.
3.  Rating distribution.
4.  Service tests.
5.  Security tests.

## Day 7: Public Frontend

1.  Homepage.
2.  Search results page.
3.  Business profile page.
4.  Review form.
5.  Login/register pages.

## Day 8: Dashboards

1.  Admin dashboard.
2.  Business owner dashboard.
3.  Review moderation UI.
4.  Business approval UI.
5.  Response management UI.

## Day 9: CI/CD, Docker, Testing, Seed Data

1.  GitHub Actions workflow.
2.  Docker build verification.
3.  Seed demo data.
4.  Postman/Bruno collection.
5.  Fix bugs.

## Day 10: Polish and Documentation

1.  UI polish.
2.  README.
3.  Screenshots.
4.  Demo video or GIF.
5.  Deployment if possible.
6.  CV bullet points.
7.  Interview notes.

# 21. Minimum Demo Data

The system should include seed data for demonstration:

## Users

1.  Admin user.
2.  Consumer user.
3.  Business owner user.

## Categories

1.  Coffee Store.
2.  Grocery Store.
3.  Restaurant.
4.  Software Company.
5.  Website Designer.
6.  Gym.
7.  Auto Insurance Agency.

## Businesses

At least 8 businesses:

1.  3 approved claimed businesses.
2.  2 approved unclaimed businesses.
3.  2 pending businesses.
4.  1 rejected business.

## Reviews

At least 20 reviews:

1.  Mix of 1-star to 5-star reviews.
2.  Some pending reviews.
3.  Some approved reviews.
4.  Some flagged reviews.
5.  Reviews with positive and negative sentiment.
6.  Reviews suitable for AI moderation demonstration.

# 22. Glossary

| Term               | Meaning                                                           |
|--------------------|-------------------------------------------------------------------|
| Approved Review    | A review accepted by admin and visible publicly.                  |
| Pending Review     | A review waiting for admin moderation.                            |
| Flagged Review     | A review needing further review due to report or AI risk.         |
| Rejected Review    | A review not allowed publicly due to violation or admin decision. |
| Claimed Business   | Business profile controlled by an approved business owner.        |
| Unclaimed Business | Business profile not yet controlled by an owner.                  |
| AI Risk            | Moderation risk level produced by AI or fallback analyzer.        |
| Top Mentions       | Common topics extracted from approved reviews.                    |
| VeracityScore      | Public rating score shown for a business.                         |
| Modular Monolith   | A single backend application organized into clean domain modules. |

# 23. Final Scope Statement

Veracity v1.0 shall deliver a complete, internship-ready consumer review
web application with strong Spring Boot backend architecture, React
frontend, MySQL database, secure JWT authentication, role-based
workflows, business verification, review moderation, AI-assisted content
analysis, AI-generated business review summaries, testing,
Dockerization, CI/CD, and professional documentation.

The project shall prioritize correctness, maintainability, security, and
demonstrable software engineering practice over unnecessary feature
expansion.

The final implementation should be strong enough to explain in
interviews as a real-world review platform rebuild from an SRS, with
clear design decisions, scoped delivery, and practical SDLC execution.
