# 04 — Roadmap, Costs, Pricing & Go-to-Market

## 1. Phased roadmap with gates

Each phase has an explicit **gate** — the next phase does not start until the gate is passed. This is the discipline that separates this plan from the all-in-one graveyard.

| Phase | Months | Ships | Gate to proceed |
|---|---|---|---|
| **1 — Wedge** | 1–3 | Client Delivery (notes→summary, prep briefs, timeline), basic scheduling embed, client portal | 5 pilot coaches using it weekly; ≥3 convert to paid |
| **2 — Money loop** | 4–8 | Bookings (Cal.com), Contracts (Documenso), Stripe billing; security re-review | ≥25 paying coaches; churn <5%/mo; coaches running real revenue through us |
| **3 — Growth loop** | 9–14 | Leads CRM (Twenty), AI follow-ups, marketing composer, landing pages; done-for-you service launch | ≥100 paying coaches; service line profitable |
| **4 — Intelligence** | 15+ | Practice dashboard, at-risk AI, referral network | Decided from data, not ambition |

## 2. Costs (planning figures, not quotes)

### Build phase (Months 1–3)
| Item | Cost |
|---|---|
| Claude Code subscription (build tool) | existing plan |
| Hosting + DB (Supabase/Vercel free→starter tiers) | $0–40/mo |
| Domain, email | ~$25/yr + $6/mo |
| **Independent security review (non-negotiable)** | $300–800 one-time |
| Legal: DPA template + contract templates review | $500–1,500 one-time |
| **Total cash to first paying customer** | **≈ $1,000–2,500** |

### Runtime AI cost (verified unit math)
- Per coach per month: ~8 AI calls × (2,000 in + 300 out tokens) on Claude Haiku 4.5 ($1/$5 per MTok) ≈ **$0.03/coach/month**
- 30 coaches ≈ $0.84/mo · 300 coaches ≈ $8.40/mo
- Open-weight hosted APIs (DeepSeek ~$0.14/$0.28; Qwen-Turbo ~$0.05 per MTok) as cost hedge — pennies at our scale
- Self-hosting open models: **rejected for now** — break-even at 15–40M tokens/mo; we project <7M at 300 coaches. Revisit only for enterprise data-residency deals.
- **Conclusion: AI cost is a rounding error against revenue. Never let it drive architecture decisions.**

## 3. Pricing

| Tier | Price | Contains |
|---|---|---|
| Solo | $29/mo | Client Delivery + Bookings |
| Practice | $59/mo | + Contracts, Payments, Leads, Marketing composer |
| Practice+ (services) | $199–399/mo | + done-for-you marketing, priority onboarding, community |
| Setup (one-time) | $149–299 | Migration done-with-you |

Rationale: $29–59 matches the incumbent tier coaches already accept (Delenta $29, Paperbell $57) — we win on being AI-first, not on being cheapest. The services tier is where margin lives early: 10 service customers ≈ 100 software customers in revenue.

**Unit economics at the Practice tier:** $59 revenue vs. ~$0.03 AI + ~$1–2 infra per coach → gross margin >95%. The scarce resource is the founder's selling time, not compute.

## 4. Go-to-market (sales-led, zero ad spend to start)

1. **Months 1–3:** recruit 5 pilots personally from ICF chapters / LinkedIn coach communities / mentoring networks. Free during pilot, weekly feedback calls.
2. **Months 3–6:** convert pilots; ask each for 2 introductions (coaches refer coaches — this profession runs on referral); publish 2 case studies ("Sana saves 4 hrs/week on admin").
3. **Months 6–12:** ICF chapter workshops ("AI for your practice" — teach first, sell second); partnership with 1–2 coach-certification programs (their graduates need a practice stack on day one).
4. **Ongoing:** the done-for-you service doubles as a GTM channel — every service client is a retained software customer and a referral source.

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
