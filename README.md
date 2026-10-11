# LeadHane: Real Estate Office CRM

LeadHane is a CRM and listing website for small real estate offices. A lot of small offices in Turkey still keep track of buyers in WhatsApp chats, and leads go cold when nobody follows up. With LeadHane every enquiry goes into a pipeline, gets assigned to a broker, and gets followed up even when nobody remembers to.

The interface is in Turkish because the product is for the Turkish market. The code and these docs are in English.

## Features

The public site has a listing catalogue with filters, a page for each listing with a photo gallery, a team page, a contact page, and a WhatsApp button on every listing. Enquiry forms create a lead directly in the CRM.

Inside the agent panel (`/admin`) brokers work from a Kanban pipeline. Each lead has a stage, a score, a follow-up date and a full history of calls and messages.

Two features use Claude:

- Property matching. Claude reads what the buyer wants (sale or rent, district, budget, number of rooms) and picks the active listings that fit, with a short reason and a score for each one.
- Lead summaries. A broker can turn a long conversation history into a few lines before calling someone back.

Listings can be added one by one or imported in bulk with a dry run first. Offices can have several users with admin, broker or intern roles, and the roles are checked on the server for every API call.

Two jobs run on Vercel Cron:

- Every working morning each broker gets a WhatsApp message with the day's follow-ups and the new leads.
- Follow-up plans send WhatsApp messages, create call reminders or tasks on the right day for each lead.

New leads are also pushed to the assigned broker on WhatsApp as soon as they come in.

Offices sign up on their own. Each plan has a limit on brokers and monthly leads, enforced on the server, and the UI links to an upgrade when a limit is reached.

## How it's put together

```
Next.js 15 (App Router) -> Supabase (Auth + Postgres)
   Claude API     property matching (Sonnet), lead summaries (Haiku)
   Evolution API  WhatsApp messages
   Vercel Cron    morning summary, follow-up plans
   Vercel Blob    listing photos
```

The schema is split by tenant: `tenants`, `brokers`, `listings`, `leads`, `lead_interactions`, `action_plans`, `lead_action_logs` and `saved_filters`. Drizzle holds the schema and migrations, and all queries go through `lib/db.ts`. API input is validated with Zod and public endpoints are rate limited. If creating a broker fails halfway, the auth user is deleted again so nothing is left behind.

Stack: Next.js 15, TypeScript, Tailwind CSS v4, Supabase, Drizzle ORM, Zod, Claude API, Evolution API, Vercel.

## Running it locally

```bash
npm install
cp .env.example .env.local   # Supabase, Anthropic and WhatsApp keys
npm run db:push              # create the tables
npm run seed                 # optional demo data
npm run dev                  # http://localhost:3000
```

Made by [Eyup Gulcu](https://github.com/eyupgulcu) at [Flovia Technologies](https://floviatech.com).
