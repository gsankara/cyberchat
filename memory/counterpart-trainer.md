---
name: counterpart-trainer
description: The Counterpart adversarial-chat / deepfake-defense training web app in the doe26 proposals folder — versions, design, and constraints
metadata:
  type: project
---

In `C:\Users\Ganesh C S\Documents\proposals\doe26\` there is a self-contained,
privacy-preserving browser training app called **Counterpart** (single-file HTML,
no build, scored client-side so nothing leaves the browser).

- `counterpart-trainer.html` = **v1**: scenarios Quiz (PII fragmentation),
  Admirer (non-explicit sextortion coercion), Pincer (multi-adversary correlation).
  Artifact: https://claude.ai/code/artifact/c2abc744-68c7-4537-a1cb-a755f6a7232a
- `counterpart-trainer-v2.html` = **v2** (superset): adds **The Sieve** (partitioning
  attack — answering "no" leaks the complement; only refusing the frame is safe) and
  **The Impersonator** (deepfake defense). Artifact:
  https://claude.ai/code/artifact/1500f28b-9960-4f51-8e6b-589de7963ae1

**Particles = the core deepfake concept.** Adapted from the user's own IoT papers
(~/Downloads/autonomous-defense.pdf, particles-v1.docx) — "You know me" /
information-as-shield authentication. A *particle* is a piece of info scored by
volatility, entropy, history, observability, and foreign/shared-secret. In v2 the
user is the VERIFIER: they type challenge questions to a suspected impersonator and
the app scores each question's particle **strength** (driven by the MAX of
volatility/liveness/shared-secret, minus exposure/OSINT; leading questions that embed
the answer are penalized as leaks; out-of-band callback is the strongest move).
Impersonator pretext is randomized per run (manager/family/IT/friend/bank).

**Live-LLM mode**: optional bring-your-own OpenAI-compatible endpoint (settings gear),
stored in localStorage, used only for adversary *wording* — scoring stays
deterministic. Works only when the file is opened locally; a published artifact's CSP
blocks external fetch, so it silently falls back to the scripted engine.

**Feedback the user gave**: iterate WITHOUT disrupting the version they're exploring —
so v2 is a separate file + separate artifact, v1 left untouched. See
[[browser-pane-file-nav]] for why restoring v1 into the preview pane isn't possible
without touching its file.

**Cover-identity profile (added):** extract scenarios flagged `usesProfile` (Quiz, Pincer)
assign the user a random synthetic identity at run start (name/city/birth year/zodiac/
favorite number/pet/street/email), shown in a "Your cover identity" rail card. Leak
detection is then EXACT and verifiable via `profileClassify`: revealing an ASSIGNED
value = leak; any other substantive value = decoy (disinformation); refusal = hold.
This solved the "is this a real city?" validation problem. Exposed identity fields get
struck through in the card. The profile also powers the Hint.

**Multiple scripts per scenario (added):** every extract scenario ships 2+ interchangeable
scripts, drawn at random per run via `scriptsOf(scen)` + `PICK`. A scenario's own `beats` is
script A; `altScripts:[...]` holds alternates (each may override persona/personas/lede/
resources). Read script-or-scenario properties through the `SC(prop)` accessor, and the active
beats via `S.beats` — never `S.scen.beats`. The Impersonator instead randomises over its
`IMPOSTORS` pretext pool.

**Voice scenario (added):** `voicex` ("The Voice Note") is an extract scenario with
`voice:true`, so adversary beats render as playable voice notes (`pushVoice`) spoken with the
browser's built-in `speechSynthesis` — local, no network, no microphone. A "🎙 Send voice note"
button simulates replying by voice and ALWAYS scores a critical `voicebio` leak: the lesson is
that seconds of clean audio is a cloneable biometric, regardless of what you said. Teaches that
audio "evidence" is synthesisable and proves nothing.

**Client-side LLM gateway (hardened):** live mode talks DIRECTLY to any OpenAI-compatible
endpoint from the browser (`callLLM`, no server). Now supports SSE streaming (`readSSE` +
`streamInto`/`pushMsgLive` render tokens into the bubble), custom headers (JSON, merged via
`llmHeaders` — for Azure `api-key`, OpenRouter referer, etc.), a stream on/off toggle, and
model discovery (`fetchModels` → `<base>/models`, populates a datalist). Presets: openai,
anthropic, openrouter, groq, together, fabric, nrp, ollama, litellm. Scoring stays
deterministic/local — the model only supplies adversary wording. Verified end-to-end against a
local mock OpenAI server (streaming, auth, custom-header echo, /models). A published artifact
still can't reach external endpoints (CORS), so it falls back to scripted.

**v3 snapshot:** `counterpart-trainer-v3.html` is the current release — same feature set as v2
plus the hardened client-side LLM gateway as its headline, relabelled v3 in title/header. Repo
`gsankara/cyberchat` now ships v1/v2/v3 + `index.html` landing page + `docs/LLM-GATEWAY.md`
(live-mode setup, provider examples, CORS/hosting notes) + an in-app "Setup & provider
examples" panel in the settings modal. GitHub Pages is enabled by the user.
