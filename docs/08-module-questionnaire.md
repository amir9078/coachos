# 08 — Module Questionnaire (interconnection-first)

**Why this is organized differently now:** CoachOS isn't 11 separate tools, it's one product — so instead of asking module-by-module in isolation, this asks **journey-by-journey**: the real, end-to-end paths a lead or a client actually travels through the system. Each journey below covers the same five things every time: **what connects** (which modules hand off to which), **the functionality** that makes the handoff work, **how the coach actually uses it** day to day, **the end goal** the journey serves, and **the result** — what comes out the other end, the 360° view of the business this journey produces.

**IDs are unchanged** (`M6-Q3` etc.) from the original module-based version, so anything already cross-referenced in docs 07/09/10 still points to the right question — only the grouping and framing changed, not the questions or their defaults.

**How to answer:** any subset, any order, reference by ID or just talk naturally. Every question has a default in italics — unanswered = default, nothing blocks.

---

## 0. System truths that shape every journey below

- **White-label:** every journey below happens inside CoachOS's own screens. No engine (Cal.com, Documenso, Listmonk, Activepieces) is ever visible mid-journey, even when a handoff crosses from one to another.
- **Failure isolation:** a break in one journey's engine (say, Documenso down) pauses *that* journey only — a lead can still move through Journey 1, a session can still get delivered in Journey 3, while Journey 2's signing step waits.
- **Approval-first, always:** every journey below ends a step *before* anything reaches a client — a draft, a proposal, a send — never past it, no matter how automated the journey becomes.

---

## Journey 1 — A stranger finds you and becomes a lead

**Connects:** Landing pages (M1) + the public Directory (M9) → both feed into the Pipeline (M2), with duplicate-detection linking them.
**End goal:** two acquisition channels that both land in the exact same place, scored the same way, with the coach never doing double-entry.
**Result the coach sees:** one pipeline, regardless of whether a lead came from their own marketing or from being discovered.

