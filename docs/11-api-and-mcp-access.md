# 11 — Per-Account API, Coach-Facing MCP, and Admin Ops

Three distinct access surfaces, each with a different key, different scope, and different risk level. Written now so every module's build prompt (starting with Module 1) already accounts for it.

---

## 1. Per-coach API — every account gets its own key

**What it is:** the same idea as Stripe or Cal.com giving every account an API key. A coach generates a key from their account settings; that key can read/write **only their own data** — the same Row-Level-Security boundary (`coach_id = auth.uid()`) that already protects every table in the plan, just reached via API key instead of a login session.

**What it's for:** a coach who wants to connect their own tools — push leads in from a personal spreadsheet macro, pull their schedule into another app, build a small Zapier-style automation of their own. Read scope and write scope are separate permissions, and a key is instantly revocable from account settings with no support ticket needed.

**What it explicitly cannot do:** see or touch any other coach's data, change billing/subscription, or reach admin-only functions (directory verification, team management, briefing content). It's a narrower door than the coach's own logged-in session, not a wider one.

## 2. Coach-facing MCP — "just tell Claude"

**What it is:** a coach connects their CoachOS account as an MCP server inside their own Claude (Desktop or Code), authenticated with the API key from §1. From then on they can say things like *"add a lead: Jonas, referred by Maya, interested in the executive package"* or *"what's on my calendar tomorrow"* or *"mark Sarah's package as renewed"* — natural language, same data boundary as the API key underneath.

**Design rule, matching approval-first everywhere else in this plan:** the MCP can **add and update the coach's own records** (leads, notes, goals, simple edits) freely — that's the coach editing their own data, no different from typing it into a form. It **cannot send anything to a client** (an email, a message, a contract) without that draft still landing in the normal in-app approval queue first. The MCP is a faster way to *enter* things, never a way to *bypass* the send-approval rule.

**This is a different, smaller MCP server from the existing Admin MCP** (doc 05 §4.6, Daily Briefing content ops) — different audience (every coach vs. our internal team), different scope (a coach's own data vs. shared briefing content), same underlying pattern (MCP as a thin, permission-gated layer over the real API).

## 3. Admin Ops MCP — for when something breaks, without the risk of ungated code changes

**The distinction that matters:** "an admin fixes a bug" can mean two very different things, and only one of them is safe to wire into a live chat tool:

| | Normal engineering (what this already is) | An ungated "edit production code from chat" tool |
|---|---|---|
| Who reviews the change before it ships | A person, looking at an actual diff | Nobody — it ships the moment the AI writes it |
| Can it be undone | Yes — git history, a real deploy pipeline | Only if someone notices before real damage happens |
| Blast radius of one mistake | Caught in review, before reaching coaches | Every coach, immediately |

**Recommendation, reflected in the plan: bug fixes stay in the first column.** That's not a limitation — it's just how software gets built safely, and it's exactly how this repository itself has been built this whole session (Claude Code, real files, real commits, real review). Nothing about "admin access" should skip that.

**What genuinely deserves a real-time admin tool instead is operations, not code-editing:**

| Ops MCP capability | What it does | Why it's safe |
|---|---|---|
| View live error logs | See what's actually breaking, right now | Read-only |
| Roll back to the last working deploy | One click undoes a bad release | Reverting to something already tested and live before |
| Toggle a feature flag off | Instantly disable a broken feature for everyone, without touching code | No code changes; a switch, not an edit |
| Restart a stuck service | Cal.com/Documenso/Listmonk/Activepieces each run as separate services (doc 07) — restart the one that's stuck without affecting the others | Isolated to one already-isolated service, per doc 07's failure-isolation design |
| Re-deploy a fix that's already been reviewed and merged | Ship a real, reviewed fix immediately once it's ready, without waiting on a manual deploy step | The fix was already reviewed — this just speeds up shipping it |

This gives real "something's broken, act now" power, gated to actions that either read data or undo/toggle something already known-good — never an unreviewed code change reaching live coach data.

**Access control:** same pattern as the existing Admin MCP — a `team_members` flag (e.g. `is_ops_admin`), revocable instantly, never given to coaches.

## 4. What this changes elsewhere

- **doc 07 (architecture):** add a per-coach API layer (thin, key-authenticated, sits beside the session-based API) and the Ops MCP as a new admin-only surface.
- **doc 05 (build guide):** the data model already has audit_log — API-key actions and Ops MCP actions both log there, same as everything else.
- **Every module's build prompt** (starting with Module 1) should note which of its actions are exposed via the per-coach API/MCP, since that's now a standing capability, not a one-off.
