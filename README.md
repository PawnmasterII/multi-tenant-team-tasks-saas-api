# SaaS API (Multi-tenant)

A multi-tenant SaaS API built with **NestJS**, **PostgreSQL**, **Prisma**, **Redis**, and **BullMQ**.

This repository provides a solid backend foundation for:
- Multi-tenant organizations (org-scoped data)
- Projects / tasks / comments (example domain)
- Auth-ready user model + refresh-token storage
- Idempotency keys, audit logs, outbox events, and webhook delivery tracking

---

## Tech Stack

- **Runtime / Framework:** NestJS (TypeScript)
- **Database:** PostgreSQL 16
- **ORM:** Prisma
- **Queue:** BullMQ
- **Cache / Broker:** Redis 7
- **Docs:** Swagger (OpenAPI)

---

## Requirements

- Node.js (recommended: latest LTS)
- Docker + Docker Compose

---

## Quick Start

### 1) Install dependencies

```bash
npm install
```

### 2) Start Postgres + Redis

```bash
docker compose up -d
```

### 3) Configure environment variables

Create a `.env` file in the project root.

Minimum required:

```bash
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/saas_api?schema=public"
```

If you also use Redis/BullMQ in your modules, add:

```bash
REDIS_HOST=localhost
REDIS_PORT=6379
```

### 4) Run Prisma

Generate Prisma client:

```bash
npm run prisma:generate
```

Run migrations (development):

```bash
npm run prisma:migrate:dev
```

### 5) Start the API

```bash
npm run start:dev
```

---

## API Documentation (Swagger)

If Swagger is enabled in the app bootstrap, you can typically access:

- `http://localhost:3000/api`

(Exact path depends on your bootstrap configuration in [`src/main.ts`](src/main.ts:1).)

---

## NPM Scripts

Common scripts from [`package.json`](package.json:1):

- `npm run start` — start
- `npm run start:dev` — start in watch mode
- `npm run start:prod` — start compiled app
- `npm run lint` — eslint + autofix
- `npm run test` / `test:e2e` / `test:cov` — tests

Prisma scripts:

- `npm run prisma:generate`
- `npm run prisma:format`
- `npm run prisma:migrate:dev`
- `npm run prisma:migrate:deploy`
- `npm run prisma:studio`

---

## Data Model (Prisma)

The schema is defined in [`prisma/schema.prisma`](prisma/schema.prisma:1) and includes:

- **Tenancy**: [`Organization`](prisma/schema.prisma:73), [`Membership`](prisma/schema.prisma:92)
- **Example domain**: [`Project`](prisma/schema.prisma:108), [`Task`](prisma/schema.prisma:123), [`Comment`](prisma/schema.prisma:155)
- **Auth support**: [`User`](prisma/schema.prisma:55), [`RefreshToken`](prisma/schema.prisma:173)
- **Platform features**: [`ApiKey`](prisma/schema.prisma:191), [`IdempotencyKey`](prisma/schema.prisma:211), [`AuditLog`](prisma/schema.prisma:227)
- **Reliability / integrations**: [`OutboxEvent`](prisma/schema.prisma:251), [`WebhookEndpoint`](prisma/schema.prisma:271), [`WebhookDelivery`](prisma/schema.prisma:290)

---

## Docker

This repo ships a simple compose file for local dependencies:

- [`docker-compose.yml`](docker-compose.yml:1) provides `postgres` and `redis`

Default ports:
- Postgres: `5432`
- Redis: `6379`

You can override ports and Postgres defaults via environment variables (see compose file for `${...:-default}` usage).

---

## Testing

```bash
npm run test
npm run test:e2e
```

---

## Notes / Conventions

- All domain data is expected to be **scoped by `orgId`** for multi-tenancy.
- Consider adding:
  - request-scoped tenant resolution (e.g., via subdomain/header)
  - row-level security (RLS) at the database layer (optional)
  - background workers for outbox/webhook delivery

---

## License

UNLICENSED (see [`package.json`](package.json:1)).
