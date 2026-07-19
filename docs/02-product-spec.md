# 02 — Product Specification

Module-by-module: what it does, what the AI does, what stays human, and how each piece gets built. Phases refer to README §4.

---

## Module 1 & 2 — Leads (Attract + Manage) · *Phase 3*

**Job:** a coach never loses a lead to a forgotten follow-up again.

| Capability | How it works | AI / Human |
|---|---|---|
| Landing page + lead magnet | Template-based page builder; coach picks template, AI drafts copy in their voice from a 10-minute voice-note interview | AI drafts, coach approves |
| Lead capture & enrichment | Form → CRM record; AI enriches from public info (role, company) | AI |
| Lead scoring | AI reads enquiry text, scores fit + urgency, sorts the pipeline | AI |
| Pipeline board | Enquiry → Discovery → Proposal → Won/Lost kanban | Platform UI |
| Follow-up nudges | "Sarah has gone quiet 6 days after her discovery call" + a drafted, personalized follow-up ready to approve-and-send | AI drafts, **human sends** |

**Design rule:** AI never sends outbound to a lead autonomously. The coach approves every message — this is both a trust feature and our EU-compliance posture.

## Module 3 — Marketing · *Phase 3*

| Capability | How | AI / Human |
|---|---|---|
| Voice profile | Onboarding interview + 3 writing samples → stored style profile | AI-built, once |
| Content composer | Weekly drafts: 3 posts + 1 newsletter in the coach's voice, from their actual session themes (anonymized) | AI drafts, coach edits |
| Email sequences | Welcome/nurture sequences from templates, personalized by niche | AI + templates |
| Content calendar | Schedule + publish reminders | Platform |
| **Done-for-you service** | Our human editors polish and manage the coach's content monthly | **Human (our service revenue)** |

## Module 4 — Bookings · *Phase 2*

Scheduling links, two-way calendar sync, buffer rules, reminder sequences (email/SMS), no-show recovery ("rebook" one-click). **Build:** integrate Cal.com (see doc 03) — we do not hand-build a scheduler.

## Module 5 — Contracts & Payments · *Phase 2*

| Capability | How | Notes |
|---|---|---|
| Agreement templates | Coaching-specific templates (packages, cancellation, confidentiality) | Lawyer-reviewed once, per-market variants |
| E-signature | Integrate Documenso | Legally binding (eIDAS/ESIGN) |
| Invoicing & billing | Stripe: one-off packages + recurring subscriptions | Platform |
| AI assist | Plain-language explanation of any clause on hover ("this means…") — explicitly labeled "not legal advice" | AI |

## Module 6 — Client Delivery · *Phase 1 — THE WEDGE*

The daily-use core, and the first thing we build.

| Capability | How it works |
|---|---|
| Session notes → summary | Coach dictates or pastes rough notes → AI returns (a) client-facing summary email, (b) coach-facing action list. Coach approves before anything is shared. |
| Progress timeline | Every session logged against the client's stated goals; visual arc of the engagement |
| AI prep brief | 30 min before a session: "Last time Maya committed to X; she flagged Y as a blocker; her goal-3 progress has stalled 3 weeks." |
| Client portal | Client sees shared summaries, actions, bookings — one link, no login friction |

**Why first:** highest-frequency use (every session), clearest AI value, generates the data that powers Modules 2, 3 and 7 later, and creates switching costs from week one.

## Module 7 — Practice Intelligence · *Phase 4*

Revenue, pipeline conversion, retention curves; AI flags at-risk clients (missed sessions, stalled goals, sentiment drop in notes) and suggests an intervention. **Guardrail:** insights go to the coach only — never scoring of employees for corporate buyers (EU AI Act line, see doc 01 §5).

---

## The human-service layer (our second revenue line)

| Service | Price posture | Who delivers |
|---|---|---|
| Onboarding & migration | One-time setup fee | Us (founder-led at first) |
| Done-for-you marketing | Monthly retainer, 3–5× software price | Our editors using platform AI |
| Community & referral network | Included in top tier | Us |

Services fund the build between phases, deepen retention, and give us weekly voice-of-customer contact no competitor's product team has.

## Cross-cutting requirements

- **Trust & privacy:** encryption at rest; per-coach data isolation; DPA template; no training on client data; AI disclosure labels on every AI output (EU AI Act limited-risk compliance).
- **Approval-first AI:** every outbound artifact (email, post, summary) requires explicit coach approval. No silent automation.
- **Security gate:** independent security review before any real client data; repeat before Phase 2 (payments).
- **Accessibility & mobile:** coaches run practices from phones between sessions; mobile-responsive from day one.
