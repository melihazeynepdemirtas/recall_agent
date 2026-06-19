---
name: recall-finance
description: Generates RECALL flashcards for finance and investment concepts — markets, instruments, metrics, risk. Say "generate cards" or "make flashcards".
---

# RECALL — Finance Card Generator

**Output:** ONE JSON code artifact (`<slug>.json`) + one summary line. No preamble, no commentary.

---

## GROUNDING

- Source text provided → base `answer` strictly on it. No invented facts. Tag `grounding::source`.
- No source → generate from model knowledge. Tag `grounding::model`.
- `note` may elaborate beyond source. `answer` may not.

---

## CARD RULES

**Atomic:** One card = one fact. Split cards that test two things.

**Crux-targeted:** Prefer "why" over "what". No definition-only cards unless the term is critical.

**Dense:** Every concept, mechanism, risk factor, and metric gets its own card.

---

## FINANCE DOMAIN RULES

**Every concept requires at minimum:**
- `definition` — what it is
- `mechanism` — how it works (cash flows, pricing, mechanics)
- `risk` — **MANDATORY** — what can go wrong, under what conditions
- `distinction` — comparison with related concept

**Metrics coverage:** One card per stage: definition → formula → interpretation → common misuse.

**`math` field:** Use LaTeX for formulas. `$PV = \\frac{FV}{(1+r)^n}$` inline, `$$...$$` for display.

**NEVER give investment advice.** Frameworks and vocabulary only. Cards teach concepts, not strategies.

---

## TYPE MIX

| Type tag | Front pattern | When |
|----------|---------------|------|
| `definition` | "What is X?" | Core concept |
| `mechanism` | "How does X work?" | Cash flows, pricing, settlement |
| `why` | "Why does X exist/matter?" | Economic rationale |
| `risk` | "What are the risks of X?" | **Mandatory for every concept** |
| `distinction` | "X vs. Y" | Related instruments, metrics |
| `procedure` | "Steps to calculate/evaluate X?" | Valuation, analysis process |
| `edge-case` | "When does X fail/not apply?" | Market conditions, assumptions |
| `application` | "When would you use X?" | Practical context |
| `formula` | "Formula for X?" | Mathematical relationship |
| `misuse` | "Common mistake with X?" | Misinterpretation, misapplication |

---

## OUTPUT FORMAT

Create as a **code artifact** (`<slug>.json`). Do NOT paste raw JSON in chat.

```json
{
  "deck": "<e.g. Finance::Fixed-Income::ch4-duration>",
  "domain": "finance",
  "cards": [
    {
      "id": "<slug>-001",
      "front": "<question — unambiguous, no answer leakage>",
      "answer": "<headline first, then elaboration. Plain English before formulas. \\n for line breaks.>",
      "note": "<intuition, common mistake, analogy, or null>",
      "code": null,
      "math": "<LaTeX formula or null>",
      "ipa": null,
      "example": "<concrete numerical example or null>",
      "audio": null,
      "application": "<real-world context or null>",
      "application_url": "<URL or null>",
      "prompts": null,
      "cloze": null,
      "source": "<source label>",
      "source_text": "<1–3 verbatim sentences or null>",
      "tags": ["recall::<type>", "diff::<1-5>", "grounding::source", "finance::<subtopic>"]
    }
  ]
}
```

**Key field rules:**
- `id`: `<slug>-NNN` zero-padded. Deterministic.
- `front`: plain text. No HTML. Clear question.
- `answer`: plain text. `\n` for line breaks. Headline first, then formula context, then elaboration.
- `math`: LaTeX formulas. Keep math in `math` field, plain-English explanation in `answer`.
- `example`: concrete numerical example showing the formula in action (e.g. "A $1000 bond with 5% coupon…").
- `tags`: always `recall::<type>` + `diff::<1-5>` + `grounding::source|model` + `finance::<subtopic>`.
- Every concept MUST have a `risk` card. No exceptions.

---

## CHAT OUTPUT

Show ONLY a compact summary (outside the artifact):

```
Generated N cards · deck: <DECK>
Types: definition ×3, mechanism ×2, risk ×3, formula ×2, ...
```

Then a short table of card fronts (no answers):

| # | Front | Type |
|---|-------|------|
| 001 | What is duration? | definition |
| 002 | What are the risks of duration matching? | risk |

The user will copy the JSON from the artifact into the PWA Import tab.
