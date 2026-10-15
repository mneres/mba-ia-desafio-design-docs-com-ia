# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is a course challenge repo ("Da Reunião ao Documento: Design Docs Gerados por IA"). The deliverable is **documentation only**, produced from two sources:

- `TRANSCRICAO.md` — literal transcript of a technical meeting (`[hh:mm] Nome: fala`) where a team decided to build an Order Notification Webhooks system on top of the existing OMS.
- `src/`, `prisma/`, `tests/` — the existing Order Management System (OMS) codebase, which serves as ground truth for how the new feature must integrate.

**The full assignment text (goals, required doc sections, acceptance criteria, suggested order of work) lives in `README.md`. Read it before doing any documentation work in this repo. Once `README.md` has been replaced by the production-process documentation, read the original with `git show main:README.md`.**

Deliverables go in `docs/` (`PRD.md`, `RFC.md`, `FDD.md`, `TRACKER.md`, `docs/adrs/ADR-NNN-*.md`, with the index in `docs/adrs/README.md`) plus a rewritten root `README.md` describing the production process. They start as placeholders and are generated with the prompts in `prompts/` (see "Documentation workflow"). When a document already has real content, the prompts review it in place and never append duplicate sections.

### Hard constraint: do not modify application code

`src/`, `prisma/`, `tests/`, and config files are reference material only — **never edit them** as part of this challenge. Every claim in the generated docs must be traceable to either `TRANSCRICAO.md` (timestamp + speaker) or a real file path in the code; nothing may be invented. `docs/TRACKER.md` is the cross-reference that enforces this — treat it as the anti-hallucination mechanism, not an afterthought.

## Documentation workflow

Each document has a generator/reviewer prompt in `prompts/`. Each prompt handles create, review, or "no changes needed", and must converge on re-runs. Run them in dependency order:

1. `prompts/adrs-generator.md`: decisions (ADRs are the source the others build on)
2. `prompts/rfc-generator.md`: architecture proposal, links ADRs, stays within ~700 to 2,600 words
3. `prompts/fdd-generator.md`: implementation design
4. `prompts/prd-generator.md`: product view, consolidation of the above
5. `prompts/tracker-generator.md`: traceability, run after any change to the documents above
6. `prompts/readme-generator.md`: process retrospective, run last
7. `prompts/final-review.md`: read-only audit of every acceptance criterion; binary APROVADA/REPROVADA

When one document changes, re-run the prompts of the documents that depend on it (for example an ADR change ripples to RFC, FDD, PRD, Tracker and README).

### Conventions used across the docs

- Docs are written in Brazilian Portuguese; section headings use the exact names required by the assignment.
- Transcript citations are `` `[hh:mm] Nome` ``, one speaker per citation (no ranges, no "Bruno, Larissa"). The speaker must be the one who said it in that minute.
- Precedence on conflicts: transcript and code are ground truth; an accepted ADR beats the RFC; the RFC beats the FDD. Divergences are reported, not silently resolved.
- Details the sources do not define are labeled `(hipótese)`, citing the code file whose pattern they follow when there is one. Open questions stay open across all documents.
- Files the feature would create (e.g. `src/modules/webhooks/`, `src/worker.ts`) are marked `(novo)` or `a criar` on every mention. Never cite a nonexistent path as existing.
- No em dashes in the generated docs.
- `docs/TRACKER.md` table columns are fixed: `ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização`, with Fonte `TRANSCRICAO` or `CODIGO`. Targets: ≥ 80% coverage, ≥ 70% `TRANSCRICAO` rows, ≥ 5 `CODIGO` rows. Unsourced statements get no row and are listed under "Itens sem origem".
- Known ambiguity in the transcript: the retry policy is "total 5 tentativas" (`[09:48] Larissa`), but the five intervals and "quase 15 horas entre primeira falha e última tentativa" (`[09:17] Diego`) only add up with an initial send plus 5 retries. Treat it as an open question in every document; any document that needs a value must label it `(hipótese)`.

## Commands

```bash
npm run dev          # start API with hot reload (tsx watch, loads .env)
npm run build         # compile to dist/ (tsconfig.build.json)
npm start             # run compiled build

npm run db:migrate    # prisma migrate dev
npm run db:reset      # prisma migrate reset --force
npm run db:seed       # tsx prisma/seed.ts

npm test               # vitest run (all tests)
npm run test:watch     # vitest watch mode
npx vitest run tests/orders.test.ts   # single test file
npx vitest run -t "creates an order"  # single test by name

npm run lint           # eslint . --ext .ts
npm run format          # prettier --write .
```

