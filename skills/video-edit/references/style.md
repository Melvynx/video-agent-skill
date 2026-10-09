# The edit style

Simple on purpose. One take of a person talking, made easy to watch on a phone with the sound off. No templates to pick, no effects to tune: the same rules every time, so a creator can post daily.

## Cuts

| Rule | Value | Why |
|---|---|---|
| Silences | cut every pause longer than 0.35 s | dead air is where people swipe |
| Breathing room | keep 0.10 s before a word and 0.15 s after | word timings drift a little; this keeps every word whole |
| Retakes | keep the last complete attempt of a line, drop the earlier ones | creators repeat a line until it sounds right |
| False starts | drop a half word or "uh" right before a restart | |
| Start | the first kept word is the first word of the hook | no breath, no "ok", no look at the camera before |
| End | cut 0.3 s after the last word | a loop restarts faster |
| Off-script talk | drop "wait", "let me redo that", asides to someone in the room | |
| Sound at a cut | a 2-frame fade out and in | a cut never clicks |

A cut is only safe in a real silence: check the gaps between words against `silencedetect` (see the skill) before cutting.

## Framing

- Output 1080x1920, 30 fps. A horizontal take is cropped to the center (the face must be near the middle).
- **Punch-in**: every cut alternates between full frame (1.0) and a light zoom (1.12). A piece shorter than 0.8 s keeps the previous framing. That hides the jump of the cut and gives rhythm without effects.
- **Safe zone**: all text stays between y 230 and y 1440, and left of x 850 from y 900 down. The apps cover the rest with their tabs, buttons, name and description.

## Captions

- 1 to 3 words at a time, uppercase, Montserrat 900, white with a thick black outline.
- The word being said turns to the accent color.
- Pop in fast (about 4 frames, 0.85 → 1 scale).
- In a box from y 1150 to 1410 and x 60 to 840: below the face, clear of the buttons on the right and the description at the bottom.
- Always on. Most people watch muted.

## Hook card

- The on-screen hook from the script: 6 words or fewer, never the same words as the spoken hook (it adds to it).
- White card, black text, at 12% from the top, for the first 2 to 3 s, then fades out.
- One `*word*` in accent color: the word that carries the tension.

## Callouts

- One big word or number, in the accent color with a black outline, at about 26% from the top for 1.2 s.
- Only where the script puts weight: a number, a result, the name of a tool. About one every 8 to 10 s, never two closer than 4 s.
- A callout repeats a word the person says at that moment. It never adds information they don't say.

## Progress bar

A 10 px bar in the accent color across the top, growing to the end. It tells the viewer the video is short.

## Audio

- Voice at -14 LUFS integrated, sample peaks limited at -2 dBFS (about -1.5 dB true peak after AAC).
- Music is optional, royalty-free only, 22 dB under the voice. None is better than a wrong one.

## What this style is not

No transitions, no sound effects on every cut, no emoji rain, no stock B-roll, no slanted or wobbling text. If the creator wants more, the composition is plain React: they can ask Claude to add a scene, but keep the defaults calm.
