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

| Layer | Choice | Why |
|---|---|---|
| Frontend + backend framework | **Next.js (App Router) + TypeScript** | One codebase for UI and API routes; the ecosystem Claude Code and Cursor know best; deploys to Vercel in minutes |
| Styling | **Tailwind CSS** + design tokens from the prototype | Fast, consistent; tokens in §5 |
| Database, auth, storage | **Supabase** (Postgres + Auth + Row-Level Security + Storage) | Hosted, free tier to start, auth built in, RLS gives per-coach data isolation at the database level |
| AI | **Claude API — Haiku 4.5** for summaries/briefs/drafts (server-side only) | Verified cost ≈ $0.03/coach/mo; upgrade individual calls to Sonnet only if quality demands |
| Scheduling (Phase 1) | **Cal.com embed** | Don't build a scheduler |
| Payments (Phase 2) | **Stripe** | Standard |
| E-sign (Phase 2) | **Documenso** (self-host or API) | See doc 03 |
| Hosting | **Vercel** | Free tier to start, zero-ops |
| Email sending | **Resend** (or Supabase SMTP) | Simple transactional email for summaries/reminders |

**Accounts you need before starting:** GitHub (done), Supabase, Vercel, Anthropic Console (API key), Resend, Cal.com. All free tiers. Keys go into `.env.local` and Vercel's environment settings — never into git.

## 3. Application structure (tell Claude Code to scaffold exactly this)

```
coachos-app/                      ← separate PRIVATE repo from this planning repo
├── CLAUDE.md                     ← project rules for Claude Code (template in §7)
├── .cursorrules                  ← same rules for Cursor
├── .env.local                    ← secrets (git-ignored)
├── app/
│   ├── (marketing)/page.tsx      ← public landing page (from prototype design)
│   ├── (app)/
│   │   ├── dashboard/            ← "Today" pane
│   │   ├── clients/              ← list + [id] detail (timeline, notes, summaries)
│   │   ├── leads/                ← Phase 3
│   │   ├── marketing/            ← Phase 3
│   │   ├── bookings/             ← Cal.com embed + session list
│   │   ├── contracts/            ← Phase 2
│   │   └── settings/             ← profile, niche pack, voice profile
│   ├── portal/[token]/           ← client-facing portal (no login; signed link)
│   └── api/
│       ├── ai/summarize/route.ts ← notes → summary (server-side Claude call)
│       ├── ai/prep-brief/route.ts
│       └── ai/draft/route.ts     ← Phase 3 (follow-ups, posts)
├── components/                   ← UI kit: Button, Card, Pill, Timeline, ApprovalQueue…
├── lib/                          ← supabase client, ai client, email, utils
└── supabase/migrations/          ← schema as SQL migrations (versioned)
```

## 4. Backend guide

### 4.1 Data model (Phase 1 core)

Tell Claude Code to create these tables via Supabase migrations:

| Table | Key fields | Notes |
|---|---|---|
| `coaches` | id (=auth user), name, niche_pack, voice_profile jsonb, timezone | One row per signed-up coach |
| `clients` | id, coach_id, name, email, status, portal_token | The coach's clients |
| `engagements` | id, client_id, package_name, sessions_total, sessions_used, start/end | e.g. "Executive package · 12 sessions" |
| `goals` | id, client_id, title, status (active/stalled/done), last_progress_at | Powers timeline + at-risk logic |
| `sessions` | id, client_id, scheduled_at, status, cal_booking_id | Synced from Cal.com webhook |
| `session_notes` | id, session_id, raw_notes text, created_at | The coach's rough input |
| `summaries` | id, session_id, client_facing text, coach_facing text, action_items jsonb, status (draft/approved/sent), sent_at | **status is the approval-first gate** |
| `audit_log` | id, coach_id, action, entity, at | Who sent what when — trust feature |