Tests require a real MySQL instance (`docker-compose up -d` brings up `mysql:8.0` on `localhost:3306`); `tests/setup.ts` connects Prisma and truncates all tables before each test — there is no mocked DB layer. Vitest runs with `singleFork: true` / `fileParallelism: false` because tests share one database and must not run concurrently.

## Architecture of the existing OMS

Express + TypeScript (ESM, `NodeNext`-style imports with explicit `.js` extensions) + Prisma over MySQL. Every domain module under `src/modules/<name>/` follows the same layering, wired together manually (no DI container) in `buildControllers()` in `src/app.ts`:

```
routes.ts        → Router: authenticate + validate middleware, maps HTTP verbs to controller methods
controller.ts     → RequestHandler: extracts req, calls service, sets status code, forwards errors via next(err)
service.ts        → business logic, throws typed errors from src/shared/errors
repository.ts      → Prisma queries only
schemas.ts        → Zod schemas for request validation, also the source of inferred input types
```

Modules: `auth`, `users`, `customers`, `products`, `orders`. `src/routes/index.ts` mounts them all under `/api/v1`.

### Order lifecycle — the core domain logic

`src/modules/orders/order.status.ts` defines the order state machine (`PENDING → PAID → PROCESSING → SHIPPED → DELIVERED`, with `CANCELLED` reachable from `PENDING`/`PAID`/`PROCESSING`). `canTransition(from, to)` is the single source of truth for legal transitions.

`OrderService.changeStatus()` (`src/modules/orders/order.service.ts`) is the key transactional flow any webhook/event work will hook into:
1. Runs inside a single `prisma.$transaction`.
2. Validates the transition via `canTransition`.
3. Debits stock on `PENDING → PAID` (`shouldDebitStock`) or replenishes it on cancellation from `PAID`/`PROCESSING` (`shouldReplenishStock`).
4. Writes the new status and appends an `OrderStatusHistory` row in the same transaction.

`OrderService.create()` similarly wraps customer/product lookup, stock/price snapshotting, order-number reservation (`OrderNumberSequence`, sequential `ORD-NNNNNN`), and the initial `OrderStatusHistory` row in one transaction.

### Error handling

All domain errors extend `AppError` (`src/shared/errors/app-error.ts`: `message`, `statusCode`, `errorCode`, optional `details`). Concrete subclasses live in `src/shared/errors/http-errors.ts` (`ValidationError`, `UnauthorizedError`, `ForbiddenError`, `NotFoundError`, `ConflictError`, `UnprocessableEntityError`, plus domain-specific ones like `InvalidStatusTransitionError`, `InsufficientStockError`). Controllers never format error responses themselves — they always `next(err)`.

`src/middlewares/error.middleware.ts` is the single place that turns errors into HTTP responses: `AppError` → its own status/code, `ZodError` → 400 `VALIDATION_ERROR`, known Prisma errors (`P2002` unique constraint → 409, `P2025` not found → 404), anything else → 500 with `logger.error(...)` and no leaked details. New error types (e.g. `WEBHOOK_*` codes) should follow this same `AppError` subclass pattern rather than inventing a new response shape.

### Auth

JWT bearer tokens (`src/middlewares/auth.middleware.ts`): `authenticate` verifies the token and attaches `req.user = { id, email, role }`; `requireRole(...roles)` gates by `'ADMIN' | 'OPERATOR'`. Route files apply `authenticate` at the router level and `requireRole` per-route where needed.

### Cross-cutting

- `src/shared/logger/index.ts` — Pino logger, redacts auth headers/passwords/tokens, pretty-printed only in development.
- `src/shared/http/response.ts` — shared pagination envelope (`paginated()`, `PaginatedResponse<T>`) used by all list endpoints.
- `src/config/env.ts` — all env vars validated/parsed through a single Zod schema at boot; process exits on invalid config.
- `src/middlewares/validate.middleware.ts` — generic Zod-based `validate({ body, query, params })` middleware used by every route file.

There is currently **no** event/outbox/webhook/queue mechanism anywhere in the codebase — that absence is the gap the documentation package is meant to fill.

### Data model (Prisma, MySQL)

`User` (ADMIN/OPERATOR), `Customer`, `Product`, `Order`, `OrderItem`, `OrderStatusHistory` (append-only audit trail of status changes, `fromStatus`/`toStatus`/`changedBy`/`reason`), `OrderNumberSequence` (single-row counter for human-readable order numbers). All PKs are UUIDs (`@db.Char(36)`); money is stored as integer cents.
