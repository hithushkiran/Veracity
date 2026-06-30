# Veracity GitHub Setup Guide

**Document:** 07-github-setup.md  
**Project:** Veracity  
**Purpose:** Manual checklist for creating the GitHub repository and GitHub Projects board

---

## 1. Repository Setup

Recommended repository name:

```text
veracity
```

Recommended description:

```text
AI-assisted consumer review platform for Sri Lankan businesses built with Spring Boot, React, MySQL, Docker, and CI/CD.
```

Recommended visibility:

```text
Public
```

Use public if you want recruiters to see the project. Use private while building if you prefer, then switch to public after polish.

---

## 2. Initial Local Git Commands

From the project root:

```bash
git init
git add .
git commit -m "docs: add Day 1 planning documents"
git branch -M main
```

After creating the empty GitHub repo, connect it:

```bash
git remote add origin https://github.com/<your-username>/veracity.git
git push -u origin main
```

Replace `<your-username>` with your GitHub username.

---

## 3. Recommended Repository Settings

After creating the repo:

1. Add a proper README.
2. Add topics.
3. Enable Issues.
4. Create GitHub Projects board.
5. Add branch protection later after CI is created.

Recommended topics:

```text
spring-boot
react
typescript
mysql
jwt-authentication
docker
ci-cd
review-platform
software-engineering
portfolio-project
```

---

## 4. GitHub Projects Board

Recommended board name:

```text
Veracity MVP Build
```

Recommended view:

```text
Board
```

Recommended status columns:

1. Backlog
2. Ready
3. In Progress
4. Review/Test
5. Done
6. Deferred

Recommended custom fields:

| Field | Type | Values |
|---|---|---|
| Priority | Single select | Must, Should, Could |
| Area | Single select | Backend, Frontend, Database, AI, DevOps, Docs, Testing |
| Target Day | Single select | Day 1, Day 2, Day 3, Day 4, Day 5, Day 6, Day 7, Day 8, Day 9, Day 10 |
| Risk | Single select | Low, Medium, High |

---

## 5. First Project Items to Add

Create these as draft issues or actual repository issues.

### Day 1 — Planning and Design

- [ ] Save finalized SRS in `docs/01-srs.md`
- [ ] Create product backlog in `docs/02-product-backlog.md`
- [ ] Create ERD in `docs/03-erd.md`
- [ ] Create API contract in `docs/04-api-contract.md`
- [ ] Create architecture notes in `docs/05-architecture.md`
- [ ] Create test plan in `docs/06-test-plan.md`
- [ ] Create GitHub repository
- [ ] Create GitHub Projects board

### Day 2 — Authentication

- [ ] Create Spring Boot backend skeleton
- [ ] Add User entity and migration
- [ ] Implement registration endpoint
- [ ] Implement login endpoint
- [ ] Add JWT generation and validation
- [ ] Add role-based authorization
- [ ] Add current user/profile endpoint
- [ ] Add AuthService tests

### Day 3 — Business and Category

- [ ] Add Category entity and migration
- [ ] Add Business entity and migration
- [ ] Add BusinessCategory join table
- [ ] Implement category endpoints
- [ ] Implement business creation
- [ ] Implement business search
- [ ] Implement admin business approval/rejection

### Day 4 — Reviews

- [ ] Add Review entity and migration
- [ ] Implement review submission
- [ ] Prevent duplicate active review
- [ ] Implement admin review moderation
- [ ] Implement public review display
- [ ] Implement rating recalculation

### Day 5 — Responses and AI Moderation

- [ ] Add BusinessResponse entity
- [ ] Implement owner response API
- [ ] Create AI review analysis interface
- [ ] Implement rule-based fallback analyzer
- [ ] Store AI analysis results
- [ ] Show AI moderation result for admin

### Day 6 — AI Summary and Backend Tests

- [ ] Add AI business summary entity
- [ ] Generate summary from approved reviews only
- [ ] Cache generated summary
- [ ] Add rating distribution endpoint
- [ ] Add critical service tests
- [ ] Add security tests

### Day 7 — Public Frontend

- [ ] Create React TypeScript frontend
- [ ] Add Tailwind CSS
- [ ] Add routing
- [ ] Build homepage
- [ ] Build search results page
- [ ] Build business profile page
- [ ] Build login/register pages
- [ ] Build review form

### Day 8 — Dashboards

- [ ] Build admin dashboard
- [ ] Build admin business management page
- [ ] Build admin review moderation page
- [ ] Build admin user management page
- [ ] Build business owner dashboard
- [ ] Build response management UI

### Day 9 — DevOps and Seed Data

- [ ] Add Docker Compose
- [ ] Add backend Dockerfile
- [ ] Add frontend Dockerfile
- [ ] Add `.env.example`
- [ ] Add GitHub Actions workflow
- [ ] Add seed demo data
- [ ] Create API testing collection

### Day 10 — Polish and Documentation

- [ ] Polish UI
- [ ] Finalize README
- [ ] Add screenshots
- [ ] Record demo video/GIF
- [ ] Final manual testing
- [ ] Prepare CV bullet points
- [ ] Prepare interview explanation notes

---

## 6. First Commit Message

Use:

```bash
git commit -m "docs: add Day 1 planning documents"
```

This gives a clean professional starting point before coding begins.