Phase 2 adds: `contracts`, `invoices`, `coach_email_accounts` (encrypted OAuth tokens, RLS-scoped, one per coach). Phase 3 adds: `leads`, `pipeline_stages`, `content_drafts`, `email_sequences`, `automation_recipes`, `automation_runs` (audit trail — every automated send logged like any other), `coach_profiles` (public directory fields — the one table where a coach explicitly opts a subset of data into public visibility), `directory_enquiries` (routes into `leads`), `daily_briefings` (one row per **category** per **day** — not per coach: `category`, `date`, `world_catchup_text`, `learn_content_text`, `learn_source_url`, `headlines` jsonb with each headline's AI takeaway + source link).

### 4.2 Security rules (give these to Claude Code verbatim)

- **Row-Level Security ON for every table**: a coach can only ever read/write rows where `coach_id = auth.uid()` (directly or via joins). Test this explicitly with two test accounts.
- **All Claude API calls happen in server routes** (`app/api/ai/*`). The API key exists only in server env. Nothing AI-related runs in the browser.
- **Client portal uses unguessable signed tokens**, read-only, showing only `approved`+`sent` artifacts.
- **Nothing sends without approval**: emails to clients fire only on an explicit approve action that flips `summaries.status` → `approved` and logs to `audit_log`.
- AI outputs are labeled "AI-drafted, coach-approved" (EU AI Act limited-risk disclosure).
- No client data in AI prompts beyond what the feature needs; never send the whole client table to the model.

### 4.3 The AI endpoints (Phase 1)

**`POST /api/ai/summarize`** — input: `session_id`. Server loads raw notes + client name + goals; calls Claude Haiku with a prompt like:

> You are the assistant of a professional {niche} coach. From the coach's rough session notes, produce (1) a warm, professional client-facing summary email (max 180 words, coach's voice profile: {voice_profile}), (2) a private coach-facing note (key risks, goal-status changes), (3) 1–4 action items as JSON. Never invent facts not in the notes. Output JSON: {client_facing, coach_facing, action_items[]}.

Saves result to `summaries` with `status='draft'`. **The model never emails anyone** — the approve button does.

**`POST /api/ai/prep-brief`** — input: `client_id`. Loads last 3 summaries + goals; returns a 120-word brief: commitments made, blockers, stalled goals. Displayed 30 min before session (and on demand).

Cost guard: log tokens per call into `audit_log`; alert at $20/mo (it should never get there — verified math in doc 04).

### 4.4 Gmail, Automations & Directory endpoints (Phase 2–3)

**`GET/POST /api/integrations/gmail/oauth`** — Google OAuth2 flow; stores encrypted tokens in `coach_email_accounts`. All summary/follow-up sends check for a connected account first and fall back to Resend if none exists.

**`POST /api/automations/[recipe_id]/toggle`** — enables/disables a pre-built recipe for a coach; stores their one or two customizable variables (delay days, template choice). The trigger check itself runs as a scheduled n8n workflow that calls back into `/api/automations/run` — which only ever sends a template the coach already approved when enabling the recipe, never freshly-generated text.

**`GET /api/directory/search`** and **`POST /api/directory/[slug]/enquire`** — public, unauthenticated routes; search uses Supabase full-text search over `coach_profiles`; an enquiry writes to `directory_enquiries` and immediately creates a row in that coach's own `leads` table.

**Security note — the one deliberate exception to "private by default":** `coach_profiles` is the single table designed to be publicly readable, and only for fields the coach explicitly opts to publish. Build the opt-in toggle and field-level publish controls before this table is reachable by any public route — see §4.2's RLS rule, which still applies to every *other* table without exception.

### 4.5 Daily Briefing endpoints (Phase 3)

**A scheduled job (n8n, doc 03), not a live API call per request** — runs once a day per category, pulls the RSS feeds listed in doc 03, has Claude summarize the world catchup and pick + summarize the day's "5-minute learn" from the curated publication feeds, and writes one row per category into `daily_briefings`. Every request that follows just reads that cached row.

**`GET /api/briefing/[category]`** — public, unauthenticated, cached (this is the endpoint both the dashboard card and the public `/briefing` marketing page call — same data, two surfaces). No RLS needed since `daily_briefings` has no per-coach data in it at all.

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

### 5.2 Screens to build (Phase 1) and their components

