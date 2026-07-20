# 09 — Engine Comparison: the re-verified open-source research (final picks)

**Why this doc exists:** before writing a line of code, every open-source engine choice was re-researched — rival repos compared feature-by-feature, and every license claim verified **directly at the source** (the repo's own license file or docs, not blog posts). Where two projects covered the same category with different features, the winner was picked and the loser's missing features assigned to our own build — the legal way to "combine two repos into one" (merging AGPL code into ours would force our product open-source; re-implementing the *features* natively in our proprietary code does not).

**Result: two engines changed, three confirmed, one dropped entirely.** Summary table at the bottom.

---

## 1. Scheduling — Cal.com ✔ CONFIRMED

| | Cal.com | Easy!Appointments (main rival) |
|---|---|---|
| License | AGPLv3 + commercial option (verified earlier, unchanged) | GPLv3 |
| Google Calendar two-way sync | Native, mature | Basic |
| Outlook/Microsoft 365 sync | Native | Weak |
| Stack fit | TypeScript/Next.js — same as ours | PHP |
| Activity | Very active | Slow |

**Verdict:** keep Cal.com. It's also the single biggest contributor to your "maximum syncing with Google Calendar" requirement — its calendar-sync engine (Google, Outlook, iCloud) is the most battle-tested in open source. What it can't do (package-credit-aware booking) we build natively on top.

## 2. E-signatures — Documenso ✔ CONFIRMED (DocuSeal examined and rejected)

| | Documenso (our pick) | DocuSeal (rival) |
|---|---|---|
| License — verified at repo | Pure AGPLv3 | AGPLv3 **"with Section 7(b) Additional Terms"** — extra conditions on top of AGPL |
| White-label | Achievable self-hosted (AGPL obligations apply: publish any modifications to Documenso itself) | **White-label is explicitly a paid Pro feature**; embedded signing SDKs also sit in the paid tier |
| Stack | TypeScript/Next.js — same as ours | Ruby/Vue |
| Signing strength | PKCS#12 cryptographic signing | QES only as paid add-on |

**Verdict:** keep Documenso. DocuSeal is a good product but its license carries additional terms and its embedding/white-label features are pay-walled — a direct conflict with the zero-third-party-branding rule. Documenso's plain AGPL means we can rebrand the self-hosted signing page by publishing that one small modification (to Documenso only — never our code), or later buy their Platform tier if we'd rather not.

## 3. CRM — Twenty ✖ DROPPED. Pipeline gets built natively (this is the "combine two CRMs into one")

The two best open-source CRMs pull in opposite directions:

| | Twenty | EspoCRM |
|---|---|---|
| Strength | Modern TypeScript/GraphQL, developer-friendly | Feature-rich out of the box: email sync inside CRM, workflows, reports |
| Weakness | Young; **needs 4 GB RAM alone, 8 GB recommended alongside other tools** | 12-year-old PHP codebase, dated architecture |
| License | AGPL-family (repo checked; exact edition immaterial given the verdict below) | AGPLv3 |

**The decisive question nobody asks: do we need a CRM *engine* at all?** Our coaches never see the CRM's UI (white-label rule) — so we'd be running a 4–8 GB service purely to store what our build guide already specifies as **three Postgres tables** (`leads`, `pipeline_stages`, plus scoring fields — docs/05 §4.1, already written). Every feature we actually need — pipeline board, stage moves, notes, AI scoring, email logging — is a thin UI over tables we already have.

**Verdict: drop the external CRM entirely.** Build the pipeline natively in our own Postgres/Next.js code, taking Twenty's UX ideas (clean board, keyboard-fast) and EspoCRM's feature ideas (email activity on the lead record, saved views) as the design brief. Result: one less service to run, ~4 GB less RAM, one less failure point, 100% white-label and closed-source by construction, and the "two CRMs combined" — legally, as re-implemented features in our proprietary code.

## 4. Newsletter — Listmonk ✔ CONFIRMED (Mautic and Keila examined)

| | Listmonk (our pick) | Mautic | Keila |
|---|---|---|---|
| Footprint | Single Go binary, <100 MB RAM, proven at 100k+ subscribers | PHP + MariaDB, **4 GB minimum, 8 GB recommended** | Elixir, modest |
| Scope | Newsletters, lists, segmentation, analytics — exactly our need | Full marketing-automation suite (drip flows, lead scoring, landing pages) | Newsletters, modern UI |
| License | AGPLv3 | GPLv3 | AGPLv3 |

**Verdict:** keep Listmonk. Mautic's extra features (drip sequences, lead scoring) are things our own AI layer + automation engine already do better in our own UX — running Mautic would duplicate our differentiation at 40× the memory cost. All options need an SMTP relay (Amazon SES) for actual delivery — already in the cost plan.

## 5. Automation — n8n ✖ REPLACED by Activepieces (a genuine license risk, caught before build)

This is the important correction. Verified directly:

- **n8n's Sustainable Use License** (read from n8n's own `LICENSE.md` on GitHub): use is allowed for *"your own internal business purposes or for non-commercial or personal use"* — and you may *"not… provide it to others"* except *"free of charge for non-commercial purposes."* Our coach-facing **Autopilot module is a paid feature powered by the engine's workflows** — that is providing its value to paying third parties, which sits between gray-zone and violation. n8n sells a separate "Embed" commercial license for exactly this scenario.
- **Activepieces** (verified at the repo): Community Edition is **MIT-licensed** — the most permissive license there is. Commercial use, modification, rebranding, embedding in a paid SaaS: all explicitly fine, free, forever. Enterprise-only features live in a separate commercial folder we don't need (we build our own recipes UI anyway — coaches only ever see CoachOS toggles).

