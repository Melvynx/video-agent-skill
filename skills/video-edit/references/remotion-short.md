# The `Short` composition

Add this to the Remotion studio (`video-agent/studio/`) once. It plays the kept ranges of a take back to back and draws the style from [style.md](style.md) on top: punch-in zooms, word captions, the hook card, callouts and the progress bar. Everything it needs comes from one edit file per video, so the code never changes between videos.

Tested with Remotion 4.0.534 and the `template-tiktok` starter.

## 1. Font package

The template's font has no accents. Add Google Fonts at the same version as Remotion:

```bash
npx remotion versions
npm i @remotion/google-fonts@<the remotion version it prints>
```

## 2. `src/Short/index.tsx`

```tsx
import { Caption, createTikTokStyleCaptions, TikTokPage } from "@remotion/captions";
import React, { useMemo } from "react";
import {
  AbsoluteFill,
  CalculateMetadataFunction,
  interpolate,
  OffthreadVideo,
  Sequence,
  Series,
  spring,
  staticFile,
  useCurrentFrame,
  useVideoConfig,
} from "remotion";
import { loadFont } from "@remotion/google-fonts/Montserrat";

// Heavy, readable, and it has accents (é, ç, ñ, ü...).
const { fontFamily: FONT } = loadFont("normal", { weights: ["900"], subsets: ["latin", "latin-ext"] });

type Range = { fromMs: number; toMs: number };

export type ShortProps = {
  video: string; // file in public/, e.g. "take.mp4"
  captions: string; // its word timings in public/, e.g. "take.json"
  accent: string; // brand color, e.g. "#FFD400"
  ranges: Range[]; // the kept parts of the take, in source ms, in order
  hook: { text: string; seconds: number } | null; // *word* = accent marker
  callouts: { text: string; atMs: number }[]; // atMs = source ms of the word
  words?: Caption[]; // filled by calculateShortMetadata, in output ms
};

const FPS = 30;
const toFrame = (ms: number) => Math.round((ms / 1000) * FPS);

// Platform safe zone on 1080x1920: text stays between y 230 and y 1440,
// and left of x 850 below y 900 (the like, comment and share buttons).
const CAPTION_BOX = { top: 1150, left: 60, width: 780, height: 260 };

// 2-frame audio fade at both ends of a kept range, so a cut never clicks.
const fade = (f: number, frames: number) =>
  frames < 6 ? 1 : interpolate(f, [0, 2, frames - 3, frames - 1], [0, 1, 1, 0], { extrapolateLeft: "clamp", extrapolateRight: "clamp" });

// Where each kept range lands in the output.
const layout = (ranges: Range[]) => {
  let at = 0;
  return ranges.map((r) => {
    const from = toFrame(r.fromMs);
    const frames = Math.max(1, toFrame(r.toMs) - from);
    const part = { ...r, from, frames, at, shiftMs: ((at - from) / FPS) * 1000 };
    at += frames;
    return part;
  });
};

// Source ms -> output ms, or null when that moment was cut.
const toOutputMs = (ms: number, parts: ReturnType<typeof layout>) => {
  const p = parts.find((p) => ms >= p.fromMs && ms < p.toMs);
  return p ? ms + p.shiftMs : null;
};

export const calculateShortMetadata: CalculateMetadataFunction<ShortProps> = async ({ props }) => {
  const parts = layout(props.ranges);
  const raw = (await (await fetch(staticFile(props.captions))).json()) as Caption[];
  const words = raw.flatMap((w) => {
    const p = parts.find((p) => w.startMs >= p.fromMs && w.startMs < p.toMs);
    if (!p) return [];
    const end = Math.min(w.endMs, p.toMs);
    return [{ ...w, startMs: w.startMs + p.shiftMs, endMs: end + p.shiftMs, timestampMs: null }];
  });
  return {
    fps: FPS,
    durationInFrames: Math.max(1, parts.reduce((n, p) => n + p.frames, 0)),
    props: { ...props, words },
  };
};

export const Short: React.FC<ShortProps> = ({ video, accent, ranges, hook, callouts, words = [] }) => {
  const parts = useMemo(() => layout(ranges), [ranges]);
  const { pages } = useMemo(
    () => createTikTokStyleCaptions({ captions: words, combineTokensWithinMilliseconds: 600 }),
    [words],
  );
  // Punch-in: the framing alternates on each cut; pieces under 0.8 s keep the previous one.
  const zooms = useMemo(() => {
    let z = 1;
    return parts.map((p, i) => {
      if (i > 0 && p.frames >= toFrame(800)) z = z === 1 ? 1.12 : 1;
      return z;
    });
  }, [parts]);

  return (
    <AbsoluteFill style={{ backgroundColor: "black" }}>
      <Series>
        {parts.map((p, i) => (
          <Series.Sequence key={i} durationInFrames={p.frames}>
            <AbsoluteFill style={{ transform: `scale(${zooms[i]})` }}>
              <OffthreadVideo
                src={staticFile(video)}
                trimBefore={p.from}
                volume={(f) => fade(f, p.frames)}
                style={{ width: "100%", height: "100%", objectFit: "cover" }}
              />
            </AbsoluteFill>
          </Series.Sequence>
        ))}
      </Series>

      {pages.map((page, i) => {
        const next = pages[i + 1];
        const from = toFrame(page.startMs);
        const until = toFrame(next ? next.startMs : page.startMs + page.durationMs);
        if (until <= from) return null;
        return (
          <Sequence key={i} from={from} durationInFrames={until - from}>
            <CaptionPage page={page} accent={accent} />
          </Sequence>
        );
      })}

      {callouts.map((c, i) => {
        const at = toOutputMs(c.atMs, parts);
        if (at === null) return null;
        return (
          <Sequence key={i} from={toFrame(at)} durationInFrames={toFrame(1200)}>
            <Callout text={c.text} accent={accent} frames={toFrame(1200)} />
          </Sequence>
        );
      })}

      {hook ? (
        <Sequence durationInFrames={toFrame(hook.seconds * 1000)}>
          <HookCard text={hook.text} accent={accent} frames={toFrame(hook.seconds * 1000)} />
        </Sequence>
      ) : null}

      <ProgressBar accent={accent} />
    </AbsoluteFill>
  );
};

const CaptionPage: React.FC<{ page: TikTokPage; accent: string }> = ({ page, accent }) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();
  const now = page.startMs + (frame / fps) * 1000;
  const pop = spring({ frame, fps, config: { damping: 200 }, durationInFrames: 4 });
  const fontSize = Math.min(110, Math.floor(CAPTION_BOX.width / (page.text.trim().length * 0.72)));
  return (
    <AbsoluteFill style={{ ...CAPTION_BOX, justifyContent: "center", alignItems: "center" }}>
      <div
        style={{
          fontFamily: FONT,
          fontSize,
          lineHeight: 1.05,
          maxWidth: "100%",
          textAlign: "center",
          textTransform: "uppercase",
          color: "white",
          WebkitTextStroke: "18px black",
          paintOrder: "stroke",
          transform: `scale(${0.85 + 0.15 * pop})`,
        }}
      >
        {page.tokens.map((t, i) => (
          <span key={i} style={{ whiteSpace: "pre", color: now >= t.fromMs && now < t.toMs ? accent : "white" }}>
            {t.text}
          </span>
        ))}
      </div>
    </AbsoluteFill>
  );
};

const HookCard: React.FC<{ text: string; accent: string; frames: number }> = ({ text, accent, frames }) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();
  const enter = spring({ frame, fps, config: { damping: 14 }, durationInFrames: 10 });
  const exit = interpolate(frame, [frames - 6, frames], [1, 0], { extrapolateLeft: "clamp", extrapolateRight: "clamp" });
  return (
    <AbsoluteFill style={{ top: "12%", bottom: undefined, height: "auto", alignItems: "center" }}>
      <div
        style={{
          background: "white",
          color: "black",
          fontFamily: FONT,
          fontSize: 76,
          lineHeight: 1.1,
          textTransform: "uppercase",
          textAlign: "center",
          maxWidth: "84%",
          padding: "28px 40px",
          borderRadius: 28,
          boxShadow: "0 12px 40px rgba(0,0,0,0.35)",
          transform: `scale(${0.6 + 0.4 * enter})`,
          opacity: Math.min(enter, exit),
        }}
      >
        {text.split(/(\*[^*]+\*)/).filter(Boolean).map((part, i) =>
          part.startsWith("*") ? (
            <span key={i} style={{ background: accent, borderRadius: 10, padding: "0 12px" }}>
              {part.slice(1, -1)}
            </span>
          ) : (
            <span key={i}>{part}</span>
          ),
        )}
      </div>
    </AbsoluteFill>
  );
};

const Callout: React.FC<{ text: string; accent: string; frames: number }> = ({ text, accent, frames }) => {
  const frame = useCurrentFrame();
  const { fps, width } = useVideoConfig();
  const pop = spring({ frame, fps, config: { damping: 12 }, durationInFrames: 8 });
  const exit = interpolate(frame, [frames - 5, frames], [1, 0], { extrapolateLeft: "clamp", extrapolateRight: "clamp" });
  const fontSize = Math.min(240, Math.floor((width * 0.8) / (text.length * 0.72)));
  return (
    <AbsoluteFill style={{ top: "26%", bottom: undefined, height: "auto", alignItems: "center" }}>
      <div
        style={{
          fontFamily: FONT,
          fontSize,
          textTransform: "uppercase",
          color: accent,
          WebkitTextStroke: "24px black",
          paintOrder: "stroke",
          transform: `scale(${0.5 + 0.5 * pop})`,
          opacity: exit,
        }}
      >
        {text}
      </div>
    </AbsoluteFill>
  );
};

const ProgressBar: React.FC<{ accent: string }> = ({ accent }) => {
  const frame = useCurrentFrame();
  const { durationInFrames } = useVideoConfig();
  return (
    <div
      style={{
        position: "absolute",
        top: 0,
        left: 0,
        height: 10,
        width: `${(frame / Math.max(1, durationInFrames - 1)) * 100}%`,
        background: accent,
      }}
    />
  );
};
```

