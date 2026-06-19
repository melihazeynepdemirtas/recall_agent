---
name: recall-cards
description: Generates flashcards from the current study conversation and/or repo source material, writes them directly to cards/*.json, updates the FSRS scheduler, and pushes to GitHub — in one step. Invoke at the end of a study session when the user says /recall-cards, "make cards", "generate flashcards", or similar.
---

# /recall-cards — one-shot JSON card pipeline

When invoked, run the ENTIRE chain below without stopping for confirmation between steps. The user's only action is invoking this skill. At the end their cards are live in the PWA.

Repo root: `D:\recall_agent\recall_agent`.

## Inputs

Parse the invocation for:
- `deck:` — e.g. `CS::OSTEP::ch28-locks`. If absent, infer from the topic and confirm in one short line, then proceed with your best guess.
- `src:` — source label, e.g. `OSTEP-ch28`. If absent, use the material filename or `claude-study`.
- A topic — infer from the conversation if not stated.

**Slug derivation:** `deck.toLowerCase().replace(/[^a-z0-9]+/g, "-").replace(/^-|-$/, "")`
Example: `CS::OSTEP::ch28-locks` → `cs-ostep-ch28-locks`

## Step 1 — Read source + generate cards

Determine the domain from the deck name or topic, then follow the corresponding skill:
- German / Language → `prompts/RECALL-German.md`
- CS / Theory → `prompts/RECALL-CS.md`
- Finance → `prompts/RECALL-Finance.md`

Read ONLY the relevant domain skill. Do not load the others.

**Source material:**
- Read only the relevant chapter section from `cs/`, `language/`, or `finance/` in the repo if it exists (keep token cost low — targeted section reads, not full files).
- Also use facts established in THIS conversation.
- **Prompt caching:** For source text ≥ 1024 tokens, include `cache_control: {"type": "ephemeral"}` on the source content block to activate Anthropic prompt caching and reduce cost on re-runs.

**Grounding tags:**
- `grounding::source` — answer comes strictly from the source file
- `grounding::model` — answer comes from the conversation or model knowledge

**GUID format:** `<slug>-NNN` (zero-padded to 3 digits, deterministic — same fact always gets the same id so re-runs update rather than duplicate).

Generate the complete card list per the format and field rules in `prompts/RECALL-Generate.md`. Write it directly to the file in Step 2 — do not dump the full JSON into chat.

## Step 2 — Write `cards/<slug>.json`

Read `cards/<slug>.json`:
- **File exists:** merge generated cards in. For each card, if its `id` already exists → replace that entry; if not → append.
- **File missing:** write fresh: `{ "deck": "...", "domain": "...", "cards": [...] }`.

Write the final merged JSON (pretty-printed, 2-space indent) back to disk using the Write tool.

## Step 3 — Update `state/scheduler.json`

Read `state/scheduler.json`.

For each card `id` that is **not already present** in the scheduler, add:

```json
"<card-id>": {
  "due": "<ISO 8601 timestamp — now>",
  "stability": 0,
  "difficulty": 0,
  "elapsed_days": 0,
  "scheduled_days": 0,
  "learning_steps": 0,
  "reps": 0,
  "lapses": 0,
  "state": 0,
  "last_review": null
}
```

**Never modify existing scheduler entries.** Existing review history must be preserved.

Write the updated `state/scheduler.json` back to disk.

## Step 4 — Update `cards/index.json`

Read `cards/index.json`. Rebuild the sorted `decks` array from all `.json` files currently in `cards/` (excluding `index.json` itself), stripping the `.json` extension.

Write back:
```json
{ "decks": ["cs-ostep-ch28-locks", "..."] }
```

## Step 5 — Commit + push

```
git -C "D:\recall_agent\recall_agent" add cards/ state/scheduler.json
git -C "D:\recall_agent\recall_agent" commit -m "cards: <deck> <YYYY-MM-DD> [recall-cards skill]

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>"
git -C "D:\recall_agent\recall_agent" push origin main
```

## Step 6 — Report

One concise summary: `N cards generated (X added, Y updated) · deck: <deck> · source: <src>`.

## Guardrails

- Never invent facts for `grounding::source` cards — answers must come verbatim from the source file.
- JSON field values use `\n` for line breaks — never literal newlines or tabs inside string values.
- Read only the relevant section of a source file, never the whole file.
- Card `front` and `answer` are plain text — no HTML tags.
- One card = one fact. Split any card that tests two things.