**Verdict: Activepieces replaces n8n everywhere** — Autopilot recipes (Module 8), Daily Briefing scheduled + on-demand pulls (Module 10), and the admin MCP refresh trigger. It's a mature n8n-class flow engine (schedules, webhooks, HTTP, AI steps, 280+ connectors) and this also means **one engine instead of two** (we'd otherwise have needed n8n internal + something safe for coach-facing). Coaches notice nothing — they never saw the engine anyway. n8n remains a fine tool for purely internal ops if ever wanted, but nothing in the product depends on it now.

## 6. Community & Chat — build natively on Supabase ✔ CONFIRMED

Re-checked the field: Discourse/Flarum/NodeBB are forums, not LinkedIn-style feeds; Rocket.Chat/Mattermost are team-chat servers (heavy, wrong shape for coach-to-coach DMs). The v1 feed is plain Postgres tables + our UI; v2 chat rides Supabase Realtime already in the stack. **No new engine — decision stands.**

## 7. Forms (intake, lead capture) — considered, build natively

Formbricks/OpnForm exist, but our forms are simple (lead capture, intake questionnaires) and deeply tied to our own modules. Another service isn't worth it. Build natively.

## 8. Meetings, recording & transcription — NEW category (added July 2026)

The goal: sessions can happen **inside CoachOS or in the tools coaches already use**, and either way the recording becomes a transcript that feeds Module 6's AI summary automatically — closing the loop so the coach never has to paste notes by hand. This lands in three phases, matching the sync principle (doc 07 §8: meet coaches in their existing tools first).

### Phase 1.5 — upload path (works with ANY meeting tool, near-zero infra)

Coach records anywhere (Zoom, Meet, Teams, phone), uploads the file → transcription → transcript attached to the session → AI summary drafted for approval. Transcription engine: a speech-to-text API at launch (commercial, pennies per hour — e.g. Deepgram/AssemblyAI class), swappable later for self-hosted **Whisper (MIT — open-weight, verified widely)** when volume justifies running it ourselves.

### Phase 2 — blend the coach's existing tools (the "bend Zoom/Meet into this" path)