**Landing pages & lead capture (M1) — ANSWERED (see docs/02 Module 1 for the full write-up):**
- **M1-Q1.** ✔ Own domain, once naming is finalized.
- **M1-Q2.** ✔ Expanded beyond the original two: a scored evaluation quiz, newsletter, blog content, Daily Briefing content, and a short certification mini-course ending in a test (simple content, no LMS platform). A more game-like interactive mechanic was raised but stays unscoped — future idea, not built yet.
- **M1-Q3.** ✔ Built by our own team (done-with-you/done-for-you service), not a self-serve AI page builder — simplifies Phase 1, revisit self-serve later once volume justifies it.
- **M1-Q4.** ✔ Name, Email, Phone — email is the unique key. Every lead gets a permanent internal Lead ID (0001, 0002…) plus a coach-editable display number/format.
- **M1-Q5.** ✔ Not a one-time score — a **live workflow**: score updates continuously from website-behavior tracking (return visits, content engagement, quiz completion), same tracking infra also powers ad attribution (Q6). Needs an events/sessions table + cookie-consent banner.
- **M1-Q6.** ✔ Meta + Google ad-pixel tracking **from day one** (moved up from "later").
- **M1-Q7.** Deferred — decide later.
- **M1-Q8.** ✔ Expanded significantly: WhatsApp, Instagram, Facebook, and LinkedIn lead capture, each behind its own platform approval (verified directly — see docs/02 Module 1 and docs/03 rows 17–19 for the access-requirement table). WhatsApp + Facebook/Instagram build first (achievable in days); LinkedIn applied for early but never blocks the roadmap (LinkedIn's own process runs "weeks to months," tracked as a risk in doc 04). Lead enrichment confirmed for all channels.
- **M1-Q9.** ✔ Folded into the post-call flow: AI drafts a call summary + feedback/testimonial request together, coach approves and sends as one action; positive responses become testimonial candidates.
- **M1-Q10.** ✔ Superseded by M12-Q5 below — full multi-language push, not just Arabic.
- **M1-Q11.** ✔ No fixed number — varies too much by coach niche/profile; track empirically from real pilot data.

**The Directory, as a second lead source (M9):**
- **M9-Q1.** Who can list — subscribers only, or open/cheap listings as their own revenue line (like Psychology Today's ~$30/mo)? *(subscribers included; open tier Phase 4)*
- **M9-Q2.** Manual credential verification (ICF certs etc.) + a "Verified" badge? *(yes — the trust moat)*
- **M9-Q3.** Rank search filters: specialty, price, language, timezone, industry background, session format. (no default)
- **M9-Q4.** Browse+filter v1, "answer 5 questions, we suggest 3 coaches" AI matching v2? *(yes, that split)*
- **M9-Q5.** Star ratings, or curated testimonials only (Noomii's model)? *(testimonials only)*
- **M9-Q6.** If a directory enquiry gets no coach response in 48h, gently suggest similar coaches to the client? *(yes)*
- **M9-Q7.** Pricing display on a profile — public rates, ranges, or "enquire"? *(per-coach choice)*
- **M9-Q8.** Standard "Book a free discovery call" CTA on every profile — feeding straight into Journey 3's booking flow? *(yes)*
- **M9-Q9.** Launch geography — global-English, or Gulf/UAE-first? *(global site, Gulf-first recruiting)*
- **M9-Q10.** Paid featured placement — extra revenue, or does it corrupt the trust the whole Directory depends on? *(no, v1)*
- **M9-Q11.** Explicit coach consent at listing time (it's a public SEO page)? *(yes, required)*

**Where the two channels merge:**
- **M2-Q9.** Same person enquires via both the Directory and a landing page — auto-merge into one pipeline record? *(yes, auto-merge on matching email, coach notified)*

---

## Journey 2 — A lead becomes a paying client

**Connects:** Pipeline (M2) + the Pipeline Agent (new, doc 10) → Agreements & Billing (M5) → Client Delivery record created (M6) → a welcome sequence offered (M3).
**Functionality:** a lead moves through pipeline stages, gets reasoned about by the agent when it stalls, and the moment it's marked Won, an agreement drafts, a client record is created, and an intake questionnaire and welcome sequence become available — one event cascading through four modules.
**End goal:** zero manual re-entry between "someone said yes" and "the coaching relationship formally starts."
**Result the coach sees:** a lead that moves from Enquiry to a signed, invoiced, onboarded client without ever re-typing the same information twice.

**Pipeline mechanics (M2) — ANSWERED (see docs/02 Module 2 for the full write-up):**
- **M2-Q1.** ✔ Fully customizable from day one — add, remove, reorder, and rename any stage.
- **M2-Q2.** ✔ 6 days (default confirmed).
- **M2-Q3.** ✔ Expanded significantly into a cross-cutting **AI Voice & Rules Center** (docs/02): an "About Me" section + writing samples + 15 tunable parameters + overridable rules per content type — powers every AI draft in the product, not just Pipeline follow-ups.
- **M2-Q4.** ✔ Drafted here, signed in M5 — built **Odoo-quotation-style**: a Proposal object linked to the lead card with its own live status (Draft/Sent/Viewed/Accepted/Declined), and accepting it is the actual Won trigger, pre-filling the Agreement.
- **M2-Q5.** ✔ Default confirmed (auto-offer to nurture list with consent), **plus a new "Communications manager"** panel — per-lead/client view of every active sequence with a stop/pause control on each.
- **M2-Q6.** ✔ Confirmed — **plus a new Lead Activity Log**: a collapsed-by-default, organized section on every lead showing every stage change, email sent/opened (with open count), newsletter send/open, call booked, and note.
- **M2-Q7.** ✔ Confirmed, always explain. Scoring rules (which sequence a lead enters based on score/source/stage) live in Autopilot (Module 8) — Module 2 exposes the levers, Module 8 runs the actual rule.
- **M2-Q8.** ✔ **Day one**, not later (supersedes the original default).
- **M2-Q10.** Default confirmed (CSV + onboarding service).
- **M2-Q11.** ✔ Reversed — coaches CAN store whatever custom fields their business needs via a **customizable fields section**; any field capturing health/special-category data gets extra consent language + encryption, flagged but not blocked.
- **M2-Q12.** ✔ Confirmed the design brief, **plus Word/Excel/PDF export** on notes and records.

**The Pipeline Agent, reasoning across this journey (doc 10) — ANSWERED:**
- **M2-Q13.** ✔ Both ship together from day one — proactive daily scanning is not gated behind "trust established."
- **M2-Q14.** ✔ Made a **per-coach setting**, not a fixed default — the coach chooses one-step-only or a short multi-step plan.
- **M2-Q15.** ✔ Confirmed, always visible.

**Standing UI principle from this module (applies everywhere going forward):** the more customizable a module gets, the more its power features belong in an organized side panel, one click away — never cluttering the primary view by default.

**The handoff into Agreements & Billing (M5) — ANSWERED (see docs/02 Module 5 for the full write-up):**
- **M5-Q1.** ✔ **Global from the beginning** — but with a key distinction: payments/currency genuinely can be global day one (Stripe already covers this), while legal agreement templates are structurally global-ready but legally reviewed **market-by-market as coaches from that market actually sign up**, not all four jurisdictions reviewed speculatively before any real coach exists.
- **M5-Q2.** ✔ Global — Stripe's own infrastructure already covers cards + a wide range of local methods across most regions, so this is largely already solved by the engine, not something built from scratch.
- **M5-Q3.** ✔ All four pricing models exist, but **which ones a coach account can use is admin-controlled** via a checkbox-style entitlement panel (internal only, never public) — access granted deliberately per coach, not self-served automatically.
- **M5-Q4.** ✔ Confirmed, **plus a dual money-flow model** — see below.
- **M5-Q5.** ✔ Reversed — coach chooses whichever currency(ies) fit their clients, not locked to one.
- **M5-Q6.** ✔ Reminder cadence fully coach-set (not fixed at +3/+7), never auto-suspending without the coach's click.
- **M5-Q7.** ✔ Built as a **Stripe Payment Link** — coach-customizable amount/split, mark-as-paid automatically or after manual verification, coach's choice.
- **M5-Q8/Q9 — the money flow, answered together:** two compliant paths, both on Stripe (verified directly, doc 03 row 8b): **Path A** — coach has their own Stripe account, money never touches us, flat subscription only, no commission (original default, unchanged). **Path B** — coach has no payment account/license; built on **Stripe Connect**, which handles KYC and fund-holding itself (we never do), with its built-in "application fee" mechanism powering an **admin-configured, case-by-case commission on Path B only**. Path A's no-commission promise stays intact.
- **M5-Q10.** ✔ Simple e-sign, **required via email**, with configurable reminder notifications if left unsigned.
- **M5-Q11.** ✔ Confirmed, and made fully coach-editable, not just a fixed template.

**Where this journey lands — the client record and first touch (M6, M3):**
- **M6-Q8.** New-client intake questionnaire auto-sent the moment the agreement is signed? *(yes, Phase 2)*
- **M3-Q6.** Of the four possible nurture sequences, the top 3 should include the new-lead-welcome sequence that fires here — confirm the other two: post-discovery-didn't-close, past-client re-engagement, or lead-magnet follow-up? *(new-lead welcome + post-discovery-didn't-close + past-client re-engagement)*

---

## Journey 3 — A booked session happens and gets captured

**Connects:** Bookings (M4) → the meeting itself (Zoom/Meet/CoachOS Meet, doc 09 §8) → Client Delivery's notes/summary/timeline/prep-brief (M6).
**Functionality:** a booking triggers a calendar hold, a meeting link, a reminder sequence; the session itself can be recorded and transcribed; the resulting notes become an AI summary that feeds the client's timeline and schedules the next prep brief.
**End goal:** the coach never has to manually log that a session happened, what was discussed, or what's next — the booking-to-summary pipe does it.
**Result the coach sees:** open the app 30 minutes before a session and the prep brief is already there; the session ends and a draft summary is already waiting for approval.

**Booking mechanics (M4) — ANSWERED (see docs/02 Module 4 for the full write-up):**
- **M4-Q1.** ✔ Not built at launch — deliberately deferred, to configure later.
- **M4-Q2.** ✔ Both, **plus** room to connect other calendar providers later (same pattern as Email Connections), an in-app personal calendar view, instant two-way sync, viewer-local timezone display, and invites blocking the slot on both sides.
- **M4-Q3.** ✔ Superseded by M4-Q13 below — in-platform video ("CoachOS Meet") prioritized over choosing between Meet/Zoom links.
- **M4-Q4.** ✔ Confirmed, Calendly-style optional per-event-type payment.
- **M4-Q5.** ✔ Email confirmed as the must-have, sent on a **configurable rule set** (same rules-engine pattern as Autopilot), not a hardcoded interval. WhatsApp stays a later investigation.
- **M4-Q6.** ✔ Confirmed.
- **M4-Q7.** ✔ Confirmed.
- **M4-Q8.** ✔ Reversed — **built at launch**, capped at 2–3 people per group session.
- **M4-Q9.** ✔ Made fully customizable per coach, not fixed at 24h.
- **M4-Q10.** ✔ Confirmed automatic, **plus made customizable** — coach can adjust/disable individual steps in the chain (structural pieces stay locked per doc 08 M12-Q16).
- **M4-Q11.** ✔ Confirmed, hard requirement.

**The meeting itself — recording and transcription (doc 09 §8) — ANSWERED:**
- **M4-Q12.** ✔ Prioritized as an in-platform, join-link experience (like Meet) with Zoom-level recording/transcription/summary richness — not a choice between external Zoom vs. Meet links.
- **M4-Q13.** ✔ **Reversed — needed from the beginning, at least to test it**, not deferred to Phase 3+. Resolved the Phase 3+ hosting gate by standing up LiveKit on one small, separate test VPS now (doc 07 §6), independent of the main app's hosting phase — folds into the full Coolify migration later rather than being thrown away.

**Where the session lands — notes to summary to timeline (M6) — ANSWERED (see docs/02 Module 6 for the full write-up):**
- **M6-Q1.** ✔ Reversed — recording + transcripts in the **first release**, built into CoachOS Meet, not Phase 1.5.
- **M6-Q1b.** ✔ Simplified to a visible notification only, no per-session confirmation step.
- **M6-Q2.** ✔ Confirmed the four outputs, **made fully coach-configurable** — coach chooses what's generated and what's in each.
- **M6-Q3.** ✔ Clarified and expanded: clients never have logins — they get either the summary emailed directly, or an optional, coach-customizable personal portal link (no login) for anything ongoing/interactive (homework, history, goals). Private notes stay coach-only either way.
- **M6-Q6.** ✔ Confirmed, and built from the first release, not deferred.

---

## Journey 4 — Ongoing delivery keeps the client renewing

**Connects:** Client Delivery's goals/homework (M6) → Autopilot's timers (M8) → back into Journey 2's renewal/proposal flow.
**Functionality:** between sessions, homework and goals accumulate; Autopilot watches for patterns (package nearly used up, a testimonial-worthy moment, a past client gone quiet) and drafts the next move at the right time — never inventing new wording, only timing what the coach already approved once.
**End goal:** a client relationship that renews itself through visible momentum, not through the coach remembering to check in.
**Result the coach sees:** a renewal proposal or testimonial ask appears in the approval queue exactly when the data says it should — not because the coach set a calendar reminder.

**Delivery mechanics that feed the timers (M6) — ANSWERED (see docs/02 Module 6):**
- **M6-Q4.** ✔ Reversed — built from the **first release**, not v1.1.
- **M6-Q5.** ✔ Lightweight, coach-created by default, **plus a permission toggle** — the coach can grant a specific client edit access to their own goals, or keep it coach-only.
- **M6-Q7.** ✔ **10 active clients** per typical pilot coach.
- **M6-Q9.** ✔ Confirmed, **plus made configurable** — retention period is a setting the coach chooses at onboarding, and the affected client gets notified before any actual deletion happens.
- **M6-Q10.** ✔ **Expanded** — 1:1 stays the core shape, but a "client" can also be a **family or team unit** (multiple people, one shared engagement) — genuinely different from and simpler than an ongoing multi-person cohort *program*, which stays deferred.
- **M6-Q11.** **Still open** — genuinely worth real thought given this is the wedge module; no default applied, revisit whenever ready.

**Autopilot, the nervous system running the timers (M8) — ANSWERED (see docs/02 Module 8 for the full write-up):**
- **M8-Q1.** ✔ **All seven** recipes at launch, not just five — plus recipes can trigger off coach-created custom fields, not just standard ones.
- **M8-Q2.** ✔ Made **per-recipe, coach-configurable at setup** — the coach chooses one-time-approval-then-auto vs. approve-every-send, for each recipe individually.
- **M8-Q3.** ✔ Made coach-configurable, not fixed at 2.
- **M8-Q4.** ✔ **Reversed — a real but constrained workflow builder is being built.** Not open-ended like Zapier/n8n: defined triggers + defined actions only (no arbitrary logic/code), and the approval-first floor stays absolute underneath whatever a coach builds. Full reasoning in docs/02.
- **M8-Q5.** Confirmed.
- **M8-Q6.** ✔ Confirmed, **plus a dedicated error page** — a persistent, browsable list of failures, not just a one-time alert.
- **M8-Q7.** ✔ Reversed — **per-workflow** pause selection, not one all-or-nothing switch.
- **M8-Q9.** Confirmed.
- **M8-Q10.** ✔ Made **fully coach-configurable** — the coach sets the allowed hours/timezone per automation, not a fixed 8am–8pm.

---

## Journey 5 — The coach's content engine runs itself

**Connects:** Marketing Studio (M3) ↔ Daily Briefing (M10), sharing both a voice profile and a "share this insight" bridge.
**Functionality:** the coach's own writing trains a voice profile once; from then on, both session-theme-derived content (M3) and Briefing headlines (M10) can become a draft in that same voice, in the same approval queue.
**End goal:** the coach never faces a blank page — for their own marketing, or for reacting to industry news.
**Result the coach sees:** a content calendar that's mostly pre-filled, from two different source materials, in one consistent voice.

**Marketing Studio (M3) — ANSWERED (see docs/02 Module 3 for the full write-up):**
- **M3-Q1.** ✔ Narrowed at launch: newsletter + bulk email marketing built and automated now. Social posting (LinkedIn/Instagram/X) is drafted + approved in-platform, but publishing stays manual (copy-paste) until direct scheduling ships — see Q2.
- **M3-Q2.** ✔ Kept as a visible **"Coming soon"** feature in the UI, not hidden or silently deferred.
- **M3-Q3.** ✔ Superseded by the cross-cutting AI Voice & Rules Center (M2-Q3) — no separate voice profile for Marketing.
- **M3-Q4.** ✔ Monthly plan, weekly drafts — **plus a human-polish step**: AI proposes → our team refines → coach approves (three steps, not two).
- **M3-Q5.** ✔ Frequency, schedule, and content all made **coach-editable and customizable**, not fixed to bi-weekly. List ownership/export confirmed.
- **M3-Q7.** ✔ Confirmed, opt-in, off by default.
- **M3-Q8.** ✔ Confirmed, double opt-in — explained: a lead-magnet download alone isn't legal marketing consent; the person gets a confirmation email first and is only subscribed once they click confirm.
- **M3-Q9.** ✔ Confirmed.
- **M3-Q10.** ✔ Confirmed, enquiries generated.
- **M3-Q11.** ✔ Confirmed — **plus a major expansion**: Email Connections now supports Gmail, Microsoft 365, or any custom-domain email via SMTP/IMAP, multiple named connections per coach, and full Brevo-style campaign analytics (sent/delivered/bounced/opened with open count/clicked) built on Listmonk's native tracking — the same metrics layer that feeds Module 2's Lead Activity Log.

**Daily Briefing, the second content source (M10):**
- **M10-Q1.** Launch categories — executive, career, life/wellness, business-of-coaching? *(those four)*
- **M10-Q2.** The 5-minute learn: rotate across coaching technique / business skills / AI-for-coaches, or pick one lane? *(rotate)*
- **M10-Q3.** Daily volume — 5 niche headlines + 3 world items + 1 learn? *(yes)*
- **M10-Q4.** Delivery: in-app + optional email? *(both)*
- **M10-Q5.** Public site: full and free, or teaser + signup? *(full and free — it's an acquisition channel)*
- **M10-Q6.** Publish anchor: ready by 6am Gulf time? *(yes)*
- **M10-Q7.** "Share this insight" → creates a Marketing Studio draft in the coach's voice — the literal bridge of this journey? *(yes, Phase 4)*
- **M10-Q8.** Keep the door open for sponsor slots later (Morning Brew model)? *(door open, nothing built)*
- **M10-Q9.** Who on your team owns the daily glance-and-correct via the Claude/MCP tool? (no default — name a person/role)
- **M10-Q10.** English only at launch? *(yes)*

---

## Journey 6 — The whole business shows up on one dashboard

**Connects:** Practice Intelligence (M7) pulls from every other module — revenue from M5, sessions from M4/M6, pipeline from M2, engagement signals from M6.
**Functionality:** nothing here originates new data — this journey is purely about synthesizing what every other journey already produced into one coherent picture.
**End goal:** a coach can answer "how is my practice actually doing?" in one glance, without opening five different tabs.
**Result the coach sees:** a Monday-morning digest and always-on dashboard where every number links to the specific pipeline/client/invoice behind it.

**ANSWERED (see docs/02 Module 7 for the full write-up):**
- **M7-Q1.** ✔ Reframed — shown as a **filterable, customizable dashboard** rather than a fixed ranking, and this "dashboard + filters" standard now applies to every module in the product.
- **M7-Q2.** ✔ Made fully customizable — coach selects which signals they want active.
- **M7-Q3.** Explained on request (see docs/02): anonymized, aggregated pricing/practice benchmarks by niche, never revealing individual coaches' numbers. Default confirmed — Phase 5, opt-in only.
- **M7-Q4.** ✔ Reversed — build now.
- **M7-Q5.** ✔ Confirmed both, **plus the digest's day/frequency made coach-customizable**.
- **M7-Q6.** ✔ Confirmed.
- **M7-Q7.** ✔ Confirmed as-is — optional, off by default.
- **M7-Q8.** ✔ Reversed — build it.
- **M7-Q9.** ✔ Confirmed.
- **M7-Q10.** ✔ Confirmed, absolute.

---

## Journey 7 — Coaches find each other and refer business

**Connects:** Coach Community (M11) → back into Journey 2's Pipeline, via a referral hand-off.
**Functionality:** coaches post, connect, and — the flagship connection — hand off a lead that isn't their niche directly into a colleague's pipeline, tracked.
**End goal:** the community isn't just social, it's a second, coach-to-coach acquisition channel layered on top of the industry's existing word-of-mouth culture.
**Result the coach sees:** a lead that doesn't fit their niche doesn't get lost — it becomes revenue for someone else in the network, and goodwill back to them.

- **M11-Q1.** **Confirm the standing assumption: coach-to-coach only, clients never enter the community.** *(yes)*
- **M11-Q2.** Real names + verified-coach status required to post? *(yes — professional network, not a forum)*
- **M11-Q3.** Post types v1: text, image, link only? *(yes)*
- **M11-Q4.** One global feed v1 with niche tags; dedicated spaces v2? *(yes)*
- **M11-Q5.** Our team moderates day one, I draft the guidelines? *(yes)*
- **M11-Q6.** Connections: request/accept (LinkedIn-style), not follow-style? *(request/accept)*
- **M11-Q7.** How do we stop referrals turning into a spam wall — a dedicated "referrals" post type, cold-pitch DMs reportable? *(yes, both)*
- **M11-Q8.** Events/webinars inside the community — v2+? *(v2+)*
- **M11-Q9.** Gamification (points, badges) — or keep it calm and professional? *(calm, none)*
- **M11-Q10.** Build the referral hand-off — the literal bridge into Journey 2 — as the Community v2 flagship feature? *(yes — it turns the community into revenue for coaches)*
- **M11-Q11.** v2 messaging: coach-to-coach DM only, tied to the referral flow, no client messaging here? *(yes)*

---

## Journey 8 — Staying in sync with the tools coaches already use

**Connects:** every module that touches an external tool — Bookings' calendar sync (M4), Pipeline's contact import (M2), the coach's inbox (Gmail) — all threaded through one sync philosophy (doc 07 §8).
**Functionality:** CoachOS meets a coach where they already are, rather than asking them to abandon Google Calendar, their contacts, or their existing lead spreadsheet on day one.
**End goal:** the lowest possible switching cost — adoption dies at the transition, so this journey exists to prevent that death.
**Result the coach sees:** their existing calendar, contacts, and leads show up inside CoachOS instead of requiring a manual, error-prone migration.

- **M12-Q12.** Rank which tools your target coaches actually live in today: Google Calendar + Gmail, Outlook + Microsoft 365, WhatsApp, Zoom, Excel/Sheets, Calendly, Notion. *(Google-first, Outlook close second)*
- **M12-Q13.** Calendar sync depth: full two-way (their events block our slots, our bookings appear in their calendar instantly)? *(yes, two-way from day one)*
- **M12-Q14.** Google Contacts: optional one-time import at onboarding, with consent? *(yes, Phase 2–3)*
- **M12-Q15.** Should our onboarding service handle specific migration tools by name (HoneyBook export, Calendly event types, Mailchimp lists)? Name the ones your pilot coaches actually use. *(CSV universal + service handles the rest manually)*

---

## Part B — Foundational decisions (gate multiple journeys, answer once)

These aren't interconnections themselves — they're the settings that determine *how* the journeys above behave everywhere at once.

- **M12-Q1.** Approve or rename the in-product module names: Attract, Pipeline, Marketing Studio, Bookings, Agreements & Billing, Clients, Insights, Autopilot, Directory, Daily Briefing, Community.
- **M12-Q2.** Packaging: every plan gets all modules with different limits, or tiers by module set? Price anchors still $29–59? *(module-tiered as sketched)*
- **M12-Q3.** **First market — UAE/Gulf, UK, US, or global-English?** Gates Journey 2's legal templates, Journey 3's WhatsApp priority, and Journey 8's sync order. (no default — the single most consequential open question)
- **M12-Q4.** Mobile: responsive web + installable PWA, or native app-store apps? *(responsive + PWA)*
- **M12-Q5.** ✔ **ANSWERED (supersedes M1-Q10):** push for full multi-language support — dashboard UI + AI-drafted communications, for both coach and client — not just Arabic. Architected so adding a language is a simple selection, not a rebuild: a standard i18n framework handles UI strings, and Claude drafts AI content directly in the target language rather than needing separate translation. Real remaining work is RTL layout (Arabic, Hebrew) and a translation+QA pass per language — so rollout is broad-and-progressive via AI-assisted translation, not "every language fully polished on day one." Full detail in docs/02's cross-cutting language section.
- **M12-Q6.** Data hosting region — EU (safe for GDPR, fine for UAE)? *(yes)*
- **M12-Q7.** White-label hardness — any acceptable exception to zero third-party branding (e.g. a tiny footer credit)? *(zero exceptions)*
- **M12-Q8.** Support model at launch — who staffs it: you, a hire, the done-for-you team? *(founder + docs at pilot stage)*
- **M12-Q9.** Reliability commitment in our terms — 99.5% uptime, daily backups, 30-day retention? *(yes)*
- **M12-Q10.** Which modules can an assistant/VA touch — Pipeline + Bookings + Marketing drafts, never session notes? *(exactly that, Phase 3)*
- **M12-Q11.** **Which two modules will you personally demo on every sales call?** Those get the deepest polish across every journey they touch. (no default)

---

## Part C — Module Interconnection (the governance layer, answer last)

Everything above is *what* connects to *what*. This is *how connections work as a system* — the switchboard behind every journey.

- **M8-Q8.** Across all the journeys above, which single cross-module chain matters most to get right first: package-ending → renewal draft (Journey 4), won-lead → onboarding sequence (Journey 2), or signed-agreement → intake + invoice (Journey 2)? *(default: all three ship as recipes together — no forced ranking)*
- **M12-Q16.** Every chain above is a named, per-coach on/off connection with editable timing and wording. Should any be **locked always-on** because turning them off breaks the product (e.g. "booking confirmed → session appears on timeline")? *(structural chains locked; behavioral chains — nudges, sequences, publishing — fully editable)*
- **M12-Q17.** Should our team be able to custom-wire *extra* connections for a specific coach, as a paid service, appearing to them as "my custom recipe"? *(yes — service revenue, same answer as M8-Q9)*
- **M12-Q18** *(new)*. Should the coach ever see a single "switchboard" screen listing every active connection across all their journeys — what's on, what's off, what's been customized — or is that too technical for a non-technical coach and better left invisible? *(default: a simple plain-language settings list — "Follow up with quiet leads: ON," not a technical diagram — full visibility, no jargon)*
- **M12-Q19** *(new)*. When the Pipeline Agent (Journey 2) or Autopilot (Journey 4) touches a chain that crosses into a *third* module (e.g. agent's draft depends on both pipeline history *and* practice-intelligence risk flags), should that cross-journey reach be unlimited, or capped to the two modules a chain was originally scoped to? *(default: capped to the chains explicitly listed above — any new cross-journey reach is a deliberate future decision, not an emergent one)*

---

## What happens with your answers

1. You answer (any subset, any order — by ID, or just talk naturally about a journey).
2. Unanswered questions take the italic defaults.
3. Per module, I write the **final build prompt** — frontend, backend, security, data model, the specific interconnection events, white-label requirements — and restructure docs 02/05 around them.
4. Build order stays phase-gated: Module 6 first (it's the backbone of Journey 3), nothing else until it earns paying coaches.
