# video-agent-skill

Free Claude Code skills to script, research and edit short-form videos (YouTube Shorts, TikTok, Instagram Reels).

Only markdown. No app, no server, no account. The skills tell Claude how to work and which free tools to call: ffmpeg, yt-dlp, and Remotion with whisper.cpp for the edit. [Treg](https://treg.to) is optional, for TikTok and Instagram numbers and your own stats.

## The skills

| Skill | What it does |
|---|---|
| `video-setup` | Start here. Checks the tools, creates `video-agent/`, writes your `voice.md` from your own videos or a short interview, pre-approves the commands, optionally connects Treg. |
| `video-viral` | Finds what over-performs in your niche: recent Shorts of 10 to 12 accounts near your size and above, outliers against each account's median, the first 3 seconds of each, saved to `swipe.md`. |
| `video-script` | A week of scripts from what really happened in your week, in your voice: 3 hooks per script from 22 formulas, scored, a re-hook, timing checked at your pace, visual notes. |
| `video-human` | Makes any script, caption or description sound like you, not like a chat model: invisible characters, AI words (English and French), AI sentence patterns, facts checked. |
| `video-edit` | A raw take into a finished short: word-by-word captions, silences and retakes cut, simple motion design (hook card, callouts, punch-in zooms), voice at -14 LUFS, a 1080×1920 MP4 with captions in the platforms' safe zone, an .srt for CapCut or Premiere, and the post copy. |
| `video-repurpose` | One long video into shorts: clips that stand alone, cut and handed to `video-edit`, plus new scripts from the ideas too spread out to clip. |

## Install

In your project folder:

```bash
npx skills add Melvynx/video-agent-skill -a claude-code
```

Add `-g` to install them for every project. `npx skills update` gets the latest version.

Or paste `https://github.com/Melvynx/video-agent-skill` into Claude Code and ask it to install these skills.

Or copy them by hand (into `~/.claude/skills/` instead for every project). macOS and Linux:

```bash
git clone https://github.com/Melvynx/video-agent-skill.git
mkdir -p .claude/skills
cp -r video-agent-skill/skills/* .claude/skills/
```

Windows (PowerShell):

```powershell
git clone https://github.com/Melvynx/video-agent-skill.git
New-Item -ItemType Directory -Force .claude\skills | Out-Null
Copy-Item -Recurse -Force video-agent-skill\skills\* .claude\skills\
```

To update a manual install: `git -C video-agent-skill pull`, then copy again.

Then, in Claude Code: `/video-setup`.

On claude.ai: zip one skill folder and upload it in the Skills part of the settings. Only the text skills (`video-script`, `video-human`) work there; the others need your computer's tools.

## The flow

```text
/video-setup      once: tools, voice.md, permissions
/video-viral      what works in your niche → swipe.md
/video-script     this week's scripts → video-agent/scripts/
                  record them (phone, camera, webcam) → video-agent/takes/
/video-edit       each take → video-agent/edits/<slug>/final.mp4
/video-repurpose  a long video → clips + new scripts
/video-human      any AI-written text, before posting
```

Everything lives in one folder at the root of your project:

```text
video-agent/
├── voice.md      who you are and how you talk
├── swipe.md      hooks that worked in your niche
├── log.md        one line per script, so nothing repeats
├── scripts/      one file per week
├── takes/        raw recordings
├── edits/        one folder per edited video
├── repurpose/    one plan per long video
├── studio/       the Remotion project (created by video-edit)
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
| Treg (optional) | TikTok and Instagram views, your own stats (CLI with Python 3.12+ on macOS and Linux, or the MCP on any OS) | your own accounts free, other accounts about $0.001 a call, $1 free credit on signup |

`video-setup` gives the install command for each, on macOS, Windows and Linux.

## Permissions

`video-setup` merges a short allow list into `.claude/settings.json` (`yt-dlp`, `ffmpeg`, `ffprobe`, `npx remotion`, `node sub.mjs`, and Treg's free read-only commands). Never `Bash(*)`. Paid Treg calls always ask first, and the skills say the price before.

## Honest limits

- The edit is simple on purpose: captions, cuts, a hook card, callouts and zooms. No b-roll, no illustrations, music only from a file you bring.
- Auto-captions mishear names and numbers. Check them.
- Without Treg, research is YouTube only.
- Only download and cut videos you own or have the rights to.

## Want the full version?

These skills are the free base of [Editing IA Pro](https://codelynx.dev/editing/vsl?utm_source=github&utm_medium=readme&utm_campaign=video-agent-skill): the full motion design engine, styles, music, a review studio, faceless avatars, thumbnails and titles.

## License

MIT
