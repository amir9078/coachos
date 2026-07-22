# 05 — Build Guide: CoachOS with Claude Code + Cursor

How a non-technical founder (plus, later, a part-time developer) actually builds CoachOS. This guide pins the stack, the data model, the frontend, and a functionality-by-functionality build order — each with the exact working method for Claude Code and Cursor.

**Scope note:** this guide covers Phase 1 (the wedge) in full detail and sketches Phases 2–3. Do not pre-build Phase 2+.

---

## 1. The two tools and how to use them together

| | Claude Code | Cursor |
|---|---|---|
| What it is | An AI agent that plans, writes, runs, and tests whole features from plain-English instructions | An AI-powered code editor — you (or your future dev) see and touch the code directly |
| Best at | Building complete features end-to-end, refactors, debugging across many files, running tests/terminal | Quick in-file edits, reviewing what was built, small visual tweaks with instant preview |
| Your usage | **Primary build tool.** 90% of the product gets built here | **Review-and-touch-up tool.** Open the project, read what changed, make small copy/style edits |

**The working loop (repeat per feature):**
1. Write the feature request in plain English (this guide gives you the exact briefs in §6).
2. Claude Code plans → builds → runs it. You watch it work.
3. Open the app in the browser and test it *as a coach would*. Wrong flow or tone? Say so in plain English.
4. When it feels right: tell Claude Code to write automated tests for the feature and commit.
5. Open in Cursor if you want to read the code, tweak copy/colors, or have its AI explain any file ("explain this file like I'm non-technical").

**Non-negotiable habits:** one feature per session; commit after every working feature (rollback insurance); never paste API keys into code — they go in `.env.local`; push to the private GitHub repo at the end of every working day.

## 2. The stack (pinned — don't relitigate per feature)

**Status note:** this table covers Phase 1 (Module 6, the wedge) as actually decided across docs 02/07/08/09/10/11. Phase 2+ rows are placeholders, rewritten when we build those modules.

| Layer | Choice | Why |
|---|---|---|
| Frontend + backend framework | **Next.js (App Router) + TypeScript** | One codebase for UI and API routes; the ecosystem Claude Code and Cursor know best; deploys to Vercel in minutes |
| Styling | **Tailwind CSS** + design tokens from the prototype | Fast, consistent; tokens in §5 |
| Database, auth, storage | **Supabase** (Postgres + Auth + Row-Level Security + Storage) | Hosted, free tier to start, auth built in, RLS gives per-coach data isolation at the database level |
| AI | **Claude API — Haiku 4.5** for summaries/briefs/drafts (server-side only) | Verified cost ≈ $0.03/coach/mo; upgrade individual calls to Sonnet only if quality demands |
| Agent layer | **Mastra** (Apache 2.0) — powers the Pipeline Agent (Module 2, Phase 3) | Not needed for Module 6 itself; noted here since it shares the AI layer (doc 10) |
| Scheduling (Phase 2) | **Cal.com embed** | Don't build a scheduler |
| Meetings, recording, transcripts (Phase 1 — moved up, doc 09 §8) | **LiveKit** (Apache 2.0) on one small, separate test VPS, independent of the main app's hosting | Native UDP needed for real call quality — Vercel/Railway can't provide this, so it runs standalone rather than gating the feature on the Phase 3+ infra migration (doc 07 §6). Zoom pull-in is the fallback path for coaches already on Zoom. |
| Speech-to-text | A commercial STT API at launch (pennies/hour), self-hosted Whisper later if volume justifies it | doc 09 §8 |
| Payments (Phase 2) | **Stripe** + **Stripe Connect** for coaches without their own account | doc 02 Module 5 |
| E-sign (Phase 2) | **Documenso** (self-host or API) | See doc 03 |
| Hosting | **Vercel** (Phase 1) → **Railway** (Phase 2, for Documenso etc.) → **Coolify + VPS** (Phase 3+) | Full phased reasoning in doc 07 §6 |
| Email sending | **Email Connections** — Gmail, Microsoft 365, or custom-domain SMTP/IMAP, multiple named connections per coach (doc 02 Module 3); **Resend as the zero-setup fallback** until a coach connects their own | Module 6 itself only needs the fallback path — full Email Connections build happens with Module 3 |

