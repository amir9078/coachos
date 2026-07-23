# 10 — The AI Agent Layer ("Pipeline Agent" → generalized into the "Practice Agent")

**What was asked, first:** an agentic AI feature inside the CRM/pipeline — not just one-shot AI drafts (which we already have everywhere), but something that can look at a lead, reason across multiple steps, and propose what to do next. Researched, verified, and built as a working demo — see §4.

**What was asked next:** expand this beyond Leads into one agent with visibility across the whole practice — leads, clients, bookings, and billing — not siloed per module. That generalization is §6.

---

## 1. What "agentic" adds on top of what we already have

Every AI feature elsewhere in this plan (session summaries, prep briefs, content drafts) is **one prompt in, one draft out** — the coach pastes notes, Claude returns a summary. That's not an agent, it's a single AI call.

An **agent** does several steps on its own before handing back a result: it looks something up, checks a second thing based on what it found, decides what that means, *then* drafts an action — and can be paused mid-task for a human decision. That's what "agentic CRM" means here: **the Pipeline Agent looks at a lead, checks how similar leads behaved historically, decides what stage of attention this lead needs, and only then drafts the next move.**

## 2. The research — open-source agent frameworks, compared and verified

Every claim below was checked directly at the project's own repo, not assumed (same discipline as docs 03/09).

