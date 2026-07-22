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
| **Pipeline Agent** | On a stalled lead: reads its full history, checks how similar past leads actually behaved, reasons through what to propose (not a fixed template), drafts the move, then **pauses** — coach sees the reasoning and approves, edits, or dismisses. Built on Mastra (Apache 2.0), running inside our own app. Full research + working demo: [`docs/10-ai-agent-layer.md`](10-ai-agent-layer.md) | AI reasons + drafts, **human decides** |

**Design rule:** AI never sends outbound to a lead autonomously — including the agent. The coach approves every message — this is both a trust feature and our EU-compliance posture.

## Module 3 — Marketing · *Phase 3*

| Capability | How | AI / Human |
|---|---|---|
| Voice profile | Onboarding interview + 3 writing samples → stored style profile | AI-built, once |
| Content composer | Weekly drafts: 3 posts + 1 newsletter in the coach's voice, from their actual session themes (anonymized) | AI drafts, coach edits |
| Newsletter &amp; email sequences | Welcome/nurture sequences from templates, personalized by niche — sent and tracked via [Listmonk](https://listmonk.app/) (self-hosted, doc 03), not hand-built | AI drafts + Listmonk sends |
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

## Cross-cutting — Gmail (and email account) integration · *Phase 2*

**The gap this closes:** by default, coach emails go out via Resend, a transactional sender — meaning a client's reply doesn't land in the coach's real inbox and there's no visible thread history. Coaches will expect email to look and behave like *their* email.

| Capability | How it works |
|---|---|
| Connect account | Coach connects their Gmail (Google OAuth) from Settings; Outlook/Microsoft 365 is a v2 candidate, same pattern |
| Sending | Approved summaries, follow-ups, and nurture emails send **from the coach's own Gmail address** (`gmail.send` scope) — replies land in their real inbox, not a CoachOS address |
| Reply visibility (v2) | Reading replies back into the client timeline needs a broader scope (`gmail.readonly`/`modify`) — treat as a later, opt-in upgrade, not Phase 2 |
| Fallback | Resend stays the **default, zero-setup path**; Gmail connect is an optional upgrade so Phase 1–2 never blocks on it |

**Real scheduling risk, not just a technical build item:** Google requires an app-verification review for the `gmail.send` scope once past a low usage threshold — a privacy policy, a demo video, and (for broader scopes) a paid third-party security assessment (CASA), which can take **several weeks** and isn't fully in our control. Start this application in parallel with Phase 2 development, not after it — see doc 04 for the cost/timeline line item.

**Security:** OAuth tokens are encrypted at rest, scoped per-coach (RLS), and revocable from Settings in one click.

## Module 8 — Automations · *Phase 3*

**Job:** stop the coach re-typing the same follow-up for the fortieth time — without breaking the platform's core trust rule (nothing sends without a human having approved the actual words).

| Capability | How it works |
|---|---|
| Automation recipes | A short list of pre-built triggers → actions the coach turns on and lightly customizes (delay, one variable) — **not** a general-purpose workflow builder |
| Example recipes | "Lead silent 6 days → send this exact follow-up template" · "Session marked complete → schedule the next prep brief" · "Invoice 3 days overdue → send this reminder" |
| Engine | Built on **n8n** (already in the stack as our internal glue, doc 03) — we curate the recipes; the coach never sees n8n's own UI |
| AI's role | Suggests a recipe from the coach's own repeated behavior ("You've sent this same message 4 times — automate it?") — AI proposes, never auto-generates the send-time content |

**The rule that keeps this safe:** an automation only ever sends a **template the coach has already approved once**, not freshly AI-generated text created at trigger time. This preserves the platform's approval-first principle (doc 02 cross-cutting requirements, doc 01 §5) — the coach saw and approved the words; the automation just handles *when*, not *what*.

## Module 9 — Coach & Mentor Directory (Marketplace) · *Phase 3, gated on coach supply*

**Job:** turn CoachOS from a tool coaches buy into a channel that also finds them clients — the differentiator no incumbent in the $20–60/mo tier offers (see doc 01 §6).

| Capability | How it works |
|---|---|
| Public coach profile | Opt-in, publishable page: photo, bio, niche pack/specialty, credentials, price range, languages, format (video/in-person) |
| Testimonials | Pulled from the coach's own approved Client Delivery summaries (Module 6), with **explicit per-testimonial client consent** — reuses data we already have instead of a separate review system |
| Search & discovery | Filter by niche, price band, language, format; built on Supabase's own full-text search for v1 — no new search engine until relevance genuinely demands one (see doc 03) |
| Enquiry routing | A visitor's enquiry drops straight into that coach's own Leads pipeline (Module 1–2) — the directory becomes a top-of-funnel input into a tool the coach already uses daily |
| Monetization | **Listing model, not commission** (doc 01 §6): included at the Practice+ tier, or a small standalone listing fee — never a cut of the coach's session fees |

**Cold-start discipline:** an empty directory has negative value — it signals nobody's here. **Gate the public launch on real coach supply** (roughly the existing Phase 3 gate of ~100 paying coaches), not on the feature being code-complete. Build it, but don't open it to public search traffic until there's enough real supply to not look empty.

**New AI use case to flag:** if a future "AI recommends your best-matched coach" feature is added, that's a recommender/matching system — minimal-risk under the EU AI Act (not an Annex III employment-evaluation use), but it's a new AI surface not covered by the original doc 01 §5 assessment and should get its own quick review before shipping.

## Module 10 — Daily Briefing · *Phase 3, public-facing too*

**Job:** the coach opens CoachOS and is already caught up — on their field, on the world, and on one new thing worth knowing — before they've had coffee.

| Capability | How it works |
|---|---|
| News by category | Curated headlines filtered to the coach's niche pack (Executive → leadership/business; Career → job market/workplace trends; Life &amp; Wellness → psychology/wellness research; Mentoring → L&amp;D/talent trends), each with an AI one-line takeaway and a link to the original source |
| World catchup | A short, neutral summary of the day's top general headlines — 2–3 minutes, not niche-specific |
| 5-minute daily learn | One bite-sized professional-development piece a day — a coaching technique, a research finding, a concept — **summarized from a real, cited source, never invented from nothing** |

**Where it lives:** a card on the dashboard for logged-in coaches, **and** a public page on the marketing site (`/briefing`) that anyone can read without an account — doubling as a content/SEO channel that feeds the same organic-acquisition thesis as the Directory (Module 9).

**The cost discipline that makes this cheap:** the briefing is generated **once per category, per day** — not once per coach. A few hundred coaches split across 4–5 niche categories means a handful of AI summarization calls a day for the whole platform, not one per coach. This is a shared, cached daily artifact, the same way a newspaper prints one edition for everyone in a region rather than one per subscriber.

**Sourcing rule, not just a nice-to-have:** every headline and every "5-minute learn" links back to its real source. AI's job here is to summarize and select, never to generate a fact from scratch — the same discipline the plan already applies to its own market research (doc 01 §6's "rejected claims" table exists because of exactly this kind of mistake).

