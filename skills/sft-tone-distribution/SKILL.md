---
name: sft-tone-distribution
description: Use when generating SFT training conversations for a chess coach LLM and user-turn phrasing has become monotonically formal, contains zero typos, zero emojis, zero slang, or identical opening lines across conversations
---

# SFT Tone Distribution for Chess Coach Training Data

## Overview

Models trained on perfectly-formed user input fail on real chat: "kngiht f6", "Nf3", "castle plz 🙏". This skill enforces mandatory tone variation across four categories with hard minimums per batch.

**Core principle:** If every user turn looks like it was written by an English teacher, the trained model will break on real users. Degraded inputs are the highest-value training examples for robustness.

**RULE**: 
      Read `../references/spec-v3.md` for the spec.  
      Focus on section `5- Data Format` and the system prompt in `3- UNIFIED SYSTEM PROMPT`

**Violating the letter of these rules is violating the spirit of these rules.**

## When to Use

Use when:
- Generating SFT conversations for the chess coach assistant (spec v3)
- Auditing a batch of generated conversations before submission
- User turns across a batch show zero variation in formality, length, or punctuation
- No typos, emojis, or slang appear in any conversation
- Disambiguation replies (Slice B) are always full sentences

Do NOT use for:
- Single-conversation spot checks (use the audit checklist instead)
- Slices J (plain chat) — Category A only, single-turn exempt from distribution
- Slice K (adversarial routing) — Categories A and C only; no typos in K (K tests routing, not input robustness)

## Quick Reference

| Category | Target % | Min–Max % | Signal |
|---|---|---|---|
| A: Formal/Polite | 40% | 35–45% | Full sentences, "please", proper case |
| B: Terse/Shorthand | 25% | 20–30% | SAN-only, 1–3 words, command-style |
| C: Casual/Varied | 20% | 15–25% | Slang, emojis, abbreviations, mixed case |
| D: Degraded/Noisy | 15% | 10–20% | Typos, broken grammar, all-caps, garbled |

**Cross-cutting minimums per 25-conversation batch:**
- ≥3 emoji-containing turns (vary the emoji; not all "🙏")
- ≥5 typo-containing turns (vary the error pattern; not all "kngiht")
- ≥40% of Slice B disambiguation replies must be terse
- Zero duplicate first-user-lines
- ≥30% of conversations must contain at least one non-Formal turn
- Every conversation with 3+ user turns must contain ≥2 different tone categories

## Category Tie-Breaking

When a user turn fits multiple categories, classify by the MOST salient non-Formal feature:

1. **Contains a typo** → Category D (even if it also has emoji or slang)
2. **Contains slang or emoji** (no typo) → Category C
3. **1–5 words with no politeness markers** → Category B
4. **None of the above** → Category A

This prevents gaming by appending emojis to formal turns to reclassify them.

## Per-Conversation Mixing Requirement

Each conversation of 3+ user turns MUST contain at least 2 different tone categories. Single-category conversations are only permitted in Slice J (single-turn plain chat). This ensures the model sees register-shifting within the same interaction, which is the real robustness signal.

## Slice-Specific Adjustments

| Slice | Tone Rule |
|---|---|
| A–H | Full distribution applies |
| I (Chess knowledge) | Full distribution applies |
| J (Plain chat) | Category A only. Single-turn conversations exempt from all distribution rules. |
| K (Adversarial) | Categories A and C only. No Category D (K tests routing, not input robustness). |

## Implementation

Paste the following block into the generator prompt from §6 of the chess assistant spec if the user specifies they will use another AI, otherwise you are expected to be the generator.

### USER INPUT TONE GUIDE — MANDATORY DISTRIBUTION

Every batch of 25 conversations (unless specified otherwise) MUST follow this distribution across all user turns.
Audit after generation — reject and regenerate if any category falls below its minimum.

#### CATEGORY A: Formal/Polite — 40% (min 35%, max 45%)
Full sentences, proper grammar, polite markers.
Examples: "I'd like to move my knight to f3, please." / "Could you show me the best move?"

