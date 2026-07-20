# 03 — Open-Source Leverage Map (build vs. integrate)

The request behind this doc: *"pull free GitHub repos, merge them, make it a full-proof plan."* Here is the honest engineering answer: we **leverage** these projects, but we **integrate** them as services rather than copy-merging their code into ours. Two reasons:

1. **Licensing.** Most of the best engines are AGPLv3. Running them as separate self-hosted services that our app talks to over APIs is clean. Copy-merging AGPL code into a proprietary codebase legally obligates us to open-source the entire product — a business-model decision we should not make by accident.
2. **Maintenance.** A merged fork means we own every future security patch of five massive codebases. Integration means upstream keeps maintaining them for free — which is the actual point of using open source.

## The map

| # | Engine | Repo | License | What we use it for | Phase | Integration mode |
|---|---|---|---|---|---|---|
| 1 | **Cal.com** | github.com/calcom/cal.com | AGPLv3 (+ paid commercial license option) | Bookings module: scheduling links, calendar sync, reminders | 2 | Self-host + embed; or their hosted API if self-hosting is premature |
| 2 | **Documenso** | github.com/documenso/documenso | AGPLv3 | Contracts module: e-signatures | 2 | Self-host + API |
| 3 | **Twenty** | github.com/twentyhq/twenty | AGPLv3 | CRM/pipeline core for Leads module | 3 | Self-host + API; we build the AI layer on top |
| 4 | **[Listmonk](https://listmonk.app/)** | github.com/knadh/listmonk | AGPLv3 | Newsletter sending, subscriber lists, nurture sequences (Module 3) | 3 | Self-host + API — single Go binary + Postgres, no separate infra to run |
| 5 | **n8n** | github.com/n8n-io/n8n | Fair-code (Sustainable Use) | Automation glue between services (triggers, syncs) | 2–3 | Self-host; note fair-code limits on reselling n8n itself |
| 6 | **Supabase** | github.com/supabase/supabase | Apache 2.0 | Our own app's database, auth, storage, row-level security | 1 | Hosted (free tier → paid) |
| 7 | **Next.js / React** | github.com/vercel/next.js | MIT | Our own app's frontend | 1 | Direct dependency (MIT = no copyleft concerns) |
| 8 | **Stripe SDKs** | github.com/stripe | MIT | Billing (not open-source service, but free SDKs) | 2 | API |
| 9 | **Gmail API** | Google (not open-source) | Google API ToS | Coach's own-address sending, doc 02 cross-cutting | 2 | Direct OAuth2 integration — requires Google app-verification review, see doc 04 |
| 10 | **n8n** (reused) | *(already row 5)* | Fair-code | Execution engine behind Module 8 Automation recipes | 3 | Same self-hosted instance, new curated workflow templates — no new service |
| 11 | **Meilisearch** (deferred) | github.com/meilisearch/meilisearch | MIT | Directory search (Module 9), only if Supabase full-text search proves insufficient | 3+ | Not adopted yet — v1 uses Supabase's built-in full-text search; add only if relevance genuinely demands it |
| 12 | **RSS feeds** (Google News RSS + curated publication feeds) | N/A — open web standard, not a repo | N/A | News/world-catchup source for Module 10, Daily Briefing | 3 | Direct feed parsing — free, unlimited, no API key |

## The news-API trap to avoid (Module 10)

Before reaching for a paid news API, know the trap: **NewsAPI.org's free tier is development-only** — its own terms explicitly forbid using it in a live, revenue-generating product, and its actual production pricing starts at **$449/month**. That's the kind of cost surprise this doc exists to prevent.

**What we use instead, in order:**
1. **RSS feeds (free, unlimited)** — Google News RSS for the general "world catchup," plus a curated list of RSS feeds from real coaching/business/psychology publications (HBR, ICF's own blog, Forbes Coaches Council, etc.) for the category news and the 5-minute learn. This is the default for all of Phase 3 and likely forever.
2. **A metered free-tier API, only if RSS coverage proves too thin for a category** — GNews (100 requests/day free, paid from $84/mo) or NewsData.io (200 credits/day free) are viable fallbacks; note their daily caps before relying on them at scale.

AI's job is to summarize and select from these real feeds — never to generate a headline or a "fact" from nothing (doc 02, Module 10).

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
