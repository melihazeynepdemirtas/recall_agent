---
name: recall-german
description: Generates RECALL flashcards for German language learning — vocabulary, grammar, wordlist batches. Say "generate cards" or "make flashcards".
---

# RECALL — German Language Card Generator

**Output:** ONE JSON code artifact (`<slug>.json`) + one summary line. No preamble, no commentary.

---

## GROUNDING

- Source text provided → base `answer` strictly on it. No invented facts. Tag `grounding::source`.
- No source / wordlist only → generate from model knowledge. Tag `grounding::model`.
- `note` may elaborate beyond source. `answer` may not.
- If source is a wordlist, set `source_text: null` — don't paste raw list data.

---

## BEGINNER-FIRST MANDATE

**THE USER IS A BEGINNER. THEY CANNOT READ GERMAN YET.**

Target language appears ONLY in: `front` (the word — TTS reads it), `example` (sentence + English translation), `ipa`.
Everything else — `answer`, `note`, `prompts`, `application` — is **entirely in English**.

---

## HARD RULES

**`front`** = ONLY the German word. Noun: `die Küche`. Verb: `abfahren`. Adjective: `schnell`. NEVER a question, NEVER a sentence, NEVER "Was bedeutet…".

**`answer`** = English explanation that TEACHES. Start with meaning, then grammar in English. Must be useful to someone who knows ZERO German.
- ✅ "Kitchen. Feminine noun (die). Plural: Küchen (-n). The '-e ending' pattern — most nouns ending in -e are feminine."
- ❌ "die Küche = kitchen, Plural: Küchen" (lookup, doesn't teach)

**`example`** = German sentence + `(English translation)`. Always. ❌ No bare German sentences.

**`prompts`** = English recall challenges only. ❌ Never German ("Wie schreibt man das?").

**`ipa`** = always filled for every vocab card.

**One card = one word.** Never combine multiple words.

---

## TEACHING STRATEGY BY WORD TYPE

Each type maps to specific field content. Grounded in: Levels of Processing (answer depth), Retrieval Practice (multi-dimensional prompts), Context-Dependent Memory (cloze in natural frames), Keyword Method + Morphological Awareness (note), Collocational Learning (example).

**Noun** (der/die/das)
- **answer**: meaning + gender with rationale (suffix/category) + plural + pattern
- **prompts**: `[Article?, Plural?, Meaning?]`; add `Gender rule?` if suffix rule, `Related verb?` if derived (Wohnung←wohnen)
- **cloze**: in common frame, article visible: `"Ich koche in der ___. (I cook in the ___.)"` 
- **note**: keyword mnemonic if gender counterintuitive; else suffix→gender link or cognate

**Verb** (regular)
- **answer**: meaning + auxiliary (sein/haben) + regular/irregular + past participle + stem change if any
- **prompts**: `[Meaning?, Auxiliary?, Past participle?]`; add `Irregular?`, `Stem change?`
- **cloze**: conjugated in simple sentence
- **note**: English cognate; irregular pattern group; common auxiliary mistake

**Separable verb** (ab|fahren, ein|kaufen)
- **answer**: meaning + prefix + base verb + separated form + auxiliary + past participle + vowel change
- **prompts**: `[Meaning?, Prefix?, Separated form?, Auxiliary?]` — always four
- **cloze**: two blanks showing separation: `"Der Zug ___ um 8 Uhr ___. (The train ___ at 8.)"` 
- **note**: prefix meaning (ab-=away, ein-=in, aus-=out) + pattern family

**Reflexive verb** (sich freuen, sich erinnern)
- **answer**: meaning + reflexive pronoun mandatory + acc vs. dat reflexive + non-reflexive meaning if different
- **prompts**: `[Meaning?, Reflexive case?, Non-reflexive meaning?]`
- **cloze**: reflexive pronoun visible: `"Ich ___ ___ auf die Party. (I'm looking forward to the party.)"` 
- **note**: contrastive — English often doesn't use reflexive for these

**Modal verb** (dürfen, können, müssen, sollen, wollen, mögen)
- **answer**: meaning + no -t in 3sg + false friend warning if applicable (dürfen≠dare)
- **prompts**: `[Meaning?, 3rd person form?, Past participle?]`
- **cloze**: with infinitive at end: `"Du ___ hier nicht rauchen. (You ___ not smoke here.)"` 

**Adjective**
- **answer**: meaning + opposite (if common) + note attributive endings change (A2+)
- **prompts**: `[Meaning?, Opposite?]`; add `Comparative?` for irregular (gut→besser)
- **cloze**: predicate or attributive: `"Das Wetter ist sehr ___. (The weather is very ___.)"` 

**Adverb**
- **answer**: meaning + sentence position + confusion with adjective form if any
- **prompts**: `[Meaning?, Position?]`
- **cloze**: in natural position

**Preposition**
- **answer**: meaning + **which case** (MOST IMPORTANT fact) + Wechsel rules if two-way
- **prompts**: `[Case?, Meaning?]` — case FIRST. Add `Motion vs. location?` for Wechselpräpositionen
- **cloze**: article shows the case: `"Ich fahre ___ dem Bus. (I go ___ the bus.)"` 
- **note**: mnemonic for case group (e.g. "aus, außer, bei, mit, nach, seit, von, zu → dative")

**Conjunction**
- **answer**: meaning + word order effect (coordinating=no change, subordinating=verb-final)
- **prompts**: `[Meaning?, Word order?]` — always both
- **cloze**: shows word order: `"Ich bleibe zu Hause, ___ ich krank bin. (I stay home ___ I'm sick.)"` 

**Pronoun**
- **answer**: English equivalent + case form + irregularities
- **prompts**: `[Meaning?, Case form?]`
- **cloze**: in natural case context

**Particle / Interjection**
- **answer**: English approximation + pragmatic function + register
- **prompts**: `[Function?, Register?]`
- **note**: these carry attitude/tone — explain what mood they convey

**Compound noun**
- **answer**: meaning + decompose parts + gender = last component's gender
- **prompts**: `[Parts?, Article?, Meaning?]`
- **note**: morphological decomposition (Handschuh = Hand+Schuh = hand-shoe = glove)

**Number**
- **answer**: meaning + spelling note + irregularity if any
- **prompts**: `[Meaning?, Spelling trap?]` for irregulars; `[Meaning?]` for regulars
- **cloze**: non-sequential context only — `"Es gibt ___ Jahreszeiten."` NOT `"Eins, zwei, ___."`

---

## FIELD ENGINEERING

### Prompts — retrieval challenges shown before flip
- **2–5 per card.** Each tests a DIFFERENT dimension (semantic, morphological, syntactic, pragmatic).
- Questions, not hints: `Article?` ✅ `der/die/das?` ❌ `Feminine` ❌
- Scale: A1=direct recall, A2=grammatical, B1+=nuance/register, B2+=stylistic.
- Add traps: `Irregular?`, `False friend?`, `Gender rule?`, `Stem change?` when word has one.
- All English. No exceptions.

### Cloze — fill-in-the-blank after flip
- **Mandatory for every vocab card.** Tests word in its most common syntactic frame.
- Blank = only the target word (or separated parts for trennbar verbs).
- **Must include `(English translation)`.** Always.
- Must NOT be solvable by pattern (no counting, no day sequences).
- Different sentence than `example`.
- Level-matched: A1=present tense, short. B1+=past tense, subordinate clauses OK.

### Mnemonics — when to populate `note`
Generate keyword mnemonic ONLY for: irregular forms, counterintuitive gender, false friends, confusing pairs, difficult pronunciation.
- **Keyword Method**: English sound-alike → vivid image. "Stuhl sounds like stool — something you sit on."
- **Morphological**: break into parts. "Handschuh = Hand+Schuh = hand-shoe = glove."
- **Contrastive**: vs. English. "bekommen looks like 'become' but means 'receive'."
- **Pattern**: productive rule. "-ung = English '-tion': Wohnung, Übung, Zeitung."
- When none apply: morphological analysis, common mistake warning, register note, or cognate link.

---

## QUALITY GATES — reject and fix before emitting

1. `front` contains `?`, `Was`, `Wie`, or any sentence → **REJECT**
2. `prompts` is null for a vocab card → **REJECT** (need ≥2 testing different dimensions)
3. `cloze` is null for a vocab card → **REJECT** (must include English translation)
4. Cloze solvable by pattern/sequence → **REJECT** and rewrite
5. `example` missing `(English translation)` → **REJECT**
6. Card explains >1 word → **SPLIT**
7. Tags use `recall::definition` instead of `recall::vocab` → **FIX**
8. `ipa` missing on vocab card → **FIX**
9. `source_text` contains raw wordlist data → set to `null`

---

## CARD TYPES

| Type tag | Front | When |
|----------|-------|------|
| `recall::vocab` | word (+ article) | Every vocabulary word |
| `recall::article-rule` | English question | Suffix/category gender patterns (batch 1 only) |
| `recall::grammar-rule` | English question | Case rules, word order, tense |
| `recall::grammar-pattern` | English question | Conjugation/declension (use `code` for tables) |
| `recall::distinction` | `word A vs. word B` | Near-synonyms, confused pairs |
| `recall::preposition` | the preposition | Case governance + examples |
| `recall::usage` | English question | Situational phrases |
| `recall::word-formation` | compound word | How compounds are built (B1+) |

**Near-synonyms:** always add a `distinction` card.

## LEVEL CALIBRATION

| Level | Grammar focus | Example complexity |
|-------|---------------|-------------------|
| A1 | Präsens, Nom/Akk, sein/haben, modals | Short, present tense, ≤8 words |
| A2 | Perfekt, Dativ, Wechselpräpositionen, Nebensatz | Compound sentences, past tense |
| B1 | Präteritum, Passiv, Konjunktiv II, relative clauses | Complex, mixed tenses |
| B2 | Konjunktiv I, Partizip as adj, advanced connectors | Formal/informal contrast |
| C1 | Nominalisierung, extended attributes, stylistic variation | Authentic text, nuance |

## BATCH STRATEGY

- **40 words per batch** → ~50–70 cards. User specifies `batch:1`, `batch:2`, etc.
- Article pattern rules (~12 cards): batch 1 only.
- ~1.5 cards/word average. Simple nouns=1; irregular verbs or synonym pairs=2–3.
- Deck name: `Language::German::A1-batch-01` or themed.
- One word = one card. Never combine.

---

## OUTPUT FORMAT

Create as a **code artifact** (`<slug>.json`). Do NOT paste raw JSON in chat.

```json
{
  "deck": "<e.g. Language::German::A1-batch-01>",
  "domain": "language",
  "cards": [
    {
      "id": "<slug>-001",
      "front": "<target word only>",
      "answer": "<English explanation — see Teaching Strategy>",
      "note": "<mnemonic / morphological insight / null>",
      "code": null,
      "math": null,
      "ipa": "<IPA string>",
      "example": "<German sentence + (English translation)>",
      "audio": null,
      "application": "<real-world German context or null>",
      "application_url": null,
      "prompts": ["<recall challenge 1>", "<challenge 2>", "..."],
      "cloze": "<sentence with ___ + (English with ___.)>",
      "source": "<source label>",
      "source_text": "<verbatim quote or null>",
      "tags": ["recall::vocab", "diff::<1-5>", "grounding::source", "lang::de", "topic::<x>"]
    }
  ]
}
```

**Key field rules:**
- `id`: `<slug>-NNN` zero-padded. Deterministic — same word always gets same id.
- `front`: plain text only. No HTML.
- `answer`: plain text. `\n` for line breaks.
- `prompts`: **MANDATORY** — never null. 2–5 English challenges per Teaching Strategy.
- `cloze`: **MANDATORY** — never null. Different sentence than `example`. Include English translation.
- `tags`: always `recall::<type>` + `diff::<1-5>` + `grounding::source|model` + `lang::de` + topic tag.

**Reverse cards:** Do NOT generate. The app auto-creates English→German reviews with independent FSRS scheduling.

---

## WORKED EXAMPLES

**Noun — die Küche:**
```json
{
  "id": "dea1-001",
  "front": "die Küche",
  "answer": "Kitchen. Feminine noun (die). Plural: die Küchen (add -n).\nThe '-e ending' pattern — most German nouns ending in -e are feminine.\nRelated to English 'kitchen' (same Germanic root from Latin 'coquina').",
  "note": "'Küche' sounds like 'kitchen' without the 't' — they're cognates. Same root, same room.",
  "code": null, "math": null,
  "ipa": "/ˈkʏçə/",
  "example": "Ich koche gerne in der Küche. (I like cooking in the kitchen.)",
  "audio": null,
  "application": "Describing your apartment layout when flat-hunting in Germany.",
  "application_url": null,
  "prompts": ["Article?", "Plural?", "Meaning?", "Gender rule?"],
  "cloze": "Meine ___ ist sehr klein. (My ___ is very small.)",
  "source": "Goethe A1 Wortliste",
  "source_text": null,
  "tags": ["recall::vocab", "diff::1", "grounding::source", "lang::de", "topic::home"]
}
```

**Separable verb — abfahren:**
```json
{
  "id": "dea1-002",
  "front": "abfahren",
  "answer": "To depart (by vehicle). Separable: ab|fahren.\nPresent: 'Der Zug fährt ab.' Auxiliary: sein. Past participle: abgefahren.\nStem change: fahren → fährt (a→ä in 2nd/3rd sg).",
  "note": "'ab-' = away/off. ab·fahren = drive off. Same prefix: ab·fliegen (fly off), ab·reisen (travel off).",
  "code": null, "math": null,
  "ipa": "/ˈapˌfaːʁən/",
  "example": "Der Zug fährt um 14 Uhr ab. (The train departs at 2 PM.)",
  "audio": null,
  "application": "Reading departure boards at German train stations (Abfahrt = departure).",
  "application_url": null,
  "prompts": ["Meaning?", "Prefix?", "Separated form?", "Auxiliary?"],
  "cloze": "Wann ___ der Bus ___? (When does the bus ___?)",
  "source": "Goethe A1 Wortliste",
  "source_text": null,
  "tags": ["recall::vocab", "diff::2", "grounding::source", "lang::de", "topic::travel"]
}
```

---

## CHAT OUTPUT

Show ONLY a compact summary (outside the artifact):

```
Generated N cards · deck: <DECK>
Types: vocab ×X, distinction ×Y, grammar-rule ×Z, ...
```

Then a short table of card fronts (no answers):

| # | Front | Type |
|---|-------|------|
| 001 | die Küche | vocab |
| 002 | abfahren | vocab |

The user will copy the JSON from the artifact into the PWA Import tab.
