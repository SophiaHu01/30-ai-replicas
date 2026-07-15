English | [简体中文](README.zh-CN.md)

# lazycut · Turn your AI coding assistant into a video editor

> Episode 1 of "Replicating 30 AI Products": the core capabilities of AI video editors
> (ChatCut and friends). Free, fully local, nothing uploaded — and the output goes
> straight into **the editor you already use**: it asks which one during onboarding.
> Deepest support today: CapCut/JianYing native drafts; Resolve/Premiere/FCP via
> EDL/OTIO; a universal asset pack for anything else.

## What it does (and doesn't — honesty first)

| Capability | Status | Notes |
|---|---|---|
| AI editing (fillers / pauses / retakes) | ✅ | Retakes auto-clustered, last take wins by default, ambiguous ones go to you |
| Text-based editing | ✅ | The transcript is the remote control: strike a sentence, the cut follows |
| Captions (word-level timing + keyword highlights) | ✅ | Paged by natural speech pauses, never mechanically |
| Smart-paced silent screen recordings | ✅ | Dead time fast-forwarded, typing medium, reveals slowed |
| **Editable project into your editor** | ✅ signature | Asked at onboarding; CapCut/JianYing drafts deepest, EDL/OTIO/asset-pack fallback |
| Voice cleanup | ✅ optional | Local model; skipped automatically on weak machines |
| Motion graphics (7 component types) | 🔧 wiring | Component library built; pipeline wiring tracked in CHANGELOG |
| Long video → Shorts | 🔧 methodology | Structure diagnosis suggests cut points; one-click is on the roadmap |
| Real-time preview panel | ❌ won't build | Your editor *is* the panel |
| AI-generated assets (voice/music/images) | ❌ won't build | Dedicated tools do it better; your editor's library is rich |

Cost: **$0**. Models (speech recognition, denoising) download to your machine on first
run and stay local. The only cost you already carry is your AI coding assistant.

## Quick start (three steps)

```bash
git clone https://github.com/SophiaHu01/lazyskills.git
cd lazyskills/lazycut
python3 scripts/doctor.py   # health check — paste the output to your AI, it installs what's missing
```

Then tell your AI: **"Read lazycut/SKILL.md and use lazycut to edit `<footage folder>`."**
First use walks you through three things (~5 minutes): claiming your editor, a six-question
style quiz, and — if you've never created a draft — a 40-second "introduce your editor to
the AI" ritual.

## How it works: ten stations

1. **Archive** — each project gets a git ledger: asset map, change log. The expensive
   full-understanding pass happens once; every revision after works off diffs.
2. **Transcribe** — Whisper (open-source) turns every word into text with per-second
   timestamps, locally.
3. **Lock (signature #1: the transcript lock)** — transcribe once, lock it as the single
   source of truth; all subsequent edits are derived by time mapping, never re-transcribed.
   Why: every re-transcription produces *different* typos — an unwinnable whack-a-mole
   unless you kill it at the root.
4. **Pick takes** — said the same line three times? Adjacent-similarity clustering keeps
   the last take (industry convention), flags "did you rephrase?" cases for you, and has
   guards against false-positives on parallel sentence structures.
5. **Think before cutting** — at least two candidate structures scored against a
   hook/retention checklist; a full read for meaning (broken logic, dangling references,
   unkept promises); machine detection of semantic repetition.
   **The polished transcript plus editing-style questions go to you for written
   confirmation — cutting starts only after you approve** (hard stop #1).
6. **Cut** — every cut coordinate is looked up from your actual words, never hand-typed
   seconds; every new seam gets a "read-aloud test", and broken joints must be bridged.
7. **Captions** — paged by your natural pauses; long lines balanced without splitting
   English words; keywords enlarged and highlighted; safe margins for platform UI;
   line-width adapts to portrait/landscape.
8. **Motion graphics** — the AI reads the transcript and marks "animation moments"
   (a number → count-up, several numbers → chart, a process → timeline…). **Every
   animation must justify itself in writing; unjustifiable ones are cut.** The motion plan
   goes to you for approval before rendering (hard stop #2).
9. **QA** — five machine checks (black frames / clipping / caption gaps / dead-air in
   quiet zones / loudness), any failure blocks delivery; then the captions, cards and
   emphasis are merged into one "what the viewer actually receives" stream and read
   end-to-end for global coherence.
10. **Deliver** — a preview mp4 plus your editor's project, paths reported. **Nothing is
    sent anywhere without your explicit yes** (hard stop #3).

## Two signature mechanisms

**The transcript lock** (station 3): the industry default is to re-transcribe after every
recut, which spawns fresh typos every round. We transcribe once, lock it, and derive all
edits via time mapping — the problem disappears at its root.

**Template cloning** (the draft exporter): editor draft formats are proprietary and
undocumented; hardcoded-schema tools live in fear of version updates. We don't hardcode —
**we read a real draft from *your* machine as the format template** and write new drafts
in its image. Whatever version you run is, by construction, the version we support.
The price: your machine needs at least one existing draft (no drafts? a 40-second guided
ritual creates one). A built-in "identity chain" self-check validates every generated
draft before it reaches you.

## How it learns you

- **Style assets** (assets/style.json): caption size, highlight color, pacing, line width,
  end-card branding — all data. Edit the file, change the style; upgrades never overwrite
  it. (Some parameters are still being wired to the scripts — tracked in CHANGELOG.)
- **The mistake journal** (reference/lessons.local.md): every correction you make becomes
  a recorded case; a repeated correction becomes a proposed rule. Rules grow from your
  feedback — that's the difference between this and a fixed-parameter tool.

## Output formats (no lock-in)

CapCut/JianYing native draft (deepest) · EDL + SRT (DaVinci/Premiere, beta) · OTIO
(timeline interchange, needs opentimelineio) · layered asset pack (clean base + SRT +
card PNGs — any editor accepts it) · finished mp4.

## Privacy & boundaries

- Fully local. **Nothing is uploaded.** No account, no server, no telemetry.
- Any outbound send — even to yourself — requires your explicit confirmation in chat.
- Scope: talking-head and screen-demo videos. Beat-synced montages, product showcases
  and mood vlogs are different animals (later episodes).

## FAQ

**Q: Why do I need an existing draft for draft export?** Template cloning learns the
format from a real draft on your machine — that's the price of being upgrade-proof.
A 40-second guided step (drop any video in, add one caption line) does it once per machine.
**Q: Windows?** Code paths are in place (transcription/OCR/fonts/draft directories all
have Windows branches); full real-machine testing is being crowdsourced — open an issue
whether it works or breaks, first testers get credited.
**Q: How strong a machine?** Any mainstream computer from the last five years; the voice
enhancement model is heavy and auto-skips on weak machines.
**Q: Something broke?** Paste the full error to your AI — every error message is written
to be self-explanatory to an AI assistant. If that fails, open an issue: your error report
is the next version's changelog.

License: MIT
