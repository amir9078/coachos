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

**Pipeline mechanics (M2):**
- **M2-Q1.** Pipeline stages: fixed default (Enquiry → Discovery → Proposal → Won/Lost), or customizable? *(default + 2 custom)*
- **M2-Q2.** Silent-lead nudge after how many days quiet? *(6 days)*
- **M2-Q3.** Coach's voice for AI drafts: trained once at onboarding, or continuously from sent emails? *(once, re-tunable anytime)*
- **M2-Q4.** Proposals (packages + pricing sent to a lead): drafted here, or handled in Agreements (M5)? *(drafted here, signed in M5 — this is the literal handoff point of this journey)*
- **M2-Q5.** Lost leads: auto-offer to move to the newsletter nurture list (Journey 5), with consent? *(yes)*
- **M2-Q6.** Booking a discovery call auto-advances the pipeline stage (a Journey 3 event feeding back into Journey 2)? *(yes)*
- **M2-Q7.** Coach always sees *why* the AI scored a lead hot/cold? *(yes, always explain)*
- **M2-Q8.** Assistant/VA pipeline access — v1 or later? *(later, Phase 3)*
- **M2-Q10.** Import existing pipeline data (spreadsheet, HoneyBook) at launch? *(CSV + our onboarding service)*
- **M2-Q11.** Anything that must never be stored on a lead (health details etc.)? *(we warn against sensitive data)*
- **M2-Q12.** One pipeline feature from HoneyBook/Pipedrive/Dubsado a coach would genuinely miss? *(design brief covers board view, saved filters, email activity, notes)*

**The Pipeline Agent, reasoning across this journey (doc 10):**
- **M2-Q13.** Agent runs only on-click, or also proactive daily scans that surface stalled leads on their own? *(both, eventually — on-click now, proactive once trust is earned)*
- **M2-Q14.** How far ahead can the agent plan — one next step only, or a short multi-step plan? *(one step at a time)*
- **M2-Q15.** Agent's reasoning always visible to the coach? *(yes, always)*

**The handoff into Agreements & Billing (M5):**
- **M5-Q1.** **Which market's coaches first — UAE, UK, US, EU?** This single decision sets the legal template, VAT logic, and payment methods for every agreement this journey produces. (no default — the biggest open decision in the whole plan)
- **M5-Q2.** Local payment methods beyond Stripe cards (bank transfer, Tabby)? *(Stripe + manual mark-as-paid)*
- **M5-Q3.** Pricing models at launch: one-off, package, retainer, subscription? *(one-off + package v1)*
- **M5-Q4.** Auto-invoice the moment the agreement is signed — the literal trigger into the next step of this journey? *(yes)* Per-coach VAT rate field? *(yes)*
- **M5-Q5.** Multi-currency per coach, or one? *(one per coach)*
- **M5-Q6.** Late-invoice reminders at +3/+7 days, then coach decides? *(yes — never auto-suspend a client without the coach clicking it)*
- **M5-Q7.** Deposits/split payments for large packages? *(50% deposit option v1)*
- **M5-Q8.** Confirm: money flows client → coach's own Stripe → coach's bank, we never touch it? *(yes)*
- **M5-Q9.** Confirm: flat subscription revenue only, never a commission on coach revenue? *(yes, no commission ever)*
- **M5-Q10.** Simple e-sign sufficient, or does any target market need ID verification? *(simple e-sign)*
- **M5-Q11.** Refund/cancellation policy shown before signing? *(yes, template provided)*

**Where this journey lands — the client record and first touch (M6, M3):**
- **M6-Q8.** New-client intake questionnaire auto-sent the moment the agreement is signed? *(yes, Phase 2)*
- **M3-Q6.** Of the four possible nurture sequences, the top 3 should include the new-lead-welcome sequence that fires here — confirm the other two: post-discovery-didn't-close, past-client re-engagement, or lead-magnet follow-up? *(new-lead welcome + post-discovery-didn't-close + past-client re-engagement)*

---

## Journey 3 — A booked session happens and gets captured

**Connects:** Bookings (M4) → the meeting itself (Zoom/Meet/CoachOS Meet, doc 09 §8) → Client Delivery's notes/summary/timeline/prep-brief (M6).
**Functionality:** a booking triggers a calendar hold, a meeting link, a reminder sequence; the session itself can be recorded and transcribed; the resulting notes become an AI summary that feeds the client's timeline and schedules the next prep brief.
**End goal:** the coach never has to manually log that a session happened, what was discussed, or what's next — the booking-to-summary pipe does it.
**Result the coach sees:** open the app 30 minutes before a session and the prep brief is already there; the session ends and a draft summary is already waiting for approval.