| Framework | License (verified) | Fit for us |
|---|---|---|
| **Mastra** | **Apache 2.0** (core) | ✔ **Chosen.** Pure TypeScript — same stack as our Next.js app, no separate service to run. Built-in "suspend/resume" for human-in-the-loop — this is the exact mechanism our approval-first rule needs. Has its own MCP support (lines up with our admin MCP server). 26k+ GitHub stars, used in production by companies like Replit and Adobe. Works with Claude directly. |
| **Vercel AI SDK** | Open source (Vercel) | Good fit too, and we may still use it for simpler one-shot AI calls (it's literally made by our own Phase-1 hosting provider) — but its agent loop is simpler than Mastra's, without a built-in pause-and-resume model, which is the piece we need most. |
| **LangGraph / CrewAI / AutoGen** | Various (mostly permissive) | Rejected on stack-fit alone — all three are Python-first ecosystems. Bolting a Python service onto our TypeScript/Next.js app just for the agent layer means an extra service to deploy, monitor, and keep in sync — real complexity for no real benefit over Mastra, which does the same job natively in our own language. |
| **n8n / Activepieces AI nodes** | Activepieces already in our stack (doc 09) | These are for *automation recipes* (Module 8) — scheduled/triggered workflows, not a reasoning agent that investigates a specific lead on demand. Different job; both stay in the plan, doing different things. |

**Verdict: Mastra**, called with our existing Claude API key — no new AI provider, just a TypeScript library that gives Claude the ability to plan multiple steps and pause for approval, running inside our own app process (not a separate self-hosted engine, so it doesn't add to the failure-isolation list in doc 07 — if it errors, it errors like any other part of our own code).

## 3. What the Pipeline Agent actually does (Module 1–2, Leads)

**The flow, in plain terms:**

1. Coach (or a scheduled check) points the agent at a lead that needs attention — e.g. gone quiet after a discovery call.
2. The agent reads that lead's full history in our own database.
3. It checks how *similar* past leads (same stage, same silence pattern) actually turned out — did a hard pitch work, or a soft check-in?
4. It decides what to propose — not a fixed template, a reasoned pick based on step 3.
5. It drafts the message in the coach's own voice.
6. **It stops.** Nothing sends. The coach sees the reasoning trace (transparent, matches the existing "always explain why" rule from doc 07/M7-Q9), reads the draft, and can Approve & send, edit first, or dismiss.

This is the same approval-first principle used everywhere else in CoachOS (session summaries, marketing drafts, automation recipes) — the agent just does more thinking before it reaches the draft stage.

**Where this sits in the roadmap:** Module 1–2 (Leads) is a Phase 3 module (per README §4) — so the Pipeline Agent ships alongside the rest of the pipeline, not before.

## 4. Built: a working demo

Added to the live prototype so this is something to click through, not just read about — [`prototype/index.html`](../prototype/index.html), **Leads pane → scroll below the pipeline board → "Pipeline Agent."**

Click **"Investigate & propose next step"** on the Sarah K. card: watch the agent's reasoning trace appear step by step (reading history → checking similar leads → deciding → drafting), then the drafted message appears with its reasoning visible, and nothing sends until you click **Approve & send**.

## 5. What this changed in other docs (from the original, Leads-only build)

- **doc 02** (product spec) — Module 1–2 got this capability added.
- **doc 03** (stack) — Mastra added as a new row (application-layer library, not a hosted service).
- **doc 07** (architecture) — AI layer (③) got Mastra alongside Claude API.
- **doc 08** (questionnaire) — M12-Q19 explicitly **capped** the agent's cross-module reach to what was scoped, treating any wider reach as "a deliberate future decision, not an emergent one." §6 below is that deliberate decision, made on request.

---

## 6. The generalization: Pipeline Agent → Practice Agent

**No new research needed.** Mastra was already picked for its multi-step reasoning and suspend/resume support — this is the same engine, given more to look at and a wider reasoning brief, not a new system. The Pipeline Agent doesn't get replaced; it becomes **one entry point into a single underlying agent** that now has read access across the whole practice.

### What actually changes

| | Pipeline Agent (original) | Practice Agent (this generalization) |
|---|---|---|
| What it can read | One lead's history + similar past leads (Module 2 only) | Leads/pipeline (M2), Client Delivery (M6: engagements, goals, homework, session patterns), Bookings (M4), Billing (M5: invoices, packages), Marketing (M3: what's been sent, what generated leads), Autopilot (M8: what's already running) |
| Where it's invoked | The "Investigate" button on a lead card | Contextually anywhere (a client's page, a lead's page), **and** as the reasoning engine behind Practice Intelligence's (Module 7) daily insights |
| What it reasons about | "What should happen with this one lead" | Cross-module patterns a single-module view can't see — see examples below |
| Safety floor | Approval-first, always | **Unchanged — approval-first, always.** Wider visibility does not mean wider authority. It can read more and suggest more; it can still never send, post, or charge anything without the coach approving the exact words/action first. |

### Concrete cross-module reasoning this unlocks (examples, not an exhaustive list)

- A client's package is 80% used, no renewal proposal has gone out, and their engagement has stayed steady → surface a renewal suggestion **before** the coach would have noticed the credit was running low (crosses M6 engagements + M5 billing + M2/proposals).
- An invoice is overdue **and** that same client has a session tomorrow → flag it so the coach can decide how to handle the money conversation before walking in, rather than an invoice reminder firing at an awkward moment (crosses M5 + M4).
- A newsletter hasn't gone out in three weeks, and the last one that did produced five new leads → surface that content is working and content has gone quiet, not just "you haven't posted" (crosses M3 + M2's lead-source data).
- A lead went quiet, but they were referred by a past client who's still active and happy → the follow-up tone the agent proposes can reasonably differ from a cold lead's (crosses M2 + M6 history).

None of these are visible from inside a single module's own dashboard — that's the actual point of generalizing past Leads.

### Where it lives

Primarily surfaces inside **Practice Intelligence (Module 7)** — it's the reasoning engine behind that module's own standing rule that "every insight comes with an action button, never a bare number" (doc 02 Module 7, M7-Q9). Also invokable contextually from a client's or lead's own detail page ("check on this client"), which reuses the identical underlying agent and tools rather than a second implementation.

### The governance point, made explicit (updates doc 08 M12-Q19)

The interconnection-governance rule written earlier said any agent reach beyond what was explicitly scoped should be "a deliberate future decision, not an emergent one" — **this is that decision, made deliberately, on request, not something the agent quietly grew into.** The cap moves from "Leads only" to "Leads, Client Delivery, Bookings, Billing, Marketing, Autopilot state" — still a defined, bounded list, not open-ended access to every table in the system. Any future expansion (e.g. into Directory or Community once those are built) should get the same explicit sign-off, not be assumed.

### One build cost worth flagging

Reasoning across six modules' worth of data costs a little more per run than reasoning across one lead — still cheap on Haiku (doc 04's unit economics), but the daily practice-wide scan should be built to **re-check only what's materially changed since the last run** (an invoice went overdue, a package crossed a usage threshold) rather than re-reading everything for every coach every day from scratch — a caching/incremental-check detail for whoever builds Module 7, not a reason not to build this.

## 7. What this changes in other docs (this generalization)

- **doc 02** — Module 7 (Practice Intelligence) gets the Practice Agent as its insight engine; Module 2's Pipeline Agent section gets a note that it's now one entry point into the broader agent, not a separate implementation.
- **doc 08** — M12-Q19 updated to reflect the deliberate, explicit expansion rather than reading as still-capped.

---

## 8. Customization: per-module rules for the Practice Agent, and what it costs

**What was asked:** give the coach a customization option to set rules for the Practice Agent per module (not one global on/off), plus a real token-usage estimate for the wider cross-module scope from §6.

### The "Practice Agent Rules" panel

A new settings panel, sitting alongside the existing **AI Voice & Rules Center** (doc 02, cross-cutting section) rather than folded into it — that panel governs *tone*, this one governs *reach and behavior*. One row per module the agent can read: Leads/Pipeline, Client Delivery, Bookings, Billing, Marketing, Autopilot state. Per module, three settings:

1. **On/off** — include this module's data in the agent's reasoning at all. Off means the agent literally cannot see that module's data, not just "won't surface it" — the same hard boundary as any other data-access toggle in this product.
2. **Frequency** — "include in the daily scan" vs. "only when I ask it to check on something" (e.g. from a client's own page). Set independently per module — a coach might want Billing checked daily but Marketing only on request.
3. **Which insight types are active** — a short, named list per module (e.g. Billing: "flag overdue invoices," "flag packages running low"; Client Delivery: "flag stalled goals," "surface renewal timing"), each individually toggleable. This reuses the exact pattern already established for the Voice & Rules Center's "rules can override the defaults per situation" (doc 02) and for Autopilot's per-chain on/off switchboard (doc 08 M12-Q16/M12-Q18) — same UI language, same plain-language listing ("Flag overdue invoices: ON"), not a technical diagram.

**Default:** every module ON, daily scan ON, all insight types ON — the coach opts out per module/insight rather than opting in, consistent with how every other AI feature in this product ships (default-on, coach-editable) rather than requiring setup before it does anything useful.

**Floor unchanged regardless of any toggle:** approval-first. These settings control what the agent can *see* and *surface* — never what it's allowed to *do* unattended. Turning every module on doesn't grant the agent permission to send, post, or charge anything without the coach approving the exact action first.

### Token usage / cost estimate

Grounded in doc 04's verified Haiku 4.5 unit economics — `~8 AI calls × (2,000 in + 300 out tokens) on Claude Haiku 4.5 ($1/$5 per MTok) ≈ $0.03/coach/month` for the existing single-call features — rather than a fresh guess. The Practice Agent is a materially different shape of workload: multi-step reasoning across up to six modules' worth of data per invocation, not one prompt in / one draft out, so the cost is higher than that baseline. Here's the estimate, and by how much.

**Assumptions** (a typical pilot solo-coach account, all modules on): ~15 active leads, ~10 active clients, ~20 upcoming bookings, ~20 invoices/packages, ~10 recent marketing sends, ~5–10 active Autopilot recipes.

| Source (per full scan, all modules on) | Approx. tokens |
|---|---|
| Leads/Pipeline summaries | ~1,200 |
| Client Delivery (engagement, goals, homework) | ~1,500 |
| Bookings | ~600 |
| Billing (invoices, packages) | ~700 |
| Marketing (recent sends, what worked) | ~500 |
| Autopilot state | ~400 |
| **Data subtotal** | **~4,900** |

The agent reasons in steps (read → cross-reference → decide → draft), so a full scan is 3–4 model calls, not one — but with the system prompt and tool definitions prompt-cached (doc 03/agent-design pattern), only the fresh tool-result tokens cost full price on steps 2+. Effective total per full scan: **~10,000–12,000 input tokens, ~1,200–1,500 output tokens** (reasoning notes + drafted insights with their action buttons).

**Cost per full daily scan** (Haiku 4.5, $1/$5 per MTok):
- Input: 12,000 × $1/1M ≈ **$0.012**
- Output: 1,500 × $5/1M ≈ **$0.0075**
- **≈ $0.02 per scan**

**Cost per on-demand check** ("check on this client/lead," single-entity scope, closer to the original Pipeline Agent): ~2,000 input + 400 output ≈ **$0.004 per check**.

**Monthly, per coach** (30 daily scans + a pilot-usage guess of ~15 on-demand checks):
- Daily scans: 30 × $0.02 ≈ $0.60
- On-demand checks: 15 × $0.004 ≈ $0.06
- **≈ $0.65–$0.70/coach/month**

**In context:** that's roughly **20x** doc 04's $0.03/coach/month baseline for the older single-call AI features — expected, since this reads six modules and runs a multi-step loop instead of one prompt/one draft. It's still under 2% of the $59/mo Practice-tier price point (doc 04's margin math), so it doesn't change the >95%-gross-margin picture. Two levers bring it down further, in order of impact:

1. **The build-time lever already flagged (§6):** re-check only what's materially changed since the last run (an invoice went overdue, a package crossed a threshold) instead of re-reading every module fresh every day. For a stable practice with few daily changes, this alone could cut the daily-scan cost by more than half.
2. **The rules panel above, directly:** a coach who turns off Marketing and Autopilot from the agent's scope cuts the per-scan data volume by roughly a fifth to a quarter, and a coach who sets most modules to "on-demand only" instead of "daily scan" replaces the $0.60/month daily-scan cost with occasional $0.004 checks.

**Model choice:** stays Haiku 4.5, consistent with the rest of the product's cost-first defaults. If cross-module correlation (the "invoice overdue *and* session tomorrow" kind of reasoning) turns out to need materially better judgment than Haiku delivers once this is actually built and tested, Sonnet 5 is the fallback (~3x Haiku's per-token price — still ~$2/coach/month at this volume) — worth a real side-by-side eval against actual output quality once built, not a switch to make from this estimate alone.
