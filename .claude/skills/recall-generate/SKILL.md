---
name: recall-generate
description: Generates a RECALL flashcard JSON block from the current conversation or source text. Output only - no file writes, no git. Paste the result into the PWA Import tab. Triggers on /recall-generate, "make cards", or "generate flashcards".
---

# /recall-generate - card JSON generator (copy-paste workflow)

Output a single JSON block that can be pasted into the RECALL PWA Import tab.
No file I/O, no git operations. Works in any Claude context (Claude Code or Claude.ai chat).

## Inputs

Parse from the invocation:
- `deck:` - e.g. `CS::OSTEP::ch28-locks`. If absent, infer from the topic and state it in one line before the JSON.
- `src:` - source label, e.g. `OSTEP-ch28`. If absent, use `claude-study`.
- Topic - infer from the conversation if not stated.

**Slug:** `deck.toLowerCase().replace(/[^a-z0-9]+/g, "-").replace(/^-|-$/, "")`

## Card generation rules

Determine the domain from the deck name or topic, then follow the corresponding skill:

| Domain | Skill file |
|--------|-----------|
| German / Language | `prompts/RECALL-German.md` |
| CS / Theory | `prompts/RECALL-CS.md` |
| Finance | `prompts/RECALL-Finance.md` |

Read ONLY the relevant domain skill. Do not load the others.

## Output

Emit exactly:
1. One JSON block (the complete card set per the domain skill's format)
2. One summary line: `Generated N cards · types:{type:X, ...} · deck <DECK>`

## Next step (remind the user)

After the JSON, add one line:
> Paste this into the RECALL PWA → Import tab → Import button.
