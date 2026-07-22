# 10 — The AI Agent Layer ("Pipeline Agent")

**What was asked:** an agentic AI feature inside the CRM/pipeline — not just one-shot AI drafts (which we already have everywhere), but something that can look at a lead, reason across multiple steps, and propose what to do next. Researched, verified, and built as a working demo — see §4.

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

## 5. What this changes in other docs

- **doc 02** (product spec) — Module 1–2 gets this capability added.
- **doc 03** (stack) — Mastra added as a new row (application-layer library, not a hosted service).
- **doc 07** (architecture) — AI layer (③) gets Mastra alongside Claude API.
- **doc 08** (questionnaire) — new questions on how far the agent should be allowed to look ahead / act.
