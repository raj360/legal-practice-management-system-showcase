# LegitNexus / LPMS

Public showcase for **LPMS** (Legal Practice Management System), the flagship product under the **LegitNexus** brand (LNX Technologies), delivered during work at Tek Juice.

LPMS is multi-tenant SaaS for law firms: quotes, cases, documents, communications, and UK conveyancing workflows.

## Problem statement

Conveyancing and legal ops teams generate quotes from many provider rate cards, then manually open cases, assign solicitors, create milestones, produce documents, and chase email. That handoff is slow, error-prone, and hard to audit across firm tenants that need isolation.

## Solution path

1. **Multi-tenant foundation:** schema-per-tenant Postgres with Keycloak-provisioned realms and roles.
2. **Quote engine:** rate-card CRUD and dynamic fee calculation for buy, sell, and remortgage across many providers.
3. **Quote to case:** convert an accepted quote into a case with client records, solicitor assignment, and milestones.
4. **Case and document ops:** lifecycle, attachments, generated documents, storage.
5. **Communications:** Outlook or SES-style delivery with queued jobs.
6. **Compliance path:** HMRC SDLT submission flows for stamp duty where required.
7. **Ship reliably:** CI/CD to ECS/Fargate so quote and case services stay available under concurrent use.

## Tech stack

| Area | Choices |
| --- | --- |
| Backend | NestJS monorepo (gateway, auth, case, quote, document, communication, tasks) |
| Frontend | React, Vite, Redux Toolkit, TanStack Query |
| Data | PostgreSQL, TypeORM (schema-per-tenant) |
| Auth | Keycloak |
| Files | S3-compatible storage |
| Email | Outlook Graph, SES, and provider fallback via BullMQ |
| Cloud | AWS ECS/Fargate, ECR, GitHub Actions |

## Architecture (clean plan)

```
                 ┌─────────────────────┐
                 │  React + Vite app   │
                 └──────────┬──────────┘
                            │
                 ┌──────────▼──────────┐
                 │  Core / API gateway │
                 └──────────┬──────────┘
        ┌─────────┬─────────┼─────────┬─────────┐
        ▼         ▼         ▼         ▼         ▼
     auth      quote     case     document  communication
   (Keycloak)  engine   lifecycle  engine    + queues
        │         │         │         │         │
        └─────────┴────┬────┴─────────┴─────────┘
                       ▼
              PostgreSQL (tenant schemas)
                       │
              S3 · SES/Outlook · HMRC SDLT
```

**Bounded contexts**
- **Tenancy and auth:** realms, roles, schema provisioning
- **Pricing:** rate cards, quotes, promos, quote-to-case
- **Matters:** cases, parties, milestones, time, reporting
- **Documents:** templates, generation, folder storage
- **Comms:** mailboxes, templates, outbound providers

**Related sample:** [legal-practice-management-api](https://github.com/raj360/legal-practice-management-api) is a smaller NestJS challenge API (JWT roles, cases, time tracking, simulated data layer).

**Role on this project:** Backend Software Engineer. Rate-card engine, quote-to-case automation, case/document/comms services, and ECS delivery paths.

## Note

This repo is a README showcase. Production LPMS source remains private.
