# 01 — Market Research (verified)

All figures below were adversarially fact-checked against primary sources during a multi-agent research pass (July 2026). Figures that failed verification are listed at the bottom with reasons — do not use them in decks without that caveat.

## 1. Market size & demand

| Figure | Value | Source | Confidence |
|---|---|---|---|
| Global coaching industry revenue | **$5.34B**, +17% vs 2023 | [ICF/PwC 2025 Global Coaching Study](https://coachingfederation.org/blog/global-coaching-industry-surpasses-5-34-billion-as-emerging-markets-drive-growth/) (n=10,035 coaches, 127 countries) | Verified against primary source |
| Coach practitioners worldwide | **122,974**, +13% vs 2023 | Same study | Verified |
| Coaches who invested in new tech (incl. AI) last year | **19%** | Same study | Verified |
| Coaches who call better platforms/tech a priority | **54%** | Same study | Verified |
| Coaches reporting digital platforms not yet part of practice | **53%** | Same study | Verified |

**The opportunity in one line:** the profession's own trade body reports most of its members haven't bought the tools they say they need. That 19%→54% gap is our beachhead.

**Caveat:** ICF data is self-reported and published by the industry's own body — directionally reliable, not audit-grade.

## 2. Competitive landscape

### AI-replaces-the-coach products (we do NOT compete here)
| Product | Position | Signal |
|---|---|---|
| Valence "Nadia" | Enterprise AI executive coaching | $50M round, Sept 2025 |
| CoachHub + AIMY | 3,500+ human coaches + AI module | $42.3M debt financing Dec 2024; AIMY launched Feb 2025 |
| BetterUp | Human+AI hybrid, enterprise | ~$3,000–5,000/user/year (reported) |
| Rocky.ai | AI-first leadership coaching | ~$10–13/user/month (reported) |

### Tools-for-the-coach products (our actual competitors)
| Product | Reported price | Weakness we exploit |
|---|---|---|
| Delenta | ~$29/mo | Generic practice management; AI is a bolt-on checkbox |
| Paperbell | ~$57/mo | No AI-first workflows |
| Simply.Coach | ~$29/mo | Same |
| CoachAccountable | ~$20/mo | Same |

(Pricing reported by vendors/comparison sites; not independently re-verified — treat as directional.)

**The gap:** nobody in the $20–60/mo tier is AI-first around the coach's actual daily workflow (notes → summary → prep → follow-up). Enterprise players won't come down-market for solo coaches; solo-tier incumbents haven't rebuilt around AI.

## 3. Why "all-in-one" — and why it usually fails

Coaches currently stitch a scheduler + CRM + e-sign + email tool + invoicing + notes app. Consolidation is a real pain point (it's why Delenta/Paperbell exist at all). But all-in-one platforms fail when founders build breadth before earning depth. Our mitigation is the wedge strategy (README §4): earn daily usage with Client Delivery first; add modules only after revenue proves the base.

## 4. Defensibility

- **"GPT-wrapper" risk is real:** model capability advantages last ~6 months ([Andrew Chen, a16z](https://andrewchen.substack.com/p/revenge-of-the-gpt-wrappers-defensibility)); Jasper fell from ~$120M (2023) to $35–55M (2024) revenue when its wrapper advantage evaporated.
- **Our moats, in order:** (1) accumulated client history + signed contracts + pipeline inside the platform (switching cost), (2) distribution through coach communities/ICF chapters (relationship moat), (3) the human-service layer (services don't commoditize like features).

## 5. Regulation (EU AI Act + GDPR)

- Assistive AI (summaries, drafts supporting the coach's judgment) sits in the **limited-risk tier**: disclose "this is AI" and we're largely compliant ([EU AI Act summary](https://artificialintelligenceact.eu/high-level-summary/)).
- **Lines we never cross:** no employee-evaluation features for corporate customers (pushes us toward the high-risk Annex III regime); no emotion/sentiment scoring of employees (restricted workplace use).
- **GDPR:** the coach is data controller, we are processor → standard DPA before first paying EU customer; client data never used for model training without explicit consent; session notes encrypted at rest.
- **Security is existential in this category:** a vibe-coded product (EnrichLead, built with Cursor) was publicly breached post-launch. A breach of coaching notes would be brand-ending. Independent security review is a non-negotiable line item before real client data flows.

## 6. Figures we REJECTED (do not use)

| Claim | Why rejected |
|---|---|
| "AI coaching market = $62B by 2026" | Contradicts the entire coaching industry's verified size by >10×; uncited vendor report |
| "AI Career Coach market = $5.48B (2025), $14.82B by 2030" | No disclosed methodology; contradicted by competing vendor estimates for the same category/year |
| "Life coaching market = $3.64B (2025)" | Inconsistent with ICF primary data; methodology opaque |
| "25% of YC W25 cohort was 95% AI-generated code" | Failed source verification in fact-check pass |

**Team rule:** if a market number isn't in §1 of this document, it doesn't go in a deck without a fresh primary-source check.