**Accounts you need before starting:** GitHub (done), Supabase, Vercel, Anthropic Console (API key), Resend. All free tiers. Keys go into `.env.local` and Vercel's environment settings — never into git. (Cal.com, LiveKit test VPS, and Email Connections accounts come with their respective modules, not needed for Module 6's first build.)

## 3. Application structure (tell Claude Code to scaffold exactly this)

```
coachos-app/                      ← separate PRIVATE repo from this planning repo (docs/11's confidentiality rules apply here too)
├── CLAUDE.md                     ← project rules for Claude Code (template in §7)
├── .cursorrules                  ← same rules for Cursor
├── .env.local                    ← secrets (git-ignored)
├── app/
│   ├── (marketing)/page.tsx      ← public landing page (from prototype design)
│   ├── (app)/
│   │   ├── dashboard/            ← "Today" pane
│   │   ├── clients/              ← list + [id] detail (timeline, notes, summaries, homework, goals)
│   │   ├── meetings/[session_id]/← CoachOS Meet room (LiveKit) + recording/transcript status
│   │   ├── leads/                ← Phase 3
│   │   ├── marketing/            ← Phase 3
│   │   ├── bookings/             ← Phase 2 (Cal.com embed + session list)
│   │   ├── contracts/            ← Phase 2
│   │   └── settings/
│   │       ├── voice/            ← the AI Voice & Rules Center (doc 02 cross-cutting)
│   │       ├── data/             ← per-client retention period, export, delete
│   │       └── delivery/         ← portal on/off, portal branding, delivery mode
│   ├── portal/[token]/           ← optional client-facing portal (no login; signed link, doc 02 Module 6)
│   └── api/
│       ├── ai/summarize/route.ts ← notes → configurable outputs (server-side Claude call)
│       ├── ai/prep-brief/route.ts
│       ├── meetings/[session_id]/route.ts   ← LiveKit room create/join
│       ├── meetings/[session_id]/recording/route.ts  ← egress webhook → transcript pipeline
│       └── ai/draft/route.ts     ← Phase 3 (follow-ups, posts)
├── components/                   ← UI kit: Button, Card, Pill, Timeline, ApprovalQueue, MeetingRoom, HomeworkList…
├── lib/                          ← supabase client, ai client, email, livekit, utils
└── supabase/migrations/          ← schema as SQL migrations (versioned)
```

## 4. Backend guide

### 4.1 Data model — Module 6, as actually decided (docs 02/08 §Journey 3–4)

Tell Claude Code to create these tables via Supabase migrations. This supersedes the earlier draft — the biggest structural change is `clients` splitting into a **unit** (the engagement) and its **members** (the actual people), to support family/team coaching, not just 1:1.

