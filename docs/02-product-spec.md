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