**Booking mechanics (M4):**
- **M4-Q1.** Package-aware booking — checks and shows remaining session credits? *(yes)*
- **M4-Q2.** Calendar sync: Google only, or + Outlook/Microsoft 365? *(both)*
- **M4-Q3.** Video links auto-created: Google Meet, Zoom, or both? *(Meet v1, Zoom Phase 3)*
- **M4-Q4.** Pay-to-book single sessions (charged at booking, via M5)? *(optional per session type)*
- **M4-Q5.** WhatsApp reminders (huge in the Gulf) — priority or later? *(email v1; WhatsApp investigated Phase 3)*
- **M4-Q6.** No-show handling: auto-flag + one-click rebook + optional fee? *(flag + rebook; fees off)*
- **M4-Q7.** Buffer/daily-cap defaults? *(15-min buffer, no cap)*
- **M4-Q8.** Group sessions/workshops — v1 or later? *(later, Phase 4)*
- **M4-Q9.** Client self-reschedule window? *(24h)*
- **M4-Q10.** Confirm the full chain: booking confirmed → session on client timeline → prep brief scheduled → reminder sent, fully automatic? *(yes)*
- **M4-Q11.** Booking page 100% CoachOS-branded, zero third-party name visible anywhere? *(yes — hard requirement)*

**The meeting itself — recording and transcription (doc 09 §8):**
- **M4-Q12.** What do your target coaches actually run sessions on today — Zoom, Meet, Teams, phone? Rank them; this decides which integration we build first. *(Zoom first — the only one where recording + transcript both pull in automatically)*
- **M4-Q13.** In-platform video ("CoachOS Meet," Phase 3+) — must-have, nice-to-have, or skip if Zoom already works? *(nice-to-have, Phase 3 decision gate)*

**Where the session lands — notes to summary to timeline (M6):**
- **M6-Q1.** Should the upload-any-recording→transcript path move into Phase 1 itself, as part of the wedge? *(Phase 1.5, right after typed notes work)*
- **M6-Q1b.** Recording consent: agreement + in-session notice enough, or per-session client confirmation too? *(agreement + notice)*
- **M6-Q2.** Summary outputs — client email + coach note + action items + next-session agenda? *(yes, all four)*
- **M6-Q3.** What clients see in the portal (summaries, action items, goals, history, files) — and confirm coach's private notes are never visible there? *(all five visible; private notes never, enforced at the database level)*
- **M6-Q6.** Prep brief: 30 min before, containing last session's commitments, open blockers, stalled goals — exactly as specced? *(yes)*

---

## Journey 4 — Ongoing delivery keeps the client renewing

**Connects:** Client Delivery's goals/homework (M6) → Autopilot's timers (M8) → back into Journey 2's renewal/proposal flow.
**Functionality:** between sessions, homework and goals accumulate; Autopilot watches for patterns (package nearly used up, a testimonial-worthy moment, a past client gone quiet) and drafts the next move at the right time — never inventing new wording, only timing what the coach already approved once.
**End goal:** a client relationship that renews itself through visible momentum, not through the coach remembering to check in.
**Result the coach sees:** a renewal proposal or testimonial ask appears in the approval queue exactly when the data says it should — not because the coach set a calendar reminder.

