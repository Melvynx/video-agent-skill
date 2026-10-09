---
name: video-human
description: Makes a script, caption, post or video description sound like the creator instead of a chat model. Use when the user says humanize, sounds like AI, sounds robotic, anti-AI, make it sound like me, clean this script, or before posting any text written with AI. Removes invisible characters, swaps AI-sounding words (English and French lists, plus the creator's "never use" words), rewrites the patterns that give AI text away, checks that every number, name and placeholder survived, and scores the text before and after.
argument-hint: "[check] <file, slug or text>"
allowed-tools: Bash(grep *) Bash(perl -CSD -pi -e *)
---

# Video Human

Text that sounds like the creator talking, not like a chat model writing. Same meaning, same facts, fewer tells.

The goal is to sound like the person, not to fool a detector. Detectors are unreliable; viewers are not.

## Modes

- `/video-human <file, script slug or pasted text>` → cleans it and shows what changed.
- `/video-human check <...>` → only the report, nothing changed.

A script slug is looked up in `video-agent/scripts/`. Talk to the creator in their language; clean the text in its own language.

## Before cleaning

1. Read `video-agent/voice.md` if it exists: "Words I use a lot" (never replace those), "Words I never use" (always replace those), "How I sound" (pace, energy, how they open and close).
2. Read [references/ai-tells.md](references/ai-tells.md).
3. Note every fact in the text: numbers, names, brands, quotes, `{{placeholders}}`, links, the ask. They come out unchanged.

## 1. Invisible characters

In a file:

```bash
grep -n -e $'\xe2\x80\x8b' -e $'\xe2\x80\x8c' -e $'\xe2\x80\x8d' -e $'\xe2\x81\xa0' -e $'\xef\xbb\xbf' -e $'\xc2\xad' -e $'\xf3\xa0\x80' -e $'\xf3\xa0\x81' "<file>"
```

These are zero-width spaces and joiners, the word joiner, a stray byte-order mark, soft hyphens and Unicode tag characters (they can hide text no one sees). Remove them in place; this keeps the joiner inside an emoji (👩‍💻 is built with one) and the tags of a flag like Scotland's:

```bash
perl -CSD -pi -e 's/(\x{1F3F4}[\x{E0020}-\x{E007F}]+)|[\x{200B}\x{200C}\x{2060}\x{FEFF}\x{00AD}\x{E0000}-\x{E007F}]|\x{200D}(?!\p{Extended_Pictographic})/$1/g' "<file>"
```

Run the grep again: no output means clean.

Pasted text: you cannot see these characters, so don't claim a count. The text you write back is clean anyway.

French text: a no-break space before `: ; ! ?` and inside `« »` is correct typography, keep it.

## 2. Typography

- Em dashes (U+2014) in English: the most visible tell. Replace each with what a person would type: a comma, a period, a colon, or parentheses. In French, a dash used as a pause gets the same treatment.
- Emoji used as bullet points, and a row of hashtags at the end: cut to what the platform needs (at most 5 hashtags, at the end).
- Bold on every other phrase, Title Case Headings in a caption: plain text.
- Curly quotes and `…`: leave them unless the creator wants straight ones. Phones type them too.

## 3. Words

Go through the lists in [ai-tells.md](references/ai-tells.md) and the creator's "Words I never use". Replace each hit with the plain word a person would say, or cut it when the sentence works without it. Never replace a word that is in their "Words I use a lot", even if it is on a list.

## 4. Structure

The patterns in the "Structure" part of [ai-tells.md](references/ai-tells.md). Rewrite each one so the line says its point directly. The meaning stays, the shape changes. At most one false contrast ("It's not X, it's Y") per text, and only when X is something people really believe.

## 5. Sound

Read it as if spoken:

- Sentence lengths vary. A 3-word line after a 14-word one is normal speech.
- Contractions when the creator uses them ("it's", "you're"; in French "t'as", "je sais pas" only if voice.md shows they talk like that).
- One idea per line in a script; the ```script block format stays exactly as it was.
- Nothing they wouldn't say to a friend.

## 6. Check the facts

Compare before and after: every number, name, brand, quote, link, placeholder and the ask is still there, unchanged. Anything missing: put it back. Never add a fact, a number or an example that wasn't there; a generic example becomes `{{a real example}}`.

## 7. Output

Files: edit in place, keeping their format (headings, visual notes, the script block). Pasted text: give the clean version in one block, ready to copy.

Then the report, short:

```text
hooks-too-long (scripts/week-2026-10-05.md)
- 2 invisible characters removed (lines 4, 9)
- 3 em dashes → 2 commas, 1 period
- words: "leverage" → "use", "seamless" → cut, "game-changer" → "the thing that fixed it"
- line 6: "It's not about posting more, it's about posting better" → "Post less. Make each one count."
- kept: 4 numbers, 1 placeholder, the ask
```

End the report with a score, before and after: `score 4/10 → 9/10 PASS`. Five checks, 0 to 2 each:

| Check | 2 when |
|---|---|
| Clean | no invisible characters, no em dashes |
| Words | no list word and none of their "never use" words |
| Shapes | no structure tell left, at most one false contrast |
| Rhythm | sentence lengths vary, it reads as spoken |
| Facts | every fact survived (0 or 2, nothing in between) |

PASS = 9 or more with Facts at 2. Under that, do another pass or say what blocks it.

`check` mode: the same report with the suggested changes, the score before, plus a count of tells per kind.

## Honest limits

- Not a way to pass AI detectors, and no promise about them. The aim is the creator's voice.
- A thin voice.md gives a clean but neutral text. The more real words of theirs it has, the more the result sounds like them.
- Some list words are fine in context ("navigate" a website, "robust" for a real test). Judge the sentence, not the word.