#### CATEGORY B: Terse/Shorthand — 25% (min 20%, max 30%)
Short, functional, no fluff. Often SAN-only. Include command-style variants, not just bare SAN.
Examples: "Nf3" / "e4" / "castle short" / "play Nf3" / "do e4" / "from e4" / "best move?"

#### CATEGORY C: Casual/Varied — 20% (min 15%, max 25%)
Slang, emojis, abbreviations, mixed case. Vary the slang across turns.
Examples: "yo send the knight f6" / "ima castle rn" / "plz show me the move 🙏" / "thx coach" / "hey what's the eval lookin like" / "bro am I cooked here"

#### CATEGORY D: Degraded/Noisy — 15% (min 10%, max 20%)
Typos, broken grammar, all-caps, intent-recoverable. Follow natural error patterns:
transposition ("kngiht"), phonetic ("queeen"), dropped character ("cstle"), proximity ("biship").
No artificial lengthening ("plzzzzzzz").
Examples: "kngiht to f6" / "CASLTE" / "whre can my rook go" / "i wanna tak back that mov" / "whos winnig rn"

#### CROSS-CUTTING RULES
1. Vary sentence length: 1-word to 8+ words.
2. Vary punctuation: proper, missing, excessive (!!, ..., ???).
3. Vary capitalization: proper, all-lowercase, ALL-CAPS.
4. Slice B disambiguation: ≥40% terse ("e4", "from e4", "the e4 one").
5. Zero duplicate first-user-lines across the batch.
6. ≥3 emoji-containing turns per batch. Vary the emoji.
7. ≥5 typo-containing turns per batch. Vary the error pattern.
8. ≥30% of conversations must contain at least one non-Formal turn.
9. Each conversation with 3+ user turns must mix ≥2 tone categories.
10. No Category D turns in Slice K. Slice J is Category A only.

#### CATEGORY TIE-BREAKING
Typo → D. Slang/emoji (no typo) → C. Terse (1-5 words, no politeness) → B. Otherwise → A.
Audit Checklist
Complete after EVERY batch of 25. Submit with the data.

text
- [ ] Category A: ____ turns (target 40%, range 35–45%)
- [ ] Category B: ____ turns (target 25%, range 20–30%)
- [ ] Category C: ____ turns (target 20%, range 15–25%)
- [ ] Category D: ____ turns (target 15%, range 10–20%)
- [ ] Slice B disambiguation: ____/____ terse (target ≥40%)
- [ ] Emoji turns in batch: ____ (target ≥3)
- [ ] Typo turns in batch: ____ (target ≥5)
- [ ] First user lines unique: YES / NO
- [ ] Conversations with ≥1 non-Formal turn: ____ (target ≥30% of batch)
- [ ] Conversations (3+ turns) mixing ≥2 categories: ____ / ____ (target 100%)
Common Mistakes
Mistake	Why It Happens	Fix
Category B is all bare SAN ("Nf3", "e4")	Generator over-fits to simplest terse form	Require command-style variants: "play Nf3", "do e4"
Same typo repeated ("kngiht" ×5)	Generator latches onto one example	Rule #7 explicitly demands varied error patterns
"Casual" = "bro" in every turn	Artificial pattern model will learn as marker	List varied slang in examples: yo, hey, bro, man, dude, ima, gonna, rn, thx, plz
All variation in first user turn only	Generator front-loads diversity	Per-conversation mixing rule forces distribution throughout
Degraded skipped entirely	Generator avoids intentional errors	Category D has hard 10% minimum — failing = regenerate
Disambiguation always full sentences	Generator defaults to polite register	Rule #4 with explicit 40% terse minimum
Emoji-appended formal turns counted as C	Generator games the tie-breaking	Tie-breaking rule: slang/emoji without typo → C only if the turn is genuinely casual, not formal+emoji
All degraded turns in one conversation	Generator hits quota in one place	Per-conversation mixing rule forces spread


Red Flags — Regenerate Batch If:
Any category below stated minimum

Three or more conversations open with identical first-user phrasing

Every typo is the same word

Every disambiguation reply is a full sentence

"Yo" or "bro" appears >3 times in one batch

All emoji turns use the same emoji

Fewer than 30% of conversations contain any non-Formal turn

All degraded turns appear in a single conversation

Category C turns differ from Category A only by appended emoji
