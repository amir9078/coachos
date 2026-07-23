# 03 — Open-Source Leverage Map (build vs. integrate)

The request behind this doc: *"pull free GitHub repos, merge them, make it a full-proof plan."* Here is the honest engineering answer: we **leverage** these projects, but we **integrate** them as services rather than copy-merging their code into ours. Two reasons:

1. **Licensing.** Most of the best engines are AGPLv3. Running them as separate self-hosted services that our app talks to over APIs is clean. Copy-merging AGPL code into a proprietary codebase legally obligates us to open-source the entire product — a business-model decision we should not make by accident.
2. **Maintenance.** A merged fork means we own every future security patch of five massive codebases. Integration means upstream keeps maintaining them for free — which is the actual point of using open source.

> **Re-verified July 2026:** every engine below was re-researched against its rivals (features + licenses fetched directly from each repo). Two changes resulted — Twenty dropped for a native build, n8n replaced by Activepieces. Full comparison with license proofs: [`docs/09-engine-comparison.md`](09-engine-comparison.md).

## The map

| # | Engine | Repo | License | What we use it for | Phase | Integration mode |
|---|---|---|---|---|---|---|
| 1 | **Cal.com** | github.com/calcom/cal.com | AGPLv3 (+ paid commercial license option) | Bookings module: scheduling links, calendar sync, reminders | 2 | Self-host + embed; or their hosted API if self-hosting is premature |
| 2 | **Documenso** | github.com/documenso/documenso | AGPLv3 | Contracts module: e-signatures | 2 | Self-host + API |
| 3 | ~~Twenty~~ **→ built natively** | *(dropped — see [`docs/09`](09-engine-comparison.md) §3)* | Ours (proprietary) | CRM/pipeline for Leads — native Postgres tables + our UI, with Twenty and EspoCRM as the design brief. One less service, ~4 GB less RAM, fully white-label | 3 | Native build |
| 4 | **[Listmonk](https://listmonk.app/)** | github.com/knadh/listmonk | AGPLv3 | Newsletter sending, subscriber lists, nurture sequences (Module 3) | 3 | Self-host + API — single Go binary + Postgres, no separate infra to run |
| 5 | ~~n8n~~ **→ Activepieces** | github.com/activepieces/activepieces | **MIT** (Community Edition — verified at the repo) | Automation engine: recipes, scheduled flows, triggers, syncs. Replaced n8n: its Sustainable Use License doesn't cover powering a paid customer-facing feature ([`docs/09`](09-engine-comparison.md) §5, license text quoted) | 2–3 | Self-host + API; coaches only ever see our own recipes UI |
| 6 | **Supabase** | github.com/supabase/supabase | Apache 2.0 | Our own app's database, auth, storage, row-level security | 1 | Hosted (free tier → paid) |
| 7 | **Next.js / React** | github.com/vercel/next.js | MIT | Our own app's frontend | 1 | Direct dependency (MIT = no copyleft concerns) |
| 8 | **Stripe SDKs** | github.com/stripe | MIT | Billing, Path A — coach has their own Stripe account, money never touches us, doc 02 Module 5 | 2 | API |
| 8b | **Stripe Connect** | Stripe (not open-source) | Stripe ToS | Billing, Path B — coach with no payment account of their own; Stripe handles KYC/identity verification and holds/moves funds, we never do; built-in "application fee" mechanism powers the admin-configured commission option, verified directly | 2 | API — same Stripe integration, a second configured mode, not a second system |
| 9 | **Gmail API** | Google (not open-source) | Google API ToS | Coach's own-address sending — one of three options under "Email Connections," doc 02 cross-cutting | 2 | Direct OAuth2 integration — requires Google app-verification review, see doc 04 |
| 9b | **Microsoft Graph API** (Outlook/365 mail) | Microsoft (not open-source) | Microsoft API ToS | Second "Email Connections" option, for coaches on Microsoft 365 rather than Gmail | 2 | OAuth2 integration, standard app-registration process — no equivalent app-review risk verified yet, but not assumed risk-free either |
| 9c | **Generic SMTP/IMAP** | Standard email protocols, no vendor | N/A — open protocol | Third "Email Connections" option — any custom-domain email a coach already has, connected with host/port/credentials like any email client | 2 | The most universal option — works with literally any email provider, no OAuth review process at all, at the cost of a slightly more manual one-time setup for the coach |
| 10 | **Activepieces** (reused) | *(already row 5)* | MIT | Execution engine behind Module 8 recipes AND Module 10 Daily Briefing pulls AND the MCP refresh trigger | 3 | Same self-hosted instance, curated flow templates — no new service |
| 11 | **Meilisearch** (deferred) | github.com/meilisearch/meilisearch | MIT | Directory search (Module 9), only if Supabase full-text search proves insufficient | 3+ | Not adopted yet — v1 uses Supabase's built-in full-text search; add only if relevance genuinely demands it |
| 12 | **Wikipedia Current Events Portal** | en.wikipedia.org/wiki/Portal:Current_events | CC BY-SA 3.0 | World-catchup source for Module 10, Daily Briefing | 3 | Explicitly licensed for commercial reuse with attribution — verified directly, not assumed |
| 13 | **Direct publisher RSS feeds** (ICF's own blog confirmed; expand list, verifying each individually) | Each publisher's own site | Varies — verify per publisher | Category news + 5-minute learn source for Module 10 | 3 | Standard headline + snippet + link-back aggregator use, never full-text republishing |
| 14 | **Supabase Realtime** (reused — same Supabase project, row 6) | Already in the stack | Apache 2.0 | Real-time chat for Module 11 v2 (Broadcast for instant delivery, Presence for online/typing status) | 4 | No new engine — confirmed directly (doc's own examples show a chat component built on Broadcast) that this covers real-time messaging without adding a dedicated chat service like Rocket.Chat or Stream |
| 15 | **Coolify** | github.com/coollabsio/coolify | Apache 2.0 | Self-hosted deployment platform — the Phase 3+ landlord for every self-hosted engine in this table, plus a self-hosted Supabase instance | 3+ | Self-host on one VPS; migration trigger and full topology in [`docs/07-architecture.md`](07-architecture.md) |
| 16 | **Mastra** | github.com/mastra-ai/mastra | Apache 2.0 (core) | The "Pipeline Agent" — multi-step reasoning + human-in-the-loop approval for Leads (Module 1–2), running inside our own app, not a separate service | 3 | Library, not a hosted service — full research and demo in [`docs/10-ai-agent-layer.md`](10-ai-agent-layer.md) |
| 17 | **Meta Graph API** (Lead Ads) | Meta (not open-source) | Meta Platform Terms | Facebook/Instagram lead capture, Module 1 | 3 | Requires Meta App Review (`leads_retrieval` permission) — verified directly, real but standard process, see doc 04 |
| 18 | **WhatsApp Business Cloud API** | Meta (not open-source) | Meta Platform Terms | WhatsApp lead capture, Module 1 | 3 | Requires Meta Business verification (2–5 business days) — verified directly, achievable |
| 19 | **LinkedIn Marketing API** (Lead Gen Forms) | LinkedIn (not open-source) | LinkedIn API ToS | LinkedIn lead capture, Module 1 | 3 | Requires formal LinkedIn Partner Program approval — verified directly, **"weeks to months," selective** — apply early, tracked as a timeline risk in doc 04, do not block the roadmap on it |

**Module 11's v1 (posts, likes, comments, connections, shares) needs no new engine either** — it's the same Postgres tables + Next.js pattern as everything else in this app. The only genuinely new infrastructure in the whole Community module is Realtime for chat, and that's already part of the Supabase project this plan already runs on.

**On row 6, Supabase, long term:** Phase 1–2 uses Supabase's hosted free/Pro tier — simplest path while validating the product. From Phase 3, once usage genuinely approaches the free tier's limits or Pro-tier overages start appearing on an actual bill, the plan is to self-host the same Supabase stack (Postgres + Auth + Storage + Realtime — all open-source, row 6 stays Apache 2.0 either way) via Coolify on a flat-cost VPS instead of paying per-MAU/GB. Nothing about the application code changes in that migration — only where the software runs. Full reasoning and diagram: [`docs/07-architecture.md`](07-architecture.md).

**Lead capture on the public marketing site (`coachos-site`) also runs on Supabase, starting now — not Phase 1.** Decided after considering Tasjeel (the domain/hosting registrar) as a cheaper-seeming option for a small `leads` table: Tasjeel's shared cPanel hosting caps outbound email at 100/hour and isn't built for real data volume, which matters once the founder's actual target ("lakhs of customers") is factored in. A `leads` table in the same Supabase project the real app will eventually run on scales cleanly (the free tier's 50k-MAU limit only counts *authenticated* users, not raw table rows — a plain lead-capture table holds low lakhs of rows well inside the free tier's 500MB) and needs no migration later, since it's already the right database. Schema + RLS policy: `coachos-site/supabase/schema.sql`. Tasjeel stays the right call for domain registration and business email specifically — those don't have a volume-scaling problem the way lead capture and newsletter sending do.

## The news-source trap to avoid (Module 10) — corrected after direct verification

**Every "free" commercial news API and aggregator we checked turned out to restrict its free tier to non-commercial/development use — including sources this plan originally recommended.** Rather than assume any of these are safe, each was checked directly (fetching the actual terms page, not guessing):

| Source | What it looked like | What it actually says |
|---|---|---|
| NewsAPI.org | "Free tier" | Free tier is **development-only**; forbidden in production; real pricing starts at **$449/month** |
| Google News RSS | Free, no key needed, works instantly | Its own copyright notice restricts it to **"personal, non-commercial use...within a personal feed reader"** |
| Mediastack | "100 free calls/mo" | Free plan explicitly **excludes commercial use** — that feature only appears on paid tiers |
| GNews | "Free, no credit card" | Own FAQ states: **"the free subscription cannot be used for commercial projects... non-commercial projects, development, and testing purposes only"** |
| NewsData.io | "200 free credits/day" | Terms not confirmable via direct check; given every comparable provider above restricts free tiers the same way, **do not rely on this without a direct, current confirmation from NewsData.io itself** |

**This plan previously listed Google News RSS as the primary source and GNews/NewsData.io as fallbacks — all three were wrong, and none of them had actually been verified at the time.** That's now corrected below.

**What we actually use, verified this time:**
1. **Wikipedia's Current Events Portal, for the world catchup** — explicitly CC BY-SA licensed, confirmed permits commercial reuse with attribution. Structured chronologically with categorized world events, already close to a "world catchup" shape.
2. **Direct publisher RSS feeds, for category news and the 5-minute learn** — ICF's own blog feed (`coachingfederation.org/feed/`) is confirmed real and directly relevant. Before adding any other publisher (HBR, Forbes, etc.) to the curated list, fetch that specific publisher's actual feed and terms — HBR's RSS syndication page, for example, sits under their paid *store* and its commercial terms were not confirmable, so it's excluded until someone gets a direct answer from HBR. The safe, standard practice regardless of a given publisher's stated terms: show only the headline, a one-line AI summary, and a link back to the original — never republish full article text.

AI's job is to summarize and select from these verified sources — never to generate a headline or a "fact" from nothing (doc 02, Module 10).

## Why Listmonk specifically (Module 3 — Marketing)

Newsletter sending is a genuinely solved problem — no reason to build it. [Listmonk](https://listmonk.app/) is a single self-hosted binary (Go + Postgres, Docker image available) that already handles the parts that are tedious to build correctly:

- **Subscriber lists at scale** — single/double opt-in, SQL-based segmentation (e.g. "clients on the Executive pack who haven't opened the last 3 sends") — no cap that matters at our size (it's proven at 7M+ sends on a fraction of one CPU core)
- **Campaign sending** — visual drag-and-drop or Markdown/HTML composer, multi-threaded SMTP with rate limiting so we don't get flagged as spam
- **Built-in analytics** — opens, bounces, click-throughs per campaign, without us building a tracking pipeline
- **Unsubscribes & compliance** — handled by Listmonk out of the box, which matters directly for GDPR (doc 01 §5)

CoachOS's own code only does two things Listmonk can't: (1) the AI draft — turning the coach's session themes + voice profile into the actual newsletter text, and (2) pushing that draft into Listmonk's API once the coach approves it. Listmonk does the sending, tracking, and list hygiene.

## What we build ourselves (the differentiation)

Only the things no repo gives us — which is exactly where our moat is:

- The **AI workflow layer**: notes→summary, prep briefs, voice-profile content drafting, lead scoring, follow-up drafting (Claude API; open-weight hosted API as cost hedge — see README §6)
- The **unified coach dashboard** stitching all services into one UI
- The **client portal**
- The **approval-first AI UX** (drafts queue, one-click approve/edit/send)

## Architecture sketch

```
┌─────────────────────────────────────────────────┐
│  CoachOS app (Next.js + Supabase)               │
│  • Unified dashboard  • Client portal           │
│  • AI workflows (Claude API / open-weight API)  │
└──────┬──────────┬──────────┬──────────┬─────────┘
       │ API      │ API      │ API      │ API
   ┌───▼───┐ ┌────▼────┐ ┌───▼───┐ ┌────▼────┐
   │Cal.com│ │Documenso│ │Twenty │ │Listmonk │   ← self-hosted OSS services
   └───────┘ └─────────┘ └───────┘ └─────────┘
              (n8n as automation glue between them)
```

## Phase-1 reality check

Phase 1 (the wedge) needs only: **Next.js + Supabase + Claude API + a Cal.com embed**. Everything else arrives in later phases. Do not stand up five services on day one — each self-hosted service is an operational liability until revenue justifies it. Until Phase 2, prefer the hosted/free tiers of these projects over self-hosting.

## License obligations checklist (before Phase 2 ships)

- [ ] Confirm each AGPL service runs unmodified (or publish our modifications for that service)
- [ ] Keep our proprietary app code in a separate repository from any AGPL code
- [ ] n8n: verify our usage fits the Sustainable Use license (internal automation = yes; reselling n8n as a feature = requires their license)
- [ ] Cal.com: decide self-host (AGPL obligations) vs. commercial license vs. hosted API — decision gate at Phase 2 start
- [ ] Legal review of the license posture in the same pass as the contracts-template legal review
