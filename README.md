# LeadHane — Real Estate Office CRM

A multi-tenant SaaS for small, independent real estate offices: a listing website plus a lead CRM, with AI property matching and WhatsApp automation built in. Leads stop getting lost in WhatsApp chats — every enquiry lands in a pipeline, gets a broker, and gets followed up automatically.

> UI language is Turkish (built for the Turkish market). Code and docs are in English.

## What it does

**Public website**
- Listing catalogue with search filters, listing detail pages and photo gallery
- Team (broker) page and contact page
- Lead capture forms and one-tap WhatsApp contact on every listing

**Agent panel (`/admin`)**
- **Lead pipeline** — drag-and-drop Kanban board by stage, lead scoring, follow-up dates, full interaction history
- **AI property matching** — Claude reads a lead's profile (sale/rent, district, budget, rooms) and ranks the active listings that fit, returning a reason and a score for each match
- **AI lead summary** — one click turns a long interaction history into a short brief before a call
- **Listings management** — create, edit, publish; bulk import with a dry-run preview
- **Team & roles** — admin / broker / intern roles, enforced on every API route
- **Reports** — pipeline and performance overview

**Automations (Vercel Cron)**
- **Daily hotsheet** — every working morning each broker gets a WhatsApp summary of today's follow-ups and new leads
- **Action plans** — multi-step follow-up sequences (WhatsApp / call / email / task) that run day by day for each lead
- **Instant lead alert** — a new lead is pushed to the responsible broker's WhatsApp within seconds

**SaaS layer**
- Self-serve office sign-up with multi-step onboarding
- Plans with server-side limits (brokers per office, leads per month) and an upgrade path in the UI

## Architecture

```
Next.js 15 (App Router) ──► Supabase (Auth + Postgres)
        │
        ├── Anthropic Claude ── property matching (Sonnet), lead summaries (Haiku)
        ├── Evolution API ───── WhatsApp messages
        ├── Vercel Cron ─────── daily hotsheet, action plans
        └── Vercel Blob ─────── listing photos
```

- Tenant-scoped schema: `tenants`, `brokers`, `listings`, `leads`, `lead_interactions`, `action_plans`, `lead_action_logs`, `saved_filters`
- Drizzle defines the schema and migrations; runtime queries go through one data-access module (`lib/db.ts`)
- Zod validation and role checks on every API route, rate limiting on public endpoints
- Broker creation rolls back the auth user if the database insert fails

## Tech stack

Next.js 15 · TypeScript · Tailwind CSS v4 · Supabase · Drizzle ORM · Zod · Anthropic Claude API · Evolution API (WhatsApp) · Vercel

## Running locally

```bash
npm install
cp .env.example .env.local   # fill in Supabase, Anthropic and WhatsApp keys
npm run db:push              # apply the schema
npm run seed                 # optional demo data
npm run dev                  # http://localhost:3000
```

## Author

Built by [Eyup Gulcu](https://github.com/eyupgulcu) · [Flovia Technologies](https://floviatech.com) — AI automation for small businesses.
