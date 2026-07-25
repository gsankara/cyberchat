# Counterpart — Adversarial Chat & Deepfake Defense Range

Privacy-preserving training web apps that put a user across from a **simulated adversary**.
Some scenarios train you to give away nothing; others train you to **unmask a deepfake** by
asking the right questions.

Each app is a **single self-contained HTML file** — no build, no dependencies. By default
all scoring runs client-side and nothing you type leaves the browser. An optional
[live LLM mode](docs/LLM-GATEWAY.md) can voice the adversary via your own gateway.

## Files

| File | Description |
|---|---|
| [`counterpart-trainer-v3.html`](counterpart-trainer-v3.html) | **v3** (current) — v2 plus a hardened client-side LLM gateway: streaming, custom headers, model discovery |
| [`counterpart-trainer-v2.html`](counterpart-trainer-v2.html) | **v2** — deepfake defense, information-theoretic leakage, voice extortion, countermeasures |
| [`counterpart-trainer.html`](counterpart-trainer.html) | **v1** — the original PII-fragmentation scenarios |

Open any file directly in a browser, or use the [landing page](index.html).

## Scenarios

**v1 and v2**
- **The Friendly Quiz** — a "personality quiz" bot harvests security-question answers and
  password ingredients one fragment at a time (zodiac → favourite number → first pet → email).
- **The Admirer** — the grooming-and-coercion pattern used in sextortion. Non-explicit by
  design: the skill practised is recognising the tactics, refusing, and knowing what to do
  if threatened.
- **The Pincer** — two personas in parallel chats each ask "harmless" questions; a live
  correlation panel assembles the fragments into one attackable identity.

**v2 only**
- **The Sieve** — partitioning attacks, where answering *"no"* leaks the complement just as
  surely as *"yes"*. A candidate-pool meter halves 8 billion → 4 → 2 … per bit disclosed.
- **The Impersonator** — a deepfake of someone you know makes an urgent request
  (manager, family, IT desk, friend, or bank — randomised per run). You are the *verifier*.

## The particle model (deepfake defense)

The Impersonator scenario scores the **questions you ask**, using an authentication idea
adapted from IoT research: a *particle* is a piece of information scored by **volatility,
entropy, history, observability, and shared-secret** value. A deepfake can clone a face, a
voice, and everything public — but not what's fresh, private, prearranged, or live.

| Particle type | What the deepfake does | Verdict |
|---|---|---|
| Public / OSINT (birthday, job title) | answers confidently | weak — it scraped that |
| Volatile / fresh ("what did we *just* discuss?") | stalls, gets urgent | strong |
| Liveness ("touch your ear, say today's date") | can't perform | strong |
| Shared secret (pre-agreed code word) | fails | strongest |
| Leading ("…our code word is X, right?") | banks your answer | penalised as a leak |
| Out-of-band ("I'll call you back on your known number") | resists hardest | best countermeasure |

Question strength tracks the **strongest** anti-deepfake dimension — you only need one thing
it cannot fake.

## Countermeasures

Beyond refusing, v2 recognises two tactics as legitimate defenses:

- **⏸ Stay silent** — a non-answer probes the adversary's persistence and motive. The harder
  they push a "harmless" question, the more it's worth to them.
- **🃏 Decoy** — deliberate disinformation. A false or malformed value carries no real
  information, so it is scored as noise, not a leak. (Caveats surfaced in-app: keep fakes
  consistent, and never use them where a service must genuinely verify you.)

## Cover identity

Scenarios that harvest PII assign you a **random synthetic identity** at the start of each run
(name, city, birth year, zodiac, favourite number, first pet, childhood street, email), shown
in a side panel. This makes scoring exact and verifiable:

- revealing an **assigned** value → **leak** (the field is struck through)
- any **other** value → **decoy** (noise)
- nothing → **hold**

## Live adversary mode (optional)

The adversary can be voiced by a real LLM through any **OpenAI-compatible** gateway, entirely
**client-side** — the browser POSTs directly to `<endpoint>/chat/completions`, with no server
in between. Configure it under the settings **⚙** (endpoint, model, key, custom headers;
stored only in your browser's `localStorage`).

v3 makes it a proper gateway client:

- **Streaming** replies token-by-token (SSE), with a toggle for gateways that don't support it
- **Custom headers** for Azure (`api-key`), OpenRouter referer, org IDs, …
- **Model discovery** — *Fetch models* lists `<endpoint>/models`
- **Presets**: OpenAI, Anthropic, OpenRouter, Groq, Together, FABRIC AI, NRP, Ollama, LiteLLM

**Scoring always stays deterministic and local** — the model only supplies the adversary's
words. If a call fails, the scenario silently falls back to the script. Because the request is
made from the browser, a web-hosted page (e.g. GitHub Pages) can only reach a **CORS-enabled**
gateway; running the file locally avoids this. Full setup: **[docs/LLM-GATEWAY.md](docs/LLM-GATEWAY.md)**.

## Safety note

These are safety-training simulations. The scripted adversaries never request or display
explicit content. If a real situation resembles these scenarios — especially one involving a
minor — stop and reach a trusted adult and the reporting resources shown at the end of each run.
