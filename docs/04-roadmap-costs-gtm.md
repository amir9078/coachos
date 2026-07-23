# 04 — Roadmap, Costs, Pricing & Go-to-Market

## 1. Phased roadmap with gates

Each phase has an explicit **gate** — the next phase does not start until the gate is passed. This is the discipline that separates this plan from the all-in-one graveyard.

| Phase | Months | Ships | Gate to proceed |
|---|---|---|---|
| **1 — Wedge** | 1–3 | Client Delivery (notes→summary, prep briefs, timeline), basic scheduling embed, client portal | 5 pilot coaches using it weekly; ≥3 convert to paid |
| **2 — Money loop** | 4–8 | Bookings (Cal.com), Contracts (Documenso), Stripe billing; **Gmail integration** (start Google's verification review in parallel, month 4 — see risk below); security re-review | ≥25 paying coaches; churn <5%/mo; coaches running real revenue through us |
| **3 — Growth loop** | 9–14 | Leads CRM (Twenty), AI follow-ups, marketing composer, landing pages, **Automations v1** (Module 8); done-for-you service launch; **Coach & Mentor Directory** built but held unlaunched; **Daily Briefing** (Module 10) — in-app and public `/briefing` page; **Admin MCP server** (F20) | ≥100 paying coaches; service line profitable; Directory opens to public search only once coach supply clears the same ≥100 gate |
| **4 — Intelligence + Community v1** | 15–20 | Practice dashboard, at-risk AI; **Coach Community v1** (Module 11) — connect, post, like, comment, share | Decided from data, not ambition; Community v1 gated on a real base of connected, active coaches — an empty feed is the same cold-start problem as an empty Directory |
| **5 — Community v2 + scale** | 21+ | **Message + real-time Chat** (Module 11 v2, Supabase Realtime); evaluate corporate/L&D partnerships from leverage | Community v1 shows real engagement (posts, connections, repeat visits) before chat infrastructure is built |

## 2. Costs (planning figures, not quotes)

### Build phase (Months 1–3)
| Item | Cost |
|---|---|
| Claude Code subscription (build tool) | existing plan |
| App hosting (**Railway from day one**, not Vercel — decided so Cal.com can self-host immediately too, doc 07 §6) | ~$30–70/mo (usage-metered; a single small service already runs ~$30/mo in compute alone, per Railway's own pricing) |
| Database (Supabase, free→Pro tier) | $0–25/mo |
| Domain, email | ~$15/yr (Tasjeel) + $1–8/mo (Zoho Mail or Google Workspace) |
| **Independent security review (non-negotiable)** | $300–800 one-time |
| Legal: DPA template + contract templates review | $500–1,500 one-time |
| **Total cash to first paying customer** | **≈ $1,100–2,700** |

### Runtime AI cost (verified unit math)
- Per coach per month: ~8 AI calls × (2,000 in + 300 out tokens) on Claude Haiku 4.5 ($1/$5 per MTok) ≈ **$0.03/coach/month**
- 30 coaches ≈ $0.84/mo · 300 coaches ≈ $8.40/mo
- Open-weight hosted APIs (DeepSeek ~$0.14/$0.28; Qwen-Turbo ~$0.05 per MTok) as cost hedge — pennies at our scale
- Self-hosting open models: **rejected for now** — break-even at 15–40M tokens/mo; we project <7M at 300 coaches. Revisit only for enterprise data-residency deals.
- **Conclusion: AI cost is a rounding error against revenue. Never let it drive architecture decisions.**

### New line items (Gmail, Automations, Directory)
| Item | Cost | Note |
|---|---|---|
| Gmail API usage | $0 | Free within Google's standard quotas at this scale |
| Google app-verification review | $0–~$5,000 (only if a CASA security assessment is required) | **Timeline risk, not just cost:** can take several weeks; start the application in Phase 2, month 4, not after the module is built |
| LinkedIn Marketing API partner approval (Module 1, LinkedIn lead capture) | $0 | **Timeline risk, verified directly:** LinkedIn's own docs say "weeks to months," and they're selective about approving new marketing-tech partners. Apply as early as Phase 3 planning starts, in parallel with everything else — WhatsApp and Facebook/Instagram lead capture (both achievable in days, see doc 03) ship regardless of LinkedIn's decision timeline. |
| n8n (Automations engine) | $0 | Already self-hosted for internal glue; new templates only |
| Directory search (Supabase full-text) | $0 | No new infra for v1; Meilisearch only if relevance demands it later |

**Business-model note:** the Directory is priced as a listing perk inside existing subscription tiers, **not** a commission on coaching fees (doc 01 §6) — it does not change the unit-economics table above.

| Daily Briefing (Module 10) | Cost | Note |
|---|---|---|
| News/world-catchup sources | $0 | Wikipedia Current Events Portal (CC BY-SA, world catchup) + direct publisher RSS feeds (category news, learn) — every source individually verified, not assumed; see doc 03 for the four "free tier" traps this avoids (NewsAPI.org, Google News RSS, Mediastack, GNews all restrict free use to non-commercial/dev only) |
| Admin MCP server (push/pull/update briefing content from Claude) | $0 new infra | Runs locally on the admin's own machine (doc 05 §4.6); image storage rides on Supabase's existing free/starter tier — cost is Claude usage the founder already pays for, not a new line item |

| Coach Community (Module 11) | Cost | Note |
|---|---|---|
| Feed v1 (posts, likes, comments, connections, shares) | ~$0 new infra | Same Postgres + Next.js pattern as everything else — no new engine (doc 03) |
| Chat v2 (Supabase Realtime) | Usage-based, check current limits before Phase 5 build | Already part of the Supabase project; re-verify concurrent-connection and message limits against the coach count at the time, not the numbers in this document |
| Image/media storage growth | Scales with usage | Budget for this explicitly once posting is live — it's the one cost in this module that isn't flat |
| **Moderation & trust-and-safety** | **Real cost, materially bigger than originally scoped** | Module 11's scope reversed from a closed coach-only peer network to a fully open, LinkedIn-style public network (docs/02, docs/08 §Journey 7). That changes this line from "report/block + a content policy" to needing automated content filtering at scale, spam/fake-account prevention, and a real legal review pass (EU Digital Services Act exposure applies more directly to public platforms than to a closed professional community). Budget and timeline for this module should be re-estimated against that reality, not the original closed-community assumption. |
| AI summarization | Pennies/month | Generated once per category per day, not per coach — a shared cached artifact, not a per-user cost |
| Public `/briefing` page | $0 new infra | Same Next.js app; doubles as an SEO/content-marketing channel alongside the Directory |

## 3. Pricing

| Tier | Price | Contains |
|---|---|---|
| Solo | $29/mo | Client Delivery + Bookings |
| Practice | $59/mo | + Contracts, Payments, Leads, Marketing composer |
| Practice+ (services) | $199–399/mo | + done-for-you marketing, priority onboarding, community, **full launch package (below)** |
| Setup (one-time) | $149–299 | Migration done-with-you, or the launch package below for coaches/mentors/freelancers starting from nothing |

### Launch Services — confirmed real, human-delivered (decided on request, July 2026)

Not a software feature — a genuine service line, staffed by our team, using AI-assisted tooling to make each build fast and fully customized to the individual (not a shared template): a real website, domain registration, and business email, set up and personally delivered rather than left to the coach/mentor/freelancer to figure out. This is what the public site's "Getting you live" category promises, and it's confirmed as real, not marketing language.

- **Positioning:** bundled into Practice+ (recurring) and/or the one-time Setup fee for brand-new practices with no existing web presence — exact allocation between the two still needs a real pricing pass once the team/tooling for delivering this is chosen.
- **Cost impact:** real, ongoing labor (even AI-assisted, a human reviews and customizes every build) plus pass-through costs — domain registration (~$12–20/yr/client) and business email hosting (~$6–7/mo/client, e.g. Google Workspace-equivalent). This is materially different from the platform's own $0.03/coach/month AI-cost line above — it's a staffed service, not a software feature, and needs its own cost model before it's priced with confidence. Flagging this explicitly rather than inventing a number: **get a real per-build time/cost estimate before finalizing what this costs at scale.**
- **What it is not:** it doesn't change the core CoachOS software's unit economics (§2 above) — those stay AI-cost-driven and cheap. This is a separate, human-delivered line sold alongside the software, the same category as the existing done-for-you marketing service already in Practice+.

Rationale: $29–59 matches the incumbent tier coaches already accept (Delenta $29, Paperbell $57) — we win on being AI-first, not on being cheapest. The services tier is where margin lives early: 10 service customers ≈ 100 software customers in revenue.

**Unit economics at the Practice tier:** $59 revenue vs. ~$0.03 AI + ~$1–2 infra per coach → gross margin >95%. The scarce resource is the founder's selling time, not compute.

## 4. Go-to-market (sales-led, zero ad spend to start)

1. **Months 1–3:** recruit 5 pilots personally from ICF chapters / LinkedIn coach communities / mentoring networks. Free during pilot, weekly feedback calls.
2. **Months 3–6:** convert pilots; ask each for 2 introductions (coaches refer coaches — this profession runs on referral); publish 2 case studies ("Sana saves 4 hrs/week on admin").
3. **Months 6–12:** ICF chapter workshops ("AI for your practice" — teach first, sell second); partnership with 1–2 coach-certification programs (their graduates need a practice stack on day one).
4. **Ongoing:** the done-for-you service doubles as a GTM channel — every service client is a retained software customer and a referral source.
5. **Months 12+:** once the Coach & Mentor Directory clears its coach-supply gate (§1 above), it becomes a genuine organic acquisition channel in its own right — SEO-driven searches ("executive coach in Dubai") bring prospects in, and every enquiry both serves a coach's pipeline *and* demonstrates the platform's value to that coach's peers.

**Positioning line:** *"You coach. CoachOS runs everything else."*

## 5. Top risks & standing mitigations

| Risk | Mitigation |
|---|---|
| Scope creep into "AI that coaches" | Product line: AI never delivers coaching to the client; assistive-only. Revisit only from Phase-4 leverage. |
| All-in-one sprawl before revenue | Phase gates above are hard gates. |
| Feature commoditization | Moat = data lock-in + community + services, never the model. |
| Security breach of client notes | Independent review before real data; re-review each phase; encryption at rest; incident plan. |
| EU AI Act drift | No employee-evaluation features; no emotion scoring; AI-disclosure labels everywhere. |
| Founder bus-factor | Everything in this repo; weekly plan reviews against gates. |

## 6. KPIs that matter (in order)

1. **Weekly active coaches** (are coaches logging sessions weekly?) — the retention truth-teller
2. Month-6 logo retention
3. Paying-coach count & MRR (software vs. services split)
4. Pilot→paid conversion rate
5. Referrals per active coach
