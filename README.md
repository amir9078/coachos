# CoachOS — The Operating System for Coaches & Mentors

> **Working codename.** One platform where a coach or mentor runs their entire business — leads, marketing, bookings, contracts, client delivery, and payments — with AI doing the repetitive work and humans doing the human work.

**Status:** Plan + clickable prototype. Not yet in build.
**Owner:** Amir Hussain
**Last updated:** July 2026
**Confidential:** This repo is private until launch — invited collaborators only (see §8b). To view the plan as a website, open `index.html` in any browser; the prototype is `prototype/index.html`. The hosted GitHub Pages link is offline while the repo is private.

---

## 1. The one-paragraph pitch

122,974 professional coaches worldwide generate $5.34B a year, yet only 19% invested in any new technology last year while 54% say better platforms are a priority — the largest documented demand-adoption gap in the industry ([ICF/PwC 2025 Global Coaching Study](https://coachingfederation.org/blog/global-coaching-industry-surpasses-5-34-billion-as-emerging-markets-drive-growth/)). Today a coach stitches together 5–8 disconnected tools (CRM, scheduler, e-sign, email, invoicing, notes). CoachOS replaces the stitching: **one desk, every room of the business**, with AI built into each workflow rather than bolted on — at solo-coach pricing ($29–59/mo), a tier where no incumbent has built AI-first.

## 2. What it does — the eleven modules

| # | Module | What the coach gets | AI or Human? |
|---|--------|--------------------|--------------|
| 1 | **Leads — Attract** | Landing page + lead magnet builder, lead capture forms, AI-scored inbound enquiries | Platform (AI) |
| 2 | **Leads — Manage** | Pipeline board (Enquiry → Discovery call → Proposal → Won), AI follow-up nudges and drafted replies, plus a **Pipeline Agent** that reasons across a stalled lead's history before proposing a next step — always pausing for approval ([`docs/10`](docs/10-ai-agent-layer.md)) | Platform (AI agent) |
| 3 | **Marketing** | AI-drafted posts, newsletters and email sequences in the coach's own voice; content calendar | Platform (AI) + optional human service |
| 4 | **Bookings** | Scheduling links, calendar sync, automated reminders, no-show recovery | Platform |
| 5 | **Contracts & Payments** | Coaching-agreement templates, e-signature, invoicing, package/subscription billing | Platform |
| 6 | **Client Delivery** | Session notes → AI summary + action items, client progress timeline, AI session-prep briefs | Platform (AI) — *the wedge, see §4* |
| 7 | **Practice Intelligence** | Dashboard: revenue, pipeline health, client retention, at-risk clients flagged by AI | Platform (AI) |
| 8 | **Automations** | Pre-built recipes (silent-lead follow-up, prep-brief scheduling, overdue-invoice reminders) — toggle on, no workflow builder | Platform (Activepieces-backed) |
| 9 | **Coach & Mentor Directory** | Public opt-in profile, search, and enquiry routing straight into the coach's own Leads pipeline — our own acquisition channel, not just a feature | Platform |
| 10 | **Daily Briefing** | News by niche category, a quick world catchup, and a 5-minute daily learn — one shared digest per category (not per coach), also readable on the public website | Platform (AI, RSS-sourced) |
| 11 | **Coach Community** | LinkedIn-style peer network for coaches: connect, post, like, comment, share — v1. Direct messaging and real-time chat are v2, a separate build (see §4) | Platform |

**The human-service layer** (sold by us, alongside the software — this is deliberate, not a gap):
- **Onboarding & implementation** — we migrate the coach's clients, templates and calendar in a done-with-you session (paid setup fee; also our retention weapon).
- **Done-for-you marketing** — monthly content package produced by our team using the platform's AI + human editing (recurring service revenue at 3–5× software price).
- **Community & referrals** — curated coach community; coaches refer coaches. This profession sells by word of mouth.

**Plus three cross-cutting capabilities, not numbered modules:**
- **Meetings & recordings** — sessions runnable via the coach's Zoom/Meet (recordings + transcripts pulled in automatically where the platform allows), any recording uploadable for transcription, and in-platform "CoachOS Meet" video from Phase 3+ — every path ends in transcript → AI summary → coach approval. Consent built in; full comparison in [`docs/09-engine-comparison.md`](docs/09-engine-comparison.md) §8.
- **Email Connections** (expanded from "Gmail integration") — coach-approved sends go out from the coach's own email, not a CoachOS address, so replies land in their real inbox. Supports Gmail (OAuth), Microsoft 365/Outlook (OAuth), or **any custom-domain email via standard SMTP/IMAP** — a coach isn't required to use Gmail. Multiple named connections are supported per coach (e.g. a main address for client emails, a separate one for automated reminders). Phase 2; Google's app-verification review is a real multi-week timeline risk, tracked in doc 04.
- **Admin MCP server** — lets the team push, pull, and update Daily Briefing content (news items, images, corrections) directly through a Claude conversation instead of a web admin panel — internal tooling only, never given to coaches (Phase 3, full spec in [`docs/05-build-guide.md`](docs/05-build-guide.md) §4.6).

Full module-by-module specification: [`docs/02-product-spec.md`](docs/02-product-spec.md)
Full system architecture — one diagram, every layer, module-mapped: [`docs/07-architecture.md`](docs/07-architecture.md)

## 3. Why this wins (and what we deliberately don't do)

- **We are the coach's infrastructure, not the coach's replacement.** AI-only coaching products (Valence/$50M raise, CoachHub AIMY, Rocky.ai) compete with the coach. We make the human coach more profitable — so the 122,974 coaches are our allies, not our disruptees. This also keeps us in the EU AI Act's *limited-risk* tier instead of the high-risk regime. Details: [`docs/01-market-research.md`](docs/01-market-research.md).
- **The moat is workflow + data lock-in, not the AI.** Any AI feature copies in weeks (see Jasper's collapse from ~$120M to $35–55M revenue). Years of client history, signed contracts, and pipeline living inside CoachOS does not copy. We never compete on "our model is smarter."
- **We don't build everything at once.** See the phased roadmap below — the graveyard of all-in-one platforms is founders who built seven modules before earning one paying user.

## 4. Build order — the wedge strategy

**Phase 1 (Months 1–3) — the wedge: Client Delivery.**
Session notes → AI summary, prep briefs, progress timeline, scheduling. This is the single most-requested AI capability in the coaching-tools research, buildable by a solo founder with Claude Code, and it creates daily usage + data lock-in from day one. *Ship, pilot with 5 coaches, convert to paid.*

**Phase 2 (Months 4–8) — the money loop: Bookings + Contracts + Payments + Gmail.**
Now the coach's revenue flows through us. Integrate, don't build: scheduling and e-sign have mature open-source engines (see §5). Start the Gmail app-verification review this phase — it runs in parallel, on Google's clock, not ours.

**Phase 3 (Months 9–14) — the growth loop: Leads + Marketing + Automations + Directory + Daily Briefing.**
Pipeline CRM, AI follow-ups, content composer, landing pages, automation recipes. Launch the done-for-you marketing service here — service revenue funds the build. The Coach & Mentor Directory gets built this phase too, but stays unlaunched to public search until coach supply clears the same paying-coach gate as everything else — an empty directory is worse than no directory. Daily Briefing ships alongside it: a per-category news/world-catchup/5-minute-learn digest, generated once a day and shared across every coach in that niche rather than regenerated per coach, and readable on the public site as its own SEO channel.

**Phase 4 (Months 15–20) — intelligence + community v1.** Practice dashboard, at-risk-client AI, and the Coach Community's feed: connect, post, like, comment, share. Gated the same way as the Directory — a community with no one in it is worse than no community, so this waits for a real base of engaged coaches, not a launch-day feature.

**Phase 5 (Months 21+) — community v2 + scale.** Direct messaging and real-time chat — deliberately a separate phase from the feed, because live chat is a genuinely different engineering problem (presence, instant delivery) from posting and commenting, not just "more of the same." Only from here, with real leverage, do we evaluate corporate/L&D partnerships.

Costs, revenue model and milestones: [`docs/04-roadmap-costs-gtm.md`](docs/04-roadmap-costs-gtm.md)

## 5. Build vs. integrate — the open-source leverage map

We do **not** hand-build commodity modules. Proven open-source engines exist for scheduling, e-sign, CRM, newsletters and automation; we integrate them (self-hosted or via their APIs) and concentrate our own code on the AI workflows that differentiate us.

| Need | Open-source engine | License |
|---|---|---|
| Scheduling | [Cal.com](https://github.com/calcom/cal.com) | AGPLv3 (+commercial) |
| E-signatures | [Documenso](https://github.com/documenso/documenso) | AGPLv3 |
| CRM/pipeline | **Built natively** — Twenty + EspoCRM as design brief, see `docs/09` | Ours (proprietary) |
| Email/newsletters | [Listmonk](https://github.com/knadh/listmonk) | AGPLv3 |
| Automation engine | [Activepieces](https://github.com/activepieces/activepieces) — replaced n8n after direct license verification, see `docs/09` §5 | MIT |
| Backend/auth/DB | [Supabase](https://github.com/supabase/supabase) | Apache 2.0 |

Every engine choice was re-verified against its rivals feature-by-feature and license-by-license (fetched from each repo directly, not assumed) — full comparison and the two resulting changes in [`docs/09-engine-comparison.md`](docs/09-engine-comparison.md). All engines run headless behind our own screens: coaches and clients only ever see CoachOS.

**Why we integrate rather than "merge" these repos into ours:** most are AGPL-licensed — running them as separate self-hosted services with API calls between them is clean; copying their code into a proprietary codebase would legally force us to open-source the whole product. The integration architecture, license obligations, and per-phase adoption plan are in [`docs/03-open-source-stack.md`](docs/03-open-source-stack.md).

## 6. The AI engine — two-model strategy

| Path | Use | Cost at MVP scale |
|---|---|---|
| **Primary: Claude API (Haiku 4.5)** for summaries, prep briefs, follow-up drafts | Launch → scale | ~$1–10/month (verified unit math in [`docs/04`](docs/04-roadmap-costs-gtm.md)) |
| **Secondary: open-weight models (DeepSeek/Qwen) via hosted API** | Cost hedge + price-sensitive features | Pennies/month; adds China data-transfer considerations for EU clients |
| Self-hosting open models | **Deferred** — break-even is 15–40M tokens/month; we project <7M even at 300 coaches | Revisit only for an enterprise data-residency deal |

## 7. Numbers we trust vs. numbers we don't

Every market figure in this plan survived an adversarial fact-check against primary sources (ICF/PwC study, EU AI Act text). Figures we **rejected** — including "$62B AI coaching market by 2026" — are documented with reasons in [`docs/01-market-research.md`](docs/01-market-research.md). Team rule: **we plan on the 19%→54% adoption gap, not on inflated TAM slides.**

## 8. Repository contents

```
coachos/
├── README.md                      ← this file (master plan)
├── LICENSE                        ← proprietary — all rights reserved
├── docs/
│   ├── 01-market-research.md      ← verified market data, competitors, regulation
│   ├── 02-product-spec.md         ← module-by-module spec, niche packs, AI vs human split
│   ├── 03-open-source-stack.md    ← build-vs-integrate map, licenses, architecture
│   ├── 04-roadmap-costs-gtm.md    ← phases, costs, pricing, go-to-market, risks
│   └── 05-build-guide.md          ← how to build it with Claude Code + Cursor (frontend, backend, functionality-by-functionality)
│   ├── 06-features-guide.md       ← plain-language "what coaches can actually do" guide, no jargon
│   ├── 07-architecture.md        ← the single project architecture diagram — every layer, module-mapped, phase-gated
│   ├── 08-module-questionnaire.md ← 130+ product decisions, organized journey-by-journey (interconnection-first) with defaults — answered → per-module build prompts
│   ├── 09-engine-comparison.md   ← re-verified open-source research: rivals compared per category, final picks, license proofs
│   ├── 10-ai-agent-layer.md      ← the Pipeline Agent: agent framework research, license proofs, and a working demo
│   └── 11-api-and-mcp-access.md  ← per-coach API + MCP, and an Ops MCP for admins (bug fixes stay in normal engineering, not ungated code edits)
├── features.html                  ← the same guide, as a page — open in any browser
└── prototype/
    └── index.html                 ← animated clickable prototype (open in any browser)
```

**To view the prototype:** open `prototype/index.html` in a browser — no install needed.

## 8b. Confidentiality — the binding rules for the entire build (STANDING ORDER)

This is a competitive market and this plan is a competitive asset. **From now until a deliberate, owner-approved public launch, everything about this project is private — the plan, the source code, the infrastructure, all of it.** These rules bind every session, every collaborator, and every future repository:

**Repositories**
1. **This planning repo is private.** GitHub: Settings → General → Danger Zone → *Change visibility* → Private. Verify the "Private" badge shows.
2. **Every future repository is created private from birth** — frontend, backend, the MCP server, infrastructure configs, everything. No repo for this project is ever created public, even for a moment "to test something."
3. **Access by invitation only.** Named individuals, least role needed (Read for reviewers, Write for contributors). No org-wide access, no public links, no link-sharing.
4. **Proprietary license in every repo** (see `LICENSE`) so every collaborator sees the terms.

**Deployments & infrastructure**
5. **No public URLs during the build.** Staging and test deployments sit behind authentication (password/Vercel protection/IP allowlist) and carry `noindex` — nothing crawlable, nothing guessable, nothing demoable without a login we issued.
6. **No secrets in any repo, ever** — private or not. API keys, tokens, credentials live in git-ignored `.env` files and the host's secret manager only.
7. **Third-party services get minimum footprint**: accounts created for the project reveal nothing in public profiles, app names, or OAuth consent screens beyond what a service strictly requires.

**People & sharing**
8. **Shared artifacts (documents, prototypes, links) go to named people only** — never public share links.
9. **No public writing about what we're building** — no posts, screenshots, demos, or "sneak peeks" by anyone on the team before launch.
10. **The go-public decision is a single deliberate event** owned by Amir — nothing becomes public gradually or by accident. If open-sourcing any part is ever chosen post-launch, that's a separate, explicit licensing decision made at that time (nothing in the build blocks it; nothing in the build presumes it).

## 9. What this plan is not

- Not a commitment to build all seven modules — Phases 2+ ship only if Phase 1 earns paying users.
- Not financial advice or a guarantee of outcomes; scenario analysis (bear/base/bull to 2036) is in the companion dossier.
- Not final branding — "CoachOS" is a working codename pending a trademark search.
