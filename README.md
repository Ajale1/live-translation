# LiveTranslate for DaVinci Resolve

Free & open source. Made by Williams Ameh — Regent Films Studio. If this
tool helps you, star the repo and share it. Need your videos professionally
subtitled in any of the 30+ languages below? Contact Regent Films Studio
(links on the GitHub profile).

Real-time audio transcription and instant translation, live, as the speaker
talks — plus offline, frame-accurate transcription of the current timeline.
Pick any spoken source language and translate into one or MANY targets
simultaneously. Runs inside DaVinci Resolve with a live-caption window,
SRT export per language, and one-click subtitle insertion into the timeline.

Supported languages (30+): English, French, Spanish, Arabic, Portuguese,
German, Italian, Dutch, Russian, Chinese, Japanese, Korean, Hindi, Turkish,
Polish, Ukrainian, Vietnamese, Indonesian, Persian, Hebrew — and African
languages: Hausa, Yoruba, Igbo*, Swahili, Amharic, Somali, Shona, Lingala,
Afrikaans, Malagasy. (*Igbo is target-only: no speech-recognition model
exists for spoken Igbo, so it can't be the source.)

Language selection is searchable in both UIs and remembered between
sessions (`~/.livetranslate/config.json`).

> **New to this? Read [INSTALL.md](INSTALL.md)** — a plain-language,
> step-by-step install guide for Mac and Windows, free Resolve and Studio.

Everything runs **locally by default** — no API keys, no network, no cost:

- Speech-to-text: [faster-whisper](https://github.com/SYSTRAN/faster-whisper)
  (English-only model for English, multilingual model otherwise — switched
  automatically)
- Translation routing per target: [Argos Translate](https://github.com/argosopentech/argos-translate)
  for pairs it covers, [NLLB-200](https://huggingface.co/facebook/nllb-200-distilled-600M)
  for everything else (incl. all African languages; its engine + model
  download automatically on first use, ~3 GB one time)
- UI: Tkinter (script version) / Electron panel (Studio plugin)

An optional cloud mode (OpenAI Whisper API + gpt-4o-mini) is available for
low-powered machines — see Configuration.

## How it works

```
Microphone ──▶ VAD segmenter ──▶ Whisper (EN) ──▶ Argos (FR, ES) ──▶ Live window
  30 ms blocks   speech/silence     partial +          instant          EN / FR / ES
                 segmentation       final text        translation       + SRT recorder
```

- While you speak, **partial** English transcripts appear within ~1 s.
- When you pause (600 ms silence), the segment is finalized, translated to
  French and Spanish, and both translations replace the live line instantly.
- Every finalized segment is timestamped, so the whole session can be
  exported as `en/fr/es` SRT files and pushed straight into the timeline.

## Two ways to run it

| | Script version | Workflow Integration plugin |
|---|---|---|
| Where it appears | **Workspace ▸ Scripts ▸ LiveTranslate** | **Workspace ▸ Workflow Integrations ▸ LiveTranslate** |
| Resolve edition | Free **and** Studio | **Studio only** (Blackmagic limitation) |
| OS | Windows / macOS / Linux | Windows / macOS |
| Install | `python3 dist/LiveTranslate.py` (self-installing) | `python3 install-plugin.py` |
| UI | Tkinter window | Native Electron panel |

Both share the same engine, models, and config (`~/.livetranslate`).

## Install — self-installing single file (recommended)

The whole plugin ships as **one file**: `dist/LiveTranslate.py`. Run it once
with any Python 3.9+ (ideally the one Resolve uses):

```bash
python3 LiveTranslate.py
```

On first launch a setup window appears and it installs itself:

1. Dependencies go into a **private folder** `~/.livetranslate/lib` — no
   admin rights, nothing touches your system Python.
2. It downloads the Whisper model and the Argos en→fr / en→es packages, so
   every later launch is instant and fully offline.
3. It **copies itself into Resolve's Scripts menu**
   (`Fusion/Scripts/Utility`), so from then on it lives under
   **Workspace ▸ Scripts ▸ LiveTranslate**.

If setup fails midway (e.g. network drop), just run it again — every step is
idempotent and resumes where it left off. Updating = replacing the one file;
it re-extracts itself automatically on next launch.

You can also drop `dist/LiveTranslate.py` straight into the
`Fusion/Scripts/Utility` folder first and run it from the Scripts menu — the
first-run setup happens there just the same.

### Classic install (alternative)

Prefer an explicit installer? From the source tree:

```bash
python3 install.py
```

Same result, driven from the terminal instead of the setup window.

### Rebuilding the single file

After editing the `livetranslate/` package, regenerate the distributable:

```bash
python3 build.py   # writes dist/LiveTranslate.py
```

### Can't find it in Resolve after installing?

- The **script version** appears under **Workspace ▸ Scripts** (NOT Workflow
  Integrations) — and only after **restarting Resolve**.
- Confirm the file landed in the right folder:
  - Windows: `%APPDATA%\Blackmagic Design\DaVinci Resolve\Support\Fusion\Scripts\Utility\LiveTranslate.py`
  - macOS: `~/Library/Application Support/Blackmagic Design/DaVinci Resolve/Fusion/Scripts/Utility/LiveTranslate.py`
  - Linux: `~/.local/share/DaVinciResolve/Fusion/Scripts/Utility/LiveTranslate.py`
  If it isn't there, the first-run setup didn't finish — run
  `python3 LiveTranslate.py` again and let the setup window complete.
- The **plugin version** appears under **Workspace ▸ Workflow Integrations**
  and requires Resolve **Studio**; the free edition never shows that menu.

## Install — the Workflow Integration plugin (Resolve Studio)

The plugin variant runs as a native Resolve panel (Electron) and talks to
Resolve through the official Workflow Integration API. Requires Resolve
**Studio** on Windows/macOS, plus Node.js and Python 3.9+.

```bash
python3 install-plugin.py
```

It sets up the shared Python engine (same as the script version), copies
`workflow-plugin/` + the engine package into Resolve's
`Workflow Integration Plugins` folder, runs `npm install`, and copies
`WorkflowIntegration.node` from Resolve's developer examples. Then restart
Resolve and open **Workspace ▸ Workflow Integrations ▸ LiveTranslate**.

Architecture: the panel (Electron) spawns `python -m livetranslate.headless`
and exchanges JSON events over stdio — live captions stream into the panel,
and SRT → timeline goes through the Workflow Integration API.

### Standalone mode

You can also run it outside Resolve:

```bash
python3 LiveTranslate.py
```

To let it push SRTs into the Resolve timeline while standalone, enable
**Preferences ▸ System ▸ General ▸ External scripting using: Local**.

## Use

1. Open your project and timeline, launch the plugin.
2. Press **▶ Start** — status turns green ("Listening — speak in English").
3. Talk. English appears live; French (blue) and Spanish (amber) captions
   update the moment each phrase completes.
4. Press **■ Stop**, then:
   - **Export SRT** — writes `livetranslate-<timestamp>.{en,fr,es}.srt` to `~/LiveTranslate/`
   - **SRT → Timeline (FR)** / **(ES)** — imports the SRT into the Media Pool
     and appends it to the current timeline as a subtitle track.

## Configuration

Create `~/.livetranslate/config.json` to override any default, e.g.:

```json
{
  "whisper_model": "base.en",
  "engine": "local",
  "translator": "local",
  "font_size_translation": 32,
  "vad_energy_threshold": 0.004,
  "input_device": null
}
```

Useful knobs:

| Key | Default | Notes |
|---|---|---|
| `whisper_model` | `small.en` | `tiny.en`/`base.en` = faster, `medium.en` = more accurate |
| `engine` | `local` | `openai` = Whisper API (set `OPENAI_API_KEY`) |
| `translator` | `local` | `openai` = gpt-4o-mini translation |
| `partial_interval_s` | `1.0` | how often live partials refresh |
| `silence_end_ms` | `600` | pause length that finalizes a phrase |
| `vad_energy_threshold` | `0.006` | lower if quiet mic, raise if noisy room |
| `input_device` | `null` | sounddevice input index (see below) |

List available microphones:

```bash
python3 -c "from livetranslate.audio_capture import list_input_devices; print(list_input_devices())"
```

## Latency expectations

On a modern laptop (local mode, `small.en` int8): partials ~0.8–1.5 s behind
speech; finalized + translated captions ~1–2 s after you pause. Use `base.en`
or `tiny.en` to cut latency further, or the OpenAI engine on weak CPUs.

## Files

```
LiveTranslate.py            entry script (goes into Resolve's Scripts menu)
install.py                  one-shot installer
livetranslate/
  config.py                 defaults + ~/.livetranslate/config.json loader
  audio_capture.py          mic capture + energy VAD segmenter
  transcriber.py            faster-whisper / OpenAI Whisper engines
  translator.py             Argos / OpenAI translation engines
  pipeline.py               threaded mic→STT→MT pipeline + caption recorder
  srt.py                    per-language SRT export
  resolve_bridge.py         Resolve API: import SRT → append to timeline
  ui.py                     Tkinter live-caption window
```

## Notes and limits

- Resolve's scripting API cannot tap the timeline's live audio output, so the
  plugin listens to a **microphone / audio input device**. To caption audio
  playing inside Resolve, route system audio to a loopback device (BlackHole
  on macOS, VB-Cable on Windows, `snd-aloop`/PipeWire loopback on Linux) and
  set it as `input_device`.
- Requires Python 3.9+ with Tkinter (standard on Windows/macOS installers;
  on Linux: `sudo apt install python3-tk portaudio19-dev`).
- Resolve Studio is not required for the Scripts menu; free Resolve 18+ works.

## License, credits & contributing

- Code: MIT (see [LICENSE](LICENSE)). Models have their own licenses — see
  [ATTRIBUTIONS.md](ATTRIBUTIONS.md). Note that the NLLB-200 translation
  model is **non-commercial** (CC-BY-NC-4.0); replace it before using this
  in a paid product.
- Found a bug or want a language/feature? Open a GitHub issue.
- Made by Williams Ameh · Regent Films Studio. For professional subtitling
  and localization work, reach out via the GitHub profile.
