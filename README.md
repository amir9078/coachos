# CoachOS — The Operating System for Coaches & Mentors

> **Working codename.** One platform where a coach or mentor runs their entire business — leads, marketing, bookings, contracts, client delivery, and payments — with AI doing the repetitive work and humans doing the human work.

**Status:** Plan + clickable prototype. Not yet in build.
**Owner:** Amir Hussain
**Last updated:** July 2026

---

## 1. The one-paragraph pitch

122,974 professional coaches worldwide generate $5.34B a year, yet only 19% invested in any new technology last year while 54% say better platforms are a priority — the largest documented demand-adoption gap in the industry ([ICF/PwC 2025 Global Coaching Study](https://coachingfederation.org/blog/global-coaching-industry-surpasses-5-34-billion-as-emerging-markets-drive-growth/)). Today a coach stitches together 5–8 disconnected tools (CRM, scheduler, e-sign, email, invoicing, notes). CoachOS replaces the stitching: **one desk, every room of the business**, with AI built into each workflow rather than bolted on — at solo-coach pricing ($29–59/mo), a tier where no incumbent has built AI-first.

## 2. What it does — the seven modules

| # | Module | What the coach gets | AI or Human? |
|---|--------|--------------------|--------------|
| 1 | **Leads — Attract** | Landing page + lead magnet builder, lead capture forms, AI-scored inbound enquiries | Platform (AI) |
| 2 | **Leads — Manage** | Pipeline board (Enquiry → Discovery call → Proposal → Won), AI follow-up nudges and drafted replies | Platform (AI) |
| 3 | **Marketing** | AI-drafted posts, newsletters and email sequences in the coach's own voice; content calendar | Platform (AI) + optional human service |
| 4 | **Bookings** | Scheduling links, calendar sync, automated reminders, no-show recovery | Platform |
| 5 | **Contracts & Payments** | Coaching-agreement templates, e-signature, invoicing, package/subscription billing | Platform |
| 6 | **Client Delivery** | Session notes → AI summary + action items, client progress timeline, AI session-prep briefs | Platform (AI) — *the wedge, see §4* |
| 7 | **Practice Intelligence** | Dashboard: revenue, pipeline health, client retention, at-risk clients flagged by AI | Platform (AI) |

**The human-service layer** (sold by us, alongside the software — this is deliberate, not a gap):
- **Onboarding & implementation** — we migrate the coach's clients, templates and calendar in a done-with-you session (paid setup fee; also our retention weapon).
- **Done-for-you marketing** — monthly content package produced by our team using the platform's AI + human editing (recurring service revenue at 3–5× software price).
- **Community & referrals** — curated coach community; coaches refer coaches. This profession sells by word of mouth.

Full module-by-module specification: [`docs/02-product-spec.md`](docs/02-product-spec.md)

## 3. Why this wins (and what we deliberately don't do)

- **We are the coach's infrastructure, not the coach's replacement.** AI-only coaching products (Valence/$50M raise, CoachHub AIMY, Rocky.ai) compete with the coach. We make the human coach more profitable — so the 122,974 coaches are our allies, not our disruptees. This also keeps us in the EU AI Act's *limited-risk* tier instead of the high-risk regime. Details: [`docs/01-market-research.md`](docs/01-market-research.md).
- **The moat is workflow + data lock-in, not the AI.** Any AI feature copies in weeks (see Jasper's collapse from ~$120M to $35–55M revenue). Years of client history, signed contracts, and pipeline living inside CoachOS does not copy. We never compete on "our model is smarter."
- **We don't build everything at once.** See the phased roadmap below — the graveyard of all-in-one platforms is founders who built seven modules before earning one paying user.

## 4. Build order — the wedge strategy

**Phase 1 (Months 1–3) — the wedge: Client Delivery.**
Session notes → AI summary, prep briefs, progress timeline, scheduling. This is the single most-requested AI capability in the coaching-tools research, buildable by a solo founder with Claude Code, and it creates daily usage + data lock-in from day one. *Ship, pilot with 5 coaches, convert to paid.*

**Phase 2 (Months 4–8) — the money loop: Bookings + Contracts + Payments.**
Now the coach's revenue flows through us. Integrate, don't build: scheduling and e-sign have mature open-source engines (see §5).

**Phase 3 (Months 9–14) — the growth loop: Leads + Marketing.**
Pipeline CRM, AI follow-ups, content composer, landing pages. Launch the done-for-you marketing service here — service revenue funds the build.

**Phase 4 (Year 2) — intelligence & scale:** practice dashboard, at-risk-client AI, referral network, and (only now, from leverage) evaluate corporate/L&D partnerships.

Costs, revenue model and milestones: [`docs/04-roadmap-costs-gtm.md`](docs/04-roadmap-costs-gtm.md)

## 5. Build vs. integrate — the open-source leverage map

We do **not** hand-build commodity modules. Proven open-source engines exist for scheduling, e-sign, CRM, newsletters and automation; we integrate them (self-hosted or via their APIs) and concentrate our own code on the AI workflows that differentiate us.

| Need | Open-source engine | License |
|---|---|---|
| Scheduling | [Cal.com](https://github.com/calcom/cal.com) | AGPLv3 (+commercial) |
| E-signatures | [Documenso](https://github.com/documenso/documenso) | AGPLv3 |
| CRM core | [Twenty](https://github.com/twentyhq/twenty) | AGPLv3 |
| Email/newsletters | [Listmonk](https://github.com/knadh/listmonk) | AGPLv3 |
| Automation glue | [n8n](https://github.com/n8n-io/n8n) | Fair-code |
| Backend/auth/DB | [Supabase](https://github.com/supabase/supabase) | Apache 2.0 |

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
└── prototype/
    └── index.html                 ← animated clickable prototype (open in any browser)
```

**To view the prototype:** open `prototype/index.html` in a browser — no install needed.

## 8b. Confidentiality & repo-privacy plan

This plan is a competitive asset. Standing rules:

1. **Repository stays private.** On GitHub: Settings → General → Danger Zone → *Change visibility* → Private (if it was created public). Verify the repo page shows the "Private" badge.
2. **Access by invitation only.** Settings → Collaborators → add team members individually with the least role they need (Read for reviewers, Write for contributors). No org-wide or public links.
3. **Proprietary license in-repo** (see `LICENSE`) so every collaborator sees the terms — a private repo controls access; the license states the rights.
4. **No secrets in the repo, ever.** API keys, tokens and credentials live in `.env` files (git-ignored) and the host's secret manager — never committed, even to a private repo.
5. **Shared artifacts stay private** until deliberately shared from their share menus; share with named people, not public links.
6. **Before any public launch page**, split marketing content into a separate public repo — this planning repo never goes public.

## 9. What this plan is not

- Not a commitment to build all seven modules — Phases 2+ ship only if Phase 1 earns paying users.
- Not financial advice or a guarantee of outcomes; scenario analysis (bear/base/bull to 2036) is in the companion dossier.
- Not final branding — "CoachOS" is a working codename pending a trademark search.