## Module 11 — Coach Community · *Phase 4, gated on real supply*

**Job:** coaches and mentors connect with each other, share what they're learning, and build the reputation that makes "coaches refer coaches" (README §2, the human-service layer) an actual product feature instead of something we do by hand.

**Scope assumption, stated explicitly:** this is a **peer network — coach to coach, mentor to mentor** — not a client-facing feature. It's the built version of the "Community & referrals" line already in the plan. If the intent was clients connecting with each other too, that's a different (and much bigger, trust-and-safety-heavier) build — flag it and this spec changes.

| Capability | v1 or v2 | How it works |
|---|---|---|
| Connect | v1 | LinkedIn-style: send a connection request, the other coach accepts — mutual, not a one-way follow |
| Post | v1 | Text + optional image, visible to your connections (or public, coach's choice per post) |
| Like | v1 | One tap, on any post or comment |
| Comment | v1 | Threaded under a post |
| Share | v1 | Reposts a connection's post to your own network with credit to the original author |
| Message | v2 | Direct, async 1:1 messages — like email, no expectation of an instant reply |
| Chat | v2 | Real-time: typing indicators, online presence, instant delivery — genuinely different infrastructure from Message, see doc 03 |

**Why v1/v2, not one build:** a social feed (posts/likes/comments/shares) is standard database work — read, write, display. Real-time chat is a different engineering problem entirely — presence, live delivery, connection state — and deserves its own scoped build rather than being smuggled in as "basically the same as messaging." Build the feed first, prove coaches actually use it, then build chat.

**The line this module doesn't cross:** no AI posts on a coach's behalf, ever — same approval-first principle as every other module. An AI-assisted post-drafting option (reusing the Marketing composer's voice profile, doc 02 Module 3) is a reasonable v2 add-on, not a v1 requirement.

**Trust & safety — not optional for user-generated content:** report and block tools, a content policy coaches agree to, and spam/abuse rate-limiting are v1 requirements, not later polish. Any platform that lets users post, comment, and message each other carries real moderation and liability exposure (relevant to GDPR and, for EU users, the Digital Services Act) — this needs the same kind of deliberate design as the AI-disclosure and data-processor obligations already in doc 01 §5, not an afterthought.

**Relationship to the Directory (Module 9):** the Directory is client-facing discovery — a prospect finding a coach. The Community is peer-facing — coaches finding each other. A coach's public Directory profile can surface their Community activity (posts, connections) as social proof, but the two audiences and the two trust models are different; don't conflate them in the build.

---

## The human-service layer (our second revenue line)

| Service | Price posture | Who delivers |
|---|---|---|
| Onboarding & migration | One-time setup fee | Us (founder-led at first) |
| Done-for-you marketing | Monthly retainer, 3–5× software price | Our editors using platform AI |
| Community & referral network | Included in top tier | Us |

Services fund the build between phases, deepen retention, and give us weekly voice-of-customer contact no competitor's product team has.

## Niche packs — how CoachOS gets industry-specific

One platform, specialty-tuned. Each niche pack bundles the assets a coach in that specialty actually needs, so day one feels built-for-them rather than generic:

| Pack | Contract templates | Email/nurture sequences | Lead magnet templates | AI voice/prompt presets | Onboarding questionnaire |
|---|---|---|---|---|---|
| **Executive coaching** | 12-session, monthly retainer, team offsite | C-suite nurture, board-season check-in | "First 90 days" scorecard | Board-savvy, concise | 360-context intake |
| **Career coaching** | Per-package, outplacement | Job-search sprint, salary-negotiation | CV/LinkedIn teardown offer | Practical, encouraging | Career-history intake |
| **Life & wellness** | Session bundles, cancellation-sensitive | Habit-loop nurture | Wellness self-assessment | Warm, non-clinical | Wheel-of-life intake |
| **Mentoring programs** (org-run) | Mentor–mentee agreements, program MOU | Cohort onboarding, milestone nudges | Program one-pager | Program-coordinator tone | Matching questionnaire |

Rules: packs are content + presets, not separate codebases — one platform, swappable packs. Launch order: **Executive first** (highest willingness-to-pay, founder's network), then career, then life/wellness, then mentoring programs. The mentoring-program pack is also our future door into B2B (universities, corporates running mentor schemes) — *without* crossing the employee-evaluation line in doc 01 §5.

## Cross-cutting requirements

- **Trust & privacy:** encryption at rest; per-coach data isolation; DPA template; no training on client data; AI disclosure labels on every AI output (EU AI Act limited-risk compliance).
- **Approval-first AI:** every outbound artifact (email, post, summary) requires explicit coach approval. No silent automation.
- **Security gate:** independent security review before any real client data; repeat before Phase 2 (payments).
- **Accessibility & mobile:** coaches run practices from phones between sessions; mobile-responsive from day one.
