# nextjs-cloudflare-saas-starter

A production-ready SaaS starter kit built on Next.js 16, Hono, Drizzle, and Cloudflare. Auth, billing, webhooks, and edge deployment — wired together and ready to extend.

**[Live Demo →](https://nextjs-cloudflare-saas-starter-web.real-estate.workers.dev/)**

![Starter kit preview](./image.png)

---

## What's included

### Auth
- Clerk authentication with page-based sign-in and sign-up
- Protected routes with redirect intent preservation
- Local user sync via Clerk webhooks (`user.created`, `user.updated`, `user.deleted`)
- Welcome email sent automatically on first sign-up

### Billing
- Stripe Checkout with monthly and yearly subscription support
- Webhook handler that keeps your database in sync with Stripe
- Billing dashboard route showing live subscription status
- `isProUser()` helper ready to gate any feature

### Dashboard
- Protected dashboard with real nested routes — `/dashboard`, `/dashboard/billing`, `/dashboard/settings`
- Desktop collapsible sidebar and mobile drawer navigation
- Active route highlighting out of the box

### API
- Standalone Hono API running on Cloudflare Workers
- Clerk bearer token verification on every protected route
- Request-scoped Drizzle client via worker bindings
- Health check, user, and subscription endpoints included

### Database
- Drizzle ORM with Neon (serverless Postgres)
- `users` and `subscriptions` tables with migrations ready to run
- Drizzle Studio for local data inspection

### Email
- Resend integration for transactional email
- Welcome email triggered from the Clerk webhook on `user.created`

### Deploy
- Next.js deployed via OpenNext on Cloudflare Workers
- Hono API deployed as a standalone Cloudflare Worker
- Single `make deploy` command deploys both

---

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 16, React 19, App Router |
| Styling | Tailwind CSS v4, shadcn/ui primitives |
| Animation | Framer Motion |
| State | TanStack Query (server state) |
| Auth | Clerk |
| API | Hono 4.x on Cloudflare Workers |
| Validation | Zod, @hono/zod-validator |
| Database | Drizzle ORM + Neon (serverless Postgres) |
| Billing | Stripe |
| Email | Resend |
| Deploy | OpenNext + Wrangler + Cloudflare |
| Monorepo | Turborepo + pnpm |

---

## Project structure

```
apps/
  web/          Next.js 16 frontend
  api/          Hono API Cloudflare Worker

packages/
  db/           Drizzle schema, migrations, DB client
  config/       Shared TypeScript configuration
```

---

## Getting started

### Prerequisites

- Node.js 20+
- pnpm 9+
- Accounts: [Clerk](https://clerk.com), [Neon](https://neon.tech), [Stripe](https://stripe.com), [Resend](https://resend.com), [Cloudflare](https://cloudflare.com)

### Setup

```bash
# 1. Extract the downloaded project folder
cd nextjs-cloudflare-saas-starter

# 2. Install dependencies
pnpm install

# 3. Set up environment variables
cp .env.example .env                              # root — used by Drizzle CLI
cp apps/web/.env.example apps/web/.env.local      # web runtime
cp apps/api/.dev.vars.example apps/api/.dev.vars  # API worker runtime

# 4. Fill in your API keys in each file

# 5. Push the database schema
make db:push

# 6. Start development
make dev
```

> **Why three env files?** Each runtime reads from a different location. The root `.env` exists solely for Drizzle CLI commands (`make db:push`, `make db:migrate`). The web app reads from `apps/web/.env.local`. The API worker reads from `apps/api/.dev.vars`.

After `make dev`:
- Web → http://localhost:3000
- API → http://localhost:8787

### Stripe local testing

```bash
make stripe:listen
# Forwards Stripe events to http://localhost:3000/api/webhooks/stripe
```

---

## Environment variables

### Root `.env`
Used only by Drizzle CLI commands. Keep it minimal.

```bash
DATABASE_URL=postgresql://user:password@ep-xxx.neon.tech/neondb?sslmode=require
```

### `apps/web/.env.local`

```bash
# Clerk — https://dashboard.clerk.com → API Keys
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=
CLERK_WEBHOOK_SECRET=

# Neon — https://console.neon.tech → Connection String
DATABASE_URL=

# Stripe — https://dashboard.stripe.com → Developers → API Keys
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=
STRIPE_PRO_MONTHLY_PRICE_ID=
STRIPE_PRO_YEARLY_PRICE_ID=     # optional — yearly checkout is enabled only when this is set

# Resend — https://resend.com/api-keys
RESEND_API_KEY=
RESEND_FROM_EMAIL=

# App
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:8787
```

### `apps/api/.dev.vars`

```bash
APP_URL=http://localhost:3000
API_ALLOWED_ORIGINS=http://localhost:3000,http://127.0.0.1:3000
CLERK_SECRET_KEY=
DATABASE_URL=
```

---

## Deployment

```bash
# Deploy frontend to Cloudflare Workers (via OpenNext)
make deploy:web

# Deploy API to Cloudflare Workers
make deploy:api

# Deploy both
make deploy
```

Set your production environment variables in the Cloudflare Dashboard under each Worker's settings before deploying.

---

## Makefile reference

```bash
make dev              # start web + api in parallel
make build            # build all apps
make lint             # lint all packages
make type-check       # type check all packages
make clean            # remove build outputs and node_modules

make db:push          # push schema to Neon (no migration file)
make db:generate      # generate migration from schema diff
make db:migrate       # run pending migrations
make db:studio        # open Drizzle Studio

make stripe:listen    # start Stripe webhook listener
make deploy:web       # deploy frontend
make deploy:api       # deploy API
make deploy           # deploy everything
```

---

## Pricing

**$59 — one-time payment, lifetime access.**

You get the full source code. No subscriptions, no license renewals. Use it in unlimited personal and commercial projects.

**[Buy now →](#)**

---

## FAQ

**Do I need to know Cloudflare Workers to use this?**
No. The deploy config is already set up. Once Cloudflare env vars and Wrangler auth are configured, `make deploy` deploys both targets. Understanding Workers helps if you want to extend the API layer, but it is not required to get started.

**Can I use Vercel instead of Cloudflare?**
The starter is built and tested for Cloudflare. Switching to Vercel would require removing the OpenNext adapter and adjusting the API worker setup. A Vercel variant is planned as a separate kit.

**Can I use a different database?**
Postgres-compatible providers (like Supabase or Railway Postgres) are the easiest swap and usually only require updating the connection string. Moving to MySQL or SQLite-based providers would require adapting the DB package, schema, and migrations.

**Can I use NextAuth instead of Clerk?**
Not with this kit as-is. Clerk is deeply integrated into the auth flow, webhook sync, and API middleware. Replacing it would require significant refactoring.

**Will I get updates?**
Yes. Future updates are available via the same download link at no extra cost.

**Is this production-ready?**
Yes. The demo is deployed live on Cloudflare, and the starter includes working auth, billing, webhook, and deployment flows.

---

## License

This is a commercial product. Purchase grants you a personal license to use this code in unlimited personal and commercial projects. Redistribution or resale of the starter kit itself is not permitted.