## 3. Register it in `src/Root.tsx`

Keep the template's composition and add `Short` next to it (wrap both in `<>...</>`):

```tsx
import { calculateShortMetadata, Short } from "./Short";

// inside RemotionRoot's return:
<Composition
  id="Short"
  component={Short}
  calculateMetadata={calculateShortMetadata}
  width={1080}
  height={1920}
  defaultProps={{
    video: "take.mp4",
    captions: "take.json",
    accent: "#FFD400",
    ranges: [],
    hook: null,
    callouts: [],
  }}
/>
```

Check it compiles: `npx tsc --noEmit`.

## 4. The edit file: `public/<slug>.edit.json`

One per video. Every time is in **source** milliseconds (the times in the captions JSON). The composition maps them to the output after the cuts.

```json
{
  "video": "my-take.mp4",
  "captions": "my-take.json",
  "accent": "#FFD400",
  "ranges": [
    { "fromMs": 380, "toMs": 4120 },
    { "fromMs": 4790, "toMs": 9650 },
    { "fromMs": 14200, "toMs": 31880 }
  ],
  "hook": { "text": "Your hooks are *too long*", "seconds": 2.5 },
  "callouts": [{ "text": "3x", "atMs": 17420 }]
}
```

- `ranges`: the parts to keep, in order, never overlapping. The output length is their sum.
- `hook`: 6 words or fewer. `*word*` gets the accent marker. `null` for no card.
- `callouts`: `atMs` = the `startMs` of the word the callout rides on. A callout inside a cut part is skipped.

