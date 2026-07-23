---
name: browser-pane-file-nav
description: In the in-app Browser pane, local file:// pages can only be loaded by the Write/Edit preview hook — navigate and JS location changes are blocked
metadata:
  type: reference
---

When working on a local `.html` file on this machine, the in-app Browser pane
(mcp__Claude_Browser__*) can only load `file://` URLs via the **Write/Edit
PostToolUse preview hook**, which loads the file you just wrote/edited into the pane.

These do NOT work for `file://`:
- `navigate` / `preview_start` with a `file://` URL → "navigation to https://file was denied".
- Setting `window.location.href` to another local file from a `file://` page →
  silently ignored; href stays put.

Consequence: you cannot switch the pane between two local files (e.g. restore v1 after
editing v2) without touching one of their files to trigger the hook. To drive/test a
loaded local page, use `javascript_tool` (evaluate DOM, click, set input values).
Screenshots often time out in this pane — verify via DOM reads instead. Long
promise-based test scripts can exceed the 30s tool ceiling while the page keeps running;
split steps across separate calls. See [[counterpart-trainer]].
