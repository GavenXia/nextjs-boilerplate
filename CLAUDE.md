# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
pnpm install                     # Install dependencies
pnpm run dev                     # Start dev server at localhost:3000
pnpm run build                   # Production build (runs prisma generate + next build)
pnpm run start                   # Start production server
pnpm run lint                    # ESLint with next/core-web-vitals + next/typescript
npx prisma migrate dev --name init  # Initialize database tables
npx prisma migrate dev           # Apply schema changes to database
npx prisma generate              # Regenerate Prisma client after schema changes
```

## Architecture

### Directory structure

- **`app/`** — Next.js App Router pages and layouts
  - `(landing-page)/` — Public marketing site at `/` (hero, features, pricing, etc.)
  - `login/` — Login page (Google OAuth)
  - `dashboard/` — Protected dashboard (`(overview)/` for default view)
  - `billing/` — Stripe subscription management page
  - `account/` — User account page
  - `api/[...nextauth]/` — Auth.js route handler
  - `api/payment/checkout_sessions/` — Stripe checkout session creation
  - `api/payment/webhook/` — Stripe webhook handler
  - `api/impersonate/` — Admin impersonation endpoint
  - `ui/` — Shared UI components (login-form, stripe subscribe button, skeletons, side nav, etc.)
  - `lib/` — Utility functions and placeholder data

- **`domain/`** — Clean architecture domain layer
  - `user/` — User entity, repository (Prisma-backed), use-cases (GetUser, CreateUser, UpdateUser)
  - `company/` — Company entity, repository, use-cases (CreateCompany, RegisterTransaction)
  - Pattern: Entity → Port (interface) → Repository (implements Port) → Use-Case (calls Repository)

- **`infra/`** — Infrastructure / external service wrappers
  - `prisma.ts` — Singleton PrismaClient
  - `stripe.ts` — Stripe.js client-side loader wrapper
  - `mailgun.ts` — Mailgun client wrapper (EU endpoint by default)
  - `googleAnalytics.tsx` — Google Analytics script component
  - `googleTagManager.tsx` — GTM script component
  - `providerDetector.ts` — Runtime detection of which services are configured based on env vars; each wrapper checks `providersList` before initializing

- **`prisma/`** — Prisma schema and migrations. Models: User, Company, Account, Session, VerificationToken, PaymentTransaction

### Auth flow

NextAuth v5 (beta) with JWT session strategy. Providers: Google OAuth + Credentials (for admin impersonation). On sign-up (`trigger === 'signUp'`), `auth.config.ts` callbacks auto-create a Company for the new user. The `middleware.ts` protects all routes except `/` (landing page), `/api/*`, and static assets — unauthorized requests redirect to `/login`. Admin impersonation: set `NEXT_PUBLIC_ADMIN_USER_ID` env var, then admins can log in as any user via `/api/impersonate`.

### Payment flow

1. Client-side: `<SubscribeComponent>` loads Stripe.js, then POSTs `priceId` to `/api/payment/checkout_sessions`
2. Checkout API route creates a Stripe Checkout Session with user/company metadata
3. On success/cancel, user is redirected to `/billing` with query params
4. Stripe webhook at `/api/payment/webhook` listens for `checkout.session.completed` and records the transaction via `RegisterTransaction` use-case

### Path aliases

`@/*` maps to the project root (configured in `tsconfig.json` baseUrl + paths).

### Environment variables

Copy `.env.example` to `.env` and fill required values: `AUTH_SECRET`, `DATABASE_URL`, `AUTH_GOOGLE_ID`, `AUTH_GOOGLE_SECRET`, Stripe keys, Google Analytics ID, Mailgun API key, Google Tag Manager ID, and `NEXT_PUBLIC_ADMIN_USER_ID` for impersonation.
