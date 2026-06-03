# Present — AI-Assisted A&P Notes

*This tool is currently in beta development. The goal is to explore what is possible, but in its current state any actual clinical use should be strictly avoided.*

> Runs entirely in your browser. Clinical dictation, transcripts, prompts, and generated notes are processed locally on the device.

[Live App](https://pedscoffee.github.io/present/)

Present converts spoken or typed clinical assessment and plan dictation into clean, structured, telegraphic notes using on-device speech-to-text and local browser-based LLM inference. It also includes **SmartChart**, a live template engine that assembles boilerplate note text from typed input without any AI generation.

---

## Features

- **Voice input** — record A&P dictation; Whisper transcribes locally in the browser with a real-time circular waveform visualizer.
- **Long recording support** — audio longer than 27 seconds is automatically chunked and transcribed in overlapping segments with boundary deduplication.
- **Text input** — paste or type dictation directly into the unified input field.
- **Editable input** — correct transcription or typed content before note generation.
- **Two-pass AI pipeline** — Pass 1 cleans rough dictation; Pass 2 generates structured note blocks.
- **Selectable local models** — choose LLM and Whisper model sizes based on hardware.
- **Medical terminology anchoring** — add practice-specific terms to guide transcript cleanup.
- **Shorthand macros** — expand dot phrases like `.aom` or `.rtp` while typing or before generation.
- **SmartChart** — live, AI-free template panel that assembles boilerplate note content from keyword triggers as you type using Regex. 
- **SmartChart Templates** — fully customizable trigger-based content blocks, editable in Settings.
- **Configurable SmartChart behavior** — set auto-copy delay (0.5–10s) and auto-clear delay (10–300s) in Settings.
- **Extra pipeline steps** — generate AVS text, billing analysis, teaching prompts, or other custom outputs after note generation.
- **Pipeline library** — browse and add pre-built pipeline step templates
- **Drag-to-reorder pipeline steps** — rearrange enabled steps in the settings drawer.
- **Boilerplate detection modes** — each boilerplate entry uses `regex`, `llm`, or `both` detection; regex mode uses configurable keyword lists for fast, reliable matching.
- **Smart boilerplate injection** — condition-specific paragraphs are appended based on detected tags; LLM emits tags, regex keywords match independently, and `both` mode uses a pre-detection hint to the LLM.
- **Editable output** — revise the final note before pasting into the EMR.
- **Rich copy** — copies both plain text and HTML so pasted content preserves formatting in Epic and other rich-text fields.
- **Auto-copy and manual copy** — copy the full note, individual problem blocks, or extra pipeline step outputs.
- **Copy dropdown** — lists each problem block for targeted copying without scrolling.
- **Local settings** — prompts, boilerplate, models, terminology, macros, templates, and pipeline steps persist in `localStorage` with automatic migration from previous settings versions.
- **Settings import/export** — export your settings as a JSON file to share or backup, and import settings from a previously exported file.
- **Installable PWA** — app shell, dependencies, and fetched model assets are cached for offline local use after first load.
- **Static deployment** — no backend, no database, no account system, and no cloud AI API.

---

## Patient Data Posture

Present is designed for local/on-device clinical documentation:

- Audio capture uses the browser microphone API.
- Transcription runs locally with Transformers.js and Whisper.
- Note generation runs locally with WebLLM and WebGPU.
- SmartChart template assembly runs entirely in-page JavaScript with no model calls.
- Patient text and audio are not intentionally sent to an external AI service or application backend.
- Generated note content is held in the browser session and copied to the clipboard for clinician use.

The default hosted app loads JavaScript dependencies and model assets from third-party static origins during first setup. After those assets are fetched, the PWA service worker and browser caches allow local offline use. For stricter institutional review, it would be suggested to deploy a self-hosted version where app files, dependencies, and model assets are served from an approved institution-controlled origin.

---

## Usage

1. Open the app in Chrome 113+ or Edge 113+.
2. Wait for the LLM and Whisper models to load. Model files are large and are cached after first load.
3. **Voice:** click the mic button, speak the A&P, then stop recording. The waveform ring animates while recording. Transcription runs locally after recording stops; recordings longer than 27 seconds are processed in chunks automatically.
4. **Text:** type or paste dictation into the input field. Dot-phrase macros expand as you type.
5. Edit the input if needed, then click **Generate Notes**.
6. Review and edit the structured note output.
7. Paste into the EMR using Copy All, a per-problem copy button, or the copy dropdown.

Always review AI-generated notes before use in the medical record.

---

## Settings Import/Export

You can now **export** your settings (prompts, boilerplate, templates, macros, terminology, and pipeline steps) as a JSON file to share with colleagues or backup your configuration. To import settings, simply upload a previously exported JSON file.

**How to Export:**

1. Open the **Settings** panel (gear icon).
2. Click the **Export Settings** button.
3. A JSON file will be downloaded with all your configurations.

**How to Import:**

1. Open the **Settings** panel (gear icon).
2. Click the **Import Settings** button.
3. Select a previously exported JSON file to restore your configurations.

> **Note:** Importing settings will overwrite your current configuration. Ensure you have a backup if needed.

---

## SmartChart

SmartChart is a live, AI-free panel that assembles template text as you type — no model generation required. It is useful for quickly building boilerplate-heavy note components (well-child checks, supportive care, injury visits) from a short typed one-liner.

**How it works:**

1. Type any input in the unified input field (e.g., `well child check`, `otitis media`, `fever and cough`).
2. SmartChart scans the input for keyword triggers defined in SmartChart Templates.
3. Matching templates are assembled in priority order into a single formatted note block.
4. The note auto-copies to clipboard after a configurable delay (default 1.5 seconds).
5. The input and output auto-clear after a configurable idle delay (default 30 seconds).

**Copy behavior:**

- SmartChart output auto-copies after the configured delay once a match is found.
- Click **Copy SmartChart** in the SmartChart panel to copy manually at any time.
- The copy indicator in the panel header shows when an auto-copy is pending.

**SmartChart Templates** define the trigger keywords and content blocks and are fully editable in Settings → SmartChart Templates. Each template has a name, comma-separated trigger list, content (supports HTML `<em>` for italic), and a numeric priority controlling assembly order.

---

## Present the patient

For more complex patients, simply hit record and present the patient. Whisper runs locally in the browser to transcribe what you say, and then the transcript is cleaned and transformed into a note using WebLLM.

**Key Differentiators:**

- System prompt is open and easy to modify allowing for true customization
- Pipeline allows for additional prompts in order for user to take full advantage of large language models supporting their charting

---

## Default System Prompt Note Output Format

For each problem or diagnosis, only the relevant bullets are emitted:

```text
Diagnosis or Problem Name
- Labs
- Imaging
- Medications with exact doses if stated
- Treatment / plan actions
- Supportive care
- Differential includes X, Y, Z
- Conditional plans
- Return precautions include...
- Nursing orders
- Follow-Up: ...

[Condition-appropriate boilerplate appended automatically]
```

Bullets not mentioned in the dictation are omitted.

---

## Copying Notes

- **Copy All** copies the full note including boilerplate as both plain text and HTML.
- **Per-problem copy** appears on each problem block header.
- **Copy dropdown** (chevron next to Copy All) lists each problem for targeted copying.
- **Auto-copy** copies the completed note ~400ms after generation finishes.
- Per-problem and pipeline step copies also copy rich HTML for EMR paste compatibility.

The output remains editable after generation.

---

## PWA / Offline Local Use

Present can be installed as a local PWA from a trusted HTTPS origin or from `localhost`.

1. Open the app while online.
2. Let the selected LLM and Whisper models finish loading.
3. Open Settings → Local Security → Check offline cache.
4. Install the app from the browser toolbar/menu.
5. Disconnect from the network and reopen the installed app to confirm it starts locally.

The app shell is pre-cached by `sw.js`. Runtime dependencies and model files are cached as they are first requested. If you switch to a different model, reconnect once and let that model finish loading before relying on offline use.

**Security notes:**

- Use HTTPS or `localhost`; browsers require a secure context for service workers, microphone access, and WebGPU.
- The service worker adds no backend, analytics, or remote logging.
- Patient note text is transient page memory and clipboard content; it is not written to `localStorage`.
- Browser extensions, OS clipboard managers, endpoint security posture, and device access controls remain institution responsibilities.

---

## Settings

Open the gear icon in the top-right header to access settings.


| Setting                            | Description                                                                                                             |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| Language Model                     | Selects the local WebLLM model.                                                                                         |
| Whisper Speech-to-Text Model       | Selects the local transcription model. Larger models are more accurate but require more memory and download time.       |
| Medical Terminology                | Practice-specific vocabulary used during transcript cleanup.                                                            |
| Shorthand Macros                   | Dot phrases such as `.aom: acute otitis media`, one per line.                                                           |
| Pass 1 – Transcript Cleanup Prompt | System prompt for cleaning raw dictation.                                                                               |
| Pass 2 – Note Generation Prompt    | System prompt for generating structured A&P notes.                                                                      |
| Extra Pipeline Steps               | Post-processing outputs (AVS, billing, teaching, or custom). Enable, disable, reorder by drag, or add from the library. |
| Boilerplate Entries                | Condition-specific text blocks with configurable detection mode (`regex`, `llm`, or `both`) and keyword lists.          |
| SmartChart Templates               | Trigger-based content blocks assembled live without AI. Each entry has a name, triggers, content, and priority.         |
| SmartChart Auto-Copy Delay         | Seconds before SmartChart output auto-copies (0.5–10s).                                                                 |
| SmartChart Auto-Clear Delay        | Seconds of idle before input and SmartChart output auto-clear (10–300s).                                                |
| Note Template                      | Template controlling how SmartChart assembles `{input}` and `{templates}` into the final output.                        |
| **Import/Export Settings**         | Export or import your configuration as a JSON file.                                                                     |


Click **Save & Apply** to persist settings. Model changes require **Reload Models**. **Reset defaults** restores all factory settings.

Settings are stored in browser `localStorage`; avoid placing patient identifiers in persistent settings.

---

## Model Options

### Language Models (WebLLM / WebGPU)


| Option       | Approx. size | Notes                                 |
| ------------ | ------------ | ------------------------------------- |
| Gemma 2 2B   | ~1.3 GB      | Fastest option.                       |
| Phi 3.5 Mini | ~2.2 GB      | Balanced-light option.                |
| Qwen3 4B     | ~2.3 GB      | Default balanced option.              |
| Llama 3 8B   | ~4.5 GB      | Higher quality; requires more memory. |


### Whisper Models (Transformers.js / WASM)


| Option         | Approx. size | Notes                                |
| -------------- | ------------ | ------------------------------------ |
| Whisper Tiny   | ~75 MB       | Fastest, lower accuracy.             |
| Whisper Base   | ~145 MB      | Lightweight balance.                 |
| Whisper Small  | ~488 MB      | Default recommended option.          |
| Whisper Medium | ~1.5 GB      | Best accuracy; requires more memory. |


---

## Extra Pipeline Steps

Extra pipeline steps run after the main note is generated. Each enabled step produces its own output box with a separate copy button. Steps can be enabled/disabled individually, reordered by drag, and sourced from either the cleaned transcript or the structured note output.

### Pipeline Library

Click **Browse Library** in Settings → Extra Pipeline Steps to add pre-built step templates:


| Template            | Category       | Input       | Description                                                                             |
| ------------------- | -------------- | ----------- | --------------------------------------------------------------------------------------- |
| AVS / Sign-Off      | Documentation  | Note output | Generates 3 personalized sign-off options and an actionable family to-do list.          |
| Billing Analysis    | Administrative | Note output | Estimates established-patient E/M coding using 2021 MDM rules with CPT code suggestion. |
| Teaching – Socratic | Teaching       | Note output | Extracts a clinical pearl and asks a Socratic follow-up question.                       |


Custom steps can use either the cleaned transcript or the generated note as input, with fully editable prompts.

---

## Boilerplate System

### Detection Modes

Each boilerplate entry can be configured with one of three detection modes:


| Mode    | Behavior                                                                                               |
| ------- | ------------------------------------------------------------------------------------------------------ |
| `llm`   | LLM emits `[BOILERPLATE:KEY]` tags in the note; tag is replaced at render time.                        |
| `regex` | Keywords are matched directly against the cleaned transcript; no LLM tag required.                     |
| `both`  | Keywords pre-detect the condition and hint the LLM; LLM tag or keyword match either trigger injection. |


### Default Boilerplate Entries


| Key           | Default Detection | Trigger                                              |
| ------------- | ----------------- | ---------------------------------------------------- |
| `WCC`         | regex             | Well child check / health maintenance                |
| `ILLNESS`     | both              | Illness, infection, virus, fever                     |
| `INJURY`      | llm               | Injury                                               |
| `OTITIS`      | regex             | Otitis media / ear infection                         |
| `STREP`       | regex             | Strep throat / rapid strep                           |
| `DEHYDRATION` | both              | Dehydration, vomiting, diarrhea, decreased urination |
| `RESP`        | llm               | Trouble breathing, wheezing, respiratory distress    |
| `PCMH`        | regex             | ADHD, obesity, weight concern                        |


Boilerplate entries can be added, edited, or deleted in Settings without changing code.

---

## SmartChart Templates

SmartChart uses its own set of trigger-based templates, separate from the AI boilerplate system. Default templates include well-child/health maintenance, illness supportive care, injury supportive care, ear infection risk, strep risk, dehydration risk, respiratory distress risk, eczema, PCMH reminder, and a follow-up dropdown block.

Templates are matched in priority order, so higher-priority entries appear first in the assembled output. All templates are fully editable in Settings → SmartChart Templates.

---

## Deployment

### GitHub Pages

1. Fork this repo.
2. Go to Settings → Pages.
3. Set source to `main` branch, `/ (root)` folder.
4. Save. The app will be available at `https://[username].github.io/[repo-name]/`.

No build step is required.

### Enterprise / IT Deployment

For institutional review, consider self-hosting the following from an approved origin:

- `index.html`, `style.css`, `app.js`, and `favicon.svg`
- `manifest.webmanifest` and `sw.js`
- WebLLM JavaScript dependency
- Transformers.js dependency
- LLM model assets
- Whisper model assets

See [SECURITY.md](SECURITY.md) for the data-flow table, network disclosure, PHI handling statement, and suggested IT approval language.

---

## Browser Requirements


| Browser     | WebGPU  | WASM | Status                                 |
| ----------- | ------- | ---- | -------------------------------------- |
| Chrome 113+ | Yes     | Yes  | Fully supported                        |
| Edge 113+   | Yes     | Yes  | Fully supported                        |
| Safari      | Partial | Yes  | Experimental / may require flags       |
| Firefox     | No      | Yes  | Not supported for WebGPU LLM inference |


If WebGPU is disabled, try enabling `chrome://flags/#enable-unsafe-webgpu`.

---

## Tech Stack


| Component                | Library / Technology                                                        |
| ------------------------ | --------------------------------------------------------------------------- |
| In-browser LLM inference | [WebLLM](https://github.com/mlc-ai/web-llm) + WebGPU                        |
| Default language model   | Qwen3 4B MLC variant                                                        |
| Speech-to-text           | [Transformers.js](https://github.com/huggingface/transformers.js) + Whisper |
| Markdown rendering       | [marked](https://github.com/markedjs/marked)                                |
| Frontend                 | Vanilla HTML, CSS, and JavaScript ES modules                                |
| Persistence              | Browser `localStorage` for settings (auto-migrates from v1/v2)              |
| Acceleration             | WebGPU for LLM, WASM for Whisper                                            |
| Offline                  | PWA service worker (`sw.js`)                                                |


---

## Clinical Disclaimer

*This tool is currently in beta development. The goal is to explore what is possible, but in its current state any actual clinical use should be strictly avoided.*

Even in a future goal state that has not yet been achieved; Present's intended use would be for clinical documentation assistance only. It is not a substitute for clinical judgment, billing expertise, legal review, or institutional compliance review.