| | Zoom (via coach's own account) | Google Meet (via coach's own account) |
|---|---|---|
| Create meeting from a booking | ✔ API | ✔ Calendar API (already planned) |
| Meeting **inside** our app | ✔ Meeting SDK embeds the real Zoom meeting in our web page | ✖ Meet cannot be embedded — opens in its own tab |
| Auto-pull recording after the call | ✔ Cloud Recording API | ✔ Meet REST API — **but** only on Workspace Business Standard+ plans |
| Auto-pull transcript | ✔ VTT transcript via the same API | ✔ via API, but recording must be started manually in-meeting, and API transcripts expire after 30 days |
| Verdict | **Primary integration** — the full loop automates | **Supported pull** — works when the coach's plan and habits allow; upload path covers the rest |

So: connect Zoom once (OAuth) → every recorded session flows in automatically: recording → transcript → AI summary → approval queue. Meet gets the same where Google's limits allow.

### Phase 3+ — CoachOS Meet: our own in-platform video

| | LiveKit (recommended) | Jitsi Meet (alternative) |
|---|---|---|
| License — verified | **Apache 2.0** (same family as Supabase — zero copyleft issues, full white-label by right) | **Apache 2.0** |
| Shape | WebRTC infrastructure + SDKs — we build our own meeting UI (total CoachOS branding by construction) | Complete meeting app, embeddable via iframe, rebrandable with effort |
| Recording | Egress service → MP4 straight into our own storage | Jibri — a headless Chrome + FFmpeg instance per concurrent recording (heavy) |
| Transcription | Track forwarding via WebSocket → real-time Whisper pipeline (open-source ecosystem exists) | Via Vosk (open-source) or paid Google Speech-to-Text |
| Verdict | **The target**: cleanest white-label, cleanest recording→transcript pipeline, scales | Faster to ship a full meeting UI if we want a shortcut — decision gate at Phase 3 |

**Built-in consent is non-negotiable in every path:** recording requires a visible in-meeting notice plus the client's explicit consent (captured once in the agreement and confirmable per-session); the coach controls retention and deletion, and transcripts inherit the same confidentiality rules as session notes (docs/05 §4.2). Recording a coaching conversation is the most sensitive data we will ever hold.

---

## The final stack (before → after)

| Category | Was | Now | Why |
|---|---|---|---|
| Scheduling | Cal.com | **Cal.com** ✔ | Best-in-class calendar sync — serves the Google/Outlook sync requirement directly |
| E-sign | Documenso | **Documenso** ✔ | Pure AGPL; DocuSeal's white-label is pay-walled |
| CRM | Twenty (self-hosted) | **Native build** (Twenty + EspoCRM as design brief) | Coaches never see it; 3 tables we already have; −1 service, −4 GB RAM, fully closed-source |
| Newsletter | Listmonk | **Listmonk** ✔ | Mautic 40× heavier for features our AI layer owns |
| Automation | n8n | **Activepieces (MIT)** | n8n's license doesn't cover powering paid customer features; MIT does, unconditionally |
| Community/Chat | Native + Supabase Realtime | **Same** ✔ | Nothing in the field fits better |
| Meetings & transcripts | *(not in plan)* | **NEW: Zoom/Meet pull (Phase 2) → LiveKit in-platform (Phase 3+), Whisper for transcription** | Session → recording → transcript → AI summary, fully automated (§8) |
| Backend | Supabase | **Same** ✔ | Unchanged (hosted Phase 1–2 → self-hosted Phase 3, doc 07) |

**Net effect: 4 external engines instead of 5, zero license gray zones, less RAM, fewer failure points** — and each engine remains a separate isolated service per the architecture (doc 07), so one failing never takes down the rest.

## What this changes in other docs

- **docs/03** — stack map rows updated (Twenty → native; n8n → Activepieces), rejection notes added.
- **docs/07** — architecture diagram updated to match.
- **docs/05** — gets fully restructured in the per-module build-prompt pass (after docs/08 answers), reflecting all of the above.
- **docs/08** — questionnaire engine-name table updated; new sync/interlink questions added.
