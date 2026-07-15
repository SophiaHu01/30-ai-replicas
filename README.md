# lazyskills · 挑战复刻 30 个 AI 产品 | Replicating 30 AI Products as Open-Source Skills

我会陆续复刻市面上 AI 产品的核心功能,把它们做成开源 skill——装进 **AI 编程助手**
(AI coding assistant,即 Claude Code、Cursor、Windsurf、Codex 这类产品)就能达到
原产品约 **80% 的效果**。免费、本地运行、隐私(不上传任何内容)。
复刻成功=开源送你;复刻失败=拆解它的护城河给你看。

> I'm replicating the core features of commercial AI products, one by one, as open-source
> **skills** — install them into an **AI coding assistant** (Claude Code, Cursor, Windsurf,
> Codex, etc.) and get ~**80% of the original product's capability**. Free, fully local,
> private (nothing is ever uploaded). If a replication succeeds, you get it open-source;
> if it fails, you get a teardown of the product's moat.

| 期数 Episode | 复刻对象 Target | skill | 状态 Status |
|---|---|---|---|
| EP01 | ChatCut(AI 剪辑 / AI video editing) | [lazycut](lazycut/) | 内测中 Beta |

---

## EP01 · lazycut 详细介绍 | In Detail

### 能做什么 | What it does

转录你的口播 → 自动挑掉重录和口误 → 按逐字稿剪辑(划掉哪句删哪句)→ 字幕(自然断句+
重点词标黄)→ 静音录屏智能变速 → 动效规划 → 机器质检 → **直出剪映/CapCut 草稿**
(分轨可编辑,不是死的 mp4)。完整能力对照表和工作原理见 [lazycut/README.md](lazycut/README.md)。

> Transcribes your talking-head footage → auto-picks the best take → text-based editing
> (strike a sentence to cut it) → captions with natural line breaks and keyword highlights →
> smart-paced silent screen recordings → motion-graphics planning → machine QA →
> **exports an editable CapCut/JianYing draft** (separate tracks, not a baked mp4).
> Full capability table and how-it-works in [lazycut/README.md](lazycut/README.md).

### 框架与逻辑 | Architecture

三层结构,规则和工具分家:
- **SKILL.md(指令层)**:AI 读的主线——八步工序、三个硬停点(剪前/渲前/发送前必须
  经你确认)、通道禁令。
- **scripts/(工具层)**:30 个单文件确定性工具。判断交给 AI,执行交给代码——同样输入
  永远同样输出。
- **assets/style.json(风格层)+ reference/lessons.local.md(错题本)**:你的口味是数据,
  规则从你的纠正里长出来。

两个核心机制:**转录锁**(只转录一次,锁死为真相源,剪辑靠时间映射推算——治「每转一遍
错字都不同」)和**模板克隆法**(读你本机的真实草稿当格式模板写新草稿——不逆向、不硬编码,
剪辑软件怎么升级都不怕)。

> Three layers: **SKILL.md** (instructions the AI reads: the 8-step pipeline, 3 hard stops
> requiring your confirmation, a channel ban), **scripts/** (30 deterministic single-file
> tools — judgment belongs to the AI, execution belongs to code), **assets/style.json +
> lessons.local.md** (your taste as data; rules grow from your corrections).
> Two core mechanisms: the **transcript lock** (transcribe once, lock it as the source of
> truth, derive every edit via time mapping) and **template cloning** (learn the draft format
> from a real draft on *your* machine — no reverse-engineered hardcoding, upgrade-proof).

### 引用的开源库 | Open-source components

| 组件 Component | 用途 Role | 必装? Required? |
|---|---|---|
| [ffmpeg](https://ffmpeg.org) | 视频处理引擎 video engine | ✅ |
| [Whisper](https://github.com/openai/whisper)(经 mlx-whisper / faster-whisper) | 语音转文字 speech-to-text | ✅ 自动二选一 auto-selected |
| [Pillow](https://python-pillow.org) | 字幕绘制 caption rendering | ✅ |
| [RapidOCR](https://github.com/RapidAI/RapidOCR) | 屏幕文字识别(非 macOS)OCR on non-macOS | 按平台 per-platform |
| [ClearVoice](https://github.com/modelscope/ClearerVoice-Studio) | 人声增强 voice enhancement | 可选 optional |
| [Remotion](https://remotion.dev) | 动效组件 motion graphics | 可选 optional |
| [OpenTimelineIO](https://opentimelineio.readthedocs.io) | 时间线交换 timeline interchange | 可选 optional |

模型在首次运行时自动下载到本机(约 1-2GB,只下一次),之后离线可用。

> Models download to your machine on first run (~1–2 GB, once); everything runs offline after.

### 你可能要安装什么 | What you may need to install

Python 3.10+、ffmpeg、若干 pip 包(转录引擎按芯片自动选)、可选 Node.js(动效)。
**不用自己记**:跑 `python3 lazycut/scripts/doctor.py` 体检,把输出整段贴给你的
AI 编程助手,它会带你把缺的装齐。

> Python 3.10+, ffmpeg, a few pip packages (transcription engine auto-selected per chip),
> optional Node.js for motion graphics. No need to memorize: run
> `python3 lazycut/scripts/doctor.py` and paste the output to your AI coding assistant —
> it installs what's missing.

### 机器要求 | Machine requirements

macOS(Apple 芯片最顺)与 Windows 都支持:转录双引擎、OCR 双引擎、中文字体自动探测。
Windows 尚未真机全链实测——你是 Windows 用户?跑通或跑挂都请开 issue,首批实测者进
README 鸣谢。

> Works on macOS (smoothest on Apple silicon) and Windows: dual transcription engines,
> dual OCR engines, automatic CJK font detection. Windows hasn't had a full end-to-end
> real-machine test yet — Windows users, please open an issue whether it works or breaks;
> first testers get credited here.

---

## 通用玩法 | How to use

```bash
git clone https://github.com/SophiaHu01/lazyskills.git
cd lazyskills/lazycut && python3 scripts/doctor.py
```

**Claude Code**:把 `lazycut/` 拷进 `~/.claude/skills/`,对 AI 说「用 lazycut 帮我剪 <素材文件夹>」。
**Cursor / Windsurf / Codex**:仓库自带 `AGENTS.md`,打开项目即生效;或直接说
「读 lazycut/SKILL.md,按它的流程帮我剪视频」。
skill 本体就是 markdown 规则+python 脚本,任何能读文件、能跑命令的 AI 助手都能用。

> **Claude Code**: copy `lazycut/` into `~/.claude/skills/`, then say "use lazycut to edit
> <footage folder>". **Cursor / Windsurf / Codex**: the bundled `AGENTS.md` activates on
> open, or just say "read lazycut/SKILL.md and follow it". A skill is only markdown rules +
> python scripts — any AI assistant that can read files and run commands can drive it.

License: MIT
