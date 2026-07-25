# Live adversary mode — LLM gateway guide

By default Counterpart's adversary is a **deterministic script**: reliable, offline, and
private. **Live mode** instead lets a real LLM voice the adversary's words, while
**all scoring stays local and deterministic** — the model never sees or influences your
score, it only supplies chat text.

The browser talks **directly** to an OpenAI-compatible endpoint. There is no server of
ours in the loop: the request goes client-side from the page to
`<endpoint>/chat/completions`.

> ⚠️ Live mode sends the conversation to the endpoint you configure — your typed messages
> leave the browser to reach it. Use a **test persona**, never real personal data.

## Enabling it

1. Open the app and click the **⚙ gear** (top right).
2. Toggle **Enable live adversary**.
3. Pick a **Gateway preset** (or *Custom*). The preset fills the endpoint; set the model
   by typing it or clicking **Fetch models**.
4. Paste your **API key** (sent as `Authorization: Bearer <key>`).
5. Add **custom headers** if your gateway needs them (see below).
6. Click **Test connection**, then **Save**. The status badge turns violet ("Live LLM").

If any call fails, the scenario **silently falls back** to the scripted engine.

## What gets sent

For each adversary turn the page POSTs an OpenAI chat-completions request:

```
POST <endpoint>/chat/completions
Authorization: Bearer <your key>          # omitted if no key
Content-Type: application/json
<your custom headers, merged in>

{ "model": "<model>", "messages": [...], "max_tokens": 220,
  "temperature": 0.9, "stream": true }    # stream only when streaming is on
```

- **Streaming** (default on) reads a Server-Sent-Events stream and renders the reply
  token-by-token. Turn it off if your gateway doesn't support SSE.
- **Model discovery**: *Fetch models* does `GET <endpoint>/models` and lists the result.
- Responses are parsed from `choices[0].message.content` (non-streaming),
  `choices[0].delta.content` (streaming), or `content[0].text` (Anthropic-style).

## Provider examples

| Provider | Endpoint | Model | Key / headers |
|---|---|---|---|
| OpenAI | `https://api.openai.com/v1` | `gpt-4o-mini` | Bearer key |
| Anthropic (OpenAI-compat) | `https://api.anthropic.com/v1` | `claude-3-5-haiku-latest` | Bearer key |
| OpenRouter | `https://openrouter.ai/api/v1` | `openai/gpt-4o-mini` | Bearer key; header `{"HTTP-Referer":"…"}` |
| Groq | `https://api.groq.com/openai/v1` | `llama-3.1-8b-instant` | Bearer key |
| Together AI | `https://api.together.xyz/v1` | `meta-llama/Llama-3.1-8B-Instruct-Turbo` | Bearer key |
| Ollama (local) | `http://localhost:11434/v1` | `llama3.1` | no key |
| LiteLLM proxy | `http://localhost:4000/v1` | any routed model | per your proxy config |
| Azure OpenAI | `https://<res>.openai.azure.com/openai/deployments/<dep>` | (deployment) | header `{"api-key":"…"}`, key left blank |

### Custom headers

The **Custom headers** field takes JSON that is merged into every request. Uses:

- Azure OpenAI: `{"api-key":"<key>"}` (Azure uses `api-key`, not `Authorization`)
- OpenRouter attribution: `{"HTTP-Referer":"https://your.site","X-Title":"Counterpart"}`
- Org / project scoping: `{"OpenAI-Organization":"org-…"}`

## CORS and hosting

Because the call is made from the browser, the endpoint must allow the page's origin.

- **Running the file locally** (`file://`) or from your own dev server: works with most
  endpoints, since there's no restrictive origin to block.
- **From a web host (e.g. GitHub Pages)**: the browser will only reach a gateway that
  returns permissive **CORS** headers (`Access-Control-Allow-Origin`). Most cloud LLM APIs
  do **not** send these to arbitrary origins, so live mode there falls back to the script.
  A **local proxy** (LiteLLM, Ollama, vLLM) with CORS enabled is the usual way to use live
  mode from a hosted page.

## Privacy summary

- No data leaves the browser **unless** you enable live mode.
- With live mode on, only the **conversation text** goes to **your configured endpoint** —
  nowhere else.
- Your API key and settings live in this browser's `localStorage` only.
- **Scoring is always computed locally**; the LLM has no part in it.
