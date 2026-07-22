# 08 — Module Questionnaire (answer these, then each module gets its build prompt)

**How to use this doc:** answer in any order, in any form — chat message, voice-note transcript, partial answers, whatever. Reference questions as `M6-Q3` (Module 6, Question 3). **Every question has a default in italics** — if you don't answer it, the default is what gets built, so nothing blocks. Once a module's answers are in, I write its final build prompt and restructure the docs around it.

**Recommended order:** Module 6 first (it's Phase 1 — the wedge), then 4 → 5 → 1 → 2 → 3 → 8 → 9 → 10 → 7 → 11, matching build order. Section 12 (cross-cutting) matters early too — its answers shape everything.

---

## 0. Before the questions — three structural decisions already reflected below

### 0.1 White-label reality (the "open → closed source" instruction)

- Coaches and clients see **only CoachOS** — our screens, our names, our domain. The open-source engines (Cal.com, Documenso, Twenty, Listmonk, n8n) run **headless in the background**; no engine UI is ever shown to a user.
- We **cannot legally relicense** AGPL code as closed source — copying their code into ours would force our whole product open. So: engines stay separate hidden services; **our own code stays 100% proprietary**. If we ever modify an engine itself, we publish only that modification (or buy the vendor's commercial license, which Cal.com sells).
- Proposed in-product rebrand (confirm or rename in M12-Q1):

| Engine (hidden) | What the coach sees it as |
|---|---|
| Cal.com | **Bookings** — "CoachOS Calendar" |
| Documenso | **Agreements** — signing happens on a CoachOS-branded page |
| *(no engine — built natively)* | **Pipeline** — our own code end to end (see doc 09 §3) |
| Listmonk | Invisible — the sending engine behind **Marketing Studio** |
| Activepieces (MIT) | Invisible — the engine behind **Autopilot** and the Daily Briefing (replaced n8n, doc 09 §5) |
| Supabase | Invisible — infrastructure |

### 0.2 Failure isolation (the "one error shouldn't affect the rest" instruction)

Already the architecture (doc 07): each engine is a **separate running service**. If Documenso crashes, contracts pause but bookings, notes, marketing, and everything else keep working. Inside our own app, each module gets its own folder, own API routes, own database tables — connected only through defined events (below), never by reaching into each other's internals.

### 0.3 The interconnection map (the "everything interconnected" instruction)

These are the chains that make it one product instead of eleven tools. **Confirm, correct, or add chains:**

1. Directory enquiry → lands in **Leads pipeline** (M9→M2)
2. Landing-page capture → **Leads pipeline** + optional newsletter list (M1→M2, M1→M3)
3. Lead books discovery call → pipeline stage auto-advances (M4→M2)
4. Lead marked **Won** → client record auto-created in Client Delivery + agreement drafted (M2→M6, M2→M5)
5. Agreement signed → invoice sent + intake questionnaire sent + welcome sequence available (M5→M5, M5→M6, M5→M3)
6. Booking confirmed → session appears on client timeline + prep brief scheduled (M4→M6)
7. Session summary approved → client portal updated + progress timeline fed (M6→M6)
8. Package nearly used up → renewal proposal drafted for approval (M6→M2/M5)
9. Everything feeds **Practice Intelligence** — revenue (M5), sessions (M4/M6), pipeline (M2), engagement (M6) (all→M7)
10. **Autopilot** recipes are the timers that run chains 3–8 (M8 = the nervous system)
11. Briefing insight → "share to my audience" → Marketing Studio draft (M10→M3, later phase)
12. Community referral → colleague's Leads pipeline (M11→M2, later phase)

---

## Module 1 — Leads: Attract

*Inspired by: Leadpages, Typeform, HoneyBook lead capture, Kit (ConvertKit) landing pages.*

- **M1-Q1.** Coach's landing page: hosted on our domain (`coachos…/coach-name`) v1, custom domains later? *(Default: our domain v1.)*
- **M1-Q2.** Lead magnets at launch — PDF guide, quiz, free discovery call, email mini-course? Pick the two that matter. *(Default: PDF guide + free discovery call.)*
- **M1-Q3.** Page creation: AI interviews the coach and builds the whole page, or coach picks a template and AI writes the text? *(Default: templates + AI-written text.)*
- **M1-Q4.** Capture form: minimal (name, email, message) or qualifying questions (goal, budget, timeline)? *(Default: coach picks up to 3 extra questions from our question bank.)*
- **M1-Q5.** What makes a lead "hot" for a coach — budget signals, seniority, urgency, niche fit? What should the AI score on?
- **M1-Q6.** Paid-ads support (Meta/Google tracking pixels on landing pages) — launch or later? *(Default: later.)*
- **M1-Q7.** One landing page per coach, or one per offer/package? *(Default: up to 3, one per offer.)*
- **M1-Q8.** Leads from outside the landing page — manual add, forwarded emails, LinkedIn — which channels matter day one? *(Default: manual add + CSV import.)*
- **M1-Q9.** Testimonials on the page: self-entered by coach, or collected from real clients via the portal (more trustworthy)? *(Default: self-entered v1, portal-collected v2.)*
- **M1-Q10.** Arabic-language landing pages for the Gulf market — when? *(Default: English v1.)*
- **M1-Q11.** Success number: how many leads/month should a typical coach expect this module to produce before we call it working?

## Module 2 — Leads: Manage (Pipeline)

*Inspired by: Pipedrive, HubSpot, HoneyBook, Dubsado. Built natively — no external engine (doc 09 §3).*

- **M2-Q1.** Pipeline stages: fixed default (Enquiry → Discovery → Proposal → Won/Lost) or customizable? *(Default: default stages, coach can rename + add 2.)*
- **M2-Q2.** Silent-lead nudge: after how many days quiet does the AI nudge the coach with a drafted follow-up? *(Default: 6 days.)*
- **M2-Q3.** Coach's voice for AI drafts: trained once at onboarding from pasted samples, or continuously from sent emails? *(Default: once at onboarding, re-tunable anytime.)*
- **M2-Q4.** Proposals (packages + pricing sent to a lead): part of this module, or part of Agreements (M5)? *(Default: drafted here, signed in M5.)*
- **M2-Q5.** Lost leads: auto-offer to move them to the newsletter nurture list (with consent)? *(Default: yes, one-click with consent.)*
- **M2-Q6.** Booking a discovery call auto-advances the pipeline stage? *(Default: yes.)*
- **M2-Q7.** Should the coach always see *why* the AI scored a lead hot or cold? *(Default: yes — always explain.)*
- **M2-Q8.** Assistant/VA access to the pipeline — v1 or later? *(Default: solo coach v1, assistant seats Phase 3.)*
- **M2-Q9.** Same person enquires twice (directory + landing page): auto-merge? *(Default: auto-merge on matching email, coach notified.)*
- **M2-Q10.** Import from existing tools (spreadsheet, HoneyBook, Excel) at launch? *(Default: CSV import + our onboarding service does the migration — that's the paid setup fee.)*
- **M2-Q11.** Anything that must NEVER be stored on a lead (health details, etc.)? *(Default: we warn coaches not to store sensitive personal data on leads.)*
- **M2-Q12.** The pipeline is now built natively (doc 09 §3) with Twenty + EspoCRM as the design brief. From tools coaches already use (HoneyBook, Pipedrive, Dubsado, a spreadsheet): is there one pipeline feature they'd genuinely miss that we should make sure to include? *(Default: the design brief covers board view, saved filters, email activity on the lead record, and notes.)*

**Pipeline Agent (added after the agentic-AI instruction — full plan in doc 10):**

- **M2-Q13.** Should the agent run only when the coach clicks "investigate," or also proactively flag leads that need attention (e.g. a daily scan that surfaces 2–3 stalled leads on its own, still never sending without approval)? *(Default: both — on-demand button now, an optional daily proactive scan once trust is established.)*
- **M2-Q14.** How far should the agent be allowed to look ahead — one next message only, or a short multi-step plan ("message now, if no reply in 4 days suggest X")? *(Default: one next step at a time — simpler to trust, matches every other approval-first feature in the plan.)*
- **M2-Q15.** Should the agent's reasoning (why it picked this approach) always be visible to the coach, the way it is in the demo? *(Default: yes, always — same rule as Practice Intelligence's M7-Q9.)*

## Module 3 — Marketing Studio

*Inspired by: Kit (ConvertKit), Flodesk, Jasper, Buffer. Engine underneath: Listmonk (invisible).*

- **M3-Q1.** Channels day one: newsletter only, or newsletter + LinkedIn/Instagram post drafts (coach copies and pastes to post)? *(Default: newsletter + LinkedIn drafts, no auto-posting.)*
- **M3-Q2.** Auto-posting direct to social platforms (needs Meta/LinkedIn API approvals) — ever, and when? *(Default: Phase 4+ evaluation.)*
- **M3-Q3.** Voice profile: how many writing samples should onboarding ask for? *(Default: 3 samples minimum.)*
- **M3-Q4.** Content calendar: AI proposes a full month the coach approves once, or drafts week-by-week? *(Default: monthly plan, weekly drafts.)*
- **M3-Q5.** Newsletter frequency default? *(Default: bi-weekly.)* Subscriber list is the coach's property, exportable anytime — confirm. *(Default: yes, always.)*
- **M3-Q6.** Pre-built nurture sequences — pick top 3: new-lead welcome, post-discovery-didn't-close, past-client re-engagement, lead-magnet follow-up, testimonial ask? *(Default: first three.)*
- **M3-Q7.** AI drafting from anonymized session themes: explicit opt-in per coach? *(Default: opt-in, off by default — privacy-first.)*
- **M3-Q8.** Lead-magnet download auto-subscribes to newsletter with double opt-in (GDPR-clean)? *(Default: yes.)*
- **M3-Q9.** Done-for-you service: does our human team get an "agency seat" inside the coach's account to produce content? *(Default: yes — separate role, coach still approves sends.)*
- **M3-Q10.** Headline metric coaches see: opens/clicks, or "replies + enquiries this content generated"? *(Default: enquiries generated, front and center.)*
- **M3-Q11.** Sending addresses: 1:1 emails go via the coach's own Gmail, but bulk newsletters can't (Gmail caps ~500/day) — newsletters send from our infrastructure in the coach's name (industry standard). OK? *(Default: yes.)*

## Module 4 — Bookings

*Inspired by: Calendly, Acuity. Engine underneath: Cal.com (white-labeled — client sees a CoachOS-branded booking page).*

- **M4-Q1.** Package-aware booking: if a client has 3 of 6 sessions left, booking checks and shows remaining credits? (Generic schedulers can't do this — it's a differentiator.) *(Default: yes.)*
- **M4-Q2.** Calendar sync at launch: Google only, or Google + Outlook/Microsoft 365? *(Default: both — many corporate-adjacent coaches live on Outlook.)*
- **M4-Q3.** Video links auto-created: Google Meet, Zoom, or both? *(Default: Google Meet v1, Zoom Phase 3.)*
- **M4-Q4.** Pay-to-book for single sessions (card charged at booking, via M5)? *(Default: optional per session type.)*
- **M4-Q5.** Reminders: email at 24h + 1h is standard. WhatsApp reminders (huge in the Gulf) — priority or later? *(Default: email v1; WhatsApp Business API investigated Phase 3.)*
- **M4-Q6.** No-show handling: auto-flag + one-click rebook invite + optional no-show fee? *(Default: flag + rebook; fees off by default.)*
- **M4-Q7.** Sensible defaults — 15-min buffer between sessions, max sessions/day cap? *(Default: 15-min buffer, no daily cap.)*
- **M4-Q8.** Group sessions/workshops (multiple attendees) — v1 or later? *(Default: 1:1 only v1; groups are a Phase 4 decision.)*
- **M4-Q9.** Client self-serve rescheduling allowed up to how many hours before? *(Default: 24h, coach-adjustable.)*
- **M4-Q10.** Confirm the chain: booking confirmed → session on client timeline → prep brief scheduled → reminder sent. *(Default: yes, fully automatic.)*
- **M4-Q11.** Booking page 100% CoachOS-branded on our domain — no third-party name visible anywhere? *(Default: yes — hard requirement.)*

**Meetings & recordings (added after the meetings instruction — full comparison in doc 09 §8):**

- **M4-Q12.** What do your target coaches run sessions on today — Zoom, Google Meet, Teams, phone/WhatsApp calls? Rank them. This decides which recording integration we build first. *(Default: Zoom first — it's the only one whose recordings AND transcripts pull in fully automatically.)*
- **M4-Q13.** In-platform video ("CoachOS Meet," sessions happen inside CoachOS itself, Phase 3+): must-have, nice-to-have, or skip-if-Zoom-works? *(Default: nice-to-have — Phase 3 decision gate.)*

## Module 5 — Agreements & Billing

*Inspired by: PandaDoc, Dropbox Sign, Bonsai, HoneyBook, Stripe Invoicing. Engines underneath: Documenso + Stripe (invisible).*

- **M5-Q1.** **Which market's coaches first?** Contract templates need per-jurisdiction legal review — UAE, UK, US, EU? This is the biggest decision in the module. *(Default: English-language templates reviewed for one jurisdiction first — you pick which.)*
- **M5-Q2.** Payments: Stripe covers cards globally — do Gulf coaches need local methods (bank transfer, Tabby)? *(Default: Stripe cards v1 + "mark as paid manually" for bank transfers.)*
- **M5-Q3.** Pricing models supported at launch: one-off session, package (e.g. 6 sessions), monthly retainer, subscription — all or subset? *(Default: one-off + package v1; retainer/subscription Phase 3.)*
- **M5-Q4.** Auto-invoice the moment the agreement is signed? *(Default: yes.)* VAT: coach enters their own rate (UAE 5%, UK 20%…)? *(Default: yes, per-coach tax rate field.)*
- **M5-Q5.** Currency: one currency per coach, or multi-currency? *(Default: one per coach.)*
- **M5-Q6.** Late invoices: reminder at +3 and +7 days, then coach decides? *(Default: yes — never auto-suspend a client's sessions without the coach clicking it.)*
- **M5-Q7.** Deposits / split payments for large packages? *(Default: 50% deposit option v1.)*
- **M5-Q8.** Money flow: client pays → coach's own Stripe account → coach's bank. **We never hold or touch the money** (keeps us out of financial licensing entirely). Confirm. *(Default: yes — strongly recommended.)*
- **M5-Q9.** Our own revenue: flat SaaS subscription only, **no commission** on coach revenue (matches the Directory promise)? *(Default: yes, no commission ever.)*
- **M5-Q10.** E-signature: simple e-sign (click-to-sign on phone, legally binding in most jurisdictions) — sufficient, or does any market need ID verification? *(Default: simple e-sign.)*
- **M5-Q11.** Refund/cancellation policy: coach-defined, shown to client before signing? *(Default: yes, template provided.)*

## Module 6 — Client Delivery (THE WEDGE — answer this one first)

*Inspired by: Paperbell, CoachAccountable, Quenza, Practice, Delenta — plus Fathom/Otter for AI session notes.*

- **M6-Q1.** Note input v1 is typed/pasted notes. The recording path now has a full plan (doc 09 §8): upload-any-recording → transcript → AI summary (Phase 1.5), Zoom auto-pull (Phase 2), in-platform CoachOS Meet (Phase 3+). Should the upload path move INTO Phase 1 as part of the wedge — it's a demo-killer feature? *(Default: Phase 1.5 — right after the typed-notes core works.)*
- **M6-Q1b.** Recording consent: captured once in the coaching agreement + visible notice each recorded session, coach controls retention/deletion. Enough, or should the client also confirm per-session in the portal? *(Default: agreement + in-session notice.)*
- **M6-Q2.** Summary outputs: client-facing email + coach private note + action items. Anything else — homework list, next-session agenda? *(Default: those three + next-session agenda.)*
- **M6-Q3.** Client portal — what do clients SEE: approved summaries, action items, goals, session history, shared files? And confirmed NEVER: coach's private notes. *(Default: all five visible; private notes never, enforced at the database level.)*
- **M6-Q4.** Homework/tasks between sessions with client check-off + reminders (the heart of CoachAccountable/Quenza)? *(Default: v1.1 — right after the wedge core.)*
- **M6-Q5.** Goals: formal (milestones, target dates) or lightweight themes? Who sets them — coach, client, or together? *(Default: lightweight goals with optional milestones, coach-created.)*
- **M6-Q6.** Prep brief: 30 min before, in-app + email, containing last session's commitments, open blockers, stalled goals? *(Default: exactly that.)*
- **M6-Q7.** Reality check for scale + pricing: how many **active clients** does a typical pilot coach carry? (10? 25? 50?)
- **M6-Q8.** Intake: new-client questionnaire (coach-customizable) auto-sent when the agreement is signed? *(Default: yes, Phase 2.)*
- **M6-Q9.** Data sensitivity: session notes are deeply personal. Client's right to be forgotten, coach-controlled retention, full export — confirm. *(Default: yes to all — GDPR requires it anyway.)*
- **M6-Q10.** 1:1 coaching only in v1 — group/cohort programs are a separate later build? *(Default: yes, 1:1 only.)*
- **M6-Q11.** Your sales gut: in a live demo, which single flow makes a coach say "I need this" — notes→summary, the prep brief, or the client portal? (We polish that one hardest.)

## Module 7 — Practice Intelligence

*Inspired by: Baremetrics, ChartMogul, HubSpot dashboards, the Stripe dashboard.*

- **M7-Q1.** Rank the numbers a coach checks weekly: revenue this month, sessions booked, pipeline value, outstanding invoices, client retention, lead-response time.
- **M7-Q2.** At-risk client signals — which are fair, which feel creepy: missed sessions, slowed replies, stalled goals, shorter sessions? *(Default: missed sessions + stalled goals only — the conservative pair.)*
- **M7-Q3.** Anonymized benchmarks ("coaches in your niche charge $X") — valuable or invasive? *(Default: Phase 5, opt-in only.)*
- **M7-Q4.** Simple revenue forecast from booked packages — v1 or later? *(Default: later.)*
- **M7-Q5.** Delivery: Monday-morning email digest + always-on dashboard? *(Default: yes.)*
- **M7-Q6.** Accountant export (income summary CSV) at launch? *(Default: yes — cheap to build, loved at tax time.)*
- **M7-Q7.** Coach's own revenue target ("62% to your $10k month") — motivating or pressure? *(Default: optional, off by default.)*
- **M7-Q8.** Time analytics (hours coaching vs admin) — do coaches care? *(Default: skip v1.)*
- **M7-Q9.** Every insight has an action button ("3 silent leads → review drafted follow-ups") — never a number without a next step. Confirm philosophy. *(Default: yes.)*
- **M7-Q10.** Intelligence is coach-eyes-only — no client ever sees analysis about themselves. Confirm. *(Default: yes, absolute.)*

## Module 8 — Autopilot (Automations)

*Inspired by: Zapier, Make, HoneyBook automations. Engine underneath: Activepieces (invisible — MIT-licensed, replaced n8n per doc 09 §5).*

- **M8-Q1.** First five recipes — pick from: silent-lead follow-up, overdue-invoice reminder, no-show recovery, pre-session prep brief, post-session "write your notes" nudge, testimonial ask when a package ends, past-client re-engagement after 90 days. *(Default: first five listed.)*
- **M8-Q2.** Approval model: coach approves exact wording once when enabling a recipe; after that it runs on schedule. Are there recipes where even that's too autonomous and every send needs individual approval? *(Default: client-facing money messages — invoice reminders — run auto after one-time approval; everything else too. You veto specifics.)*
- **M8-Q3.** Frequency cap: never message the same client more than X times/week across all automations? *(Default: 2.)*
- **M8-Q4.** Customization: timing + wording editable, but no free-form workflow builder — ever? *(Default: correct, never — recipes only.)*
- **M8-Q5.** "What CoachOS did for you this week" activity log — on the dashboard? *(Default: yes.)*
- **M8-Q6.** If an automation fails (e.g. Gmail disconnected): notify coach how? *(Default: in-app alert + email, auto-retry 3 times.)*
- **M8-Q7.** Vacation mode — one switch pauses all automations? *(Default: yes.)*
- **M8-Q8.** Which cross-module chain matters most: package-ending → renewal draft, won-lead → onboarding sequence, or signed-agreement → intake + invoice? *(Default: all three ship as recipes.)*
- **M8-Q9.** Custom automations built by our human team for a coach, as a paid service (we build it in the hidden engine; coach sees it as "my custom recipe")? *(Default: yes — service revenue.)*
- **M8-Q10.** Quiet hours: no client-facing sends outside 8am–8pm client-local time? *(Default: yes.)*

## Module 9 — Coach & Mentor Directory

*Inspired by: Noomii, Psychology Today's directory (the best model in any adjacent industry), Bark, Malt.*

- **M9-Q1.** **Who can list:** only paying CoachOS subscribers, or free/cheap listings open to all coaches (grows supply + becomes its own revenue line — Psychology Today charges ~$30/mo for listing alone)? *(Default: CoachOS subscribers included; listing-only tier considered Phase 4.)*
- **M9-Q2.** Verification: we manually verify credentials (ICF certs etc.) and show a "Verified" badge? *(Default: yes — manual at first, it's our trust moat.)*
- **M9-Q3.** Search filters — rank: specialty, price range, language, timezone/location, industry background, session format.
- **M9-Q4.** Matching: browse + filter v1; "answer 5 questions, we suggest 3 coaches" AI matching v2? *(Default: yes, that split.)*
- **M9-Q5.** Public star ratings: coaches broadly hate them; Noomii uses curated testimonials instead. Coach-curated testimonials only? *(Default: testimonials only, no star ratings.)*
- **M9-Q6.** Enquiry flow: straight into the coach's pipeline; if no response in 48h, gently suggest similar coaches to the client? *(Default: yes — supply-side accountability without punishing coaches publicly.)*
- **M9-Q7.** Pricing display: public rates, ranges, or "enquire" — per-coach choice? *(Default: per-coach choice.)*
- **M9-Q8.** Standard CTA on every profile: "Book a free discovery call" (feeds M4)? *(Default: yes.)*
- **M9-Q9.** Launch geography: global-English, or Gulf/UAE-first where your network is? *(Default: global-English site, Gulf-first recruitment push.)*
- **M9-Q10.** Featured/premium placement slots — extra revenue, or does it corrupt trust? *(Default: no paid placement v1; revisit with data.)*
- **M9-Q11.** Each profile is a public SEO page — explicit coach consent at listing time. *(Default: yes, consent required.)*

## Module 10 — Daily Briefing

*Inspired by: Morning Brew (tone), Blinkist (5-minute learn), Feedly (aggregation).*

- **M10-Q1.** Launch categories: executive coaching, career coaching, life/wellness — plus "business of coaching" (marketing yourself, pricing, tools)? *(Default: those four.)*
- **M10-Q2.** The 5-minute learn: coaching techniques, business skills, AI-for-coaches — rotate or pick a lane? *(Default: rotate across all three.)*
- **M10-Q3.** Volume: 5 niche headlines + 3 world items + 1 learn per day? *(Default: yes.)*
- **M10-Q4.** Delivery: in-app + optional email version? *(Default: both.)*
- **M10-Q5.** Public site: full briefing free (max SEO), or teaser + sign-up for the rest (conversion)? *(Default: full and free — it's an acquisition channel, not a product.)*
- **M10-Q6.** Publish time anchor: ready by 6am Gulf time? *(Default: yes — one daily generation.)*
- **M10-Q7.** "Share this insight" → creates a LinkedIn draft in Marketing Studio? *(Default: yes, Phase 4.)*
- **M10-Q8.** Sponsor slots someday (the Morning Brew model) — keep the door open? *(Default: door open, nothing built.)*
- **M10-Q9.** Editorial ownership: who on your team does the daily glance-and-correct via the Claude/MCP tool? (Name a person/role.)
- **M10-Q10.** English only at launch? *(Default: yes.)*

## Module 11 — Coach Community

*Inspired by: LinkedIn (mechanics), Circle & Skool (spaces), Mighty Networks.*

- **M11-Q1.** **Confirm the standing assumption: coach-to-coach only — clients never enter the community.** Right? *(Default: yes, coaches only.)*
- **M11-Q2.** Real names + verified-coach status required to post? *(Default: yes — it's a professional network, not a forum.)*
- **M11-Q3.** Post types v1: text, image, link. Video and polls later? *(Default: yes.)*
- **M11-Q4.** One global feed v1 with niche tags; dedicated spaces ("Executive coaches") v2? *(Default: yes.)*
- **M11-Q5.** Moderation day one: your team moderates; I draft the community guidelines? *(Default: yes.)*
- **M11-Q6.** Connections: LinkedIn-style request/accept (as specced), not follow-style? *(Default: request/accept.)*
- **M11-Q7.** Promotion rules: referrals and collaboration are the point — but how do we stop it becoming a spam wall? (e.g. no cold-pitching in comments, dedicated "referrals" tag?) *(Default: dedicated referral post type; cold-pitch DMs reportable.)*
- **M11-Q8.** Events (virtual meetups, webinars) inside the community — v2+? *(Default: v2+.)*
- **M11-Q9.** Gamification (points, top-contributor badges) — or keep it calm and professional? *(Default: calm, none.)*
- **M11-Q10.** The killer interconnect: a **referral hand-off** — "this lead isn't my niche → refer to a colleague → lands in their pipeline, tracked." Build as Community v2 flagship? *(Default: yes — it turns the community into revenue for coaches.)*
- **M11-Q11.** v2 messaging: coach-to-coach DM only (no client messaging here), tied to the referral flow? *(Default: yes.)*

## Section 12 — Cross-cutting (shapes everything — answer early)

- **M12-Q1.** In-product module names — approve or rename: **Attract**, **Pipeline**, **Marketing Studio**, **Bookings**, **Agreements & Billing**, **Clients** (delivery), **Insights** (intelligence), **Autopilot** (automations), **Directory**, **Daily Briefing**, **Community**.
- **M12-Q2.** Packaging: every plan gets all modules with different limits, or tiers by module (Starter = Clients + Bookings; Pro = + Pipeline/Marketing/Agreements; Studio = everything)? Price anchors still $29–59? *(Default: module-tiered as sketched.)*
- **M12-Q3.** **First market** (drives legal templates, payment methods, VAT, WhatsApp priority): UAE/Gulf, UK, US, or global-English? Single most consequential unanswered question in the plan.
- **M12-Q4.** Mobile: responsive web (works on any phone) v1; native app-store apps only if coaches demand it? *(Default: responsive web + installable PWA.)*
- **M12-Q5.** Languages: English v1; Arabic — Phase what?
- **M12-Q6.** Data hosting region v1: EU region (safest for GDPR, fine for UAE) — confirm? *(Default: EU, single region.)*
- **M12-Q7.** White-label hardness: zero third-party branding anywhere a coach or client looks — any acceptable exception (e.g. tiny "powered by" in email footers)? *(Default: zero exceptions.)*
- **M12-Q8.** Support model at launch: help docs + email/chat support + paid done-with-you onboarding. Who staffs support — you, a hire, our done-for-you team? *(Default: founder + docs at pilot stage.)*
- **M12-Q9.** Reliability promise in our terms: 99.5% uptime target, daily backups, 30-day retention — sane starting commitment? *(Default: yes.)*
- **M12-Q10.** When a coach adds an assistant: which modules can a VA touch — Pipeline + Bookings + Marketing drafts, but never session notes? *(Default: exactly that, Phase 3.)*
- **M12-Q11.** Sales reality: which two modules will YOU personally demo in every sales call? (Those get the deepest polish first.)

**Sync & migration (added after the max-sync instruction — see doc 07 §8 for the full sync table):**

- **M12-Q12.** Which tools do your target coaches live in today — rank: Google Calendar + Gmail, Outlook + Microsoft 365, WhatsApp, Zoom, Excel/Google Sheets, Calendly, Notion? This sets sync build order. *(Default: Google-first, Outlook a close second.)*
- **M12-Q13.** Calendar sync depth: full two-way (their existing events block our booking slots, and our bookings appear in their calendar instantly)? *(Default: yes, two-way from day one — Cal.com does this natively.)*
- **M12-Q14.** Google Contacts: optional one-time import at onboarding (with the coach's consent) so their existing contacts are ready to become leads/clients? *(Default: yes, Phase 2–3.)*
- **M12-Q15.** Migration importers beyond CSV — should our done-with-you onboarding service handle specific tools (HoneyBook export, Calendly event types, Mailchimp lists)? Name the ones your pilot coaches actually use. *(Default: CSV universal + onboarding service handles the rest manually.)*

**Interlink editability (added after the editable-connections instruction — see doc 07 §7):**

- **M12-Q16.** Every module-to-module chain is a named connection the coach can switch on/off and tune (timing, wording, trigger stage). Should any chains be locked always-on because turning them off breaks the product (e.g. "booking → session on timeline")? *(Default: yes — structural chains locked, behavioral chains [nudges, sequences, publishing] fully editable.)*
- **M12-Q17.** Should our team be able to custom-wire extra connections for a coach as a paid service (built on the hidden engine, appears as "my custom recipe")? *(Default: yes — same answer as M8-Q9, confirming it applies to interlinks too.)*

---

## What happens with your answers

1. You answer (any subset, any order — `M6-Q3`-style references, or just talk naturally).
2. Unanswered questions take the italic defaults.
3. Per module, I write the **final build prompt** — frontend, backend, security, data model, interconnection events, white-label requirements — and restructure docs 02/05 around them.
4. Build order stays phase-gated: Module 6 first, nothing else until it earns paying coaches.
