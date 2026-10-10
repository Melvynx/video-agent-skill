# video-agent-skills

Free Claude Code skills to script, edit, translate and publish your videos: short-form (YouTube Shorts, TikTok, Instagram Reels) and full YouTube videos.

Only markdown. No app, no server. The skills tell Claude how to work and which tools to call: ffmpeg, yt-dlp, and Remotion with whisper.cpp for the edit. Optional: [Treg](https://treg.to) to audit your Instagram and publish on YouTube, Instagram and TikTok, an ElevenLabs key to translate, Codex to generate images.

## The skills

| Skill | What it does |
|---|---|
| `/video-setup` | Start here. Sets up video editing on your computer and in your project: checks the tools, creates `video-agent/`, prepares the editing studio, writes your `voice.md` from your own videos or a short interview, pre-approves the commands, and optionally connects Treg, ElevenLabs and Codex. |
| `/video-audit` | Installs Treg if needed, connects your Instagram and audits it: your last reels with real stats (views, watch time, saves, shares), what over-performs against your own median, the first seconds of your best and worst reels, and the 3 changes to make, in `audit.md`. |
| `/video-viral` | Finds what over-performs in your niche: recent Shorts of 10 to 12 accounts near your size and above, outliers against each account's median, the first 3 seconds of each, saved to `swipe.md`. |
| `/video-script` | A week of scripts from what really happened in your week, in your voice: 3 hooks per script from 22 formulas, scored, a re-hook, timing checked at your pace, visual notes, then a humanize pass (invisible characters, AI words in English and French, AI sentence shapes, facts checked). Also `hook <topic>` and `humanize <text>` for any caption or description. |
| `/video-edit` | A raw take into a finished video: a short (1080×1920, captions in the platforms' safe zone, hook card) or a full YouTube video (1920×1080, chapters). Word-by-word captions, silences and retakes cut, simple motion design (callouts, punch-in zooms), voice at -14 LUFS, an .srt for CapCut or Premiere, and the post copy. |
| `/long-to-short-videos` | One long video (yours, a podcast, a live) into finished shorts: the moments that stand alone, each one edited with `video-edit` (captions, hook card, callouts), plus new scripts from the ideas too spread out to clip. |
| `/video-translate` | A take in another language with your own cloned voice, through ElevenLabs Dubbing and your key, then edited with captions in the new language. |
| `/codex-images` | Thumbnails, covers and stills generated with Codex's image tool (your ChatGPT plan), several in parallel, with your photos as reference. |
| `/video-publish` | Publishes a finished video on YouTube (Shorts or long, scheduled or now), Instagram Reels and TikTok through Treg, with the light export, the cover, the caption and the title. Nothing goes out before your yes. Without Treg, a ready-to-post kit. |

## Install

In your project folder:

```bash
npx skills add Melvynx/video-agent-skills -a claude-code
```

Add `-g` to install them for every project. `npx skills update` gets the latest version.

Or paste `https://github.com/Melvynx/video-agent-skills` into Claude Code and ask it to install these skills.

Or copy them by hand (into `~/.claude/skills/` instead for every project). macOS and Linux:

```bash
git clone https://github.com/Melvynx/video-agent-skills.git
mkdir -p .claude/skills
cp -r video-agent-skills/skills/* .claude/skills/
```

Windows (PowerShell):

```powershell
git clone https://github.com/Melvynx/video-agent-skills.git
New-Item -ItemType Directory -Force .claude\skills | Out-Null
Copy-Item -Recurse -Force video-agent-skills\skills\* .claude\skills\
```

To update a manual install: `git -C video-agent-skills pull`, then copy again.

Then, in Claude Code: `/video-setup`.

On claude.ai: zip one skill folder and upload it in the Skills part of the settings. Only `video-script` works there (scripts, hooks, humanize); the others need your computer's tools.

## The flow

```text
/video-setup           once: tools, studio, voice.md, permissions, accounts
/video-audit           what works on your own Instagram → audit.md
/video-viral           what works in your niche → swipe.md
/video-script          this week's scripts → video-agent/scripts/
                       record them (phone, camera, webcam) → video-agent/takes/
/video-edit            each take → video-agent/edits/<slug>/final.mp4 (short or long)
/long-to-short-videos  a long video → finished shorts
/video-translate       a take → the same take in another language, your voice
/codex-images          thumbnails and covers
/video-publish         YouTube, Instagram, TikTok
```

Everything lives in one folder at the root of your project:

```text
video-agent/
├── voice.md      who you are and how you talk
├── swipe.md      hooks that worked in your niche
├── audit.md      what works on your own Instagram
├── log.md        one line per script, so nothing repeats
├── scripts/      one file per week
├── takes/        raw recordings
├── edits/        one folder per edited video (final.mp4, post.md, cover.jpg, published.md)
├── repurpose/    one plan per long video
├── images/       generated images not tied to a video
├── studio/       the Remotion project
├── .env          your API keys (git-ignored)
└── .tmp/         scratch files
```

## Tools

| Tool | For | Cost |
|---|---|---|
| Node 18+, git | the edit (Remotion) | free |
| ffmpeg | cuts, audio, conversions | free |
| yt-dlp + deno | YouTube views, captions, transcripts | free |
| build tools | whisper.cpp compiles on the first edit (`xcode-select --install`, `build-essential`) | free |
| Remotion + whisper.cpp | captions and motion design, downloaded on the first edit | free for individuals and small teams ([Remotion license](https://www.remotion.dev/license)) |
| Treg (optional) | your Instagram audit, publishing on YouTube, Instagram and TikTok, TikTok and Instagram numbers of other accounts (CLI with Python 3.12+ on macOS and Linux, or the MCP on any OS) | your own accounts free, other accounts about $0.001 a call, $1 free credit on signup |
| ElevenLabs (optional) | `video-translate`, with your API key | your ElevenLabs plan |
| Codex (optional) | `codex-images` (`npm i -g @openai/codex`) | your ChatGPT plan |

`video-setup` gives the install command for each, on macOS, Windows and Linux.

## Permissions

`video-setup` merges a short allow list into `.claude/settings.json` (`yt-dlp`, `ffmpeg`, `ffprobe`, `npx remotion`, `node sub.mjs`, and Treg's free read-only commands). Never `Bash(*)`. Paid Treg calls always ask first, and the skills say the price before. Publishing and dubbing wait for your explicit yes. Keys live in `video-agent/.env`, never in a chat or a commit.

## Honest limits

- The edit is simple on purpose: captions, cuts, a hook card, callouts and zooms. No b-roll, music only from a file you bring.
- Auto-captions mishear names and numbers. Check them.
- Without Treg, research is YouTube only and publishing is manual (the skill hands you a ready kit).
- Translation has no lip-sync: the image stays the original.
- Generated images need a check: text can come out misspelled.
- Only download and cut videos you own or have the rights to.

## Want the full version?

These skills are the free base of [Editing IA Pro](https://codelynx.dev/editing/vsl?utm_source=github&utm_medium=readme&utm_campaign=video-agent-skill): the full motion design engine, styles, music, a review studio, faceless avatars, thumbnails and titles.

## License

MIT