## 5. Commands (from `video-agent/studio/`)

```bash
# QC frames (Read the PNGs and check them)
npx remotion still Short out/<slug>-qc-<frame>.png --frame=<frame> --props=public/<slug>.edit.json

# Full render
npx remotion render Short out/<slug>.mp4 --props=public/<slug>.edit.json

# Live preview in the browser, scrub and tweak (optional)
npx remotion studio --props=public/<slug>.edit.json
```

`--frame` is in output frames at 30 fps: second × 30.

## When something breaks

| Symptom | Fix |
|---|---|
| `trimBefore` is not a prop | an older Remotion: run `npx remotion upgrade`, or use `startFrom` (its old name) |
| Captions out of sync after the cuts | a range overlaps the next one or is out of order: sort and merge them |
| Black frames at a cut | a range ends after the end of the video: clamp `toMs` to the duration from ffprobe |
| Captions show the wrong words | fix the words in `public/<slug>.json` (keep `startMs` / `endMs`), never the timings |
| Text too wide | the size comes from the character count: shorten the hook or the callout |
| Washed-out colors | an HDR take converted with ffmpeg: use the original file, `OffthreadVideo` maps HDR to normal colors on its own |
| Render slow | normal on a laptop: about 3 to 5 s of render per second of video. `--concurrency=50%` frees the machine |