| Table | Key fields | Notes |
|---|---|---|
| `coaches` | id (=auth user), name, niche_pack, timezone, portal_enabled bool, delivery_mode (`email_only`\|`email_and_portal`), retention_period_months int (null = indefinite) | One row per signed-up coach. Voice now lives in its own table below, not a single jsonb column. |
| `voice_profiles` | coach_id (unique), about_me text, writing_samples jsonb, parameters jsonb (the 15 tunable values from doc 02's AI Voice & Rules Center), rules jsonb (per-content-type overrides) | Powers every AI draft in the whole product, not just this module — built here first since summaries are the first consumer |
| `client_units` | id, coach_id, unit_type (`individual`\|`family`\|`team`), display_name, status | The engagement-level record. 1:1 is `unit_type='individual'` with exactly one member below — same shape, not a special case. |
| `client_members` | id, client_unit_id, name, email, phone, portal_token, is_primary_contact bool | One row per actual person. One row for 1:1; multiple for family/team. **Each member gets their own portal_token** (assumption — confirm before build if a family should instead share one token). |
| `engagements` | id, client_unit_id, package_name, sessions_total, sessions_used, start/end | e.g. "Executive package · 12 sessions" |
| `goals` | id, client_unit_id, title, status (active/stalled/done), last_progress_at, **editable_by_client bool default false** | The permission toggle from doc 02 — coach-only by default, coach can grant client edit access |
| `homework_tasks` | id, client_unit_id, title, status (open/done), source_session_id, due_before_session_id, reminder_sent_at | New table — built from Phase 1, not deferred |
| `sessions` | id, client_unit_id, scheduled_at, status, cal_booking_id (Phase 2), **meeting_provider** (`livekit`\|`zoom`\|`upload`\|`none`), **recording_url**, **recording_consent_notified_at**, **transcript_text**, **transcript_status** (`pending`\|`ready`\|`failed`\|`none`) | Recording/transcript fields are new — built in from day one, not bolted on |
| `session_notes` | id, session_id, raw_notes text, created_at | The coach's rough input (typed, or the transcript once ready) |
| `summaries` | id, session_id, **outputs_enabled jsonb** (which of the 4 the coach wants: client_email, coach_note, action_items, next_agenda), client_facing text, coach_facing text, action_items jsonb, next_agenda text, status (draft/approved/sent), **delivery_mode_used** (email/portal/both), sent_at | `outputs_enabled` makes the four outputs coach-configurable, per doc 02; `status` stays the approval-first gate |
| `client_data_events` | id, client_unit_id, event_type (`retention_notice_sent`\|`deleted`\|`exported`), at | The pre-deletion notice trail doc 02 requires — separate from `audit_log` since these are client-rights events specifically, easy to query for compliance |
| `audit_log` | id, coach_id, action, entity, at | Who sent what when — trust feature, unchanged |

**Phase 2+ tables** (`contracts`, `invoices`, `stripe_connect_accounts`, `coach_email_accounts`, `leads`, `pipeline_stages`, custom fields, `proposals`, Autopilot/Community/Directory/Briefing tables) are sketched further down in this doc but **stale relative to this session's decisions** — each gets its own rewrite pass when we build that module, same as this section just got for Module 6.

### 4.2 Security rules (give these to Claude Code verbatim)

- **Row-Level Security ON for every table**: a coach can only ever read/write rows where `coach_id = auth.uid()` (directly, or via `client_unit_id → client_units.coach_id` for the tables that key off the unit). Test this explicitly with two test accounts.
- **All Claude API calls happen in server routes** (`app/api/ai/*`). The API key exists only in server env. Nothing AI-related runs in the browser.
- **Client portal is optional per coach** (`coaches.portal_enabled`) and uses unguessable signed tokens per `client_member`, read-only, showing only `approved`+`sent` artifacts, homework, and goals (respecting `editable_by_client` for whether checkboxes/edits are writable).
- **Nothing sends without approval**: emails to clients fire only on an explicit approve action that flips `summaries.status` → `approved` and logs to `audit_log`.
- **Recording consent**: the moment a session starts recording, log a visible notification to the client and stamp `sessions.recording_consent_notified_at` — this is the actual compliance record, not just a UI toast that could go unlogged.
- **Data retention**: a scheduled job checks `coaches.retention_period_months` against each `client_unit`'s last activity; before deleting anything, write a `client_data_events` row (`retention_notice_sent`) and notify the client, then only actually delete after that notice — never delete silently.
- AI outputs are labeled "AI-drafted, coach-approved" (EU AI Act limited-risk disclosure).
- No client data in AI prompts beyond what the feature needs; never send the whole client table to the model.

### 4.3 The AI endpoints (Phase 1)

**`POST /api/ai/summarize`** — input: `session_id`. Server loads raw notes (typed or transcript) + client name + goals + the coach's `voice_profiles` row + `summaries.outputs_enabled` for that coach; calls Claude Haiku with a prompt like:

> You are the assistant of a professional {niche} coach. Voice: {voice_profile parameters + about_me + rules}. From the coach's session notes, produce only the outputs enabled: {client-facing summary email if enabled} (max 180 words), {private coach-facing note if enabled}, {1–4 action items as JSON if enabled}, {one-line next-session agenda if enabled}. Never invent facts not in the notes. Output JSON matching only the enabled fields.

Saves result to `summaries` with `status='draft'`. **The model never emails or posts to the portal** — the approve action does, and it respects `coaches.delivery_mode` (email only, or email + portal link).

**`POST /api/ai/prep-brief`** — input: `client_unit_id`. Loads last 3 summaries + goals + open homework; returns a 120-word brief: commitments made, blockers, stalled goals. Displayed 30 min before session (and on demand). Built from Phase 1, not deferred.

**Homework endpoints** — `POST /api/homework` (create, from a session or standalone), `PATCH /api/homework/[id]` (coach edits, or client checks off via the portal if `portal_enabled`), reminder job checks `due_before_session_id` and nudges if still open close to the next session.

**Goals endpoints** — `PATCH /api/goals/[id]` checks `editable_by_client` before allowing a portal-authenticated (token-based) write; coach's own session always allowed.

Cost guard: log tokens per call into `audit_log`; alert at $20/mo (it should never get there — verified math in doc 04).

### 4.3b Meetings — CoachOS Meet, recording, and transcripts (Phase 1, moved up — doc 09 §8)

**Infrastructure:** LiveKit (Apache 2.0), self-hosted on one small, separate test VPS — plain Docker Compose, independent of the main app's Vercel/Railway hosting (doc 07 §6). This is deliberately not the full Phase 3+ Coolify migration; it's a narrow, cheap exception made specifically because native UDP (which Railway can't provide) matters for real call quality.

**`POST /api/meetings/[session_id]/create`** — generates a LiveKit room + a join token for the coach and each `client_member` on that session. The join experience is a plain link — click, browser opens the room, no app download, matching the "as easy as Google Meet" target.

**Recording flow:** LiveKit Egress records the room to storage → webhook hits `POST /api/meetings/[session_id]/recording` → stamps `sessions.recording_url`, sets `transcript_status='pending'` → sends the file to the speech-to-text service → on completion, writes `sessions.transcript_text`, sets `transcript_status='ready'`, and the transcript becomes available as input to `session_notes`/`/api/ai/summarize` exactly like typed notes would be.

**Consent, not optional:** the moment recording starts, the client-facing meeting UI shows a visible, unmissable notice, and the backend stamps `recording_consent_notified_at` (§4.2) — this has to happen before or at recording start, not logged after the fact.

**Zoom fallback path (also Phase 1):** for coaches already on Zoom, a connect-once OAuth flow pulls the Cloud Recording + VTT transcript automatically after each call via Zoom's API — same downstream pipeline (transcript → `session_notes` → summarize), different upstream source. Google Meet's API can be pulled from too, with the caveat (verified, doc 09 §8) that it requires the coach's own Workspace plan to support it and recording must be started manually in-meeting.

### 4.4–4.7 — STALE, pre-questionnaire draft (not Module 6's concern; rewritten when we build each of these)

Everything from here to the end of §4 predates this session's questionnaire pass and contradicts several decisions already locked in — most importantly: **Gmail is now one of three Email Connections options** (doc 02 Module 3), **the automation engine is Activepieces, not n8n** (doc 09 §5), **Autopilot gets a real constrained workflow builder now** (reversed from "never," doc 08 M8-Q4), and **Community is a fully open public network with a two-tier profile system**, not the closed coach-only peer network described below (doc 08 §Journey 7). Kept here only as a rough placeholder of *what exists*, not *how it's built* — do not hand this to Claude Code as-is for any module past 6.

### 4.4 Gmail, Automations & Directory endpoints (Phase 2–3)

**`GET/POST /api/integrations/gmail/oauth`** — Google OAuth2 flow; stores encrypted tokens in `coach_email_accounts`. All summary/follow-up sends check for a connected account first and fall back to Resend if none exists.

**`POST /api/automations/[recipe_id]/toggle`** — enables/disables a pre-built recipe for a coach; stores their one or two customizable variables (delay days, template choice). The trigger check itself runs as a scheduled n8n workflow that calls back into `/api/automations/run` — which only ever sends a template the coach already approved when enabling the recipe, never freshly-generated text.

**`GET /api/directory/search`** and **`POST /api/directory/[slug]/enquire`** — public, unauthenticated routes; search uses Supabase full-text search over `coach_profiles`; an enquiry writes to `directory_enquiries` and immediately creates a row in that coach's own `leads` table.

**Security note — the one deliberate exception to "private by default":** `coach_profiles` is the single table designed to be publicly readable, and only for fields the coach explicitly opts to publish. Build the opt-in toggle and field-level publish controls before this table is reachable by any public route — see §4.2's RLS rule, which still applies to every *other* table without exception.

### 4.5 Daily Briefing endpoints (Phase 3)

**n8n is the default engine for every part of this pipeline — scheduled and manual alike, not a bespoke script.** Two n8n workflows, both already living in the same self-hosted n8n instance doc 03 uses for Automations (Module 8):

1. **Scheduled workflow** — runs once a day per category, pulls Wikipedia's Current Events Portal (world catchup) and the verified publisher RSS feeds (category news, learn) listed in doc 03, has Claude summarize each into the digest, and writes into `briefing_days` + `briefing_headlines` with `created_by='system'`.
2. **On-demand workflow** — the exact same pull-and-summarize logic, but triggered by an n8n webhook instead of the clock. This is what the admin MCP server's `refresh_category_now` tool calls (§4.6) — the MCP server never re-implements the pull logic itself, it just triggers the same workflow the scheduler uses.

Every request that follows just reads the cached data — no live API call per request. **Before adding any new publisher feed to the curated list, verify its actual terms directly (fetch the feed, check for a stated license) — doc 03 documents four sources that looked free but weren't when actually checked; don't repeat that by assumption here.**

**`GET /api/briefing/[category]`** — public, unauthenticated, cached (this is the endpoint both the dashboard card and the public `/briefing` marketing page call — same data, two surfaces). No RLS needed since these tables have no per-coach data in them at all.

### 4.6 Admin MCP server — push, pull, and update briefing content from Claude

**What this is:** a small MCP (Model Context Protocol) server that exposes the Daily Briefing tables as tools Claude can call directly in a conversation — so whoever runs content for CoachOS can manage the news feed by talking to Claude in Claude Desktop or Claude Code, instead of waiting for a web admin panel to exist. This is **internal tooling for the team, never given to coaches** — a separate credential and a separate surface from anything in the coach-facing app.

**Access control — a database flag, not a hardcoded person.** A new `team_members` table (`id`, `email`, `name`, `is_content_admin` boolean default `false`, `created_at`) is the single source of truth for who's allowed to use this tool. Tick `is_content_admin` to `true` for a row (via Supabase's own table editor, or an internal settings screen later) and that person's login now works with the MCP server; untick it and access is revoked immediately. This means the tool isn't wired to one person — anyone on the team can be granted or revoked access by flipping one column, no code changes and no redeploy.

**Tools it exposes:**

| Tool | What it does |
|---|---|
| `list_briefing_categories` | Returns the niche categories (Executive, Career, Life & Wellness, Mentoring) |
| `get_todays_briefing(category)` | Pulls what's currently live for a category — the world catchup, the learn piece, and every headline — so you can see the day's content before touching it |
| `push_news_item(category, headline, summary, source_url, image_url?)` | Adds a new headline to a category's briefing for today (or a given date), `created_by` set to the calling admin's `team_members.id` |
| `update_briefing_item(item_id, fields)` | Edits an existing headline, world catchup, or learn piece — fix a summary, swap a source link, replace an image |
| `remove_briefing_item(item_id)` | Pulls something down if it's wrong or stale |
| `upload_image(file_or_url, alt_text)` | Uploads to Supabase Storage (already in the stack, doc 03) and returns a URL to attach to any item |
| `refresh_category_now(category)` | Calls the n8n on-demand workflow (§4.5) for one category immediately, instead of waiting for the next scheduled run — useful for breaking news |

**How you'd actually use it, once built:** open a conversation with Claude, say something like *"Pull up today's Executive briefing"* or *"Add this article as a headline in the Career category, here's the link and a photo"* — Claude calls the matching tool, you see the result, done. No admin dashboard to log into.

**Security — this is the part not to skip:**
- Every tool call checks the caller against `team_members.is_content_admin = true` **before** touching any data — this is the actual access gate, not a formality. A revoked flag means an immediately dead session, not "dead after the next deploy."
- The MCP server itself authenticates to Supabase with its **own dedicated service-role credential**, scoped only to the briefing tables — never a coach's key, never the same key the coach-facing app uses. The `team_members` check happens in application logic in front of that credential, not instead of it — the service-role key is never hand-delegated to individual admins directly.
- That service-role credential lives in a local `.env` file on the machine running the MCP server, the same "never commit a secret" rule as everywhere else in this guide (§4.2).
- **Run it locally first, not hosted.** The simplest, safest v1 is a small Node/TypeScript process running on your own machine, using the MCP SDK's stdio transport, configured once in Claude Desktop's or Claude Code's MCP settings. A hosted/remote version is a real upgrade to make later, once there's a team spread across machines — the `team_members` flag model is what makes that later upgrade easy, since access is already data-driven, not hardcoded.
- Log every push/update/delete to `audit_log` with the admin's `team_members.id`, same as every other write in this system (§4.2) — an admin tool is still a tool that touches production data.

### 4.7 Coach Community endpoints (Phase 4 v1, Phase 5 v2)

**v1 data model (Phase 4):** `connections` (`requester_id`, `recipient_id`, `status` [pending/accepted], RLS: both parties can read, only the recipient can update status), `posts` (`coach_id`, `content`, `image_url`, `visibility` [connections/public]), `post_likes` (`post_id`, `coach_id` — composite key), `post_comments` (`post_id`, `coach_id`, `content`), `post_shares` (`post_id`, `sharer_id`, credits the original `coach_id`).

**v1 endpoints:** standard CRUD under `/api/community/*` — `POST /connections/[coach_id]/request`, `POST /connections/[id]/accept`, `POST /posts`, `POST /posts/[id]/like`, `POST /posts/[id]/comment`, `POST /posts/[id]/share`, `GET /feed` (posts from accepted connections + any public posts, paginated). All RLS-scoped to `coach_id = auth.uid()` for writes; feed reads respect each post's `visibility`.

**v2 (Phase 5) — Message + Chat, built on Supabase Realtime (doc 03):** `conversations`, `conversation_participants`, `messages` (persisted to Postgres — this is the durable record) with Realtime Broadcast layered on top for instant delivery to open clients and Presence for online/typing indicators. Re-verify Supabase's current Realtime limits against the actual coach count before this phase, not against the numbers in doc 04.

**Trust & safety, built in from v1, not added later:** a `reports` table (`reporter_id`, `target_type` [post/comment/message], `target_id`, `reason`, `status`), a `blocks` table (`blocker_id`, `blocked_id` — hides all content both directions once set), and basic rate limiting on posts/comments/connection requests to blunt spam. This is a v1 requirement per doc 02, Module 11 — build the report/block buttons alongside the like/comment buttons, not after.

**Build brief to give Claude Code:** *"Create a small MCP server (TypeScript, `@modelcontextprotocol/sdk`) exposing the seven tools listed in docs/05 §4.6 against the `briefing_days`/`briefing_headlines` tables via a dedicated Supabase service-role key, stdio transport, runnable locally and configurable in Claude Desktop's MCP settings. Log every write to `audit_log`."*

**Content rule to build in, not bolt on later:** every headline and the daily learn piece must carry a real `source_url`. If the scheduled job can't find a real source for a category on a given day, it skips that section rather than having Claude invent one — same discipline as doc 01 §6's rejected-claims table.

## 5. Frontend guide

### 5.1 Design tokens (carry the prototype identity 1:1)

```
bone #F0EBDD · card #F5F1E6 · well #E2DAC4 · ink #1E2B24 · ink-soft #54604F
brass #8A6A1F · green #2E6B4F · rust #9A4A2C · line rgba(30,43,36,.16)
Display serif: Iowan Old Style / Palatino stack · UI sans: Seravek / Segoe stack · Data: mono, tabular numerals
Radius 2–4px (sharp, not bubbly) · shadows soft and warm · generous whitespace
```

Rule for Claude Code: *"Match `prototype/index.html` in the planning repo for look and feel; it is the design source of truth."*

### 5.2 Screens to build (Phase 1, Module 6) and their components

| Screen | Contains | Key components |
|---|---|---|
| Dashboard ("Today") | Greeting, KPI tiles, **Approval Queue** (drafts with Approve/Edit buttons), today's sessions, prep briefs due | `KpiTile`, `ApprovalQueue`, `SessionRow` |
| Clients list | Search, cards with engagement progress, at-risk badge; a client can be an **individual, family, or team unit** | `ClientCard`, `RiskBadge`, `UnitTypeTag` |
| Client detail | Goals (with an edit-access toggle per goal) + progress timeline, sessions list, notes box, **"✦ Summarize with AI"** with per-output toggles, draft view with Approve & send, **homework list** with add/check-off, member list (for family/team units) | `Timeline`, `NotesEditor`, `AiDraftPanel`, `HomeworkList`, `MemberList` |
| Meeting room (`/meetings/[session_id]`) | CoachOS Meet — join link, in-call recording indicator, post-call transcript status | `MeetingRoom`, `RecordingBadge` |
| Settings → Voice | The AI Voice & Rules Center: About Me, writing samples, the 15 parameters as sliders/toggles, per-content-type rule overrides | `VoiceProfileForm`, `ParameterSlider`, `RuleOverrideList` |
| Settings → Delivery | Portal on/off, portal branding, delivery mode (email only / email + portal) | `DeliverySettingsForm` |
| Settings → Data | Per-client retention period, manual export, manual delete (with the pre-deletion notice flow visible) | `RetentionSettingsForm`, `ExportButton` |
| Client portal (public, optional) | Approved summaries, action items, homework (checkable if the coach allows), goals (editable if `editable_by_client`), session history — coach-brandable, only shown if `coaches.portal_enabled` | `PortalCard`, `HomeworkCheckItem` |

**UX laws** (put in CLAUDE.md): approval-first everywhere; every AI output editable before approve; empty states teach ("No sessions yet — record your first one"); mobile-responsive from day one; loading states for AI calls and transcript processing (typing shimmer / processing spinner, like the prototype).

## 6. Functionality-by-functionality build order (with the brief to give Claude Code)

Build in this exact order. Each has a **done-when** — do not move on until it passes. **This is Module 6's actual build order, updated from the earlier draft** — recording/transcripts and homework both moved up to the first release; family/team units are in the schema from F1, not retrofitted later.

| # | Functionality | Give Claude Code this brief | Done when |
|---|---|---|---|
| F0 | Scaffold | "Create a Next.js App Router + TypeScript + Tailwind + Supabase project with auth (email magic link), the folder structure and design tokens from docs/05 §3+§5, and a CLAUDE.md from §7." | You can sign up, sign in, see an empty dashboard |
| F1 | Client units + members CRUD | "Coaches can add/edit/archive client_units (individual/family/team) with client_members, engagements, and goals per §4.1, RLS on. Goals get an editable_by_client toggle." | Two test coach accounts can't see each other's clients; a family unit with 3 members works the same as an individual with 1 |
| F2 | Voice Center | "Build Settings → Voice: About Me, writing samples, the 15 parameters, rule overrides — save to voice_profiles." | A coach can set up their voice once and see it reflected in a test AI draft |
| F3 | Session notes → AI summary | "Build §4.3 summarize endpoint + client-detail notes flow: paste notes → Summarize → editable draft, respecting per-coach outputs_enabled and the voice_profiles row." | Real notes in, sensible editable draft out in the coach's configured voice; nothing sent |
| F4 | Approval queue + send | "Dashboard approval queue; Approve sends per coaches.delivery_mode (email via Resend, and/or portal), flips status, logs to audit_log." | Approved summary reaches the client by whichever mode is configured; unapproved never sends |
| F5 | Homework | "Add/edit homework_tasks from client detail and from a session; client can check off via the portal if enabled; reminder job nudges if still open near the next session." | A task created after a session shows up and can be checked off |
| F6 | Meetings — CoachOS Meet + recording + transcript | "Stand up the LiveKit test VPS (§4.3b); build /meetings/[session_id] join flow, Egress recording, transcript pipeline feeding into session_notes. Add the Zoom OAuth fallback path." | A real test call records, produces a transcript, and that transcript can be summarized like typed notes |
| F7 | Progress timeline | "Client detail shows goals with status and a session-by-session timeline fed by approved summaries' action items." | Timeline reads like the prototype's Maya example |
| F8 | Prep brief | "Prep-brief endpoint + 'Prep me' button on client detail and today's sessions, including open homework." | Brief correctly reflects the last sessions' commitments and open tasks |
| F9 | Client portal (optional) | "Build /portal/[token] per client_member, gated on coaches.portal_enabled: approved summaries, actions, homework, goals (respecting editable_by_client), next session." | Portal link works logged-out on a phone; shows only approved content; respects the on/off setting |
| F10 | Data rights | "Settings → Data: retention period per coach, a scheduled job that writes a retention_notice_sent event and notifies the client before any delete, manual export/delete on demand." | A test deletion actually notifies first, then deletes, and is logged |
| F11 | Polish + landing | "Marketing landing page reusing the prototype hero + demo look; app-wide empty/loading/error states." | A stranger understands the product in 30 seconds |
| — | **Security gate** | Independent review (budgeted in doc 04) + Claude Code self-audit: "Audit auth, RLS, portal tokens, recording consent logging, API routes for the §4.2 rules." | Findings fixed **before any real client data** |

Then pilots (doc 04 §4).

### Phase 2+ — stale, rewritten module by module as we get there

Everything below reflects the *original* pre-questionnaire plan and is known to be out of date against docs 02/08/09/10/11's actual decisions (Bookings' scope, Agreements' dual-path Stripe Connect model, Marketing Studio's narrowed launch scope and Email Connections, Autopilot's Activepieces engine and reversed workflow-builder decision, the Directory's deferral, Community's full open-network reversal, the Pipeline Agent, and per-coach API/MCP from doc 11 aren't reflected here yet). Treat the phase/module list itself as still roughly right; treat every technical detail under it as a placeholder until we do that module's own build-prompt pass, the same way §4.1–§4.3b and §5.2/§6 just got done for Module 6.