| Screen | Contains | Key components |
|---|---|---|
| Dashboard ("Today") | Greeting, KPI tiles, **Approval Queue** (drafts with Approve/Edit buttons), today's sessions, **Daily Briefing card** (category news, world catchup, 5-min learn) | `KpiTile`, `ApprovalQueue`, `SessionRow`, `BriefingCard` |
| Public briefing (`/briefing`) | Same content as the dashboard's Briefing card, no login required — a content/SEO surface | `BriefingCard` (reused) |
| Clients list | Search, cards with engagement progress, at-risk badge | `ClientCard`, `RiskBadge` |
| Client detail | Goals + progress timeline, sessions list, notes box, **"✦ Summarize with AI"**, draft view with Approve & send | `Timeline`, `NotesEditor`, `AiDraftPanel` |
| Bookings | Cal.com embed, upcoming list with reminder status | `CalEmbed`, `SessionRow` |
| Settings | Profile, niche pack picker, voice-profile setup (3 writing samples + short interview form) | `NichePicker`, `VoiceProfileForm` |
| Client portal (public) | Approved summaries, action items, next session, book link — mobile-first | `PortalCard` |

**UX laws** (put in CLAUDE.md): approval-first everywhere; every AI output editable before approve; empty states teach ("No sessions yet — share your booking link"); mobile-responsive from day one; loading states for AI calls (typing shimmer, like the prototype).

## 6. Functionality-by-functionality build order (with the brief to give Claude Code)

Build in this exact order. Each has a **done-when** — do not move on until it passes.

| # | Functionality | Give Claude Code this brief | Done when |
|---|---|---|---|
| F0 | Scaffold | "Create a Next.js App Router + TypeScript + Tailwind + Supabase project with auth (email magic link), the folder structure and design tokens from docs/05 §3+§5, and a CLAUDE.md from §7." | You can sign up, sign in, see an empty dashboard |
| F1 | Clients CRUD | "Coaches can add/edit/archive clients with engagements and goals per the §4.1 schema, RLS on." | Two test coach accounts can't see each other's clients |
| F2 | Session notes → AI summary | "Build §4.3 summarize endpoint + client-detail notes flow: paste notes → Summarize → editable draft (client-facing, coach-facing, action items)." | Real notes in, sensible editable draft out; nothing sent |
| F3 | Approval queue + send | "Dashboard approval queue; Approve sends the client-facing summary by email (Resend), flips status, logs to audit_log." | Approved summary lands in a real inbox; unapproved never sends |
| F4 | Progress timeline | "Client detail shows goals with status and a session-by-session timeline fed by approved summaries' action items." | Timeline reads like the prototype's Maya example |
| F5 | Prep brief | "Prep-brief endpoint + 'Prep me' button on client detail and today's sessions." | Brief correctly reflects the last sessions' commitments |
| F6 | Bookings | "Embed Cal.com, sync bookings to `sessions` via webhook, list upcoming with status." | Booking made in Cal.com appears in-app |
| F7 | Client portal | "Read-only portal at /portal/[token]: approved summaries, actions, next session, book link." | Portal link works logged-out on a phone; shows only approved content |
| F8 | Niche packs v1 | "Niche picker in settings loads the pack's templates/presets (from doc 02) into summaries' tone and portal copy." | Switching pack visibly changes tone/templates |
| F9 | Polish + landing | "Marketing landing page reusing the prototype hero + demo look; app-wide empty/loading/error states." | A stranger understands the product in 30 seconds |
| — | **Security gate** | Independent review (budgeted in doc 04) + Claude Code self-audit: "Audit auth, RLS, portal tokens, API routes for the §4.2 rules." | Findings fixed **before any real client data** |

Then pilots (doc 04 §4). **Phase 2 (F10–F14):** contracts via Documenso, Stripe billing, invoice chasing, **Gmail OAuth connect (F13 — start Google's verification review the same week, not after building)**, security re-review. **Phase 3 (F15–F20):** leads pipeline + AI scoring, follow-up drafts into the approval queue, marketing composer with voice profile, landing-page builder, **Automations v1 (F17 — 3–5 recipes, n8n-backed, toggle-only UI)**, **Coach & Mentor Directory (F18 — profile + search + enquiry routing, built but not opened to public traffic until the coach-supply gate in doc 04 clears)**, **Daily Briefing (F19 — the scheduled per-category digest job, the dashboard card, and the public `/briefing` page; every item must carry a real source link or get skipped, per §4.5)**.

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

- No chat-with-AI-coach feature for clients (that's Build 1 — see the dossier).
- No custom scheduler, editor, or e-sign implementation — integrate (doc 03).
- No multi-language, no mobile apps, no API-for-third-parties until Phase 4.
- No feature that sends anything autonomously. Ever.