**Delivery mechanics that feed the timers (M6):**
- **M6-Q4.** Homework/tasks between sessions with client check-off + reminders? *(v1.1, right after the wedge core)*
- **M6-Q5.** Goals: formal milestones or lightweight themes? Who sets them — coach, client, or together? *(lightweight, coach-created)*
- **M6-Q7.** Reality check for scale: how many active clients does a typical pilot coach carry? (no default — shapes how heavy Autopilot's workload needs to be)
- **M6-Q9.** Right to be forgotten, coach-controlled retention, full export — confirm? *(yes to all — GDPR requires it anyway)*
- **M6-Q10.** 1:1 coaching only in v1, group/cohort programs later? *(yes, 1:1 only)*
- **M6-Q11.** Your sales gut: which single flow makes a coach say "I need this" — notes→summary, prep brief, or the client portal? (no default — that flow gets polished hardest across this whole journey)

**Autopilot, the nervous system running the timers (M8):**
- **M8-Q1.** First five recipes — pick from: silent-lead follow-up, overdue-invoice reminder, no-show recovery, pre-session prep brief, post-session notes nudge, testimonial ask, past-client re-engagement (90 days)? *(first five listed)*
- **M8-Q2.** Any recipe too sensitive for one-time approval, needing per-send approval instead? *(only invoice reminders auto-run after one-time approval; you veto specifics)*
- **M8-Q3.** Max messages/week to the same client across all automations? *(2)*
- **M8-Q4.** Confirm: timing + wording editable, but never a free-form workflow builder? *(correct, never)*
- **M8-Q5.** "What CoachOS did for you this week" activity log on the dashboard? *(yes)*
- **M8-Q6.** If an automation fails (e.g. Gmail disconnected), notify how? *(in-app + email, auto-retry 3x)*
- **M8-Q7.** One-switch vacation mode pausing everything? *(yes)*
- **M8-Q9.** Custom automations built by our team for a coach, as a paid service? *(yes — service revenue)*
- **M8-Q10.** Quiet hours — no client-facing sends outside 8am–8pm client-local? *(yes)*

---

## Journey 5 — The coach's content engine runs itself

**Connects:** Marketing Studio (M3) ↔ Daily Briefing (M10), sharing both a voice profile and a "share this insight" bridge.
**Functionality:** the coach's own writing trains a voice profile once; from then on, both session-theme-derived content (M3) and Briefing headlines (M10) can become a draft in that same voice, in the same approval queue.
**End goal:** the coach never faces a blank page — for their own marketing, or for reacting to industry news.
**Result the coach sees:** a content calendar that's mostly pre-filled, from two different source materials, in one consistent voice.

**Marketing Studio (M3):**
- **M3-Q1.** Channels day one — newsletter only, or + LinkedIn/Instagram post drafts (coach copy-pastes)? *(newsletter + LinkedIn drafts, no auto-posting)*
- **M3-Q2.** Auto-posting direct to social platforms — ever, and when? *(Phase 4+ evaluation)*
- **M3-Q3.** Writing samples needed for the voice profile? *(3 minimum)*
- **M3-Q4.** Content calendar: AI proposes a full month approved once, or week-by-week drafts? *(monthly plan, weekly drafts)*
- **M3-Q5.** Newsletter frequency? *(bi-weekly)* Subscriber list exportable anytime, confirm it's the coach's property? *(yes, always)*
- **M3-Q7.** AI drafting from anonymized session themes — explicit opt-in per coach? *(opt-in, off by default — privacy first)*
- **M3-Q8.** Lead-magnet download auto-subscribes to the newsletter with double opt-in (GDPR-clean)? *(yes)*
- **M3-Q9.** Our human team gets an "agency seat" for the done-for-you service? *(yes, coach still approves sends)*
- **M3-Q10.** Headline metric shown to coaches: opens/clicks, or "enquiries this content generated"? *(enquiries generated, front and center)*
- **M3-Q11.** Bulk newsletters send from our infrastructure in the coach's name (Gmail can't do bulk) — OK? *(yes)*

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

- **M7-Q1.** Rank the numbers a coach checks weekly: revenue, sessions booked, pipeline value, outstanding invoices, client retention, lead-response time. (no default)
- **M7-Q2.** At-risk client signals — which feel fair, which feel creepy: missed sessions, slowed replies, stalled goals, shorter sessions? *(missed sessions + stalled goals only — the conservative pair)*
- **M7-Q3.** Anonymized niche benchmarks ("coaches like you charge $X") — valuable or invasive? *(Phase 5, opt-in only)*
- **M7-Q4.** Simple revenue forecast from booked packages — v1 or later? *(later)*
- **M7-Q5.** Monday-morning digest + always-on dashboard, both? *(yes)*
- **M7-Q6.** Accountant export (income summary CSV) at launch? *(yes — cheap, loved at tax time)*
- **M7-Q7.** Revenue-target nudges ("62% to your $10k month") — motivating or pressure? *(optional, off by default)*
- **M7-Q8.** Time analytics (coaching vs. admin hours) — do coaches actually care? *(skip v1)*
- **M7-Q9.** Every insight comes with an action button, never a bare number — confirm this philosophy holds everywhere? *(yes)*
- **M7-Q10.** Coach-eyes-only — no client ever sees analysis about themselves? *(yes, absolute)*

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
