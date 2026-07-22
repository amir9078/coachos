# 02 — Product Specification

Module-by-module: what it does, what the AI does, what stays human, and how each piece gets built. Phases refer to README §4.

---

## Module 1 & 2 — Leads (Attract + Manage) · *Phase 3*

**Job:** a coach never loses a lead to a forgotten follow-up again.

### Module 1 — Attract (decided in docs/08 §Journey 1)

- **Landing pages:** built on our own domain to start; custom domains once naming is finalized. **Built by our own team, not a self-serve AI page builder** — this is a done-with-you/done-for-you service (matches the existing human-service layer in README §2), which also simplifies the Phase 1 build (no page-builder UI needed yet). A self-serve builder can be revisited once volume justifies it.
- **Lead magnets:** a scored self-assessment/evaluation quiz, newsletter signup, blog content, Daily Briefing content (reusing Module 10), and a short certification-style mini-course — **simple content (PDF/video/email sequence), no LMS platform, but ending in a scored test** built on the same quiz mechanism as the evaluation form. A more game-like interactive mechanic (points/levels, not just a scored quiz) is noted as a future idea — not scoped or built yet, revisit once the simpler magnets are proven.
- **Capture form:** Name, Email, Phone — **email is the unique key** (this is what M2's duplicate-merge logic keys on). Every lead gets a sequential, per-coach **Lead ID** (0001, 0002…) — an internal permanent ID for system integrity, plus a coach-editable, customizable **display number/format** so nothing breaks if a coach renames or reorders their own numbering.
- **Lead scoring — a live workflow, not a one-time read:** the AI scores a lead at capture *and keeps updating that score* from ongoing website-behavior tracking (return visits, content engagement, quiz completion) — the same tracking data also powers ad attribution below. Needs a lightweight events/sessions table and a cookie-consent banner (GDPR/UAE data protection) — a real but bounded addition to Phase 3 scope.
- **Ad tracking — Meta + Google pixels from day one** (moved up from the original "later" default, at the founder's explicit request) — installed alongside the landing pages themselves since they share the same underlying tracking infrastructure as the scoring above.
- **Social lead channels — WhatsApp, Instagram, Facebook, LinkedIn**, each behind that platform's own approval process (verified directly, not assumed):

  | Channel | Access process | Real timeline | Build priority |
  |---|---|---|---|
  | WhatsApp Business API | Meta Business verification (legal docs) | 2–5 business days (up to 14) | **Build first** — standard, achievable |
  | Facebook / Instagram Lead Ads | Meta App Review (`leads_retrieval` permission, extra scrutiny — lead data is personal) | Similar to the Gmail review already in doc 04 | **Build first**, alongside WhatsApp |
  | LinkedIn Lead Gen Forms | Formal LinkedIn Partner Program approval — company + business email verification | **Weeks to months**, LinkedIn is selective | **Apply early, don't block the roadmap on it** — tracked as a timeline risk in doc 04 |

  Lead enrichment (auto-filling role/company from public info) stays as originally specced, applying to leads from every channel.
- **Testimonials:** rather than a separate ask, folded into the post-call flow — after a call, an AI drafts **a call summary + a feedback/testimonial request together**; the coach reviews and sends both as one approval. A positive response becomes a testimonial candidate for the landing page. (This is the same approval-queue pattern as Module 6's session summaries — see Journey 3 in doc 08.)
- **Pages per coach/offer:** deferred, decide later.
- **Success number:** deliberately no fixed target — varies too much by coach niche/profile to guess; track empirically once pilot data exists.

### Module 2 — Manage (Pipeline) · decided in docs/08 §Journey 2

| Capability | How it works | AI / Human |
|---|---|---|
| Lead capture & enrichment | Form → CRM record (with the Lead ID from Module 1); AI enriches from public info (role, company) | AI |
| Lead scoring | Live, tracking-based score (Module 1), sorts the pipeline, **coach always sees why** | AI |
| Pipeline board | Enquiry → Discovery → Proposal → Won/Lost kanban — **fully customizable stages**: add, remove, reorder, rename, from day one | Platform UI |
| Follow-up nudges | "Sarah has gone quiet 6 days after her discovery call" + a drafted, personalized follow-up ready to approve-and-send | AI drafts, **human sends** |
| **Pipeline Agent** | On a stalled lead: reads its full history, checks how similar past leads actually behaved, reasons through what to propose, drafts the move, then **pauses** for approval. **Proactive daily scanning ships together with on-click investigation from day one** (not phased). The coach can set how far ahead it's allowed to plan — **one next step only, or a short multi-step plan** — as a per-coach setting, not a fixed system default. Built on Mastra (Apache 2.0). Full research + working demo: [`docs/10-ai-agent-layer.md`](10-ai-agent-layer.md) | AI reasons + drafts, **human decides** |
| **Proposals**, Odoo-quotation-style | A Proposal (package + pricing) is created directly from a lead card, same as Odoo's opportunity→quotation link — the lead card shows a live proposal count/status (Draft/Sent/Viewed/Accepted/Declined), same object the coach edits, never a re-typed copy. **Accepting a proposal is what triggers Won** — auto-advances the pipeline stage and pre-fills the Agreement in Module 5 from the proposal's package/pricing, so nothing gets re-entered. | Platform, AI-assisted drafting |
| **Lead activity log** | A dedicated, **collapsed-by-default** section on every lead record — every stage change, every email sent (and whether it was opened, and how many times), every newsletter send/open, every call booked, every note. Organized chronologically, hidden from the main pipeline view so it never clutters the board, but always one click away. | Platform |
| **Communications manager** | A per-lead/per-client panel showing every active sequence or list they're enrolled in (nurture sequence, newsletter, automation recipe) with a **stop/pause control on each one** — the coach's single place to see and control what's being sent to someone, and shut any of it off. | Platform |
| **Custom fields** | Beyond the standard lead fields, coaches can **add their own custom fields** for whatever their specific practice needs (e.g. a wellness coach tracking health context, a career coach tracking current employer). *Compliance note, not a blocker:* any custom field capturing health or other "special category" data should prompt extra consent language and gets the same encryption-at-rest treatment as session notes — flagged here so it's built in from the start, not bolted on later. | Platform |
| **Notes & export** | Rich notes on any lead, downloadable as **Word, Excel, or PDF** — a coach's own record, portable out of CoachOS anytime. | Platform |

**Design rule:** AI never sends outbound to a lead autonomously — including the agent. The coach approves every message — this is both a trust feature and our EU-compliance posture.

**Assistant/VA pipeline access:** confirmed for **day one**, not deferred (supersedes the earlier default) — a coach can add a team member with pipeline access from launch.

**Lead-rule assignment (bridges to Module 8 — Autopilot):** which nurture sequence or follow-up cadence a lead enters based on its score/source/stage is a **rule**, not a one-off — this is exactly what Autopilot (Module 8) is for. Module 2 exposes the levers (score, source, stage) and Module 8 is where the actual "if this, then that" recipe lives, so the same rule engine handles both — no separate rules system duplicated in the Pipeline itself.

**UI principle for this module, and really every module going forward:** as customizable as the table above sounds, the pipeline board itself stays clean and uncluttered — proposals, activity logs, communications management, and custom fields are all **one click away in an organized side panel, never sitting on the main kanban view by default.** Power and simplicity aren't a tradeoff here; they're solved by what's visible by default vs. what's a click deeper.

### Cross-cutting: the AI Voice & Rules Center (decided in docs/08 M2-Q3, applies to every AI draft, not just Leads)

Rather than a single "voice profile" living inside Marketing (Module 3) alone, this becomes its own settings area that **every AI-drafted message in the whole product reads from** — pipeline follow-ups, marketing posts, session summaries, everything.

- **An "About Me" section** — the coach's own bio, philosophy, and story, written once, used as background context for every draft (this can also seed the parameters below with sensible starting values, so the coach doesn't have to configure all of them manually from a blank slate).
- **Written samples** — the 3+ samples from Module 3's onboarding, reused here as the other input.
- **15 tunable parameters**, each editable anytime, not just set once at onboarding:
  1. Tone (warm & personal ↔ professional & polished)
  2. Formality (casual ↔ formal)
  3. Directness (gentle & exploratory ↔ blunt & to-the-point)
  4. Sentence style (short & punchy ↔ flowing & detailed)
  5. Warmth of expression (reserved ↔ effusive)
  6. Humor (none ↔ frequent)
  7. Storytelling frequency (rarely ↔ often uses anecdotes)
  8. Use of questions (mostly statements ↔ frequently asks reflective questions)
  9. Greeting style (e.g. "Hi [name]," vs. "Dear [name]," vs. none)
  10. Sign-off style (e.g. "Warmly," "Best," "Talk soon,")
  11. Vocabulary/jargon level (plain language ↔ industry terms)
  12. Signature phrases (a coach's own recurring expressions, listed by them)
  13. Voice/pronoun preference ("I" vs. "we", first-person vs. coach-as-guide phrasing)
  14. Regional/cultural phrasing (matches the multi-language work below)
  15. Response length default (concise ↔ detailed) — settable separately for email vs. social content
- **Rules can override the defaults per situation** — e.g. "session summaries always stay formal regardless of my general tone setting," "marketing posts can use more humor than client emails."

This is the single place that makes every AI draft across the whole product sound like one coach, not a template — and it's exactly what the Pipeline Agent (above), Marketing Studio, and Client Delivery's summaries all pull from.

### Cross-cutting: language support (decided in docs/08 M12-Q5, applies to every module, not just Leads)

Full multi-language support for both dashboard UI and AI-drafted communications, architected so **adding a language is a simple selection, not a rebuild** — this is genuinely more achievable than a traditional manual-translation effort, for two reasons: a standard i18n framework (extracting UI text into translatable string files, well-supported in Next.js) handles the interface, and Claude drafts AI content **directly in the target language** rather than needing separate translation (it's natively multilingual). Real remaining work: right-to-left layout (Arabic, Hebrew) needs actual layout support, not just word swaps, and each language still needs a translation + QA pass before it's genuinely reliable — so the rollout is broad-and-progressive (AI-assisted translation makes adding each one cheap), not "every language fully polished on day one."

## Module 3 — Marketing Studio · *Phase 3* · decided in docs/08 §Journey 5

**Scope at launch, deliberately narrowed:** newsletter + bulk email marketing only. Social posting (LinkedIn, Instagram, X) is **drafted, not published** — our team (AI-assisted) drafts the post, it goes into the coach's in-platform approval queue same as everything else, the coach edits and approves it, and today that means the coach copies the approved post out and publishes it themselves. Direct scheduling/publishing to LinkedIn and X is a real, visible **"Coming soon"** feature in the UI (not hidden) — it needs the same kind of platform API approval we already verified for lead capture (doc 03), so it's tracked, not silently dropped.

| Capability | How | AI / Human |
|---|---|---|
| Voice | Reads from the cross-cutting **AI Voice & Rules Center** (above) — no separate voice profile for Marketing specifically | AI-built, once, shared across the whole product |
| Content calendar | AI proposes a monthly plan → **our team refines it** → sent to the coach's approval queue (a three-step pipeline, not just AI-draft-then-approve) | AI drafts, **human polishes**, coach approves |
| Social post drafts | Weekly drafts for LinkedIn/Instagram/X in the coach's voice; approved posts are copy-paste today, scheduled/published automatically once "Coming soon" ships | AI + human drafts, coach approves, coach publishes (for now) |
| Newsletter & bulk email | Fully built now — sent and tracked via [Listmonk](https://listmonk.app/) (self-hosted, doc 03). **Frequency, schedule, and content are all coach-editable and customizable**, not fixed to one cadence | AI drafts, Listmonk sends |
| Nurture sequences | New-lead welcome, post-discovery-didn't-close, past-client re-engagement (decided in Journey 2) | AI drafts + Listmonk sends |
| Lead-magnet auto-subscribe | When someone downloads a lead magnet or takes the evaluation quiz (Module 1), they can be automatically added to that coach's newsletter list — but **double opt-in**: they first get a confirmation email ("please confirm you'd like these emails") and are only actually subscribed once they click confirm. This protects the coach from spam complaints and is a real GDPR requirement, not just good practice — a download alone isn't legally "consent to market to me" without that confirmation step. | Platform |
| Session-theme drafting | AI can draft content from a coach's own **anonymized** session themes (patterns across sessions, no client identity) — **opt-in, off by default**, given how sensitive session data is | AI drafts, opt-in required |
| Done-for-you service | Our team gets an "agency seat" inside a coach's account to draft content — the coach still approves every send | **Human (our service revenue)** |
| Headline metric | "Enquiries this content generated," not opens/clicks — ties content performance straight back to the pipeline | Platform |

### Email Connections & campaign analytics — a real, expanded build

**Sending isn't Gmail-only.** A coach connects one of three options (doc 03 rows 9/9b/9c): Gmail (OAuth), Microsoft 365/Outlook (OAuth), or **any custom-domain email via standard SMTP/IMAP** — the same host/port/username/password setup as configuring any email client, so a coach on their own business domain isn't forced onto Gmail. **Multiple named connections are supported** — e.g. a coach can use one address for direct client emails and a different one for automated reminders, each assignable per module/automation.

**Full campaign analytics, Brevo-style**, surfaced in our own dashboard (not Listmonk's raw interface): sent, delivered, bounced, opened (with **open count**, not just yes/no), clicked, and read — Listmonk already tracks opens/bounces/clicks per campaign natively (doc 03), so this is building our own branded dashboard on top of data the engine already produces, not building tracking from scratch. **This is the same metrics layer that feeds Module 2's Lead Activity Log** for 1:1 email opens — one consistent analytics system, whether it's a bulk newsletter or a single email to one lead.

## Module 4 — Bookings · *Phase 2* · decided in docs/08 §Journey 3

**Build:** integrate Cal.com (doc 03), fully white-labeled — the client never sees Cal.com's own name or UI.

| Capability | Decision |
|---|---|
| Package-aware booking | **Not built into booking at launch** — deliberately deferred, to be configured later rather than shipped now. |
| Calendar sync | Google + Outlook/Microsoft 365 both at launch, **plus room to connect other calendar providers later** (the same "multiple named connections" pattern as Module 3's Email Connections) and a real **in-app personal calendar view** inside CoachOS itself, not just a sync target. Two-way, near-instant sync; dates/times always shown in each viewer's own system timezone; a sent invite blocks the slot on both the coach's and the client's calendar. |
| Meeting experience | Prioritized to be **as easy to join as Google Meet** (one click, no app download) and **as feature-rich as Zoom** (recording, transcript, written summary) — see "CoachOS Meet" below. |
| Pay-to-book | Optional, attached per event type — same pattern as Calendly's paid event types (Module 5 handles the actual charge). |
| Reminders | Email is the must-have at launch (WhatsApp stays a Phase 3 investigation) — sent on a **configurable rule set** (e.g. 24h before, 1h before), the same rules-engine pattern as Autopilot (Module 8), not a hardcoded interval. |
| No-shows | Auto-flag + one-click rebook invite; optional no-show fee, off by default. |
| Buffers | 15-minute buffer between sessions, no daily cap. |
| Group sessions | **Built at launch**, not deferred — capped at 2–3 people per group session. |
| Client reschedule window | Fully **customizable per coach**, not a fixed 24 hours. |
| The booking→timeline→prep-brief→reminder chain | Confirmed automatic end to end, **and customizable** — a coach can adjust or disable individual steps, consistent with the interconnection-editability rule already established (doc 08 M12-Q16): the parts that would break the product if turned off stay locked, the rest is a coach's choice. |
| Branding | Zero third-party branding anywhere the client sees the booking page — hard requirement, unchanged. |

### "CoachOS Meet" — brought forward to day one for testing (supersedes the original Phase 3+ gate)

Recording, transcripts, and a written summary are core to the product, not a later nice-to-have — so rather than waiting for Phase 3's full self-hosted infrastructure, LiveKit gets stood up **now** on one small, dedicated, independent test server (doc 07 §6), purely so the real meeting/recording/transcript experience can be built and tried immediately. It folds into the full infrastructure migration later rather than being thrown away. The target experience: a simple join link like Google Meet's, recording and transcription like Zoom's, all inside CoachOS's own screens.

## Module 5 — Agreements & Billing · *Phase 2* · decided in docs/08 §Journey 2

**Market scope — global from the beginning, with one important distinction:** payments and currency genuinely can be global on day one (Stripe's own infrastructure already covers this — see below). **Legal agreement templates cannot follow the same "instant global" path** — a coaching agreement is reviewed against one jurisdiction's actual contract law at a time, and reviewing four+ legal systems (UAE, UK, US, EU) simultaneously before a single paying coach exists is real, avoidable cost and delay for no immediate benefit. The practical resolution: **the template system is built structurally global-ready (any region can be added), and the legally-reviewed content itself rolls out market-by-market as coaches from that market actually sign up** — the first real coach from a new region triggers that region's legal review, not a guess in advance.

| Capability | Decision |
|---|---|
| Agreement templates | Structurally global, legally reviewed market-by-market as above. |
| E-signature | Documenso (white-labeled). Simple click-to-sign, sent and **required via email**, with **configurable reminder notifications** if left unsigned (same rules-engine pattern as Bookings/Autopilot). |
| Payment methods | Stripe, genuinely global — Stripe's own infrastructure already supports cards plus a wide range of local methods across most regions natively, so "global payments" is largely already solved by the engine we picked, not something we build ourselves. |
| Currency | Coach chooses whichever currency (or currencies) fit their clients — not locked to one. |
| Pricing models | One-off, package, retainer, subscription all exist as capabilities, but **which ones a given coach account can actually use is admin-controlled** — a checkbox-style entitlement panel (internal, never public) where access is granted deliberately, based on understanding that coach's situation, not automatically self-served. |
| Deposits / split payments | Built as a **Payment Link** (Stripe's own product for exactly this) — shareable, coach-customizable amount/split, and mark-as-paid either automatically on payment or after manual verification, coach's choice. |
| Late invoices | Reminder cadence fully **coach-set**, never auto-suspending a client without the coach clicking something. |
| Refund/cancellation policy | Coach-defined and editable, shown to the client before signing; we provide a starting template. |

### Money flow — two paths, both compliant (this is the part worth reading carefully)

The ask was: coaches with their own payment setup collect directly; coaches **without** a license or existing account should still be able to get paid through us. Doing the second part *informally* — collecting client payments into our own account and manually transferring them out — is exactly the kind of setup that can trigger money-transmitter licensing requirements in a lot of jurisdictions, which is precisely what the original "we never touch the money" rule existed to avoid. The compliant way to get the same outcome, verified directly:

- **Path A — coach has their own Stripe account:** money flows client → coach's Stripe → coach's bank, exactly as before. We never touch it. Flat subscription fee only, no commission.
- **Path B — coach has no payment account/license of their own:** built on **Stripe Connect**, the product literally designed for this — Stripe itself handles identity verification (KYC) and holds/moves the funds properly, we never do. Stripe Connect also has a built-in **"application fee"** mechanism, which is exactly the commission feature asked for in the pricing question — **an admin-configured, case-by-case commission on Path B only**, decided deliberately per coach rather than a blanket rule. Path A's no-commission promise stays intact; Path B is where the flexibility lives, and it's Stripe carrying the regulatory weight of holding third-party funds, not us.

Both paths use one engine (Stripe/Stripe Connect), so there's no second payment system to build or maintain — just two configured modes of the same one.

### AI assist

Plain-language explanation of any clause on hover ("this means…"), explicitly labeled "not legal advice."

## Module 6 — Client Delivery · *Phase 1 — THE WEDGE* · decided in docs/08 §Journey 3–4

The daily-use core, and the first thing we build.

| Capability | How it works |
|---|---|
| Recording + transcripts | **In the first release**, built directly into CoachOS Meet — not a Phase 1.5 add-on. Client gets a visible notification that recording is happening; no extra confirmation step required. |
| Session notes → summary | Coach's notes (typed or transcribed) → AI drafts client email, coach-private note, action items, and next-session agenda. **All four are coach-configurable** — the coach chooses what gets generated and what's included in each, not a fixed one-size-fits-all set. Nothing shares until approved. |
| Delivery to the client | **Two modes, both available:** (a) the summary emailed directly to the client, readable right in their inbox, no click-through needed; (b) an optional **personal portal link** (a private, unique, no-login-required web page) for anything that needs to persist or be interactive over time — homework checkboxes, full session history, goals. Email alone can show a one-time summary; the portal is what makes ongoing things like homework tracking possible. **The portal is optional and its look/branding is coach-customizable** — a coach can run email-only if they prefer the simpler mode. |
| Homework/tasks | **Built from the first release**, not deferred — a short to-do list between sessions, client checks items off via the portal, gentle reminders if something's still open close to the next session. |
| Goals | Lightweight (not milestone-heavy). **Coach writes them by default**, but editing access is a **permission the coach controls** — they can choose to let a specific client edit their own goals through the portal, or keep that control entirely on the coach's side. |
| Progress timeline | Every session logged against the client's goals; a visual arc of the engagement. |
| AI prep brief | **Built from the first release.** 30 minutes before a session: "Last time Maya committed to X; she flagged Y as a blocker; her goal-3 progress has stalled 3 weeks." Coach-only, never shown to the client. |
| Data rights | Retention period is **a setting the coach configures** when they onboard (not a fixed rule we impose) — auto-delete after a chosen period, or keep indefinitely. Before any actual deletion happens (retention expiring, or a manual request), **the affected client gets notified first** — a courtesy/safety step before anything is permanently removed. Full export always available. |
| Coaching unit — 1:1, and family/team | **1:1 stays the core shape**, but a "client" can also be a **family or team unit** (e.g. a couple, a family of 4, a leadership team) — multiple people sharing one engagement, attending sessions together, and viewing shared portal material as one unit. This is genuinely different from — and simpler to build than — an ongoing **cohort program** (a structured multi-week course for many unrelated individuals), which stays a separate, later, not-yet-built feature. |

**Still open — no default, worth real thought:** in a live demo, which single flow makes a coach say "I need this right now" — the notes→summary flow, the prep brief, or the client portal? Whichever it is gets the deepest polish first. Come back to this whenever you're ready — everything else above is locked in.

**Why first:** highest-frequency use (every session), clearest AI value, generates the data that powers Modules 2, 3 and 7 later, and creates switching costs from week one.

## Module 7 — Practice Intelligence · *Phase 4* · decided in docs/08 §Journey 6

| Capability | Decision |
|---|---|
| The dashboard | Revenue, sessions, pipeline value, invoices, retention, lead-response time — **all shown as a filterable, customizable dashboard** rather than a fixed ranked list. This "dashboard + filters on every field" standard **applies across every module in the product**, not just here — see the new cross-cutting principle below. |
| At-risk client signals | **Fully customizable** — the coach selects which signals they want active (missed sessions, slower replies, stalled goals, shorter sessions), rather than a fixed conservative pair. |
| Anonymized niche benchmarks | *(Explained on request):* this would show a coach how their own pricing/practice compares to other coaches in their niche — e.g. "executive coaches in your region typically charge $X–$Y" — built from fully anonymized, aggregated data across the platform, never revealing which specific coach charges what. Stays a Phase 5, opt-in-only feature for now — revisit once there's real usage data to anonymize in the first place. |
| Revenue forecasting from booked packages | **Moved up — build now**, not deferred to later. |
| Delivery | Both a digest and an always-on dashboard, **with the digest's day/frequency fully coach-customizable** — not fixed to Monday mornings. |
| Accountant export | Confirmed — simple income-summary CSV. |
| Revenue-target nudges | Confirmed as an optional feature, off by default — the coach turns it on if they want the motivation. |
| Time analytics (coaching vs. admin hours) | **Reversed — build it**, not skipped. |
| Action-button philosophy | Confirmed — every insight ships with a next action, never a bare number. |
| Coach-eyes-only | Confirmed, absolute — a client never sees any analysis about themselves. **Guardrail unchanged:** never scoring of employees for corporate buyers (EU AI Act line, doc 01 §5). |

### Cross-cutting: dashboard + filters, everywhere (new standing UI principle, decided here but applies to every module)

Every module gets its own dashboard-style view of its key data, with **filter options on every available field** — Leads, Bookings, Agreements, Client Delivery, all of it. Module 7 is the whole-business rollup that pulls from all of them, but each module also gets this treatment for its own data, not just the top-level view. This sits alongside the existing "customization lives in organized side panels, not cluttering the main view" rule from Module 2 — a filterable dashboard is exactly that kind of organized, one-click-away power feature.

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

## Module 8 — Autopilot (Automations) · *Phase 3* · decided in docs/08 §Journey 4

**Job:** stop the coach re-typing the same follow-up for the fortieth time — without breaking the platform's core trust rule (nothing sends without a human having approved the actual words).

**Engine, corrected:** built on **Activepieces** (MIT-licensed, doc 03/09 — this replaced n8n after we verified n8n's license doesn't cover powering a paid customer feature). The coach never sees Activepieces' own UI.

| Capability | Decision |
|---|---|
| Recipes at launch | **All seven** built from the start, not just the original five — silent-lead follow-up, overdue-invoice reminder, no-show recovery, pre-session prep brief, post-session notes nudge, testimonial ask, past-client re-engagement. Recipes can also trigger off **custom fields** a coach has created (Module 2) — not just the standard fields — so a wellness coach's custom "last check-in mood" field, say, can be a trigger condition too. |
| Approval strictness | **Per-recipe, coach-configurable at setup** — when a coach turns a recipe on, they choose whether it's "approve the wording once, then run automatically" or "ask me every single time," rather than the system deciding that for them. |
| Frequency cap | Coach-set, not fixed at 2/week. |
| Failure handling | In-app alert + email + auto-retry (3 times), **plus a dedicated error page** — a persistent, browsable list of everything that's failed, not just a one-time toast that can be missed. |
| Vacation/pause mode | **Per-workflow**, not one all-or-nothing switch — the coach selects which specific automations to pause, not everything at once (a "pause all" convenience option can still sit alongside this). |
| Quiet hours | **Fully coach-configurable** — the coach sets exactly which hours/timezone each automation is allowed to send in, rather than a fixed 8am–8pm default. |
| Cross-module reach | All the interconnection chains from doc 08 §0.3 ship together, and this stays the standing principle for the whole product — Autopilot is deliberately the "nervous system" tying every module's timers together, not siloed per module. |
| Custom automations by our team | Confirmed as a paid service — built in the underlying engine, appears to the coach as "my custom recipe." |

### The workflow-builder question — a reversal worth being explicit about

The original plan deliberately ruled out a free-form workflow builder, for a real reason: recipe-only keeps the product simple for a non-technical coach, and reduces the risk of a coach accidentally building something that misfires and messages a real client. **This is now reversed** — coaches get to build their own automations, across every module, as many as they want.

**The way this stays safe rather than becoming a confusing, error-prone builder:** it's a **simple, constrained builder**, not an open-ended one like Zapier or n8n's own visual canvas. A coach picks from a defined list of triggers (a field changes, a stage changes, a date arrives, a custom field updates) and a defined list of actions (send a drafted message, add a tag, move a stage, wait X days, notify the coach) — building blocks, not arbitrary logic or code. And critically, **the approval-first floor stays absolute underneath it**: whatever a coach builds, any action that reaches a client still routes through the approval setting from the row above (once, or every time) — the builder changes *who designs the automation*, never *whether a human can see what's about to be sent*.

**AI's role, unchanged:** still suggests recipes from a coach's own repeated behavior ("You've sent this same message 4 times — automate it?") — proposes, never auto-generates send-time content on its own.

## Module 9 — Coach & Mentor Directory (Marketplace) · *Phase 3, gated on coach supply* · **DEFERRED — "Coming soon," per explicit founder instruction (docs/08)**

**This module is intentionally paused, not decided.** Because it faces the public directly, none of its open questions (docs/08) were pushed through with defaults just to keep momentum — they stay unanswered until deliberately revisited. The spec below is the pre-questionnaire baseline, kept for reference; treat nothing in it as locked in the way every other module's write-up in this doc now is.

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

## Module 10 — Daily Briefing · *Phase 3, public-facing too* · decided in docs/08 §Journey 5

**Job:** the coach opens CoachOS and is already caught up — on their field, on the world, and on one new thing worth knowing — before they've had coffee.

| Capability | Decision |
|---|---|
| Launch categories | Confirmed: executive, career, life/wellness, business-of-coaching. |
| News by category | Curated top headlines per niche, each with an AI one-line takeaway and a link to the original source. |
| World catchup | Short, neutral summary of the day's top general headlines. |
| 5-minute daily learn | **Broadened** — rotates across coaching technique, business skills, AI-for-coaches, and more generally whatever's hot in business/industry news that week, not a fixed narrow 3-lane rotation. Always summarized from a real, cited source. |
| Daily volume | Confirmed — 5 top niche headlines + 3 world items + 1 learn. |
| Delivery | In-app, email, and the public website — **the coach chooses which of these they receive**, not all by default. |
| Publish time | One generation per day, ready by 6am Gulf time. |
| "Share this insight" | Confirmed for later — turns a briefing item into a Marketing Studio draft (timing follows Marketing Studio's own rollout, doc 02 Module 3). |
| Sponsor slots | Door kept open for later, nothing built yet. |
| Editorial ownership | **Still open — needs a name or role from you**, not something to default. |
| Language | Follows the full multi-language push (Module 1/M12-Q5) — **and specifically inherits the account's own language setting**: if a coach changes their app language, the Briefing switches with it automatically, no separate language toggle just for this module. |

**Public-site lifecycle, new:** on the public website, the Briefing works as a **lead magnet** (feeds Module 1's lead-magnet list) — each day's edition stays live and readable for **7 days, then is removed from public view**. This is specifically about the *public* page's lifecycle; the coach-facing, logged-in history is a separate concern and isn't implied to be wiped by this rule.

**Where it lives:** a card on the dashboard for logged-in coaches, **and** a public page on the marketing site (`/briefing`) that anyone can read without an account — doubling as a content/SEO channel that feeds the same organic-acquisition thesis as the Directory (Module 9, currently deferred).

**The cost discipline that makes this cheap:** the briefing is generated **once per category, per day** — not once per coach. A few hundred coaches split across 4–5 niche categories means a handful of AI summarization calls a day for the whole platform, not one per coach. This is a shared, cached daily artifact, the same way a newspaper prints one edition for everyone in a region rather than one per subscriber.

**Sourcing rule, not just a nice-to-have:** every headline and every "5-minute learn" links back to its real source. AI's job here is to summarize and select, never to generate a fact from scratch — the same discipline the plan already applies to its own market research (doc 01 §6's "rejected claims" table exists because of exactly this kind of mistake).

## Module 11 — Coach Community · *Phase 4, gated on real supply* · **scope reversed and re-decided in docs/08 §Journey 7**

**Job:** a real, LinkedIn-style public network — not the closed, coach-only peer network originally assumed. The standing assumption ("coach-to-coach only, not client-facing") was explicitly flagged and then explicitly overturned: **this is now open to anyone — coaches and the general public both** — confirmed directly, not defaulted.

### The two-tier structure this scope change requires

Opening participation to anyone changes what "trust" means here, so the design now has two distinct tiers:

| | **Verified Coach profile** | **General/public profile** |
|---|---|---|
| Who | Coaches, **approved through our own review process** (same spirit as the Directory's existing manual verification, doc 02 Module 9) | Anyone — coach or not, self-serve signup |
| Badge | Yes, a visible "Verified Coach" badge | None |
| Where it shows | Surfaced more prominently, credibility signal | Standard profile |

This is the resolution to "verified-coach status" — it's no longer a **gate on who can participate** (since anyone now can), it's a **badge tier** that distinguishes approved coaches from the general public, the same way LinkedIn itself has ordinary members alongside verified/notable profiles.

### Access model — public read, account-gated interaction

- **Public pages** (profiles, posts) are **readable by anyone, on both the website and the app, without logging in** — this is explicit and confirmed, and matters for SEO/reach the same way the Directory's profiles already work.
- **Interacting** — sending/accepting a connection, posting, commenting, liking — **requires being logged into CoachOS** (a real account). Reading is open; participating is account-gated.

| Capability | v1 or v2 | How it works |
|---|---|---|
| Connect | v1 | LinkedIn-style request/accept, mutual — happens **inside the logged-in app**, not from the public-facing pages |
| Post | v1 | Text + optional image, visible per the poster's own privacy choice |
| Like | v1 | One tap, on any post or comment |
| Comment | v1 | Threaded under a post |
| Share | v1 | Reposts to your own network with credit to the original author |
| Message | v2 | Direct, async 1:1 |
| Chat | v2 | Real-time — genuinely different infrastructure from Message, see doc 03 |

**Why v1/v2, not one build:** unchanged reasoning — a social feed is standard database work; real-time chat is a different engineering problem (presence, live delivery, connection state) and deserves its own scoped build. Build the feed first, prove real usage, then build chat.

**The line this module doesn't cross:** no AI posts on anyone's behalf, ever — same approval-first principle as every other module.

### Trust & safety — now a materially bigger undertaking, said plainly

The original spec already treated report/block, a content policy, and abuse rate-limiting as v1 requirements, not polish. **Opening this to the general public, not just verified coaches, raises the real cost and complexity of that work significantly** — this isn't the same undertaking as moderating a small, closed, professionally-accountable community:

- **Moderation has to scale beyond manual review.** A closed coach community is small enough for a human to reasonably review reports. An open public network needs automated content filtering (spam, abuse, prohibited content) as well as human review, from day one, not added later once a problem forces it.
- **Spam/fake-account prevention becomes a real engineering concern**, not just a policy line — open signup invites bot registration in a way an approval-gated coach community doesn't.
- **Legal exposure is broader.** Platforms hosting content from the general public (not just a defined professional user base under contract) sit more squarely under things like the EU's Digital Services Act — notice-and-action obligations for illegal content, transparency requirements — even before reaching the stricter tiers aimed at very large platforms. This deserves a real legal review pass before public launch, not an assumption that the existing doc 01 §5 posture already covers it.
- **The cold-start problem gets harder, not easier.** The original gating logic (hold public launch until there's a real base of engaged coaches) now needs a base of engaged *public* participation too, alongside coaches — a fully open network with nobody in it is arguably a worse first impression than a closed one.

**Recommendation, not yet a decision:** treat this module's cost and timeline as genuinely bigger than the original Phase 4 estimate in doc 04 (updated there too) — it's closer to standing up a real public social product than adding a community feature to an existing SaaS tool, and deserves being scoped and reviewed as such before committing to a launch date.

**Relationship to the Directory (Module 9, currently deferred):** the distinction still holds even with this scope change — the Directory is structured, search-driven discovery (a prospect looking for a coach by filter); the Community is an open social feed. A Verified Coach's Directory profile can surface their Community activity as social proof, but they remain two different surfaces with two different interaction models.

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