**Phase 2 (Bookings, Agreements & Billing, Email Connections):** Cal.com integration, Stripe + Stripe Connect dual-path billing, Documenso e-sign, Email Connections (Gmail/Microsoft 365/SMTP), security re-review.

**Phase 3 (Leads/Pipeline, Marketing Studio, Autopilot, Daily Briefing):** native pipeline with Lead IDs/custom fields/activity log, the Pipeline Agent (Mastra), Odoo-style Proposals, Marketing Studio's newsletter-first scope, Autopilot on Activepieces with the constrained coach-facing workflow builder, Daily Briefing's per-category digest + admin MCP server, per-coach API + coach-facing MCP (doc 11).

**Phase 3, deferred:** Coach & Mentor Directory — paused per explicit instruction (doc 08), not built until revisited.

**Phase 4+ (Practice Intelligence, Coach Community):** dashboards-with-filters everywhere (doc 02's cross-cutting rule), at-risk signals, and the Community's full open-public-network build (two-tier Verified Coach/general profiles, trust & safety at public-platform scale, per doc 02 Module 11) — a materially bigger undertaking than the original closed-community sketch below, per doc 04's updated cost note.

## 7. CLAUDE.md / .cursorrules template (paste into the app repo)

```markdown
# CoachOS — project rules
Product: practice platform for coaches/mentors. AI assists the coach; it NEVER
coaches the client and NEVER sends anything without explicit coach approval.

Stack: Next.js App Router + TS + Tailwind + Supabase (RLS on everything) +
Claude API (server-side only, Haiku 4.5 default). Design: match
prototype/index.html tokens (bone/ink/brass/green palette, serif display, 2-4px radius).

Hard rules:
- Every table: RLS scoped to coach_id. Every new table gets a two-account isolation test.
- Claude API calls only in app/api/** server routes. Keys only in env.
- Outbound artifacts (email/post/summary) require status='approved' + audit_log entry.
- Label AI content "AI-drafted, coach-approved". No emotion-scoring or employee-evaluation features, ever.
- Simple > clever: no speculative abstractions; one feature per PR; tests for money- and send-paths.
- When my instruction conflicts with these rules, tell me instead of complying.
```

## 8. Deployment & ops (Phase 1)

1. Vercel: connect the private app repo → auto-deploy `main`; secrets in Vercel env settings.
2. Supabase: one project; migrations via files in `supabase/migrations` (Claude Code writes them); enable daily backups when first pilot starts.
3. Domains: `coachos.yourdomain` for app; portal on same domain.
4. Monitoring: Vercel analytics + a simple `/api/health`; error alerts to your email.
5. Weekly: `git push`, dependency updates ("Claude, update deps and run tests"), skim audit_log.

## 9. What NOT to build (standing guardrails)

**Note:** two of these lines were overturned by this session's questionnaire pass and are corrected below rather than left stale, since a guardrail that's silently wrong is worse than no guardrail at all.

- No chat-with-AI-coach feature for clients (that's Build 1 — see the dossier).
- No custom scheduler, editor, or e-sign implementation — integrate (doc 03).
- ~~No multi-language, no mobile apps~~ — **reversed:** full multi-language is now a cross-cutting requirement from early on (doc 02, M12-Q5), and mobile is responsive-web + installable PWA, not gated to Phase 4 (M12-Q4). Per-coach API/MCP (doc 11) is real but scoped per module, not a Phase 4 gate either.
- No feature that sends anything to a client without going through the approval-first floor — this still holds even where Autopilot/the workflow builder (doc 02 Module 8) let a coach build their own automation: the builder changes who designs it, never whether a human approved what's about to send.
- ~~No Community posting live to real coaches without report/block tooling already built~~ — **the bar is now higher, not lower:** Community is a fully open public network (doc 02 Module 11, reversed from a closed peer community), so this needs automated content moderation at scale plus report/block, and a real legal review pass (EU Digital Services Act exposure) before public launch — not just report/block tooling.
- No real-time Chat before the v1 feed proves real engagement — don't build the harder infrastructure first on a guess.
